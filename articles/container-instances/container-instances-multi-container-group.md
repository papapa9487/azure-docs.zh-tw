---
title: "Azure 容器執行個體 - 多容器群組 | Azure Docs"
description: "Azure 容器執行個體 - 多容器群組"
services: container-instances
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 94744e8138d40dd777c0c004472804e3af6c0b1e
ms.contentlocale: zh-tw
ms.lasthandoff: 07/28/2017

---

# <a name="deploy-a-container-group"></a>部署容器群組

Azure 容器執行個體支援使用*容器群組*將多個容器部署到單一主機。 在建置應用程式 Sidecar 以便記錄、監視或進行服務需要第二個附加程序的任何其他設定時，這非常有用。 

本文件會逐步解說如何使用 Azure Resource Manager 範本來執行簡單的多容器 Sidecar 設定。

## <a name="configure-the-template"></a>設定範本

建立名為 `azuredeploy.json` 的檔案，並將下列 json 複製到該檔案中。 

此範例會定義含有兩個容器和一個公用 IP 位址的容器群組。 群組的第一個容器會執行網際網路對向應用程式。 第二個容器 (也就是 Sidecar) 會透過群組的區域網路向主要 Web 應用程式提出 HTTP 要求。 

您可以擴充此 Sidecar 範例，以在其收到 200 OK 以外的 HTTP 回應碼時觸發警示。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "microsoft/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",    
    "container2image": "microsoft/aci-tutorial-sidecar"
  },
    "resources": [
      {
        "name": "myContainerGroup",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2017-08-01-preview",
        "location": "[resourceGroup().location]",
        "properties": {
          "containers": [
            {
              "name": "[variables('container1name')]",
              "properties": {
                "image": "[variables('container1image')]",
                "resources": {
                  "requests": {
                    "cpu": 1,
                    "memoryInGb": 1.5
                    }
                },
                "ports": [
                  {
                    "port": 80
                  }
                ]
              }
            },
            {
              "name": "[variables('container2name')]",
              "properties": {
                "image": "[variables('container2image')]",
                "resources": {
                  "requests": {
                    "cpu": 1,
                    "memoryInGb": 1.5
                    }
                }
              }
            }
          ],
          "osType": "Linux",
          "ipAddress": {
            "type": "Public",
            "ports": [
              {
                "protocol": "tcp",
                "port": "80"
              }
            ]
          }
        }
      }
    ],
    "outputs": {
      "containerIPv4Address": {
        "type": "string",
        "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', 'myContainerGroup')).ipAddress.ip]"
      }
    }
  }
```

若要使用私用容器映像登錄，請使用下列格式將物件新增至 json 文件。

```json
"imageRegistryCredentials": [
    {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
    }
]
```

## <a name="deploy-the-template"></a>部署範本

使用 [az group create](/cli/azure/group#create) 命令來建立資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

使用 [az group deployment create](/cli/azure/group/deployment#create) 命令部署範本。

```azurecli-interactive
az group deployment create --name myContainerGroup --resource-group myResourceGroup --template-file azuredeploy.json
```

在幾秒內，您就會從 Azure 收到首次的回應。 

## <a name="view-deployment-state"></a>檢視部署狀態

若要檢視部署的狀態，請使用 `az container show` 命令。 這會傳回可供用來存取應用程式的已佈建公用 IP 位址。

```azurecli-interactive
az container show --name myContainerGroup --resource-group myResourceGroup -o table
```

輸出：

```azurecli
Name              ResourceGroup    ProvisioningState    Image                                                             IP:ports           CPU/Memory    OsType    Location
----------------  ---------------  -------------------  ----------------------------------------------------------------  -----------------  ------------  --------  ----------
myContainerGroup  myResourceGrou2  Succeeded            microsoft/aci-tutorial-sidecar,microsoft/aci-tutorial-app:v1      40.118.253.154:80  1.0 core/1.5 gb   Linux     westus
```

## <a name="view-logs"></a>檢視記錄檔   

使用 `az container logs` 命令檢視容器的記錄輸出。 `--container-name` 引數會指定要從中提取記錄的容器。 此範例所指定的會是第一個容器。 

```azurecli-interactive
az container logs --name myContainerGroup --container-name aci-tutorial-app --resource-group myResourceGroup
```

輸出：

```bash
istening on port 80
::1 - - [27/Jul/2017:17:35:29 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:32 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:35 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:38 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

若要查看 Sidecar 容器的記錄，請執行相同命令來指定第二個容器名稱。

```azurecli-interactive
az container logs --name myContainerGroup --container-name aci-tutorial-sidecar --resource-group myResourceGroup
```

輸出：

```bash
Every 3.0s: curl -I http://localhost                                                                                                                       Mon Jul 17 11:27:36 2017

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
Accept-Ranges: bytes
Content-Length: 1663
Content-Type: text/html; charset=utf-8
Last-Modified: Sun, 16 Jul 2017 02:08:22 GMT
Date: Mon, 17 Jul 2017 18:27:36 GMT
```

如您所見，Sidecar 會透過群組的區域網路定期地向主要 Web 應用程式提出 HTTP 要求，以確保它正在執行。

## <a name="next-steps"></a>後續步驟

本文件說明了要部署多容器 Azure 容器執行個體所需的步驟。 如需端對端的 Azure 容器執行個體體驗，請參閱「Azure 容器執行個體」教學課程。

> [!div class="nextstepaction"]
> [Azure 容器執行個體教學課程]: ./container-instances-tutorial-prepare-app.md

