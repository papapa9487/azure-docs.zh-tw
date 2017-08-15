---
title: "Azure 搜尋服務常見問題集 (FAQ) | Microsoft Docs"
description: "取得 Microsoft Azure 搜尋服務常見問題的解答"
services: search
author: HeidiSteen
manager: jhubbard
ms.service: search
ms.technology: search
ms.topic: article
ms.date: 08/03/2017
ms.author: heidist
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 02d5fac8cf9067ec544668f306fe49b805b3d164
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---

# <a name="azure-search---frequently-asked-questions-faq"></a>Azure 搜尋服務 - 常見問題集 (FAQ)
 
 尋找 Azure 搜尋服務相關概念、程式碼和案例常見問題的解答。

## <a name="platform"></a>平台

### <a name="how-is-azure-search-different-from-full-text-search-in-my-dbms"></a>Azure 搜尋服務與 DBMS 的全文檢索搜尋有何不同？

Azure 搜尋服務支援多個資料來源、[許多語言的語言分析](https://docs.microsoft.com/rest/api/searchservice/language-support)、[關注與不尋常資料輸入的自訂分析](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)、透過[評分設定檔](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)搜尋排名控制項，以及自動提示、搜尋結果醒目提示和多面向導覽等使用者體驗功能。 它也包含其他功能，例如同義字和豐富的查詢語法，但這些功能通常並無不同之處。

### <a name="what-is-the-difference-between-azure-search-and-elasticsearch"></a>Azure 搜尋服務與 Elasticsearch 有何不同？

比較搜尋技術時，客戶經常詢問 Azure 搜尋服務與 Elasticsearch 相較下的詳細資料。 客戶選擇 Azure 搜尋服務而不是 Elasticsearch 來進行其搜尋應用程式專案的原因，通常是因為我們簡化了一個主要工作，或是需要與其他 Microsoft 技術的內建整合：

+ Azure 搜尋服務是受到完整管理的雲端服務，當佈建足夠的備援時 (2 個用於讀取存取的複本及 3 個用於讀寫的複本)，會達到服務等級協定 (SLA) 的 99.9%。
+ Microsoft 的[自然語言處理器](https://docs.microsoft.com/rest/api/searchservice/language-support)提供先進的語言分析。  
+ [Azure 搜尋服務索引子](search-indexer-overview.md)可以搜耙各種不同的 Azure 資料來源來建立初始和累加索引。
+ 如果您需要對查詢或索引量的波動快速回應，您可以使用 Azure 入口網站中的[滑桿控制項](search-manage.md#scale-up-or-down)或執行 [PowerShell 指令碼](search-manage-powershell.md)，並直接略過分區管理。  
+ [評分和調整功能](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)提供影響搜尋排名分數的方法，光是搜尋引擎並無法辦到。 

### <a name="can-i-pause-azure-search-service-and-stop-billing"></a>我可以暫停 Azure 搜尋服務並停止計費嗎？

您無法暫停服務。 建立服務時會配置計算和儲存體資源專供您使用。 您無法視需要釋放及回收這些資源。 

## <a name="indexing-operations"></a>索引作業

### <a name="backup-and-restore-or-download-and-move-indexes-or-index-snapshots"></a>可備份及還原 (或下載及移動) 索引或索引快照集嗎？

雖然您可以隨時[取得索引定義](https://docs.microsoft.com/rest/api/searchservice/get-index)，但沒有索引擷取、快照或備份還原功能，可將雲端中執行的「擴展」索引下載到本機系統，或移至其他 Azure 搜尋服務。 

索引是從您撰寫的程式碼建立及擴展，而且只會在雲端中的 Azure 搜尋服務上執行。 一般而言，想要將索引移至其他服務的客戶可藉由編輯其程式碼以使用新的端點，然後重新執行索引來完成。 如果您想要有擷取快照集或備份索引的功能，請在 [User Voice](https://feedback.azure.com/forums/263029-azure-search/suggestions/8021610-backup-snapshot-of-index) 上投下一票。

### <a name="can-i-index-from-sql-database-replicas-applies-to-azure-sql-database-indexershttpsdocsmicrosoftcomazuresearchsearch-howto-connecting-azure-sql-database-to-azure-search-using-indexers"></a>我可以從 SQL 資料庫複本建立索引嗎 (適用於 [Azure SQL Database 索引子](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-database-to-azure-search-using-indexers))

 從頭開始建立索引時，要使用主要或次要複本作為資料來源並沒有限制。 不過，使用累加式更新重新整理索引 (根據變更的記錄) 則需要主要複本。 這項需求是來自 SQL Database，以保證只對主要複本進行變更追蹤。 如果您嘗試針對索引重新整理工作負載使用次要複本，則不保證會取得所有資料。

## <a name="search-operations"></a>搜尋作業

### <a name="can-i-search-across-multiple-indexes"></a>我可以搜尋多個索引嗎？

不可以，不支援此作業。 搜尋的範圍一律是單一索引。

### <a name="can-i-restrict-search-corpus-access-by-user-identity"></a>我可以依使用者身分識別來限制搜尋主體存取嗎？

Azure 搜尋服務並沒有針對每個使用者資料存取的角色型安全性模型。 驗證是完整權限或唯讀 (在服務等級)。 有些客戶實作了以[`$filter`搜尋參數](https://docs.microsoft.com/rest/api/searchservice/search-documents)為基礎的解決方案，但它是最佳的因應措施。 如果您想要這項功能，請在 [User Voice](https://feedback.azure.com/forums/263029-azure-search/category/86074-security) 上投下一票。

### <a name="why-are-there-zero-matches-on-terms-i-know-to-be-valid"></a>為什麼我認為有效的字詞沒有任何相符項目？

最常見的情況是不知道每種查詢類型支援不同的搜尋行為和語言分析層級。 請針對主要工作負載的全文檢索搜尋，加入語言分析階段，以將字詞分解到根形式。 這種查詢剖析可以擴展可能的相符項目範圍，因為語彙基元化的字詞會符合更多變體。

如果您叫用[其他查詢類型](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) (萬用字元搜尋、模糊搜尋、鄰近搜尋、建議等等)，則不會進行語言分析。 這些字詞會以現況新增至查詢樹狀結構。 

### <a name="why-is-the-search-rank-a-constant-or-equal-score-of-10-for-every-hit"></a>為何搜尋排名是常數或相當於 1.0 的分數 (每次命中時)？

根據預設，搜尋結果會根據[相符字詞的統計屬性](search-lucene-query-architecture.md#stage-4-scoring)進行評分，並在結果集中由高排到低。 不過，某些查詢類型 (萬用字元、前置詞、Regex) 一律會對文件的整體分數提供常數分數。 這是設計的行為。 Azure 搜尋服務會使用常數分數，讓透過查詢擴充找到的相符項目包含在結果中，但不會影響排名。 

例如，假設在萬用字元搜尋中輸入 "tour*" 會產生 “tours”、“tourettes” 和 “tourmaline” 的相符項目。 考慮到這些結果的本質，便無法合理推斷哪些詞彙較其他詞彙更有價值。 基於這個理由，當評分導致萬用字元、前置詞和 Regex 的查詢類型時，我們會忽略字詞頻率。 依據部分輸入的搜尋結果會得到一個常數分數，以避免可能無法預期的相符項目偏差。

## <a name="design-patterns"></a>設計模式

### <a name="what-is-the-best-approach-for-implementing-localized-search"></a>實作當地語系化搜尋的最佳做法為何？

若要在相同索引中支援不同的地區設定 (語言)，大多數客戶都會選擇專用欄位，而不是集合。 地區設定特定的欄位可讓您指派適當的分析器。 例如，將 Microsoft 法文分析器指派給含有法文字串的欄位。 它也可以簡化篩選。 如果您知道查詢是在 fr-fr 頁面上起始，您可以將搜尋結果限制在此欄位。 或者，建立[評分設定檔](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)以提高欄位的相對權重。

## <a name="next-steps"></a>後續步驟

您的問題是否與缺少特性或功能相關？ 請在 [User Voice 網站](https://feedback.azure.com/forums/263029-azure-search)上要求此功能。

## <a name="see-also"></a>另請參閱

 [StackOverflow：Azure 搜尋服務](https://stackoverflow.com/questions/tagged/azure-search)   
 [全文檢索搜尋如何在 Azure 搜尋服務中運作](search-lucene-query-architecture.md)  
 [何謂 Azure 搜尋服務？](search-what-is-azure-search.md)

 
