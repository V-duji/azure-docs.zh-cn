---
title: "在 HDInsight 中将 MapReduce 和 Curl 与 Hadoop 配合使用 - Azure | Microsoft Docs"
description: "了解如何使用 Curl 在 HDInsight 上的 Hadoop 上远程运行 MapReduce 作业。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: bc6daf37-fcdc-467a-a8a8-6fb2f0f773d1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: larryfr
ms.openlocfilehash: 28d23cf397db204a22fea785521ea6a164d84374
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-rest"></a>使用 REST 在 HDInsight 上通过 Hadoop 运行 MapReduce 作业

了解如何使用 WebHCat REST API 在 HDInsight 群集的 Hadoop 上运行 MapReduce 作业。 本文档使用 Curl 演示如何通过使用原始 HTTP 请求来与 HDInsight 交互，以便运行 MapReduce 作业。

> [!NOTE]
> 如果已熟悉如何使用基于 Linux 的 Hadoop 服务器，但刚接触 HDInsight，请参阅 [HDInsight 上的基于 Linux 的 Hadoop 须知信息](hdinsight-hadoop-linux-information.md)文档。


## <a id="prereq"></a>先决条件

* HDInsight 群集上的 Hadoop
* [Curl](http://curl.haxx.se/)
* [jq](http://stedolan.github.io/jq/)

## <a id="curl"></a>使用 Curl 运行 MapReduce 作业

> [!NOTE]
> 使用 Curl 或者与 WebHCat 进行任何其他形式的 REST 通信时，必须通过提供 HDInsight 群集管理员用户名和密码对请求进行身份验证。 必须使用群集名称作为用来向服务器发送请求的 URI 的一部分。
>
> 对本部分中的命令，请将“admin”替换为要向群集进行身份验证的用户。 将 **CLUSTERNAME** 替换为群集名称。 出现提示时，请提供用户帐户的密码。
>
> REST API 使用[basic access authentication](http://en.wikipedia.org/wiki/Basic_access_authentication)（基本访问验证）进行保护。 应该始终通过使用 HTTPS 来发出请求，以确保安全地将凭据发送到服务器。


1. 在命令行中，使用以下命令验证你是否可以连接到 HDInsight 群集。

    ```bash
    curl -u admin -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    将收到类似于以下 JSON 的响应：

        {"status":"ok","version":"v1"}

    此命令中使用的参数如下：

   * **-u**：指示用来对请求进行身份验证的用户名和密码
   * **-G**：指示此操作是 GET 请求。

   所有请求的 URI 开头都是 **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**。

2. 若要提交 MapReduce 作业，请使用以下命令：

    ```bash
    curl -u admin -d user.name=admin -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar
    ```

    URI 的末尾 (/mapreduce/jar) 告知 WebHCat，此请求从 jar 文件中的类启动 MapReduce 作业。 此命令中使用的参数如下：

   * **-d**：由于不使用 `-G`，因此请求默认为使用 POST 方法。 `-d` 指定与请求一起发送的数据值。
    * **user.name**：正在运行命令的用户
    * **jar**：包含要运行的类的 jar 文件所在位置
    * **class**：包含 MapReduce 逻辑的类
    * **arg**：要传递到 MapReduce 作业的参数。 在此示例中是用于输出的输入文本文件和目录

   此命令应会返回可用来检查作业状态的作业 ID：

       {"id":"job_1415651640909_0026"}

3. 若要检查作业的状态，请使用以下命令：

    ```bash
    curl -G -u admin -d user.name=admin https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    将 **JOBID** 替换为上一步骤中返回的值。 例如，如果返回值为 `{"id":"job_1415651640909_0026"}`，则 JOBID 将是 `job_1415651640909_0026`。

    如果作业已完成，返回的状态将是 `SUCCEEDED`。

   > [!NOTE]
   > 此 Curl 请求返回包含作业相关信息的 JSON 文档。 Jq 用于仅检索状态值。

4. 在作业的状态更改为 `SUCCEEDED` 后，可以从 Azure Blob 存储中检索作业的结果。 随查询一起传递的 `statusdir` 参数包含输出文件的位置。 在本示例中，位置为 `/example/curl`。 此地址在群集默认存储的 `/example/curl` 中存储作业的输出。

使用 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) 可以列出和下载这些文件。 有关从 Azure CLI 中使用 blob 的详细信息，请参阅[将 Azure CLI 2.0 与 Azure 存储配合使用](../storage/common/storage-azure-cli.md#create-and-manage-blobs)文档。

## <a id="nextsteps"></a>后续步骤

有关 HDInsight 中的 MapReduce 作业的一般信息：

* [将 MapReduce 与 HDInsight 上的 Hadoop 配合使用](hdinsight-use-mapreduce.md)

有关 HDInsight 上的 Hadoop 的其他使用方法的信息：

* [将 Hive 与 Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)
* [将 Pig 与 Hadoop on HDInsight 配合使用](hdinsight-use-pig.md)

有关本文中使用的 REST 接口的详细信息，请参阅 [WebHCat Reference](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)（WebHCat 参考）。