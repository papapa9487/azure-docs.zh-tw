---
title: "建立索引 (入口網站 - Azure 搜尋服務) | Microsoft Docs"
description: "使用 Azure 入口網站建立索引。"
services: search
manager: jhubbard
author: heidisteen
documentationcenter: 
ms.assetid: 
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 06/20/2017
ms.author: heidist
ms.openlocfilehash: a7d98ab0937a7d3f932d5df34c19ae091129804e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="create-an-azure-search-index-using-the-azure-portal"></a>使用 Azure 入口網站建立 Azure 搜尋服務索引
> [!div class="op_single_selector"]
> * [概觀](search-what-is-an-index.md)
> * [入口網站](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

使用 Azure 入口網站中內建的索引設計工具來建立原型，或建立[搜尋索引](search-what-is-an-index.md)以在您的 Azure 搜尋服務上執行。 

## <a name="prerequisites"></a>必要條件

本文採用 [Azure 訂用帳戶](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)和 [Azure 搜尋服務](search-create-service-portal.md)。  

## <a name="find-your-search-service"></a>尋找您的搜尋服務
1. 登入 Azure 入口網站頁面，並檢閱[訂用帳戶的搜尋服務](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. 選取您的 Azure 搜尋服務。

## <a name="name-the-index"></a>為索引命名

1. 在頁面頂端的命令列中，按一下 [新增索引] 按鈕。
2. 為 Azure 搜尋服務索引命名。 
   * 以字母開頭。
   * 僅使用小寫字母、數字或連字號 ("-")。
   * 將名稱長度限制為 60 個字元。

  索引名稱會成為連至索引之連線上所使用端點 URL 的一部分，並用來在 Azure 搜尋服務 REST API 中傳送 HTTP 要求。

## <a name="define-the-fields-of-your-index"></a>定義索引的欄位

索引組合包含「欄位集合」，用以定義索引中可搜尋的資料。 更具體來說，它會指定您分別上傳的文件結構。 欄位集合包含必要與選用欄位、具名與具類型，以及索引屬性，用以判斷如何使用欄位。

1. 在 [新增索引] 刀鋒視窗中，按一下 [欄位 >]，以滑動開啟欄位定義刀鋒視窗。 

2. 接受所產生之類型為 Edm.String 的「索引鍵」 欄位。 根據預設，已將該欄位命名為 id，但您可以將它重新命名，只要字串符合[命名規則](https://docs.microsoft.com/rest/api/searchservice/Naming-rules)即可。 對於每個 Azure 搜尋服務索引而言，索引鍵欄位是必要的且必須為字串。

3. 新增欄位，以完整指定您要上傳的文件。 如果文件包含 id、hotel name、address、city 及 region，請針對索引中的每一個建立對應的欄位。 請檢閱[下一節中的設計指導方針](#design)設定屬性的說明。

4. (選擇性) 新增任何要在篩選運算式內部使用的欄位。 您可以設定欄位上的屬性，以從搜尋作業中排除欄位。

5. 完成後，按一下 [確定]，以儲存並建立索引。

## <a name="tips-for-adding-fields"></a>新增欄位的秘訣

在入口網站中建立索引，需要密集使用鍵盤。 請遵循此工作流程以將步驟數降至最低：

1. 首先，輸入名稱並設定資料類型來建置欄位清單。

2. 接下來，使用每個屬性上方的核取方塊來大量啟用所有欄位的設定，然後選擇性地清除幾個不需用到之欄位的方塊。 例如，字串欄位通常是可搜尋的。 因此，您可以按一下 [可擷取] 和 [可搜尋]，同時在搜尋結果中傳回欄位的值，以及允許在欄位上進行全文檢索搜尋。 

<a name="design"></a>
## <a name="design-guidance-for-setting-attributes"></a>設定屬性的設計指導方針

雖然您可以隨時新增欄位，但現有的欄位定義會在索引的存留期間加以鎖定。 基於這個理由，開發人員通常會使用入口網站來建立簡單的索引、測試概念，或使用管理入口網站頁面來查詢設定。 如果您遵循以程式碼為基礎的方法，則整個索引設計中頻繁的反覆動作就會更有效率，如此便能讓您輕鬆地重建索引。

系統會先將分析器與建議工具關聯至欄位，然後再儲存索引。 請務必按一下每個索引標籤頁面，以將語言分析器或建議工具新增至您的索引定義中。

字串欄位通常會標示為 [可搜尋] 和 [可擷取]。

用來縮小搜尋結果的欄位包括 [可排序]、[可篩選] 及 [可 Facet]。

欄位屬性會決定欄位的使用方式，例如，是否將它用於全文檢索搜尋、多面向導覽、排序作業等。 下表描述每個屬性。

|屬性|說明|  
|---------------|-----------------|  
|**searchable**|可進行全文檢索搜尋，受限於語彙分析，例如，在編製索引期間執行斷字功能。 如果您為可搜尋的欄位設定像是「sunny day」的值，則系統會在內部將它分割為「sunny」和「day」這兩個個別的語彙基元。 如需詳細資訊，請參閱[全文檢索搜尋如何運作](search-lucene-query-architecture.md)。|  
|**filterable**|在 **$filter** 查詢中加以參考。 類型 `Edm.String` 或 `Collection(Edm.String)` 的可篩選欄位不會執行斷字功能，因此，只會針對完全相符的項目進行比較。 例如，如果您將欄位 f 之類的欄位設為「sunny day」，`$filter=f eq 'sunny'` 就會找不到相符項目，但 `$filter=f eq 'sunny day'` 可以。 |  
|**sortable**|根據預設，系統會依分數來排序結果，但您可根據文件中的欄位設定排序。 類型 `Collection(Edm.String)` 的欄位不能是 **sortable**。 |  
|**facetable**|通常用來呈現搜尋結果，其中包含依類別的叫用次數 (例如，特定城市中的旅館)。 此選項無法與類型 `Edm.GeographyPoint`的欄位搭配使用。 類型為 `Edm.String` 的欄位 (其為 **filterable**、**sortable** 或 **facetable**) 長度最多可達 32 KB。 如需詳細資訊，請參閱[建立索引 (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)。|  
|**key**|索引內文件的唯一識別碼。 您必須只選擇一個欄位作為索引鍵欄位，而它的類型必須是 `Edm.String`。|  
|**retrievable**|判斷搜尋結果中是否會傳回該欄位。 當您想要使用某個欄位 (例如 profit margin) 作為篩選、排序或評分機制，但不想讓使用者看見該欄位時，這非常有用。 針對 `true` for `key` 。|  

## <a name="create-the-hotels-index-used-in-example-api-sections"></a>建立範例 API 各節中所使用的 hotels 索引

Azure 搜尋服務 API 文件包含具有簡單 hotels 索引功能的程式碼範例。 在以下的螢幕擷取畫面中，您可以看到索引定義，包括在索引定義期間指定的法文語言分析器，而您可以在入口網站中當作練習加以重新建立。

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next-steps"></a>後續步驟

建立 Azure 搜尋服務索引之後，您可以移到下一個步驟：[將可搜尋的資料上傳至索引](search-what-is-data-import.md)。

或者，您可能也需要深入探討索引。 除了欄位集合之外，索引也會指定分析器、建議工具、評分設定檔和 CORS 設定。 入口網站提供索引標籤頁面來定義最常見的元素：欄位、分析器及建議工具。 若要建立或修改其他元素，您可以使用 REST API 或 .NET SDK。

## <a name="see-also"></a>另請參閱

 [全文檢索搜尋如何運作](search-lucene-query-architecture.md)  
 [搜尋服務 REST API](https://docs.microsoft.com/rest/api/searchservice/) [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)

