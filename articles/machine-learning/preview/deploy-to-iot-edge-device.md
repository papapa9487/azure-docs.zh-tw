---
title: "將 Azure Machine Learning 模型部署到 Azure IoT Edge 裝置 | Microsoft Docs"
description: "本文說明如何將 Azure Machine Learning 模型部署到 Azure IoT Edge 裝置。"
services: machine-learning
author: tedway
ms.author: tedway
manager: mwinkle
ms.reviewer: garyericson, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/10/2017
ms.openlocfilehash: 60db74535edd09841a1b660c2bf832af65fbfcad
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-an-azure-machine-learning-model-to-an-azure-iot-edge-device"></a>將 Azure Machine Learning 模型部署到 Azure IoT Edge 裝置

容器化為 Docker 型 Web 服務的所有 Azure Machine Learning 模型也可以在 Azure IoT Edge 裝置上執行。 其他指令碼與指示位於[適用於 Azure IoT Edge 的 AI 工具組](http://aka.ms/AI-toolkit)。

## <a name="operationalize-the-model"></a>模型運算化
依照[部署 Azure Machine Learning 模型管理 Web 服務](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-service-deploy)中的指示進行，讓您的模型可以運作，以使用該模型建立 Docker 映像。

## <a name="deploy-to-azure-iot-edge"></a>部署至 Azure IoT Edge
Azure IoT Edge 會將雲端分析和自訂商務邏輯移至裝置。 所有 Machine Learning 模型都可以在 IoT Edge 裝置上執行。 設定 IoT Edge 裝置和建立部署的文件位於 [aka.ms/azure-iot-edge-doc](https://aka.ms/azure-iot-edge-doc)。

以下是其他必須注意的事項。

### <a name="add-registry-credentials-to-the-edge-runtime-on-your-edge-device"></a>將登錄認證新增至 Edge 裝置上的 Edge 執行階段
在您執行 IoT Edge 的機器上，新增您的登錄認證，如此執行階段才有提取容器的存取權。

針對 Windows，請執行下列命令：
```cmd/sh
iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```
針對 Linux，請執行下列命令：
```cmd/sh
sudo iotedgectl login --address <docker-registry-address> --username <docker-username> --password <docker-password>
```

### <a name="find-the-machine-learning-container-image-location"></a>找出 Machine Learning 容器映像位置
您需要 Machine Learning 容器映像的位置。 若要找出容器映像位置：

1. 登入 [Azure 入口網站](http://portal.azure.com/)。
2. 在 [Azure Container Registry] 中，選取您想要檢查的登錄。
3. 在登錄刀鋒視窗中，按一下 [儲存機制] 以查看所有儲存機制和其映像的清單。













