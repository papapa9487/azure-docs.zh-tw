---
title: "OMS Log Analytics 記錄搜尋中的規則運算式 | Microsoft Docs"
description: "您可以在 Log Analytics 記錄搜尋中使用 RegEx 關鍵字，根據規則運算式篩選結果。  這篇文章會使用數個範例提供這些運算式的語法。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: bwren
ms.openlocfilehash: 9746170f157ed5065adc953a31687ff18bd73708
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="using-regular-expressions-to-filter-log-searches-in-log-analytics"></a>使用規則運算式在 Log Analytics 中篩選記錄搜尋

[記錄搜尋](log-analytics-log-searches.md)可讓您從 Log Analytics 儲存機制中擷取資訊。  [篩選條件運算式](log-analytics-search-reference.md#filter-expressions)可讓您根據特定準則篩選搜尋的結果。  **RegEx** 關鍵字可讓您指定此篩選器的規則運算式。  

這篇文章提供 Log Analytics 所使用的規則運算式語法詳細資料。

> [!NOTE]
> RegEx 僅能搭配可搜尋的欄位使用。  如需可搜尋欄位的詳細資訊，請參閱[在 Log Analytics 中使用記錄搜尋以尋找資料](log-analytics-log-searches.md#use-additional-filters)中的**欄位類型**。


## <a name="regex-keyword"></a>RegEx 關鍵字

利用下列語法在記錄搜尋中使用 **RegEx** 關鍵字。  您可以使用本文中的其他區段來判斷規則運算式本身的語法。

    field:Regex("Regular Expression")
    field=Regex("Regular Expression")

例如，若要使用規則運算式來傳回「警告」或「錯誤」類型的警示記錄，您會使用下列的記錄搜尋。

    Type=Alert AlertSeverity=RegEx("Warning|Error")

## <a name="partial-matches"></a>部分符合結果
請注意，規則運算式必須符合屬性的所有文字。  部分符合結果不會傳回任何記錄。  例如，如果您嘗試從名為 srv01.contoso.com 的電腦傳回記錄，下列的記錄搜尋**不會**傳回任何記錄。

    Computer=RegEx("srv..")

這是因為只有名稱的第一個部分符合規則運算式。  下列兩個記錄搜尋會從這台電腦傳回記錄，因為它們符合完整名稱。

    Computer=RegEx("srv..@")
    Computer=RegEx("srv...contoso.com")

## <a name="characters"></a>字元
指定不同的字元。

| Character | 說明 | 範例 | 範例相符項目 |
|:--|:--|:--|:--|
| a | 字元的一個項目。 | Computer=RegEx("srv01.contoso.com") | srv01.contoso.com |
| 。 | 任何單一字元。 | Computer=RegEx("srv...contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| a? | 字元的零或一個項目。 | Computer=RegEx("srv01?.contoso.com") | srv0.contoso.com<br>srv01.contoso.com |
| a* | 字元的零或多個項目。 | Computer=RegEx("srv01*.contoso.com") | srv0.contoso.com<br>srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| a+ | 字元的一或多個項目。 | Computer=RegEx("srv01+.contoso.com") | srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| [abc] | 在括號中比對任何單一字元 | Computer=RegEx("srv0[123].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| [a-z] | 在範圍中比對單一字元。  可以包含多個範圍。 | Computer=RegEx("srv0[1-3].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| [^abc] | 沒有括號括住的字元 | Computer=RegEx("srv0[^123].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [^a-z] | 沒有範圍中的字元。 | Computer=RegEx("srv0[^1-3].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [*n*-*m*] | 比對數字字元的範圍。 | Computer=RegEx("srv[01-03].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| @ | 任何字元的字串。 | Computer=RegEx("srv@.contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |


## <a name="multiple-occurences-of-character"></a>字元的多個項目
指定特定字元的多個項目。

| Character | 說明 | 範例 | 範例相符項目 |
|:--|:--|:--|:--|
| a{n} |  字元的 *n* 個項目。 | Computer=RegEx("bw-win-sc01{3}.bwren.lab") | bw-win-sc0111.bwren.lab |
| a{n,} |  字元的 *n* 或多個項目。 | Computer=RegEx("bw-win-sc01{3,}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab<br>bw-win-sc0111111.bwren.lab |
| a{n,m} |  字元的 *n* 至 *m* 個項目。 | Computer=RegEx("bw-win-sc01{3,5}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab |


## <a name="logical-expressions"></a>邏輯運算式
從多個值選取。

| Character | 說明 | 範例 | 範例相符項目 |
|:--|:--|:--|:--|
| &#124; | 邏輯 OR。  如果符合任一個運算式則傳回結果。 | Type=Alert AlertSeverity=RegEx("Warning&#124;Error") | 警告<br>錯誤 |
| & | 邏輯 AND。  如果符合兩個運算式則傳回結果 | EventData=regex("(Security.\*&.\*success.\*)") | 安全性稽核成功 |


## <a name="literals"></a>常值
將特殊字元轉換成常值字元。  這包括向規則運算式提供功能的字元，例如 ?-\*^\[\]{}\(\)+\|.&。

| Character | 說明 | 範例 | 範例相符項目 |
|:--|:--|:--|:--|
| \\ | 將特殊字元轉換成常值。 | Status_CF=\\[Error\\]@<br>Status_CF=Error\\-@ | [Error]找不到檔案。<br>Error-找不到檔案。 |


## <a name="next-steps"></a>後續步驟

* 熟悉[記錄搜尋](log-analytics-log-searches.md)來檢視和分析 Log Analytics 儲存機制中的資料。
