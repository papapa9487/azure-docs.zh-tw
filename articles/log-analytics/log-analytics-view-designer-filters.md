---
title: "Azure Log Analytics 檢視中的篩選 | Microsoft Docs"
description: "Log Analytics 檢視中的篩選可讓使用者依特定屬性的值篩選檢視中的資料，而不需修改檢視本身。  本文說明如何使用篩選，以及如何在自訂檢視中新增篩選。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: bwren
ms.openlocfilehash: 5c2201292eb085dcc043e4257580c7971dbaffbd
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="filters-in-log-analytics-views"></a>Log Analytics 檢視中的篩選
[Log Analytics 檢視](log-analytics-view-designer.md)中的**篩選**可讓使用者依特定屬性的值篩選檢視中的資料，而不需修改檢視本身。  例如，您可以允許檢視的使用者篩選檢視，只顯示來自某個特定電腦或一組電腦的資料。  您可以在單一檢視上建立多個篩選，以允許使用者依多個屬性進行篩選。  本文說明如何使用篩選，以及如何在自訂檢視中新增篩選。

## <a name="using-a-filter"></a>使用篩選
按一下 [篩選] 以開啟檢視的篩選窗格。  這可讓您針對該檢視可用的任何篩選，選取時間範圍和值。  當您選取篩選時，它會顯示可用值的清單。  您可以選取一或多個值，或是輸入值。 檢視會自動更新來依據您指定的值進行篩選。 

如果未針對篩選選取任何值，該篩選就不會套用至檢視。  如果您移除篩選的所有值，系統就不會再套用該篩選。


![篩選範例](media/log-analytics-view-designer/filters-example.png)


## <a name="creating-a-filter"></a>建立篩選

請在[編輯檢視](log-analytics-view-designer.md)時，從 [篩選] 索引標籤建立篩選。  篩選適用於檢視全域，因此會套用至檢視的所有部分。  

![篩選設定](media/log-analytics-view-designer/filters-settings.png)

下表說明篩選的設定。

| 設定 | 說明 |
|:---|:---|
| 欄位名稱 | 用於篩選的欄位名稱。  這必須與 [查詢值] 中的 summarize 欄位相符。 |
| 查詢值 | 所要執行以填入使用者篩選下拉式清單的查詢。  這必須使用 [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) 或 [distinct](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/distinct-operator) 來為特定欄位提供獨特的值，並且必須與 [欄位名稱]相符。  您可以使用 [sort](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator) 來排序對使用者顯示的值。 |
| Tag | 支援篩選之查詢中使用的欄位名稱，此名稱也會對使用者顯示。 |

### <a name="examples"></a>範例

下表包含一些常見的篩選範例。  

| 欄位名稱 | 查詢值 | Tag |
|:--|:--|:--|
| 電腦   | Heartbeat &#124; distinct Computer &#124; sort by Computer asc | 電腦 |
| EventLevelName | Event &#124; distinct EventLevelName | 嚴重性 |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | 嚴重性 |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>修改檢視查詢

若要讓篩選產生任何效果，您就必須將檢視中的任何查詢修改成依據選取的值進行篩選。  如果您未修改檢視中的任何查詢，則使用者選取的任何值都不會有任何效果。

在查詢中使用篩選值的語法如下： 

    where ${filter name}  

例如，如果您的檢視有一個會傳回事件並使用名為 Computers 之篩選的查詢，您便可以使用以下語法。

    Event | where ${Computers} | summarize count() by EventLevelName

如果您新增了名為 Severity 的另一個篩選，則可以使用下列查詢來使用這兩個篩選。

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>後續步驟
* 深入了解您可以新增到自訂檢視中的[視覺效果組件](log-analytics-view-designer-parts.md)。