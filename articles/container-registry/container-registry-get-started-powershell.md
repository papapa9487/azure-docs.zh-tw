---
title: "快速入門 - 使用 PowerShell 在 Azure 中建立私用的 Docker 登錄"
description: "快速了解如何使用 PowerShell 建立私用的 Docker 容器登錄。"
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quicksart
ms.date: 10/08/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: fbf643ad342d712452d39c71b8706b6213198512
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2017
---
# <a name="create-an-azure-container-registry-using-powershell"></a>使用 PowerShell 建立 Azure Container Registry

Azure Container Registry 是用於儲存私用 Docker 容器映像的受管理 Docker 容器登錄服務。 本指南詳述如何使用 PowerShell 建立 Azure Container Registry 執行個體。

本快速入門需要 Azure PowerShell 模組 3.6 版或更新版本。 執行 `Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。

您也必須在本機上安裝 Docker。 Docker 提供可輕鬆在 [Mac](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 或 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 系統上設定 Docker 的套件。

## <a name="log-in-to-azure"></a>登入 Azure

使用 `Login-AzureRmAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>建立資源群組

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container-registry"></a>建立容器登錄庫

使用 [New-AzureRMContainerRegistry](/powershell/module/containerregistry/New-AzureRMContainerRegistry) 命令建立 ACR 執行個體。

登錄的名稱**必須是唯一的**。 下列範例中使用 *myContainerRegistry007*。 請將此更新為唯一的值。

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>登入 ACR

發送和提取容器映像之前，您必須登入 ACR 執行個體。 首先，使用 [Get AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential) 命令，取得 ACR 執行個體的管理員認證。

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

接著，使用 [docker login](https://docs.docker.com/engine/reference/commandline/login/) 命令登入 ACR 執行個體。

```bash
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

此命令在完成之後會傳回「登入成功」訊息。

## <a name="push-image-to-acr"></a>推送映像到 ACR

若要推送映像到 Azure Container Registry，您必須先有映像。 如有需要，請執行下列命令，以從 Docker Hub 提取預先建立的映像。

```bash
docker pull microsoft/aci-helloworld
```

映像必須加上 ACR 登入伺服器名稱。 執行 [Get AzureRmContainerRegistry](/powershell/module/containerregistry/Get-AzureRmContainerRegistry) 命令，以傳回 ACR 執行個體的登入伺服器名稱。

```powershell
Get-AzureRmContainerRegistry | Select Loginserver
```

使用 [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) 命令來標記映像。 將 *acrLoginServer* 取代為 ACR 執行個體的登入伺服器名稱。

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

最後，使用 [docker push](https://docs.docker.com/engine/reference/commandline/push/) 推送映像到 ACR 執行個體。 將 *acrLoginServer* 取代為 ACR 執行個體的登入伺服器名稱。

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令來移除資源群組、ACR 執行個體和所有容器映像。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您使用 Azure CLI 建立了 Azure Container Registry。 如果您想要使用 Azure Container Registry 搭配 Azure 容器執行特體，請繼續進行 Azure 容器執行個體教學課程。

> [!div class="nextstepaction"]
> [Azure 容器執行個體教學課程](../container-instances/container-instances-tutorial-prepare-app.md)