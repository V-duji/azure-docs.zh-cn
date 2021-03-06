---
title: "使用动态遥测 | Microsoft Docs"
description: "遵循本教程来了解如何配合使用 Azure IoT 套件动态遥测和远程监视预配置解决方案。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: dobett
ms.openlocfilehash: 0114f27f9b8ae76e1170d04ddf66e2c4bf20686a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>配合使用动态遥测和远程监视预配置解决方案

动态遥测可让你将发送到远程监视预配置解决方案的任何遥测数据可视化。 部署了预配置解决方案的模拟设备会发送温度和湿度遥测，可在仪表板上直观显示这些数据。 如果自定义现有的模拟设备、创建新的模拟设备或者将物理设备连接到预配置解决方案，则可以发送其他遥测值，例如外部温度、RPM 或风速。 然后，可以在仪表板上直观显示这些附加的遥测数据。

本教程使用一个简单的 Node.js 模拟设备，可以轻松对它进行修改，以体验动态遥测。

若要完成本教程，需要：

* 一个有效的 Azure 订阅。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用][lnk_free_trial]。
* [Node.js][lnk-node] 版本 0.12.x 或更高版本。

可以在任何操作系统上完成本教程，例如 Windows 或 Linux，只要能够在其中安装 Node.js 即可。

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

[!INCLUDE [iot-suite-send-external-temperature](../../includes/iot-suite-send-external-temperature.md)]

## <a name="add-a-telemetry-type"></a>添加遥测类型

下一步是将 Node.js 模拟设备生成的遥测数据替换为一组新值：

1. 在命令提示符或 shell 中键入 **Ctrl+C** 以停止 Node.js 模拟设备。
2. 在 remote_monitoring.js 文件中，可以查看现有温度、湿度和外部温度遥测的基本数据值。 如下添加 **rpm** 的基本数据值：

    ```nodejs
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. Node.js 模拟设备使用 remote_monitoring.js 文件中的 **generateRandomIncrement** 函数，向基本数据值添加随机增量。 在现有随机化后面添加一行代码，以将 **rpm** 的值随机化，如下所示：

    ```nodejs
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. 将新的 rpm 值添加到设备发送给 IoT 中心的 JSON 有效负载：

    ```nodejs
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. 使用以下命令运行 Node.js 模拟设备：

    `node remote_monitoring.js`

6. 观察仪表板中图表上显示的新 RPM 遥测类型：

![将 RPM 添加到仪表板][image3]

> [!NOTE]
> 可能需要在仪表板中的“**设备**”页上禁用 Node.js 设备然后重新将它启用，才能立即查看更改。

## <a name="customize-the-dashboard-display"></a>自定义仪表板显示内容

**Device-Info** 消息可以包含设备可发送给 IoT 中心的遥测数据的相关元数据。 此元数据可指定设备发送的遥测类型。 修改 remote_monitoring.js 文件中的 **deviceMetaData** 值，在 **Commands** 定义后附加 **Telemetry** 定义。 以下代码片段显示 **Commands** 定义（务必在 **Commands** 定义后添加 `,`）：

```nodejs
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [!NOTE]
> 远程监视解决方案会比较元数据定义和遥测流中的数据并区分大小写。


按以上代码片段中所述添加 **Telemetry** 定义不会影响仪表板的行为。 但是，元数据也可以包含 **DisplayName** 属性来自定义仪表板中的显示内容。 如以下片段中所述更新 **Telemetry** 元数据定义：

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

以下屏幕截图演示了此项更改如何修改仪表板上的图表图例：

![自定义图表图例][image4]

> [!NOTE]
> 可能需要在仪表板中的“**设备**”页上禁用 Node.js 设备然后重新将它启用，才能立即查看更改。

## <a name="filter-the-telemetry-types"></a>筛选遥测类型

默认情况下，仪表板上的图表显示遥测流中的每个数据系列。 可以使用 **Device-Info** 元数据来隐藏图表中的特定遥测类型。 

若要使图表只显示温度和湿度遥测数据，请省略 **Device-Info** **Telemetry** 元数据中的 **ExternalTemperature**，如下所示：

```nodejs
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

**室外温度**不再显示在图表上：

![筛选仪表板上的遥测数据][image5]

此更改仅影响图表的显示效果。 仍会存储 **ExternalTemperature** 数据值，并可用于任何后端处理。

> [!NOTE]
> 可能需要在仪表板中的“**设备**”页上禁用 Node.js 设备然后重新将它启用，才能立即查看更改。

## <a name="handle-errors"></a>处理错误

要使某个数据流显示在图表上，其在 **Device-Info** 元数据中的 **Type** 必须与遥测值的数据类型匹配。 例如，如果元数据指定湿度数据的 **Type** 必须为 **int**，而在遥测流中找到 **double**，则湿度遥测将不会显示在图表上。 但是，**湿度**值仍会存储，并可供任何后端处理使用。

## <a name="next-steps"></a>后续步骤

熟悉了动态遥测的使用方式，现在可深入了解预配置的解决方案如何使用设备信息：[远程监视预配置解决方案中的设备信息元数据][lnk-devinfo]。

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[image3]: media/iot-suite-dynamic-telemetry/image3.png
[image4]: media/iot-suite-dynamic-telemetry/image4.png
[image5]: media/iot-suite-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
