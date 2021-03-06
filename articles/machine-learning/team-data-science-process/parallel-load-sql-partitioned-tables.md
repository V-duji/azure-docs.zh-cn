---
title: "生成并优化表以便快速将数据并行导入到 Azure VM 上的 SQL Server | Microsoft 文档"
description: "使用 SQL 分区表并行批量导入数据"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: ff90fdb0-5bc7-49e8-aee7-678b54f901c8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: bradsev
ms.openlocfilehash: 899f20b3642612386f2513c9c8649cd845be826e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="parallel-bulk-data-import-using-sql-partition-tables"></a>使用 SQL 分区表并行批量导入数据
本文档介绍如何构建分区表来快速将数据并行批量导入 SQL Server 数据库。 要将大型数据加载/传输到 SQL 数据库，可以通过使用*分区表和视图*加快将数据导入 SQL 数据库和后续查询的速度。 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>创建一个新数据库和一组文件组
* [创建一个新数据库](https://technet.microsoft.com/library/ms176061.aspx)（如果不存在）。
* 将数据库文件组添加到将保存已分区物理文件的数据库。该操作可以通过 [CREATE DATABASE](https://technet.microsoft.com/library/ms176061.aspx)（如果是新数据库）或通过 [ALTER DATABASE](https://msdn.microsoft.com/library/bb522682.aspx)（如果数据库已存在）完成
* 向每个数据库文件组中添加一个或多个文件（根据需要）。
  
  > [!NOTE]
  > 指定保存此分区数据的目标文件组和将存储文件组数据的物理数据库文件名称。
  > 
  > 

下面的示例使用除主组和日志组以外的三个主文件组创建新数据库，每个文件组中包含一个物理文件。 在默认 SQL Server 数据文件夹中创建数据库文件，如 SQL Server 实例中所配置。 有关默认文件位置的详细信息，请参阅 [SQL Server 默认和已命名实例的文件位置](https://msdn.microsoft.com/library/ms143547.aspx)。

    DECLARE @data_path nvarchar(256);
    SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);

    EXECUTE ('
        CREATE DATABASE <database_name>
         ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ), 
         LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')

## <a name="create-a-partitioned-table"></a>创建分区表
根据映射到在上一步中创建的数据库文件组的数据架构，创建分区表。 当将数据批量导入到分区表时，会根据分区方案在文件组之中分布记录，如下所述。

**要创建分区表，需要：**

* [创建分区函数](https://msdn.microsoft.com/library/ms187802.aspx)，用于定义要包括在每个分区表中的值/边界范围，例如，按 2013 年的月份（某些 \_datetime\_ 字段）限制分区：
  
        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )
* [创建分区方案](https://msdn.microsoft.com/library/ms179854.aspx)，将分区函数中的每个分区范围映射到物理文件组，例如：
  
        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
  
  若要根据函数/方案验证每个分区中的有效范围，请运行以下查询：
  
        SELECT psch.name as PartitionScheme,
            prng.value AS ParitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>
* [创建分区表](https://msdn.microsoft.com/library/ms174979.aspx)（根据数据架构），并指定用于对表进行分区的分区方案和约束字段，例如：
  
        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

有关详细信息，请参阅[创建分区表和索引](https://msdn.microsoft.com/library/ms188730.aspx)。

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>批量导入每个分区表的数据
* 可以使用 BCP、BULK INSERT 或其他方法（如 [SQL Server 迁移向导](http://sqlazuremw.codeplex.com/)）。 提供的示例使用 BCP 方法。
* [更改数据库](https://msdn.microsoft.com/library/bb522682.aspx)，以将事务日志记录方案更改为 BULK_LOGGED 以最大限度降低日志记录开销，例如：
  
        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
* 若要加快数据加载，请并行启动批量导入操作。 有关加快将大型数据批量导入到 SQL Server 数据库的提示，请参阅[一小时之内加载 1TB 数据](http://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx)。

下面的 PowerShell 脚本是使用 BCP并行加载数据的示例。

    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0

    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>

    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"

    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir

    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }

    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }

    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }

    Get-Job

    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>创建索引以优化联接和查询性能
* 如果从多个表中提取数据进行建模，请在联接键上创建索引来提高联接性能。
* [创建索引](https://technet.microsoft.com/library/ms188783.aspx)（群集或非群集）为每个分区锁定相同文件组，例如：
  
        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  或者，
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  
  > [!NOTE]
  > 可以选择创建索引后再批量导入数据。 进行批量导入之前创建索引会降低数据加载速度。
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>操作示例中的高级分析流程和技术
有关 Cortana Analytics 过程中使用公用数据集的端到端演练示例，请参阅[操作中的 Cortana Analytics 过程：SQL Server](sql-walkthrough.md)。

