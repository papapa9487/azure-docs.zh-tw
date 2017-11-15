---
title: "使用 PowerShell 執行 Azure 資料表儲存體作業 | Microsoft Docs"
description: "使用 PowerShell 執行 Azure 資料表儲存體作業"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/02/2017
ms.author: robinsh
ms.openlocfilehash: 0174b6fe02008a1c22a165b077c694af7e8618ab
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2017
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>使用 Azure PowerShell 執行 Azure 資料表儲存體作業 

>[!NOTE]
>Azure Cosmos DB 資料表 API 提供資料表儲存體的高階功能，例如周全的全域發佈、低延遲讀取和寫入、自動次要索引，以及專用的輸送量。 在大部分情況下，本文中的 PowerShell 命令同時適用於 Azure Cosmos DB 資料表 API 和 Azure 資料表儲存體，但本文旨在說明 Azure 資料表儲存體。 如果您使用的是 Azure Cosmos DB 資料表 API，請參閱[使用 Azure PowerShell 執行 Azure Cosmos DB 資料表 API 作業](table-powershell.md)。
>

Azure 資料表儲存體是 NoSQL 資料存放區，您可以用來儲存和查詢龐大的結構化、非關聯式資料集。 服務的主要元件是資料表、實體和屬性。 資料表是一組實體。 實體是一組屬性。 每個實體最多可有 252 個屬性，也就是所有的名稱/值組。 本文假設您已熟悉 Azure 資料表儲存體服務概念。 如需詳細資訊，請參閱[了解表格服務資料模型](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model)和[以 .NET 開始使用 Azure 資料表儲存體](table-storage-how-to-use-dotnet.md)。

本做法文章涵蓋一般的 Azure 資料表儲存體作業。 您會了解如何： 

> [!div class="checklist"]
> * 建立資料表
> * 擷取資料表
> * 新增資料表實體
> * 查詢資料表
> * 刪除資料表實體
> * 刪除資料表

本做法文章示範如何在新的資源群組中建立新的儲存體帳戶，以便在完成時輕鬆地將它移除。 如果您想要使用現有的儲存體帳戶，則可以改為那麼做。

這些範例需要 Azure PowerShell 模組 4.4.0 版或更新版本。 在 PowerShell 視窗中，執行 `Get-Module -ListAvailable AzureRM` 以尋找版本。 如果未顯示任何項目，或者您必須升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。 

安裝或更新 Azure PowerShell 之後，您必須安裝 **AzureRmStorageTable** 模組，該模組包含可管理實體的命令。 若要安裝此模組，請以系統管理員身分執行 PowerShell，並使用 **Install-Module** 命令。

```powershell
Install-Module AzureRmStorageTable
```

## <a name="sign-in-to-azure"></a>登入 Azure

使用 `Login-AzureRmAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```powershell
Login-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>擷取位置清單

如果您不知道要使用哪一個位置，您可以列出可用的位置。 當清單顯示之後，尋找您想要使用的位置。 這些範例會使用 **eastus**。 將這個值儲存於變數 **location** 中供未來使用。

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>建立資源群組

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup) 命令建立資源群組。 

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 在變數中儲存資源群組名稱供日後使用。 在此範例中，會在 *eastus* 區域中建立名為 *pshtablesrg* 的資源群組。

```powershell
$resourceGroup = "pshtablesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>建立儲存體帳戶

搭配使用本地備援儲存體 (LRS) 與 [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount) 來建立標準的一般用途儲存體帳戶。 取得儲存體帳戶內容，定義要使用的儲存體帳戶。 使用儲存體帳戶時，會參考內容而非重複提供認證。

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>建立新的資料表

若要建立資料表，請使用 [New-AzureStorageTable](/powershell/module/azure.storage/New-AzureStorageTable) Cmdlet。 在此範例中，資料表名為 `pshtesttable`。

```powershell
$tableName = "pshtesttable"
New-AzureStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>擷取儲存體帳戶中的資料表清單

使用 [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable) 來擷取儲存體帳戶中的資料表清單。

```powershell
$storageTable = Get-AzureStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>擷取特定資料表的參考

若要在資料表上執行作業，您需要針對那個特定資料表的參考。 使用 [Get-AzureStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable) 來取得參考。 

```powershell
$storageTable = Get-AzureStorageTable –Name $tableName –Context $ctx
```

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>刪除資料表

若要刪除資料表，請使用 [Remove-AzureStorageTable](/powershell/module/azure.storage/Remove-AzureStorageTable)。 此 Cmdlet 可移除資料表，包括其中的所有資料。

```powershell
Remove-AzureStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzureStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>清除資源

如果您依照此做法文章開頭的步驟，建立了新的資源群組和儲存體帳戶，就可以藉由移除資源群組來移除您在此練習中建立的所有資產。 此命令會刪除群組內含的所有資源，以及資源群組本身。

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>後續步驟

在本做法文章中，您學會使用 PowerShell 進行一般的 Azure 資料表儲存體作業，包括如何： 

> [!div class="checklist"]
> * 建立資料表
> * 擷取資料表
> * 新增資料表實體
> * 查詢資料表
> * 刪除資料表實體
> * 刪除資料表

如需詳細資訊，請參閱下列文章

* [儲存體 PowerShell Cmdlet](/powershell/module/azurerm.storage#storage)

* [從 PowerShell 使用 Azure 儲存體資料表](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/) \(英文\)

* [Microsoft Azure 儲存體總管](../vs-azure-tools-storage-manage-with-storage-explorer.md) 是一個免費的獨立應用程式，可讓您在 Windows、MacOS 和 Linux 上以視覺化方式處理 Azure 儲存體資料。