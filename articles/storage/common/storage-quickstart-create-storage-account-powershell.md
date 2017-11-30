---
title: "Azure 快速入門 - 使用 PowerShell 建立儲存體帳戶 | Microsoft Docs"
description: "快速了解使用 PowerShell 建立新的儲存體帳戶"
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/29/2017
ms.author: tamram
ms.openlocfilehash: b4b917adfb3644cca71b6696df005fbf9e295240
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2017
---
# <a name="create-a-storage-account-using-powershell"></a>使用 PowerShell 建立儲存體帳戶

Azure PowerShell 模組用於從 PowerShell 命令列或在指令碼中建立和管理 Azure 資源。 本指南將詳細說明使用 PowerShell 建立 Azure 儲存體帳戶。 

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

本快速入門需要 Azure PowerShell 模組 3.6 版或更新版本。 執行 `Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。

## <a name="log-in-to-azure"></a>登入 Azure

使用 `Login-AzureRmAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```powershell
Login-AzureRmAccount
```

如果您不知道要使用哪一個位置，您可以列出可用的位置。 當清單顯示之後，尋找您想要使用的位置。 這個範例將使用 **eastus**。 將此位置儲存在變數中並使用該變數，如此您在一處即可進行變更。

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>建立資源群組

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 建立 Azure 資源群組。 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 

```powershell
# put resource group in a variable so you can use the same group name going forward
#   without hardcoding it repeatedly
$resourceGroup = "contoso-storage-accounts"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-general-purpose-standard-storage-account"></a>建立一般用途的標準儲存體帳戶

根據儲存體的用途和適用的服務 (Blob、檔案、表格或佇列)，有數種類型的儲存體帳戶。 下表顯示可能的類型。

|**儲存體帳戶的類型**|**一般用途：標準**|**一般用途：進階**|**Blob 儲存體、經常性存取和非經常性存取層**|
|-----|-----|-----|-----|
|**支援的服務**| Blob、檔案、表格、佇列服務 | Blob 服務 | Blob 服務|
|**支援的 Blob 類型**|區塊 Blob、分頁 Blob、附加 Blob | 分頁 Blob | 區塊 Blob 和附加 Blob|

使用 [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) 建立可用於全部四種服務的一般用途標準儲存體帳戶。 將儲存體帳戶命名為 *contosomvcstandard*，並將它設定為啟用「本地備援儲存體」和 Blob 加密。

```powershell
New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "contosomvcstandard" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob
```

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) 命令來移除資源群組和所有相關資源。 在此情況下，它會移除您建立的儲存體帳戶。

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>後續步驟

在這個快速入門中，您已建立一般用途的標準儲存體帳戶。 若要了解如何使用您的儲存體帳戶上傳或下載 Blob，請繼續進行 Blob 儲存體快速入門。
> [!div class="nextstepaction"]
> [使用 PowerShell 在 Azure Blob 儲存體之間傳送物件](../blobs/storage-quickstart-blobs-powershell.md)