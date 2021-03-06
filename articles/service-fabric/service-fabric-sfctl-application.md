---
title: Azure Service Fabric CLI- sfctl application| Microsoft Docs
description: "介绍 Service Fabric CLI sfctl application 命令。"
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: dc57c813a6aecabc21ac3931b7294bce909778d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-application"></a>sfctl application
创建、删除和管理应用程序及应用程序类型。

## <a name="commands"></a>命令

|命令|说明|
| --- | --- |
| create       | 使用指定说明创建 Service Fabric 应用程序。|
| delete       | 删除现有 Service Fabric 应用程序。|
| deployed     | 获取部署在 Service Fabric 节点上的应用程序的相关信息。|
| deployed-health | 获取部署在 Service Fabric 节点上的应用程序的运行状况
                      相关信息。|
| deployed-list| 获取部署在 Service Fabric 节点上的应用程序的列表。|
| health       | 获取 Service Fabric 应用程序运行状况。|
| info         | 获取 Service Fabric 应用程序的相关信息。|
| list         | 获取在 Service Fabric 群集中创建且与指定为参数的筛选匹配的
                      应用程序列表。|
| load | 获取 Service Fabric 应用程序的相关加载信息。 |
| manifest     | 获取描述应用程序类型的清单。|
| provision    | 向群集预配或注册 Service Fabric 应用程序类型。|
| report-health| 发送有关 Service Fabric 应用程序的运行状况报告。|
| type         | 获取 Service Fabric 群集中与指定名称完全匹配的应用程序类型
                      列表。|
| type-list    | 获取 Service Fabric 群集中应用程序类型的列表。|
| unprovision  | 从群集中删除或注销 Service Fabric 应用程序类型。|
| 升级      | 开始升级 Service Fabric 群集中的应用程序。|
| upgrade-resume  | 继续升级 Service Fabric 群集中的应用程序。|
| upgrade-rollback| 开始回滚 Service Fabric 群集中当前正在进行的
                      应用程序升级。|
| upgrade-status  | 获取对此应用程序执行的最近一次升级的详细信息。|
| upload       | 将 Service Fabric 应用程序包复制到映像存储区。|

## <a name="sfctl-application-create"></a>sfctl application create
使用指定说明创建 Service Fabric 应用程序。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --app-name [必需]| 应用程序名称，包括“fabric:”URI 方案。|
| --app-type [必需]| 在应用程序清单中找到的应用程序类型名称。|
| --app-version [必需]| 应用程序清单中定义的应用程序类型版本。|
| --max-node-count     | Service Fabric 为此应用程序保留的容量的最大节点数。 这并不表示此应用程序的服务放置在所有这些节点上。|
| --metrics            | 应用程序容量指标说明的 JSON 编码列表。 指标定义为一个名称，与应用程序所在的每个节点的一组容量关联。|
| --min-node-count     | Service Fabric 为此应用程序保留的容量的最小节点数。 这并不表示此应用程序的服务放置在所有这些节点上。|
| --parameters         | 创建应用程序时应用的应用程序参数替代的 JSON 编码列表。|
| --timeout -t         | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug              | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h            | 显示此帮助消息并退出。|
| --output -o          | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query              | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose            | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-application-delete"></a>sfctl application delete
删除现有 Service Fabric 应用程序。

删除现有 Service Fabric 应用程序。 必须先创建应用程序才能进行删除。 删除应用程序会删除该应用程序中包含的所有服务。 默认情况下，Service Fabric 尝试正常关闭服务副本，然后删除服务。 但是，如果服务无法正常关闭，删除操作可能需要很长时间，也可能出现停滞。 使用可选 ForceRemove 标志跳过正常关闭序列，强制删除应用程序及其所有服务。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --application-id [必需]| 应用程序标识。 这通常是不包含“fabric:”URI 方案的应用程序全名。 从
                                 version 6.0, hierarchical names are delimited with the "~"
                                 character. For example, if the application name is
                                 "fabric://myapp/app1", the application identity would be
                                 "myapp~app1" in 6.0+ and "myapp/app1" in previous versions.|
| --force-remove | 强制删除 Service Fabric 应用程序或服务，跳过正常关闭序列。 若因服务代码中问题而无法正常关闭副本，导致应用程序或服务删除超时，可使用此代码强制删除这些应用程序或服务。| | --timeout -t | 服务器超时（以秒为单位）。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                 | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h               | 显示此帮助消息并退出。|
| --output -o             | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query                 | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose               | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-application-deployed"></a>sfctl application deployed
获取部署在 Service Fabric 节点上的应用程序的相关信息。|
|     
### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --application-id [必需]| 应用程序标识。 这通常是不包含“fabric:”URI 方案的应用程序全名。 从
                                 version 6.0, hierarchical names are delimited with the "~"
                                 character. For example, if the application name is
                                 "fabric://myapp/app1", the application identity would be
                                 "myapp~app1" in 6.0+ and "myapp/app1" in previous versions.|
| --node-name [必需] | 节点名称。| | --timeout -t | 服务器超时（以秒为单位）。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                 | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h               | 显示此帮助消息并退出。|
| --output -o             | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query                 | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose               | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-application-health"></a>sfctl application health
获取 Service Fabric 应用程序运行状况。

返回 Service Fabric 应用程序的运行状态。 响应报告“Ok”、“Error”或“Warning”运行状态。 如果未在运行状况存储中找到实体，则返回“Error”。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --application-id [必需]| 应用程序标识。 这通常是不包含“fabric:”URI 方案的应用程序全名。 从版本 6.0 开始，
                                                 hierarchical names are delimited with the "~"
                                                 character. For example, if the application name is
                                                 "fabric://myapp/app1", the application identity
                                                 would be "myapp~app1" in 6.0+ and "myapp/app1" in
                                                 previous versions.|
| --deployed-applications-health-state-filter| 用于根据运行状态筛选应用程序运行状况查询结果中返回的已部署应用程序运行状态对象。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的已部署应用程序。 所有已部署应用程序都用于评估聚合运行状态。 如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可以是使用按位“OR”运算符获取的值的组合。                        例如，如果提供的值为 6，则返回 HealthState 值为“OK”(2) 和“Warning”(4) 的已部署应用程序的运行状态。 - Default - 默认值。 匹配任意 HealthState。                        值为 0。 - None - 不与任何 HealthState 值匹配的筛选器。 不返回有关给定状态集合的结果时使用。                        值为 1。 - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。 - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。 - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。 - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。| | --events-health-state-filter | 用于根据运行状态筛选返回的 HealthEvent 对象集合。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的事件。 所有事件都用于评估聚合运行状态。 如果未指定，则返回所有项。                        状态值为基于标志的枚举，因此该值可以是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为 6，则返回 HealthState 值为 OK (2) 和 Warning (4) 的所有事件。 - Default - 默认值。 匹配任意 HealthState。 值为 0。 - None - 不与任何 HealthState 值匹配的筛选器。 不返回有关给定状态集合的结果时使用。 值为 1。 - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。 - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。 - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。 - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。| | --exclude-health-statistics | 指示是否作为查询结果的一部分返回运行状况统计数据。 默认值为 False。 统计信息显示处于 Ok、Warning 和 Error 运行状态的子实体数。| | --services-health-state-filter | 用于根据运行状态筛选服务运行状况结果中返回的服务运行状态对象。 此参数的可能值包括以下运行状态之一的整数值。 仅返回与筛选器匹配的服务。 所有服务都用于评估聚合运行状态。                        如果未指定，则返回所有项。 状态值为基于标志的枚举，因此该值可以是使用按位“OR”运算符获取的值的组合。 例如，如果提供的值为“6”，则返回 HealthState 值为 OK (2) 和 Warning (4) 的服务的运行状态。 - Default - 默认值。 匹配任意 HealthState。 值为 0。                        - None - 不与任何 HealthState 值匹配的筛选器。 不返回有关给定状态集合的结果时使用。 值为 1。 - Ok - 与 HealthState 值为 OK 的输入匹配的筛选器。 值为 2。 - Warning - 与 HealthState 值为 Warning 的输入匹配的筛选器。 值为 4。 - Error - 与 HealthState 值为 Error 的输入匹配的筛选器。 值为 8。 - All - 与具有任意 HealthState 值的输入匹配的筛选器。 值为 65535。| | --timeout -t | 服务器超时（以秒为单位）。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                                 | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h                               | 显示此帮助消息并退出。|
| --output -o                             | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query                                 | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose                               | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-application-info"></a>sfctl application info
获取 Service Fabric 应用程序的相关信息。

返回 Service Fabric 群集中已创建或正在创建且名称与指定为参数的应用程序匹配的应用程序相关信息。 响应包括名称、类型、状态、参数以及应用程序的其他相关详细信息。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --application-id [必需]| 应用程序标识。 这通常是不包含“fabric:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以
                                      with the "~" character. For example, if the application name
                                      is "fabric://myapp/app1", the application identity would be
                                      "myapp~app1" in 6.0+ and "myapp/app1" in previous versions.|
| --exclude-application-parameters| 该标志指定应用程序参数是否排除在结果之外。| | --timeout -t | 服务器超时（以秒为单位）。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                      | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h                    | 显示此帮助消息并退出。|
| --output -o                  | 输出格式。  允许的值：json、jsonc、table、tsv。             默认值：json。|
| --query                      | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose                    | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-application-list"></a>sfctl application list
获取在 Service Fabric 群集中创建且与指定为参数的筛选匹配的应用程序列表。

获取 Service Fabric 群集中已创建或正在创建且与指定为参数的筛选匹配的应用程序相关信息。 响应包括名称、类型、状态、参数以及应用程序的其他相关详细信息。 如果一页无法容纳这些应用程序，则返回一页结果及一个继续标记，该标记可用于获取下一页。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
|--application-definition-kind-filter| 用于筛选应用程序查询操作的
                                          application query operations. - Default - Default value.
                                          Filter that matches input with any
                                          ApplicationDefinitionKind value. The value is 0. - All -
                                          Filter that matches input with any
                                          ApplicationDefinitionKind value. The value is 65535. -
                                          ServiceFabricApplicationDescription - Filter that matches
                                          input with ApplicationDefinitionKind value
                                          ServiceFabricApplicationDescription. The value is 1. -
                                          Compose - Filter that matches input with
                                          ApplicationDefinitionKind value Compose. The value is 2.
                                          Default: 65535.|
| --application-type-name | 用于筛选要查询的应用程序的应用程序类型名称。 此值不应包含应用程序类型版本。| | --continuation-token | 继续标记参数用于获取下一组结果。 如果单个响应无法容纳来自系统的结果，则 API 响应中包括含有非空值的继续标记。 当此值传递到下一个 API 调用时，API 返回下一组结果。 如果没有更多结果，则继续标记不包含值。 不应对此参数的值进行 URL 编码。| | --exclude-application-parameters| 该标志指定应用程序参数是否排除在结果之外。| | --timeout -t | 服务器超时（以秒为单位）。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                      | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h                    | 显示此帮助消息并退出。|
| --output -o                  | 输出格式。  允许的值：json、jsonc、table、tsv。             默认值：json。|
| --query                      | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose                    | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-application-load"></a>sfctl application load
获取 Service Fabric 应用程序的相关加载信息。

        Returns the load information about the application that was created or in the process of
        being created in the Service Fabric cluster and whose name matches the one specified as the
        parameter. The response includes the name, minimum nodes, maximum nodes, the number of nodes
        the app is occupying currently, and application load metric information about the
        application.

### <a name="arguments"></a>参数
|参数|说明|
| --- | --- |
|--application-id [必需]| 应用程序标识。 这通常是不包含
                                 the application without the 'fabric:' URI scheme. Starting from
                                 version 6.0, hierarchical names are delimited with the "~"
                                 character. For example, if the application name is
                                 "fabric://myapp/app1", the application identity would be
                                 "myapp~app1" in 6.0+ and "myapp/app1" in previous versions. |
| --timeout -t | 服务器超时（以秒为单位）。  默认值：60。|

### <a name="global-arguments"></a>全局参数
|参数|说明|
| --- | --- |
|--debug                    | 提高日志记录详细程度，以显示所有调试日志。|
    --help -h                  | 显示此帮助消息并退出。|
    --output -o                | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：
                                 json。|
    --query                    | JMESPath 查询字符串。 有关详细信息和示例，
                                 请参阅 http://jmespath.org/。|
    --verbose                  | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-application-manifest"></a>sfctl application manifest
获取描述应用程序类型的清单。
        
获取描述应用程序类型的清单。 响应包含字符串形式的应用程序清单 XML。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --application-type-name    [必需]| 应用程序类型的名称。|
| --application-type-version [必需]| 应用程序类型的版本。|
| --timeout -t                      | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                           | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h                         | 显示此帮助消息并退出。|
| --output -o                       | 输出格式。  允许的值：json、jsonc、table、tsv。                  默认值：json。|
| --query                           | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose                         | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-application-provision"></a>sfctl application provision
向群集预配或注册 Service Fabric 应用程序类型。
        
向群集预配或注册 Service Fabric 应用程序类型。 必须完成此操作才能实例化任意新应用程序。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --application-type-build-path [必需]| 应用程序包的相对映像存储区路径。|
| --timeout -t                         | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                              | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h                            | 显示此帮助消息并退出。|
| --output -o                          | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query                              | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose                            | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-application-type"></a>sfctl application type

获取 Service Fabric 群集中与指定名称完全匹配的应用程序类型的列表。

返回 Service Fabric 群集中已预配或正在预配的应用程序类型的相关信息。 这些结果为名称与指定为参数的应用程序类型完全匹配，并且符合给定查询参数的应用程序类型。 返回与应用程序类型名称匹配的所有应用程序类型版本，每个版本作为一个应用程序类型返回。 响应包括名称、版本、状态以及有关应用程序类型的其他详细信息。 这是分页查询，如果一页无法容纳所有应用程序类型，则返回一页结果及一个继续标记，该标记可用于获取下一页。 例如，如果有 10 个应用程序类型，但一页仅能容纳前 3 个应用程序类型，或者最大结果数设置为 3，则返回 3 个结果。 要访问其他结果，请使用下一查询中返回的继续标记来检索后续页面。 如果没有后续页面，则返回空继续标记。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --application-type-name [必需]| 应用程序类型的名称。|
| --continuation-token           | 继续标记参数用于获取下一组结果。 如果单个响应无法容纳来自系统的结果，则 API 响应中包括含有非空值的继续标记。 当此值传递到下一个 API 调用时，API 返回下一组结果。 如果没有更多结果，则继续标记不包含值。 不应将此参数的值进行 URL 编码。|
| --exclude-application-parameters  | 该标志指定应用程序参数是否排除在结果之外。|
| --max-results                  | 作为分页查询的一部分返回的最大结果数。 此参数定义返回结果数的上限。 如果根据配置中定义的最大消息大小限制，无法将这些结果容纳到消息中，则返回的结果数可能小于指定的最大结果数。 如果此参数为零或者未指定，则分页查询包含返回消息中最多可容纳的结果数。|
| --timeout -t                   | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                        | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h                      | 显示此帮助消息并退出。|
| --output -o                    | 输出格式。  允许的值：json、jsonc、table、tsv。               默认值：json。|
| --query                        | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose                      | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-application-unprovision"></a>sfctl application unprovision
从群集中删除或注销 Service Fabric 应用程序类型。

从群集中删除或注销 Service Fabric 应用程序类型。 仅当已删除应用程序类型的所有应用程序实例时，才可执行此操作。 注销应用程序类型后，无法为此特定应用程序类型创建新的应用程序实例。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --application-type-name    [必需]| 应用程序类型的名称。|
| --application-type-version [必需]| 应用程序类型版本。|
| --timeout -t                      | 服务器超时，以秒为单位。  默认值：60。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                           | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h                         | 显示此帮助消息并退出。|
| --output -o                       | 输出格式。  允许的值：json、jsonc、table、tsv。                  默认值：json。|
| --query                           | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose                         | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-application-upgrade"></a>sfctl application upgrade
开始升级 Service Fabric 群集中的应用程序。

验证提供的应用程序升级参数，如果参数有效，则开始升级应用程序。 请注意，升级说明将替换现有应用程序说明。 这意味着，如果未指定参数，应用程序的现有参数将替换为空的参数列表。 这会导致应用程序使用应用程序清单中的默认参数值。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --app-id [必需]| 应用程序标识。 这通常是不包含“fabric:”URI 方案的应用程序全名。 从版本 6.0 开始，分层名称以“~”字符隔开。 对于
        example, if the application name is 'fabric://myapp/app1', the application identity would be
        'myapp~app1' in 6.0+ and 'myapp/app1' in previous versions.|
| --app-version [必需]| 目标应用程序版本。| | --parameters [必需]| 升级应用程序时应用的应用程序参数替代的 JSON 编码列表。| | --default-service-health-policy| 默认情况下，用于评估服务类型运行状况的 JSON 编码运行状况策略规范| | --failure-action | 当监视的升级违反监视策略或健康策略时要执行的操作。| | --force-restart | 在升级期间强制重启进程，即使代码版本未更改。| | --health-check-retry-timeout| 如果应用程序或群集不正常，在执行 FailureAction 之前重试运行状况评估的时间长度。 以毫秒为单位。  默认值：PT0H10M0S。| | --health-check-stable-duration | 升级继续到下一升级域之前，应用程序或群集必须保持正常的时间长度。            以毫秒为单位。  默认值：PT0H2M0S。| | --health-check-wait-duration| 应用健康策略之前，完成升级域后等待的时间长度。 以毫秒为单位。            默认值：0。| | --max-unhealthy-apps | 允许的不正常已部署应用程序的最大百分比。 表示为 0 到 100 之间的数字。| | --mode | 用于在滚动升级期间监视运行状况的模式。            默认值：UnmonitoredAuto。| | --replica-set-check-timeout | 出现意外问题时，阻止处理升级域并防止可用性丢失的最大时间长度。 以秒为单位。| | --service-health-policy | 包含每个服务类型名称的服务类型健康策略的 JSON 编码映射。 默认情况下，该映射为空。| | --timeout -t | 服务器超时（以秒为单位）。  默认值：60。| | --upgrade-domain-timeout | 执行 FailureAction 前，每个升级域需等待的时间长度。 以毫秒为单位。  默认值：P10675199DT02H48M05.4775807S。| | --upgrade-timeout | 执行 FailureAction 前，整个升级需等待的时间长度。 以毫秒为单位。  默认值：P10675199DT02H48M05.4775807S。| | --warning-as-error | 将包含相同严重性的运行状况评估警告视为错误。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug                     | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h                   | 显示此帮助消息并退出。|
| --output -o                 | 输出格式。  允许的值：json、jsonc、table、tsv。            默认值：json。|
| --query                     | JMESPath 查询字符串。 有关详细信息和示例，请参阅 http://jmespath.org/。|
| --verbose                   | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="sfctl-application-upload"></a>sfctl application upload
将 Service Fabric 应用程序包复制到映像存储区。

（可选）显示包中每个文件的上传进度。 上传进度发送到 `stderr`。

### <a name="arguments"></a>参数

|参数|说明|
| --- | --- |
| --path [必需]| 本地应用程序包的路径。|
|--imagestore-string| 应用程序包上传到的目标映像存储区。  默认值：
                         fabric:ImageStore。|
| --show-progress  | 显示大型包的文件上传进度。|

### <a name="global-arguments"></a>全局参数

|参数|说明|
| --- | --- |
| --debug       | 提高日志记录详细程度，以显示所有调试日志。|
| --help -h     | 显示此帮助消息并退出。|
| --output -o   | 输出格式。  允许的值：json、jsonc、table、tsv。  默认值：json。|
| --query       | JMESPath 查询字符串。 有关详细信息和示例，
                       请参阅 http://jmespath.org/。|
| --verbose     | 提高日志记录详细程度。 使用 --debug 可获取完整的调试日志。|

## <a name="next-steps"></a>后续步骤
- [安装](service-fabric-cli.md) Service Fabric CLI。
- 了解如何通过[示例脚本](/azure/service-fabric/scripts/sfctl-upgrade-application)使用 Service Fabric CLI。