---
title: "在 Azure 应用服务中启用 Web 应用的诊断日志记录"
description: "了解如何启用诊断日志记录并将检测添加到应用程序，以及如何访问由 Azure 记录的信息。"
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: c9da27b2-47d4-4c33-a3cb-1819955ee43b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2016
ms.author: cephalin
ms.openlocfilehash: 42644bd97443e827b523503989b0b25d3d48028c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="enable-diagnostics-logging-for-web-apps-in-azure-app-service"></a>在 Azure 应用服务中启用 Web 应用的诊断日志记录
## <a name="overview"></a>概述
Azure 提供内置诊断功能，可帮助调试[应用服务 Web 应用](http://go.microsoft.com/fwlink/?LinkId=529714)。 在本文中，将了解如何启用诊断日志记录并将检测添加到应用程序，以及如何访问由 Azure 记录的信息。

本文通过 [Azure 门户](https://portal.azure.com)、Azure PowerShell 和 Azure 命令行接口 (Azure CLI) 使用诊断日志。 有关通过 Visual Studio 使用诊断日志的信息，请参阅[在 Visual Studio 中对 Azure 进行故障排除](web-sites-dotnet-troubleshoot-visual-studio.md)。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="whatisdiag"></a>Web 服务器诊断和应用程序诊断
应用服务 Web 应用为 Web 服务器和 Web 应用程序中的日志记录信息提供诊断功能。 这些诊断功能按逻辑分为 **Web 服务器诊断**和**应用程序诊断**。

### <a name="web-server-diagnostics"></a>Web 服务器诊断
可以启用或禁用以下种类的日志：

* **详细错误日志记录** - 指示故障的 HTTP 状态代码（状态代码 400 或更大数字）的详细错误消息。 其中可能包含有助于确定服务器返回错误代码的原因的信息。
* **失败请求跟踪** - 有关失败请求的详细信息，包括对用于处理请求的 IIS 组件和每个组件所用的时间的跟踪。 在尝试提高站点性能或隔离导致要返回特定 HTTP 错误的内容时，此信息很有用。
* **Web 服务器日志记录** - 使用 [W3C 扩展日志文件格式](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)的 HTTP 事务信息。 这在确定整体站点度量值（如处理的请求数量或来自特定 IP 地址的请求数）时非常有用。

### <a name="application-diagnostics"></a>应用程序诊断
应用程序诊断可以捕获由 Web 应用程序产生的信息。 ASP.NET 应用程序可使用 [System.Diagnostics.Trace](http://msdn.microsoft.com/library/36hhw2t6.aspx) 类将信息记录到应用程序诊断日志。 例如：

    System.Diagnostics.Trace.TraceError("If you're seeing this, something bad happened");

在运行时，可以检索这些日志帮助进行故障排除。 有关详细信息，请参阅[在 Visual Studio 中对 Azure Web 应用进行故障排除](web-sites-dotnet-troubleshoot-visual-studio.md)。

将内容发布到某个 Web 应用时，应用服务 Web 应用还记录部署信息。 此操作自动执行，不会对部署日志记录进行配置设置。 部署日志记录允许确定部署失败的原因。 例如，如果使用自定义部署脚本，可能会使用部署日志记录确定该脚本失败的原因。

## <a name="enablediag"></a>如何启用诊断
要在 [Azure 门户](https://portal.azure.com)中启用诊断，请转到 Web 应用的边栏选项卡，并依次单击“设置”>“诊断日志”。

<!-- todo:cleanup dogfood addresses in screenshot -->
![日志部分](./media/web-sites-enable-diagnostic-log/logspart.png)

启用“应用程序诊断”时，还需选择“级别”。 此设置将捕获的信息筛选为“信息性”信息、“警告”信息或“错误”信息。 若将此选项设置为“详细”，将记录应用程序生成的所有信息。

> [!NOTE]
> 与更改 Web.config 文件不同，启用应用程序诊断或更改诊断日志级别不会回收运行应用程序的应用域。
>
>

在[经典门户](https://manage.windowsazure.com) Web 应用的“配置”选项卡中，可选择用于“Web 服务器日志记录”的“存储”或“文件系统”。 选择“存储”可选择存储帐户，然后将日志写入 Blob 容器。 仅将“站点诊断”的所有其他日志写入文件系统。

[经典门户](https://manage.windowsazure.com) Web 应用的“配置”选项卡还包含用于应用程序诊断的其他设置：

* **文件系统** - 将应用程序诊断信息存储到 Web 应用文件系统。 可通过 FTP 访问这些文件，或使用 Azure PowerShell 或 Azure 命令行接口 (Azure CLI) 将这些文件作为 Zip 存档下载。
* **表存储** - 将应用程序诊断信息存储在指定的 Azure 存储帐户和表名中。
* **Blob 存储** - 将应用程序诊断信息存储在指定的 Azure 存储帐户和 Blob 容器中。
* **保留期** - 默认情况下，不会从“Blob 存储”自动删除日志。 如果希望自动删除日志，可选择“设置保留”并输入保存日志的天数。

> [!NOTE]
> 如果[重新生成存储帐户的访问密钥](../storage/common/storage-create-storage-account.md)，则必须重置相应的日志记录配置才能使用更新的密钥。 为此，请按以下步骤操作：
>
> 1. 在“配置”选项卡上，将相应的日志记录功能设置为“关闭”。 保存设置。
> 2. 再次将日志记录到存储帐户 blob 或表。 保存设置。
>
>

可同时启用文件系统、表存储或 Blob 存储的任意组合，并拥有单独的日志级别配置。 例如，你可能希望将错误和警告记录到 Blob 存储中作为长期的日志记录解决方案，同时将文件系统日志记录级别设置为“详细”。

尽管所有三个存储位置都可为记录事件提供相同的基本信息，但与记录到“文件系统”相比，“表存储”和“Blob 存储”记录包含额外信息，如实例 ID、线程 ID 以及更详细的时间戳（刻度格式）。

> [!NOTE]
> 只能使用存储客户端访问或由直接使用这些存储系统的应用程序访问存储在“表存储”或“Blob 存储”中的信息。 例如，Visual Studio 2013 包含的存储资源管理器可用于浏览表或 Blob 存储，而 HDInsight 可以访问存储在 Blob 存储中的数据。 还可编写通过使用 [Azure SDK](/downloads/#) 之一访问 Azure 存储的应用程序。
>
> [!NOTE]
> 也可从 Azure PowerShell 使用 **Set-AzureWebsite** cmdlet 启用诊断。 如果尚未安装 Azure PowerShell，或尚未将其配置为使用 Azure 订阅，请参阅[如何使用 Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/)。
>
>

## <a name="download"></a>如何：下载日志
可使用 FTP 直接访问存储到 Web 应用文件系统的诊断信息。 还可使用 Azure PowerShell 或 Azure 命令行接口将这些信息作为 Zip 存档下载。

存储日志采用的目录结构如下：

* **应用程序日志** - /LogFiles/Application/。 此文件夹包含一个或多个包含应用程序日志记录生成的信息的文本文件。
* **失败请求跟踪** - /LogFiles/W3SVC#########/。 此文件夹包含一个 XSL 文件和一个或多个 XML 文件。 请确保将 XSL 文件下载到 XML 文件所在的目录，因为 XSL 文件可提供格式化和筛选这些文件的内容的功能（在 Internet Explorer 中查看 XML 文件时）。
* **详细错误日志** - /LogFiles/DetailedErrors/。 此文件夹包含一个或多个 .htm 文件，这些文件可提供大量有关所有已出现 HTTP 错误的信息。
* **Web 服务器日志** - /LogFiles/http/RawLogs。 此文件夹包含使用 [W3C 扩展日志文件格式](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)进行格式化的一个或多个文本文件。
* **部署日志** - /LogFiles/Git。 此文件夹包含由 Azure Web 应用使用的内部部署过程生成的日志和 Git 部署的日志。

### <a name="ftp"></a>FTP
要使用 FTP 访问诊断信息，请在[经典门户](https://manage.windowsazure.com)中访问 Web 应用的“仪表板”。 在“速览”部分中，使用“FTP 诊断日志”链接通过 FTP 访问日志文件。 “部署/FTP 用户”项列出了应该用于访问 FTP 站点的用户名。

> [!NOTE]
> 如果未设置“部署/FTP 用户”项或忘记该用户的密码，可通过使用“仪表板”中“速览”部分的“重置部署凭据”链接创建新用户和密码。
>
>

### <a name="download-with-azure-powershell"></a>使用 Azure PowerShell 下载
若要下载日志文件，请启动 Azure PowerShell 的新实例并使用以下命令：

    Save-AzureWebSiteLog -Name webappname

这会将 **-Name** 参数指定的 Web 应用的日志保存到当前目录中名为 **logs.zip** 的文件。

> [!NOTE]
> 如果尚未安装 Azure PowerShell，或尚未将其配置为使用 Azure 订阅，请参阅[如何使用 Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/)。
>
>

### <a name="download-with-azure-command-line-interface"></a>使用 Azure 命令行接口下载
若要使用 Azure 命令行接口下载日志文件，请打开新的命令提示符、PowerShell、Bash 或终端会话，并输入以下命令：

    azure site log download webappname

这会将名为“webappname”的 Web 应用的日志保存到当前目录中名为 **diagnostics.zip** 的文件。

> [!NOTE]
> 如果尚未安装 Azure 命令行接口 (Azure CLI)，或尚未将其配置为使用 Azure 订阅，请参阅[如何使用 Azure CLI](../cli-install-nodejs.md)。
>
>

## <a name="how-to-view-logs-in-application-insights"></a>如何：在 Application Insights 中查看日志
Visual Studio Application Insights 可提供用于筛选和搜索日志的工具，以及用于将日志与请求和其他事件关联的工具。

1. 在 Visual Studio 中，将 Application Insights SDK 添加到项目。
   * 在解决方案资源管理器中，右键单击项目并选择“添加 Application Insights”。 本教程介绍所有步骤，包括创建 Application Insights 资源。 [了解详细信息](../application-insights/app-insights-asp-net.md)
2. 将跟踪侦听器包添加到项目。
   * 右键单击项目，并选择“管理 NuGet 包”。 选择 `Microsoft.ApplicationInsights.TraceListener` [了解详细信息](../application-insights/app-insights-asp-net-trace-logs.md)
3. 上传项目并运行，以生成日志数据。
4. 在 [Azure 门户](https://portal.azure.com/)中，浏览到新的 Application Insights 资源，并打开“搜索”。 会显示日志数据，以及请求、使用情况和其他遥测。 某些遥测可能需要几分钟才能到达：请单击“刷新”。 [了解详细信息](../application-insights/app-insights-diagnostic-search.md)

[了解有关使用 Application Insights 跟踪性能的详细信息](../application-insights/app-insights-azure-web-apps.md)

## <a name="streamlogs"></a>如何：流式传输日志
开发应用程序时，以近乎实时的方式查看日志记录信息通常很有用。 通过使用 Azure PowerShell 或 Azure 命令行接口将日志记录信息流式传输到开发环境，可以实现此目的。

> [!NOTE]
> 某些类型的日志记录缓冲区会对日志文件执行写入操作，这可能会导致流中的事件变成混乱。 例如，用户访问页面时出现的应用程序日志项可能显示在该页面请求所对应的 HTTP 日志项的前面。
>
> [!NOTE]
> 日志流式传输还将对写入存储在 **D:\\home\\LogFiles\\** 文件夹中的任何文本文件的信息进行流式传输。
>
>

### <a name="streaming-with-azure-powershell"></a>使用 Azure PowerShell 进行流式传输
若要流式传输日志记录信息，请启动新的 Azure PowerShell 实例并使用以下命令：

    Get-AzureWebSiteLog -Name webappname -Tail

这会连接到 **-Name** 参数指定的 Web 应用，并在该 Web 应用上出现日志事件时开始将信息流式传输到 PowerShell 窗口。 写入以 .txt、.log 或 .htm 结尾并存储在 /LogFiles 目录 (d:/home/logfiles) 中文件的所有信息将流式传输至本地控制台。

若要筛选特定事件（如错误），请使用 **-Message** 参数。 例如：

    Get-AzureWebSiteLog -Name webappname -Tail -Message Error

若要筛选特定日志类型（如 HTTP），请使用 **-Path** 参数。 例如：

    Get-AzureWebSiteLog -Name webappname -Tail -Path http

若要查看可用的路径列表，请使用 -ListPath 参数。

> [!NOTE]
> 如果尚未安装 Azure PowerShell，或尚未将其配置为使用 Azure 订阅，请参阅[如何使用 Azure PowerShell](/develop/nodejs/how-to-guides/powershell-cmdlets/)。
>
>

### <a name="streaming-with-azure-command-line-interface"></a>使用 Azure 命令行接口进行流式传输
若要流式传输日志记录信息，请打开新的命令行提示、PowerShell、Bash 或终端会话并输入以下命令：

    azure site log tail webappname

这会连接到名为“webappname”的 Web 应用，并在该 Web 应用上出现日志事件时开始将信息流式传输到窗口。 写入以 .txt、.log 或 .htm 结尾并存储在 /LogFiles 目录 (d:/home/logfiles) 中文件的所有信息将流式传输至本地控制台。

若要筛选特定事件（如错误），请使用 **--Filter** 参数。 例如：

    azure site log tail webappname --filter Error

若要筛选特定日志类型（如 HTTP），请使用 **-Path** 参数。 例如：

    azure site log tail webappname --path http

> [!NOTE]
> 如果尚未安装 Azure 命令行接口，或尚未将其配置为使用 Azure 订阅，请参阅[如何使用 Azure 命令行接口](../cli-install-nodejs.md)。
>
>

## <a name="understandlogs"></a>如何：了解诊断日志
### <a name="application-diagnostics-logs"></a>应用程序诊断日志
应用程序诊断将信息以特定格式存储在 .NET 应用程序中，具体取决于是将日志存储到文件系统、表存储还是 Blob 存储。 三种存储类型存储的基本数据信息相同 — 事件发生的日期和时间，生成事件的进程 ID，事件类型（信息、警告、错误）以及事件消息。

**文件系统**

记录到文件系统或使用流式传输收到的每行都会采用以下格式：

    {Date}  PID[{process id}] {event type/level} {message}

例如，错误事件可能类似如下所示：

    2014-01-30T16:36:59  PID[3096] Error       Fatal error on the page!

记录到文件系统可提供三种可用方法的最基本信息，仅提供时间、进程 ID、事件级别以及消息。

**表存储**

如果记录到表存储，可通过额外属性简化表中存储数据的搜索，并获取更详尽的事件信息。 以下属性（列）可用于存储在表中的所有实体（行）。

| 属性名称 | 值/格式 |
| --- | --- |
| PartitionKey |事件的日期/时间，格式为 yyyyMMddHH |
| RowKey |唯一标识该实体的 GUID 值 |
| Timestamp |事件发生的日期和时间 |
| EventTickCount |事件发生的日期和时间，刻度格式（精度更高） |
| ApplicationName |Web 应用名称 |
| 级别 |事件级别（例如错误、警告、信息） |
| EventId |此事件的事件 ID<p><p>如果未指定，则默认为 0 |
| InstanceId |发生事件的 Web 应用实例 |
| Pid |进程 ID |
| Tid |生成事件的线程的线程 ID |
| 消息 |事件详细消息 |

**Blob 存储**

如果记录到 Blob 存储，数据以逗号分隔值 (CSV) 格式存储。 与表存储类似，记录额外字段可提供更详尽的事件相关信息。 以下属性适用于每一行（CSV 格式）：

| 属性名称 | 值/格式 |
| --- | --- |
| 日期 |事件发生的日期和时间 |
| 级别 |事件级别（例如错误、警告、信息） |
| ApplicationName |Web 应用名称 |
| InstanceId |发生事件的 Web 应用实例 |
| EventTickCount |事件发生的日期和时间，刻度格式（精度更高） |
| EventId |此事件的事件 ID<p><p>如果未指定，则默认为 0 |
| Pid |进程 ID |
| Tid |生成事件的线程的线程 ID |
| 消息 |事件详细消息 |

存储在 Blob 中的数据如下所示：

    date,level,applicationName,instanceId,eventTickCount,eventId,pid,tid,message
    2014-01-30T16:36:52,Error,mywebapp,6ee38a,635266966128818593,0,3096,9,An error occurred

> [!NOTE]
> 日志的第一行将包含列标题，如示例中所示。
>
>

### <a name="failed-request-traces"></a>失败请求跟踪
失败请求跟踪存储在名为 **fr######.xml** 的 XML 文件中。 为了便于查看记录信息，在 XML 文件所在目录中提供了一个名为 **freb.xsl** 的 XSL 样式表。 在 Internet Explorer 中打开其中一个 XML 文件，使用 XSL 样式表提供易于阅读的跟踪信息。 显示结果类似如下所示：

![在浏览器中查看失败请求](./media/web-sites-enable-diagnostic-log/tws-failedrequestinbrowser.png)

### <a name="detailed-error-logs"></a>详细的错误日志
详细的错误日志是 HTML 文档，可提供有关发生的 HTTP 错误的详细信息。 由于它们只是 HTML 文档，所以可以使用 Web 浏览器查看。

### <a name="web-server-logs"></a>Web 服务器日志
可使用 [W3C 扩展日志文件格式](http://msdn.microsoft.com/library/windows/desktop/aa814385.aspx)格式化 Web 服务器日志。 可使用文本编辑器读取此信息，或使用诸如[日志分析程序](http://go.microsoft.com/fwlink/?LinkId=246619)等实用工具进行解析。

> [!NOTE]
> Azure Web 应用生成的日志不支持 **s-computername**、**s-ip** 或 **cs-version** 字段。
>
>

## <a name="nextsteps"></a>后续步骤
* [如何监视 Web 应用](web-sites-monitor.md)
* [在 Visual Studio 中对 Azure Web 应用进行故障排除](web-sites-dotnet-troubleshoot-visual-studio.md)
* [在 HDInsight 中分析 Web 应用日志](http://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)

> [!NOTE]
> 如果要在注册 Azure 帐户之前开始使用 Azure 应用服务，请转到[试用应用服务](https://azure.microsoft.com/try/app-service/)，可以在应用服务中立即创建一个生存期较短的入门 Web 应用。 不需要使用信用卡，也不需要做出承诺。
>
>

## <a name="whats-changed"></a>发生的更改
* 有关从网站更改为应用服务的指南，请参阅 [Azure 应用服务及其对现有 Azure 服务的影响](http://go.microsoft.com/fwlink/?LinkId=529714)
* 有关从旧门户更改为新门户的指南，请参阅：[有关在 Azure 门户中导航的参考](http://go.microsoft.com/fwlink/?LinkId=529715)
