---
title: "Azure CosmosDB 資料表 API .NET SDK 和資源 | Microsoft Docs"
description: "全面了解 Azure Cosmos DB 資料表 API，包括發行日期、停用日期，以及每個版本之間的變更。"
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/20/2017
ms.author: mimig
ms.openlocfilehash: 9dc0f5140a538c3a359dd90b74de822dc163fd70
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2017
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB 資料表 .NET API：下載和版本資訊
> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK 下載**|[NuGet](https://aka.ms/acdbtablenuget)|
|**API 文件**|[.NET API 參考文件](https://aka.ms/acdbtableapiref)|
|**快速入門**|[Azure Cosmos DB：使用 .NET 和資料表 API 建置應用程式 (英文)](create-table-dotnet.md)|
|**教學課程**|[Azure Cosmos DB：使用 .NET 搭配資料表 API 進行開發](tutorial-develop-table-dotnet.md)|
|**目前支援的架構**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

> [!IMPORTANT]
> 如果您在預覽期間建立了資料表 API 帳戶，請建立[新資料表 API 帳戶](create-table-dotnet.md#create-a-database-account)來使用正式推出的資料表 API SDK。
>

## <a name="release-notes"></a>版本資訊

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* 公開上市版本

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-preview
* 初始預覽版本

## <a name="release-and-retirement-dates"></a>發行和停用日期
Microsoft 至少會在停用 SDK 的 **12 個月** 之前提供通知，以供順利轉換至較新/支援的版本。

新的功能與最佳化項目只會新增至目前的 SDK，因此建議您一律盡早升級至最新的 SDK 版本。 

服務會拒絕使用已停用 SDK 的任何 Azure Cosmos DB 要求。
<br/>

| 版本 | 發行日期 | 停用日期 |
| --- | --- | --- |
| [1.0.0](#1.0.0) |2017 年 11 月 15 日|--- |
| [0.9.0-preview](#0.1.0-preview) |2017 年 11 月 11 日 |--- |

## <a name="troubleshooting"></a>疑難排解

如果發生錯誤 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

(在嘗試使用 Microsoft.Azure.CosmosDB.Table NuGet 套件時)，有兩個選項可以修正這個問題：

* 使用套件管理器主控台安裝 Microsoft.Azure.CosmosDB.Table 套件和其相依項目。 若要這樣做，請在方案的套件管理器主控台中輸入下列項目。 
    ```
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```
    
* 使用慣用的 Nuget 套件管理工具，先安裝 Microsoft.Azure.Storage.Common Nuget 套件，再安裝 Microsoft.Azure.CosmosDB.Table。

## <a name="faq"></a>常見問題集

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另請參閱
若要深入了解 Azure Cosmos DB 資料表 API，請參閱 [Azure Cosmos DB 資料表 API 的簡介](table-introduction.md)。 
