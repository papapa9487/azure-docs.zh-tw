---
title: "使用 PowerShell 執行 Azure Cosmos DB 資料表 API 作業 | Microsoft Docs"
description: "如何使用 PowerShell 執行 Azure Cosmos DB 資料表 API 作業"
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
ms.date: 11/15/2017
ms.author: robinsh
ms.openlocfilehash: d0f835db8a9fbe3833a9c7931ad1d8b4a778f016
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="perform-azure-cosmos-db-table-api-operations-with-azure-powershell"></a>使用 Azure PowerShell 執行 Azure Cosmos DB 資料表 API 作業 

>[!NOTE]
>Azure Cosmos DB 資料表 API 提供資料表儲存體的高階功能，例如周全的全域發佈、低延遲讀取和寫入、自動次要索引，以及專用的輸送量。 在大部分情況下，本文中的 PowerShell 命令同時適用於 Azure Cosmos DB 資料表 API 和 Azure 資料表儲存體，但本文旨在說明 Azure Cosmos DB 資料表 API。 如果您使用的是 Azure 資料表儲存體，請參閱[使用 Azure PowerShell 執行 Azure 資料表儲存體作業](table-storage-how-to-use-powershell.md)。
>

Azure Cosmos DB 資料表 API 可讓您儲存和查詢龐大的結構化、非關聯式資料集。 服務的主要元件是資料表、實體和屬性。 資料表是一組實體。 實體是一組屬性。 每個實體最多可有 252 個屬性，也就是所有的名稱/值組。 本文假設您已熟悉 Azure Cosmos DB 資料表 API 的概念。 如需詳細資訊，請參閱 [Azure Cosmos DB 資料表 API 的簡介](table-introduction.md)和[使用資料表 API 來建置 .NET 應用程式](create-table-dotnet.md)。

此做法文章涵蓋一般的資料表 API 作業。 您會了解如何： 

> [!div class="checklist"]
> * 建立資料表
> * 擷取資料表
> * 新增資料表實體
> * 查詢資料表
> * 刪除資料表實體

## <a name="prerequisites"></a>必要條件

這些範例需要 Azure PowerShell 模組 4.4.0 版或更新版本。 在 PowerShell 視窗中，執行 `Get-Module -ListAvailable AzureRM` 以尋找版本。 如果未顯示任何項目，或者您必須升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。 

安裝或更新 Azure PowerShell 之後，您必須安裝 **AzureRmStorageTable** 模組，該模組包含可管理實體的命令。 若要安裝此模組，請以系統管理員身分執行 PowerShell，並使用 **Install-Module** 命令。

```powershell
Install-Module AzureRmStorageTable
```

然後須在本機安裝 Azure Cosmos DB 組件，才能使用這些 PowerShell。 如需如何執行該動作的指示，請參閱[適用於 Cosmos DB 資料表的 Azure RM 儲存體資料表 PowerShell 模組](https://blogs.technet.microsoft.com/paulomarques/2017/05/23/azure-rm-storage-tables-powershell-module-now-includes-support-for-cosmos-db-tables/) \(英文\)。

若要嘗試下列練習，您需要 Azure Cosmos DB 資料庫帳戶。 如果您還沒有帳戶，請使用 [Azure 入口網站](https://portal.azure.com)建立一個新的 Azure Cosmos DB 帳戶。 如需建立新資料庫帳戶的說明，請參閱 [Azure Cosmos DB：建立資料庫帳戶](create-table-dotnet.md#create-a-database-account)。

從入口網站取得資料庫帳戶名稱和資源群組，您必須在指令碼中放入這些值以便存取資料表。 

## <a name="sign-in-to-azure"></a>登入 Azure

使用 `Login-AzureRmAccount` 命令登入 Azure 訂用帳戶並遵循畫面上的指示。

```powershell
Login-AzureRmAccount
```

## <a name="create-a-table-or-reference-a-table"></a>建立資料表或參考資料表

若要建立資料表，或是取得資料表的參考，請使用 **Get-AzureStorageTableTable**。 如果您使用不存在的資料表名稱呼叫此 Cmdlet，它會使用該名稱建立新的資料表，並傳回新資料表的參考。 如果該資料表存在，則會傳回現有資料表的參考。

```powershell
# set the name of the resource group in which your Cosmos DB Account resides.
$resourceGroup = "contosocosmosrg"
# if you want to make sure the resource group is valid, try this command
Get-AzureRmResourceGroup -Name $resourceGroup

# set the Cosmos DB account name 
$cosmosDBAccountName = "contosocosmostbl" 

# set the table name 
$tableName = "contosotable1"

# Get a reference to a table. This creates the table if it doesn't exist.
$storageTable = Get-AzureStorageTableTable `
  -resourceGroup $resourceGroup `
  -tableName $tableName `
  -cosmosDbAccount $cosmosDBAccountName 
```

您不能使用 PowerShell 列出 Azure Cosmos DB 帳戶中的資料表，但可以登入入口網站並查看資料表。 現在，讓我們來看看如何管理資料表中的實體。

[!INCLUDE [storage-table-entities-powershell-include](../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>刪除資料表 

PowerShell 不支援從 Azure Cosmos DB 刪除資料表。 若要刪除資料表，請移至 [Azure 入口網站](https://azure.portal.com)，找到您正在使用的 Azure Cosmos DB 帳戶，然後尋找並刪除資料表。 

## <a name="clean-up-resources"></a>清除資源

如果您建立了新的資源群組，並在該群組中建立了新的 Azure Cosmos DB 帳戶，便可以藉由移除資源群組來移除您在此練習中建立的所有資產。 此命令會刪除群組內含的所有資源，以及資源群組本身。

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>後續步驟

在本做法文章中，您學會使用 PowerShell 進行一般的資料表 API 作業，包括如何： 

> [!div class="checklist"]
> * 建立資料表
> * 擷取資料表
> * 新增資料表實體
> * 查詢資料表
> * 刪除資料表實體

如需詳細資訊，請參閱下列文章。

* [從 PowerShell 使用 Azure 儲存體資料表](https://blogs.technet.microsoft.com/paulomarques/2017/01/17/working-with-azure-storage-tables-from-powershell/) \(英文\)

* [Microsoft Azure 儲存體總管](../vs-azure-tools-storage-manage-with-storage-explorer.md) 是一個免費的獨立應用程式，可讓您在 Windows、MacOS 和 Linux 上以視覺化方式處理 Azure 儲存體資料。