---
title: "在 Azure 中選取 Linux VM 映像 | Microsoft Docs"
description: "了解如何使用 Azure PowerSHell 來判斷發行者、優惠、SKU 和 Marketplace VM 映像的版本。"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/12/2017
ms.author: danlep
ms.openlocfilehash: c9b35ff5f3fbd33639805b5a4f105df32562a691
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>如何使用 Azure PowerShell 在 Azure Marketplace 中尋找 Windows VM 映像

本主題描述如何在 Azure Marketplace 中使用 Azure PowerShell 尋找 Windows VM 映像。 您可以使用此資訊，在建立 Windows VM 時指定 Marketplace 映像。

確定您已安裝並設定最新的 [Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。



## <a name="table-of-commonly-used-windows-images"></a>常用 Windows 映像表
| PublisherName | 提供項目 | SKU |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-with-Containers |
| MicrosoftWindowsServer |WindowsServer |2016-Nano-Server |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2016-WS2016 |Enterprise |
| MicrosoftSQLServer |SQL2014SP2-WS2012R2 |Enterprise |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |

## <a name="find-specific-images"></a>尋找特定映像


使用 Azure 資源管理員建立新的虛擬機器時，在某些情況下，您需要使用下列映像屬性組合來指定映像：

* 發佈者
* 提供項目
* SKU

例如，使用這些值搭配 [Set-AzureRMVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) PowerShell Cmdlet，或搭配資源群組範本，而在此範本中，您必須指定要建立的 VM 類型。

如果您需要判斷這些值，可以執行 [Get AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher)、[Get AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer)和 [Get AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) Cmdlet 以導覽映像。 您要判斷這些值：

1. 列出映像發行者。
2. 針對指定的發行者，列出其提供項目。
3. 針對指定的提供項目，列出其 SKU。

首先，使用以下命令列出發行者：

```azurepowershell-interactive
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

填入您選擇的發行者名稱，然後執行以下命令：

```azurepowershell-interactive
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

填入您選擇的提供項目名稱，然後執行以下命令：

```azurepowershell-interactive
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

從 `Get-AzureRMVMImageSku` 命令的輸出中，您擁有指定新虛擬機器映像時所需的所有資訊。

下圖顯示完整範例：

```azurepowershell-interactive
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

```

輸出：

```
PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

針對 "MicrosoftWindowsServer" 發佈者：

```azurepowershell-interactive
$pubName="MicrosoftWindowsServer"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

輸出：

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServer-HUB
```

針對 "WindowsServer" 提供項目：

```azurepowershell-interactive
$offerName="WindowsServer"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

輸出：

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Nano-Server
```

從這個清單中，複製選擇的 SKU 名稱，您就可以取得 `Set-AzureRMVMSourceImage` PowerShell Cmdlet 或資源群組範本的所有資訊。

## <a name="next-steps"></a>後續步驟
現在，您可以精確地選擇想要使用的映像。 若要使用映像資訊快速建立虛擬機器，請參閱[使用 PowerShell 建立 Windows 虛擬機器](quick-create-powershell.md)。
