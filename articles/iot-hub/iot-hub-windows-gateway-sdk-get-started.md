---
title: "Azure IoT Edge 入门 (Windows) | Microsoft Docs"
description: "了解如何在 Windows 计算机上构建 Azure IoT Edge 网关，以及 Azure IoT Edge 的重要概念，如模块和 JSON 配置文件。"
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 9aff3724-5e4e-40ec-b95a-d00df4f590c5
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2017
ms.author: andbuc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 2a46a3511d4d286c26aa9ca2e4e619414d82be1d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/16/2017


---
# <a name="explore-azure-iot-edge-architecture-on-windows"></a>在 Windows 上浏览 Azure IoT Edge 体系结构

[!INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>如何生成示例

开始之前，必须[设置开发环境][lnk-setupdevbox]，以便在 Windows 上使用 SDK。

1. 打开“VS 2015 开发人员命令提示”或“VS 2017 开发人员命令提示”命令提示符。
1. 浏览到 **iot-edge** 存储库本地副本中的根文件夹。
1. 运行 **tools\\build.cmd** 脚本。 此脚本创建 Visual Studio 解决方案文件并生成解决方案。 可以在 **iot-edge** 存储库本地副本的 **build** 文件夹中找到 Visual Studio 解决方案。 可为脚本提供其他参数，用于生成和运行单元测试和端到端测试。 这些参数分别是 **--run-unittests** 和 **--run-e2e-tests**。

## <a name="how-to-run-the-sample"></a>如何运行示例

1. **build.cmd** 脚本在本地存储库副本中创建一个名为 **build** 的文件夹。 此文件夹中包含本示例中使用的两个 IoT Edge 模块。

    生成脚本将 **logger.dll** 放在 **build\\modules\\logger\\Debug** 文件夹中，将 **hello\_world.dll** 放在 **build\\modules\\hello_world\\Debug** 文件夹中。 如以下 JSON 设置文件中所示，将这些路径用于 **module path** 值。
1. hello\_world\_sample 过程使用 JSON 配置文件的路径作为命令行参数。 以下示例 JSON 文件在 SDK 存储库的以下路径中提供：**samples\\hello\_world\\src\\hello\_world\_win.json**。 除非你修改了生成脚本，将模块或示例可执行文件放置在非默认位置，否则，此配置文件可按原样工作。

   > [!NOTE]
   > 模块路径相对于 hello\_world\_sample.exe 所在的目录。 示例 JSON 配置文件默认为在当前工作目录中写入“log.txt”。

    ```json
    {
      "modules": [
        {
          "name": "logger",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "..\\..\\..\\modules\\logger\\Debug\\logger.dll"
            }
          },
          "args": { "filename": "log.txt" }
        },
        {
          "name": "hello_world",
          "loader": {
            "name": "native",
            "entrypoint": {
              "module.path": "..\\..\\..\\modules\\hello_world\\Debug\\hello_world.dll"
            }
          },
          "args": null
          }
      ],
      "links": [
        {
          "source": "hello_world",
          "sink": "logger"
        }
      ]
    }
    ```

1. 浏览到 **iot-edge** 存储库本地副本的根文件夹。

1. 运行以下命令：

   `build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json`

[!INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/iot-edge/blob/master/doc/devbox_setup.md

