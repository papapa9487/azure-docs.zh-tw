---
title: "快速入門 - 使用 PowerShell 建立您的第一個 Azure Container Instances 容器"
description: "透過使用 PowerShell 建立 Windows 容器執行個體來開始使用 Azure Container Instances。"
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: fbd1bee04c5180beda23c04607b313eec9edcab4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-container-in-azure-container-instances"></a>在 Azure Container Instances 中建立您的第一個容器

Azure Container Instances 能讓您在 Azure 中輕鬆建立及管理 Docker 容器，不需要佈建虛擬機器或採用高階服務即可完成。

在本快速入門中，您會在 Azure 中建立 Windows 容器，並透過公用 IP 位址向網際網路公開此容器。 只需要一個命令就能完成這項作業。 只要一些時間，您就能在瀏覽器中看到下列結果：

![在瀏覽器中檢視使用 Azure Container Instances 所部署的應用程式][qs-powershell-01]

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

本快速入門需要 Azure PowerShell 模組 4.4 版或更新版本。 執行 `Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。

## <a name="log-in-to-azure"></a>登入 Azure

使用 `Login-AzureRmAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>建立資源群組

使用 [New-AzureRmResourceGroup][New-AzureRmResourceGroup] 建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>建立容器

您可以對 [New-AzureRmContainerGroup][New-AzureRmContainerGroup] Cmdlet 提供名稱、Docker 映像和 Azure 資源群組來建立容器。 您可以選擇使用公用 IP 位址向網際網路公開容器。 在此案例中，我們將使用執行 Internet Information Services (IIS) 的 Windows Nano 伺服器容器。

```powershell
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -IpAddressType Public
```

在幾秒內，您就會取得要求的回應。 一開始，容器會處於 [正在建立] 狀態，但它應該會在一兩分鐘內啟動。 您可以使用 [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] Cmdlet 來檢查狀態：

```powershell
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

容器的佈建狀態和 IP 位址會顯示在 Cmdlet 的輸出中：

```
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/12345678-1234-1234-1234-12345678abcd/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            :
IpAddress                : 40.71.248.73
Ports                    : {80}
OsType                   : Windows
Volumes                  :
```

一旦容器的 **ProvisioningState** 轉換為 `Succeeded` 時，您就可以在瀏覽器中使用提供的 IP 位址來連線至容器。

![在瀏覽器中檢視使用 Azure Container Instances 所部署的 IIS][qs-powershell-01]

## <a name="delete-the-container"></a>刪除容器

當容器使用完畢後，您可以使用 [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup] Cmdlet 來移除容器：

```powershell
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已在 Azure Container Instances 中啟動預先建立的 Windows 容器。 如果您想要嘗試自行建置容器，並使用 Azure Container Registry 將它部署至 Azure Container Instances，請繼續進行 Azure Container Instances 教學課程。

> [!div class="nextstepaction"]
> [Azure Container Instances 教學課程](./container-instances-tutorial-prepare-app.md)

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png