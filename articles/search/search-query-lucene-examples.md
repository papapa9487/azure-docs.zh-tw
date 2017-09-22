---
title: "Azure 搜尋服務的 Lucene 查詢範例 | Microsoft Docs"
description: "模糊搜尋、鄰近搜尋、詞彙提升、規則運算式搜尋與萬用字元搜尋的 Lucene 查詢語法。"
services: search
documentationcenter: 
author: LiamCa
manager: pablocas
editor: 
tags: Lucene query analyzer syntax
ms.assetid: 147f360d-a5ce-4d7b-a909-c8b65bfb748c
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/21/2017
ms.author: liamca
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 1faed621039ecd04064cb074e6b9011418e6ec47
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---

# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>在 Azure 搜尋服務中建置查詢的 Lucene 查詢語法範例
建構 Azure 搜尋服務的查詢時，您可以使用預設的[簡單查詢語法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)或替代的 [Azure 搜尋服務 Lucene 查詢剖析器](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)。 Lucene 查詢剖析器支援更複雜的查詢建構，例如欄位範圍查詢、模糊搜尋、鄰近搜尋、詞彙提升，和規則運算式搜尋。

在本文中，您可以逐步執行範例，以示範使用完整語法時可用的查詢作業。

## <a name="viewing-the-examples-in-jsfiddle"></a>檢視 JSFiddle 中的範例

本文中的所有範例是針對在 [JSFiddle](https://jsfiddle.net) 中預先載入的搜尋索引執行的可執行檔查詢，JSFiddle 是測試指令碼和 HTML 的線上程式碼編輯器。 

若要執行這些範例，請以滑鼠右鍵按一下查詢範例 URL，以在個別瀏覽器視窗中開啟 JSFiddle。

> [!NOTE]
> 下列範例會根據 [紐約市 OpenData](https://nycopendata.socrata.com/) 計劃所提供的資料集，利用由可用工作所組成的搜尋索引。 這項資料不應視為目前的或已完成。 索引位於由 Microsoft 提供的沙箱服務上。 您不需要 Azure 訂用帳戶或 Azure 搜尋服務即可嘗試這些查詢。
>


## <a name="how-to-invoke-full-lucene-parsing"></a>如何叫用完整 Lucene 剖析

本文中的所有範例會指定 **queryType=full** 搜尋參數，表示 Lucene 查詢剖析器處理的完整語法。 

**範例 1** -- 以滑鼠右鍵按一下下列查詢程式碼片段，以在載入 JSFiddle 及執行查詢的新瀏覽器頁面上將其開啟：

* [&queryType=full&search=*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

在新的瀏覽器視窗中，JavaScript 來源和 HTML 輸出並排顯示。 指令碼會參考完整查詢 (而不只是連結中所示的程式碼片段)。 每個範例的 URL 中會顯示完整查詢。 

此查詢會從紐約市工作索引 (在沙箱服務上載入的 nycjobs) 傳回文件。 為求簡潔，查詢會指定僅傳回公司職稱。 完整基礎查詢如下所示：

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

**searchFields** 參數會將搜尋限制在商務標題欄位。 **queryType** 設為 **full**，指示 Azure 搜尋服務對這個查詢使用 Lucene 查詢剖析器。

> [!NOTE]
> 如需有關查詢處理的背景知識，請參閱[全文檢索搜尋如何在 Azure 搜尋服務中運作](search-lucene-query-architecture.md)。 如需搜尋參數的詳細資訊，請參閱 [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (搜尋文件 (Azure 搜尋服務 REST API))。
>

### <a name="fielded-query-operation"></a>加入欄位的查詢作業
您可以修改本文中的範例：指定 **fieldname:searchterm** 建構來定義加入欄位的查詢作業，其中欄位是單一文字，而搜尋詞彙也是一個單一文字或片語，選擇性使用布林運算子。 某些範例包括以下內容：

* business_title:(senior NOT junior)
* state:("New York" AND "New Jersey")

如果您想要將字串視為單一實體評估，請務必將多個字串放在引號中，例如搜尋 [位置] 欄位中兩個不同城市的情況。 此外，請確定運算子是大寫，如同您看到的 NOT 和 AND。

**fieldname:searchterm** 中指定的欄位必須是可搜尋的欄位。 如需欄位定義中索引屬性使用方式的詳細資訊，請參閱 [建立索引 (Azure 搜尋服務 REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) 。

**範例 2** -- 以滑鼠右鍵按一下下列查詢程式碼片段。此查詢會搜尋包含 senior 詞彙的公司職稱，而不是包含 junior：

* [&queryType=full&search= business_title:senior NOT junior](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="fuzzy-search-example"></a>模糊搜尋範例
模糊搜尋會尋找具有類似建構的相符項目。 在每個 [Lucene 文件](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)中，模糊搜尋以 [Damerau-Levenshtein 距離](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance)為基礎。

若要執行模糊搜尋，請在單一文字結尾附加波狀符號 "~"，加上選擇性參數，介於 0 和 2 之間且指定編輯距離的值。 比方說，"blue~" 或 "blue~1" 會傳回 blue、blues 和 glue。

**範例 3** -- 以滑鼠右鍵按一下下列查詢程式碼片段。 此查詢會搜尋具有詞彙關聯的工作 (其中有拼字錯誤)︰

* [&queryType=full&search= business_title:asosiate~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

> [!Note]
> 不會[分析](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis)模糊查詢，如果您預期詞幹分析或詞形歸併還原，可能會對此感到意外。 只能對完整詞彙執行語彙分析 (詞彙查詢或片語查詢)。 不完整詞彙的查詢類型 (前置詞查詢、萬用字元查詢、Regex 查詢、模糊查詢) 會直接新增至查詢樹狀結構，並略過分析階段。 只能對不完整的查詢詞彙執行小寫轉換。
>

## <a name="proximity-search-example"></a>鄰近搜尋範例
鄰近搜尋可用來尋找文件中彼此相近的詞彙。 在片語的結尾插入波狀符號 "~"，後面加上建立鄰近界限的字數。 例如，"hotel airport"~5 會在文件中每 5 個字內尋找旅館和機場等詞彙。

**範例 4** -- 以滑鼠右鍵按一下查詢。 搜尋詞彙 "senior analyst"，且其中將其分隔的字數不得超過一個字︰

* [&queryType=full&search=business_title:"senior analyst"~1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**範例 5** -- 再試一次，移除 "senior analyst" 一詞之間的單字。

* [&queryType=full&search=business_title:"senior analyst"~0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting-examples"></a>提升詞彙範例
提升一詞指的是如果文件包含提升詞彙，則將其評等提高，高於不包含該詞彙的文件。 這與評分設定檔的不同之處在於評分設定檔會提升特定欄位，而不是特定詞彙。 下列範例可協助說明差異。

請考慮使用評分設定檔，可提高特定欄位中的相符項目，例如 musicstoreindex 範例中的 **genre** 。 詞彙提升可用來進一步提升某些搜尋詞彙，使其高於其他詞彙。 比方說，"rock^2 electronic" 可提升包含搜尋詞彙的文件﹐使其在 [genre]  欄位中高於索引中的其他可搜尋欄位。 此外，包含搜尋詞彙 "rock" 的文件排名會比另一個搜尋詞彙 "electronic" 還高，此為詞彙提升值 (2) 的結果。

若要提升詞彙，請使用插入號 "^"，並在搜尋詞彙的結尾加上提升係數 (數字)。 提升係數越高，該詞彙相對於其他搜尋詞彙的關聯性也越高。 根據預設，提升係數為 1。 雖然提升係數必須是正數，但是它可能會小於 1 (例如，0.2)。

**範例 6** -- 以滑鼠右鍵按一下查詢。 搜尋包含詞彙「電腦分析師 (computer analyst)」的工作時，我們會發現包含電腦和分析師兩個單字的搜尋沒有結果，但是分析師工作會顯示在結果的頂端。

* [&queryType=full&search=business_title:computer analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**範例 7** -- 再試一次，在兩個單字並沒有同時存在的情況下，這一次提升包含電腦一詞的結果，高於包含分析師一詞。

* [&queryType=full&search=business_title:computer^2 analyst](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression-example"></a>規則運算式範例
規則運算式搜尋會根據正斜線 "/" 之間的內容尋找相符項目，如 [RegExp 類別](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html)中所記錄。

**範例 8** -- 以滑鼠右鍵按一下查詢。 搜尋包含 Senior 或 Junior 詞彙的工作。

* `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

在頁面中，此範例的 URL 將無法正確轉譯。 解決的方法是複製下列 URL 並將它貼到瀏覽器 URL 位址︰`http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`

## <a name="wildcard-search-example"></a>萬用字元搜尋範例
您可以使用一般辨識語法進行多個 (\*) 或單一 (?) 字元的萬用字元搜尋。 請注意，Lucene 查詢剖析器支援搭配使用這些符號與單一詞彙，而不是片語。

**範例 9** -- 以滑鼠右鍵按一下查詢。 搜尋包含前置詞 'prog' 的工作，其中包括內含程式設計 (programming) 與程式設計人員 (programmer) 的職稱。

* [&queryType=full&$select=business_title&search=business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26queryType=full%26$select=business_title%26search=business_title:prog*)

您無法使用 * 或 ? 符號做為搜尋的第一個字元。

## <a name="next-steps"></a>後續步驟
請在您的程式碼中嘗試指定 Lucene 查詢剖析器。 下列連結說明如何設定 .NET 和 REST API 的搜尋查詢。 這些連結會使用預設的簡單語法，因此您必須套用您從本文了解的內容來指定 **queryType**。

* [使用 .NET SDK 查詢 Azure 搜尋服務索引](search-query-dotnet.md)
* [使用 REST API 查詢 Azure 搜尋服務索引](search-query-rest-api.md)

## <a name="see-also"></a>另請參閱

 [全文檢索搜尋如何在 Azure 搜尋服務中運作](search-lucene-query-architecture.md)
