---
title: "Azure 机器学习试验执行服务概览"
description: "本文档为 Azure 机器学习试验执行服务提供简要概述"
services: machine-learning
author: gokhanuluderya-msft
ms.author: gokhanu
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/17/2017
ms.openlocfilehash: bb1c7d318939c42edb9a51e28dec31593f2485f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-machine-learning-experiment-execution-service"></a>Azure 机器学习试验执行服务概览
凭借 Azure ML（Azure 机器学习）试验执行服务，数据科学家可使用 Azure ML 的执行和运行管理功能来执行其试验。 它通过快速迭代提供敏捷试验的框架。 使用 Azure ML Workbench，可从计算机上的本地运行开始，轻松纵向和横向扩展到其他环境，如使用 GPU 的远程数据科学 VM 或运行 Spark 的 HDInsight 群集。

试验执行服务旨在为试验提供隔离、可重现且一致的运行。 它可帮助管理计算目标、执行环境和运行配置。 通过使用 Azure ML Workbench 执行和运行管理功能，可以在不同环境之间轻松移动。 

可以在本地执行或在云中大规模执行 Azure ML Workbench 项目中的 Python 或 PySpark 脚本。 

可在以下环境中运行脚本： 

* 通过 Azure ML Workbench 安装在本地计算机上的 Python (3.5.2) 环境。
* 本地计算机上 Docker 容器内部的 Conda Python 环境
* 远程 Linux 计算机上 Docker 容器内部的 Conda Python 环境。 例如，[Azure 上基于 Ubuntu 的 DSVM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* Azure 上的 [HDInsight for Spark](https://azure.microsoft.com/services/hdinsight/apache-spark/)

>[!IMPORTANT]
>Azure ML 执行服务当前支持的 Python 运行时版本为 Python 3.5.2，支持的 Spark 运行时版本为 Spark 2.1.11。 


## <a name="key-concepts-in-azure-ml-experiment-execution"></a>Azure ML 试验执行中的核心概念
请务必了解 Azure ML 试验执行中的以下概念。 在后续部分中，我们将详细讨论如何使用这些概念。 
### <a name="compute-target"></a>计算目标
计算目标指定执行用户程序的位置，如用户桌面、VM 上的远程 Docker 或群集。 计算目标必须是可寻址且可供用户访问的。 通过 Azure ML Workbench，可使用 Workbench 应用程序和 CLI 创建计算目标并管理它们。 

凭借 CLI 中的 az ml computetarget attach 命令，可创建用于运行的计算目标。

### <a name="supported-compute-targets-are"></a>支持的计算目标为：
* 通过 Azure ML Workbench 安装在本地计算机上的本地 Python (3.5.2) 环境。
* 计算机上的本地 Docker
* Linux Ubuntu VM 上的远程 Docker。 例如，[Azure 上基于 Ubuntu 的 DSVM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu)
* Azure 上的 [HDInsight for Spark 群集](https://azure.microsoft.com/services/hdinsight/apache-spark/)

Azure ML 执行服务当前支持的 Python 运行时版本为 Python 3.5.2，支持的 Spark 运行时版本为 Spark 2.1.11。 

>[!IMPORTANT]
> 不支持将运行 Docker 的 Windows VM 作为远程计算目标。

### <a name="execution-environment"></a>执行环境
执行环境定义在 Azure ML Workbench 中运行程序所需的运行时配置和依赖项。

如果在 Azure ML Workbench 默认运行时环境中运行，用户可使用自己青睐的工具和包管理器管理本地执行环境。 

Conda 用于管理本地 Docker 和远程 Docker 执行，以及基于 HDInsigh 的执行。 对于这些计算目标，通过 Conda_dependencies.yml 和 Spark_dependencies.yml 文件管理执行环境配置。 这些文件位于项目中的 aml_config 文件夹。

**支持执行环境的运行时：**
* Python 3.5.2
* Spark 2.1.11

### <a name="run-configuration"></a>运行配置
除了计算目标和执行环境，Azure ML 还提供定义和更改运行配置的框架。 在迭代试验中，试验的不同执行可能需要不同的配置。 可能需要覆盖不同的参数范围、使用不同的数据源并微调 spark 参数。 Azure ML 执行服务提供管理运行配置的框架。

运行 az ml computetarget attach 命令将在项目的 aml_config 文件夹中生成两个文件：.compute 和 .runconfig.compute，命名规则为 <your_computetarget_name>.compute 和 <your_computetarget_name>.runconfig。 方便起见，创建计算目标时，将自动创建 .runconfig 文件。 可在 CLI 中使用 az ml runconfigurations 命令创建和管理其他运行配置。 还可在文件系统中创建和编辑运行配置。

Azure ML Workbench 中的运行配置可指定环境变量。 通过在 .runconfig 文件中添加以下部分，可指定环境变量并在代码中使用它们。 

```
EnvironmentVariables:
"EXAMPLE_ENV_VAR1": "Example Value1"
"EXAMPLE_ENV_VAR2": "Example Value2"
```

可在代码中访问这些环境变量。 例如，此 phyton 代码片段将打印名为“EXAMPLE_ENV_VAR1”的环境变量
```
print(os.environ.get("EXAMPLE_ENV_VAR1"))
```

下图显示初始试验运行的简要流程。__
![](media/experiment-execution-configuration/experiment-execution-flow.png)

## <a name="azure-ml-experiment-execution-scenarios"></a>Azure ML 试验执行方案
在本部分中，我们将深入探讨执行方案，并了解 Azure ML 如何运行试验，特别是如何在本地、远程 VM 和 HDInsight 群集上运行试验。 本部分将从创建计算目标演示到执行试验。

>[!NOTE]
>对于本文的其余部分，我们将使用 CLI（命令行界面）命令来展示概念和功能。 此处所述的功能也可从 Workbench 桌面应用程序使用。

## <a name="launching-the-cli"></a>启动 CLI
快速启动 CLI 的方法是在 Azure ML Workbench 桌面应用程序中打开项目，然后选择“打开”-->“打开命令提示符”。

![](media/experiment-execution-configuration/opening-cli.png)

此命令将启动终端窗口，可在其中输入执行当前项目文件夹中脚本的命令。 此终端窗口配置了使用 Workbench 安装的 Python 3.5.2 环境。

>[!NOTE]
> 从命令窗口执行任何 az ml 命令，都需要通过 Azure 身份验证。 CLI 使用独立的身份验证缓存和桌面应用程序，因此登录到 Workbench 桌面应用程序并不意味着已在 CLI 环境中进行身份验证。 要进行身份验证，请执行以下步骤。 身份验证令牌将在本地缓存一段时间，因此令牌过期后重复这些步骤即可。 如果令牌过期或看到身份验证错误，请执行以下命令：

```
# to authenticate 
$ az login

# to list subscriptions
$ az account list -o table

# to set current subscription to a particular subscription ID 
$ az account set -s <subscription_id>

# to verify your current Azure subscription
$ az account show
```

>[!NOTE] 
>在项目文件夹中运行 az ml 命令时，请确保项目属于当前 Azure 订阅中的 Azure ML 试验帐户。 否则可能会遇到执行错误。


## <a name="running-scripts-and-experiments"></a>运行脚本和试验
凭借 Azure ML Workbench，可以使用 az ml experiment submit 命令，执行针对各种计算目标的 Python 和 PySpark 脚本。 此命令要求运行配置定义。 

创建计算目标时，Azure ML Workbench 会创建相应的 .runconfig 文件，但可使用 az ml runconfiguration create 命令创建其他运行配置。 还可以手动编辑运行配置文件。

Workbench 应用程序中的试验运行体验将显示运行配置。 

>[!NOTE]
>在[试验执行配置参考](experiment-execution-configuration-reference.md)部分，可以了解关于运行配置文件的详细信息。

## <a name="running-a-script-locally-on-azure-ml-workbench-installed-runtime"></a>在 Azure ML Workbench 安装的运行时环境中本地运行脚本
凭借 Azure ML Workbench，可直接针对 Azure ML Workbench 安装的 Python 3.5.2 运行时运行脚本。 此默认运行时在 Azure ML Workbench 安装时安装，它包括 Azure ML 库和依赖项。 本地执行的运行结果和项目仍保存在云中的运行历史记录服务内。

与基于 Docker 的执行不同，此配置不由 Conda 托管。 需要手动预配本地 Azure ML Workbench Python 环境的包依赖项。

可以执行以下命令，在 Workbench 安装的 Python 环境中本地运行脚本。 

```
$az ml experiment submit -c local myscript.py
```

可在 Azure ML Workbench CLI 窗口中键入以下命令，以查找默认 Python 环境的路径。
```
$ conda env list
```

>[!NOTE]
>目前不支持针对本地 Spark 环境直接本地运行 PySpark。 Azure ML Workbench 支持在本地 Docker 上运行 PySpark 脚本。 Azure ML 基础 Docker 映像随预装的 Spark 2.1.11 一同安装。 

_**本地执行 Python 脚本概述：**_
![](media/experiment-execution-configuration/local-native-run.png)

## <a name="running-a-script-on-local-docker"></a>在本地 Docker 上运行脚本
还可以通过 Azure ML 执行服务，针对本地计算机上的 Docker 容器运行项目。 Azure ML Workbench 提供基础 Docker 映像、Azure ML 库以及 Spark 2.1.11 运行时，使本地 Spark 执行更加轻松。 Docker 需要已在本地计算机上运行。

对于在本地 Docker 上运行 Python 或 PySpark 脚本，可以在 CLI 中执行以下命令。

```
$az ml experiment submit -c docker myscript.py
```
或
```
az ml experiment submit --run-configuration docker myscript.py
```

在本地 Docker 上的执行环境使用 Azure ML 基本 Docker 映像准备。 第一次运行时，Azure ML Workbench 将下载此映像，并使用用户的 conda_dependencies.yml 文件中指定的包将其覆盖。 此操作使初始运行较慢，但是由于 Workbench 重复使用缓存层，后续运行将非常快。 

>[!IMPORTANT]
>需要先运行az ml experiment prepare -c docker 命令来为首次运行准备 Docker 映像。 还可以在 docker.runconfig 文件中将 PrepareEnvironment 参数设置为 true。 此操作将自动准备环境作为运行执行的一部分。  

>[!NOTE]
>如果在 Spark 中运行 PySpark 脚本，除了 conda_dependencies.yml，还会使用spark_dependencies.yml。

在 Docker 映像上运行脚本有以下好处：

1. 可确保脚本在其他执行环境中可靠执行。 在 Docker 容器上运行，可帮助你发现并避免可能会影响可移植性的任何本地引用。 

2. 可以针对安装和配置复杂的运行时和框架（如 Apache Spark）快速测试代码，而无需安装这些运行时和框架。


_**本地 Docker 执行 Python 脚本概述：**_
![](media/experiment-execution-configuration/local-docker-run.png)

## <a name="running-a-script-on-a-remote-docker"></a>在远程 Docker 上运行脚本
某些情况下，用户本地计算机上的可用资源可能不足以训练出所需模型。 在这种情况下，可通过 Azure ML 执行服务使用远程 Docker 执行，在更强大的 VM 上轻松运行 Python 或 PySpark 脚本。 

远程 VM 应满足以下要求：
* 远程 VM 须运行 Linux-Ubuntu，并可通过 SSH 访问。 
* 远程 VM 需运行 Docker。

>[!IMPORTANT]
> 不支持将运行 Docker 的 Windows VM 作为远程计算目标


可使用以下命令，为基于 Docker 的远程执行创建计算目标定义和运行配置。

```
az ml computetarget attach --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --password "sshpassword" --type remotedocker
```

配置计算目标后，可使用以下命令运行脚本。
```
$ az ml experiment submit -c remotevm myscript.py
```
>[!NOTE]
>请记住，要使用 conda_dependencies.yml 中的规范配置执行环境。 如果 .runconfig 文件中指定了 PySpark 框架，还要使用 spark_dependencies.yml。 

远程 VM 的 Docker 构造过程与本地 Docker 运行的过程完全相同，所以应可用相同方式执行。

>[!TIP]
>若要避免由于构建第一次运行的 Docker 映像引入的延迟，可先使用以下命令准备计算目标，再执行脚本。 az ml experiment prepare -c <remotedocker>


远程 VM 执行 Python 脚本概述：__
![](media/experiment-execution-configuration/remote-vm-run.png)


## <a name="running-a-script-on-hdinsight-cluster"></a>在 HDInsight 群集上运行脚本
HDInsight 支持 Apache Spark 的大数据分析常用平台。 Azure ML Workbench 允许使用 HDInsight Spark 群集进行大数据试验。 

可使用以下命令为 HDInsight Spark 群集创建计算目标和运行配置：

```
$ az ml computetarget attach --name "myhdi" --address "<FQDN or IP address>" --username "sshuser" --password "sshpassword" --type cluster 
```

>[!NOTE]
>如果使用 FQDN 而不是 IP 地址，并且 HDI Spark 群集的名称为 foo，则 SSH 终结点位于名为 foo-ssh.azurehdinsight.net 的驱动程序节点上。 对于使用 --address 参数的 FQDN，请务必在服务器名称中添加 -ssh 后缀。


有了计算上下文后，可以运行以下命令执行 PySpark 脚本。

```
$ az ml experiment submit -c myhdi myscript.py
```

Azure ML Workbench 使用 Conda 在 HDInsight 群集上准备和管理执行环境。 通过 conda_dependencies.yml 和 spark_dependencies.yml 配置文件来管理配置。 

用户需要通过 SSH 访问 HDInsight 群集，才能在此模式下执行试验。 

>[!NOTE]
>支持的配置是运行 Linux（具有 Python/PySpark 3.5.2 和 Spark 2.1.11 的 Ubuntu）的 HDInsight Spark 群集。

基于 HDInsight 执行 PySpark 脚本概述__
![](media/experiment-execution-configuration/hdinsight-run.png)


## <a name="running-a-script-on-gpu"></a>在 GPU 上运行脚本
若要在 GPU 上运行脚本，可以遵循此文中的指南：[“如何在 Azure 机器学习中使用 GPU”](how-to-use-gpu.md)


## <a name="next-steps"></a>后续步骤
* [创建和安装 Azure 机器学习](quickstart-installation.md)
* [模型管理](model-management-overview.md)
