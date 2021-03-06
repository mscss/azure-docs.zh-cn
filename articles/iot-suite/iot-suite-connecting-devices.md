---
title: "在 Windows 上使用 C 连接设备 | Microsoft Docs"
description: "介绍如何使用在 Windows 上运行的以 C 编写的应用程序将设备连接到 Azure IoT 套件预配置远程监视解决方案。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 34e39a58-2434-482c-b3fa-29438a0c05e8
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 3536777690a9b00ded7c7fdf4d5f39638dad71b0
ms.contentlocale: zh-cn
ms.lasthandoff: 03/10/2017


---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-windows"></a>将设备连接到远程监视预配置解决方案 (Windows)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="create-a-c-sample-solution-on-windows"></a>在 Windows 上创建 C 示例解决方案
以下步骤演示如何创建一个客户端应用程序，用来与远程监视预配置解决方案通信。 此应用程序以 C 编写，在 Windows 上生成和运行。

在 Visual Studio 2015 或 Visual Studio 2017 中创建初学者项目并添加 IoT 中心设备客户端 NuGet 包：

1. 在 Visual Studio 中，使用 Visual C++ **Win32 控制台应用程序**模板创建一个 C 控制台应用程序。 将该项目命名为 **RMDevice**。
2. 在“Win32 应用程序向导”中的“应用程序设置”页上，确保选中“控制台应用程序”，并取消选中“预编译头”和“安全开发生命周期(SDL)检查”。
3. 在“**解决方案资源管理器**”中，删除文件 stdafx.h、targetver.h 和 stdafx.cpp。
4. 在“**解决方案资源管理器**”中，将文件 RMDevice.cpp 重命名为 RMDevice.c。
5. 在“**解决方案资源管理器**”中，右键单击e **RMDevice** 项目，然后单击“**管理 NuGet 包**”。 单击“浏览”，然后搜索并安装以下 NuGet 包：
   
   * Microsoft.Azure.IoTHub.Serializer
   * Microsoft.Azure.IoTHub.IoTHubClient
   * Microsoft.Azure.IoTHub.MqttTransport
6. 在“**解决方案资源管理器**”中，右键单击 **RMDevice** 项目，然后单击“**属性**”打开该项目的“**属性页**”对话框。 有关详细信息，请参阅[设置 Visual C++ 项目属性][lnk-c-project-properties]。 
7. 单击 **Linker** 文件夹，然后单击“**输入**”属性页。
8. 将 **crypt32.lib** 添加到“**其他依赖项**”属性。 单击“**确定**”，然后再次单击“**确定**”以保存项目属性值。

将 Parson JSON 库添加到 **RMDevice** 项目，并添加所需的 `#include` 语句：

1. 在计算机的适当文件夹中，使用以下命令克隆 Parson GitHub 存储库：

    ```
    git clone https://github.com/kgabis/parson.git
    ```

1. 将 parson.h 和 parson.c 文件从 Parson 存储库的本地副本复制到 **RMDevice** 项目文件夹。

1. 在 Visual Studio 中，右键单击 **RMDevice** 项目，单击“添加”，然后单击“现有项”。

1. 在“添加现有项”对话框中，选择 **RMDevice** 项目文件夹中的 parson.h 和 parson.c 文件。 然后，单击“添加”将这两个文件添加到项目。

1. 在 Visual Studio 中，打开 RMDevice.c 文件。 将现有 `#include` 语句替换为以下代码：
   
    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"
    ```

    > [!NOTE]
    > 现在可以通过生成项目来验证该项目是否已设置正确的依赖项。

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>生成并运行示例

添加调用 **remote\_monitoring\_run** 函数的代码，然后生成并运行设备应用程序。

1. 将 **main** 函数替换为以下代码以调用 **remote\_monitoring\_run** 函数：
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. 单击“**生成**”，然后单击“**生成解决方案**”以生成设备应用程序。

1. 在“**解决方案资源管理器**”中，右键单击 **RMDevice** 项目，单击“**调试**”，然后单击“**启动新实例**”以运行示例。 控制台会在应用程序向预配置解决方案发送示例遥测时显示消息，会接收在解决方案仪表板中设置的所需属性值，并且会响应从解决方案仪表板调用的方法。

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx

