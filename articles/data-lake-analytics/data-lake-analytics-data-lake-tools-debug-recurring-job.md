---
title: "如何針對異常的週期性作業進行疑難排解 | Microsoft Docs"
description: "了解如何使用 Azure Data Lake Tools for Visual Studio 針對異常的週期性作業進行偵錯。"
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2017
ms.author: yanacai
ms.openlocfilehash: a358f94b117c12511028a875e56b5c9dba8d3382
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-troubleshoot-an-abnormal-recurring-job"></a>如何針對異常的週期性作業進行疑難排解

本文件中，我們將介紹如何使用 [Azure Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs) 針對週期性作業進行疑難排解。 從[這裡](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/)深入了解管線和週期性作業。
週期性作業通常會共用相同的查詢邏輯和類似的輸入資料。 例如，您在每個星期一早上 8 點有一個週期性作業， 要計算上週的每週作用中使用者，這些作業的指令碼共用一個包含查詢邏輯的指令碼範本，而這些作業的輸入是上週的使用方式資料。 共用相同的查詢邏輯和類似的輸入通常表示這些作業的效能很相似而且穩定，如果其中一個週期性作業突然執行異常、失敗或速度變得很慢，您可能要：

1.  查看週期性作業前幾次執行的統計資料報告，了解發生了什麼事。
2.  比較異常作業與正常作業，找出變更之處。

Azure Data Lake Tools for Visual Studio 中的**相關作業檢視**可協助您加快兩種案例的疑難排解進度。

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>步驟 1：找出週期性作業，並開啟 [相關作業檢視]

若要使用 [相關作業檢視] 針對週期性作業的問題進行疑難排解，您必須先在 Visual Studio 中找出週期性作業，然後開啟 [相關作業檢視]。

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>案例 1：您有週期性作業的 URL

透過 [工具] > [Data Lake] > [作業檢視]，您可以貼上作業 URL 以在 Visual Studio 中開啟 [作業檢視]，並透過 [檢視相關作業] 開啟 [相關作業檢視]。

![Data Lake Analytics 工具的 [檢視相關作業]](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>案例 2：您有週期性作業的管線，而不是 URL

在 Visual Studio 中，您可以透過 [伺服器總管] > 您的 Data Lake Analytics 帳戶 > [管線] 開啟管線瀏覽器 (如果您在伺服器總管中找不到這個節點，請在[這裡](http://aka.ms/adltoolsvs)取得最新的工具)。 在管線瀏覽器中，ADLA 帳戶的所有管線都會列在左側，您可以展開管線以尋找所有週期性作業，按一下有問題的作業，[相關作業檢視] 就會在右側開啟。

![Data Lake Analytics 工具的 [檢視相關作業]](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

![Data Lake Analytics 工具的 [檢視相關作業]](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-statistics-report"></a>步驟 2：分析統計資料報告

摘要和統計資料報告會顯示在 [相關作業檢視] 的頂端，您可以透過這些資料獲得異常的潛在根本原因。 

1.  首先，您必須在報告中找出異常的作業。 X 軸顯示作業提交時間，您可以透過這裡找出異常的作業。
2.  依照下列程序來檢查統計資料，並獲得異常的深入資訊和可能的解決方案。

![Data Lake Analytics 工具的 [檢視相關作業]](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-recurring-job-to-a-normal-job"></a>步驟 3：比較異常的週期性作業與正常作業

您可以透過 [相關作業檢視] 底部的作業清單，找出所有已提交的週期性作業。 以滑鼠右鍵按一下，您可以比較異常的作業與前一個正常的作業，在 [作業差異] 檢視中找出更多深入資訊和可能的解決方案。

![Data Lake Analytics 工具的 [檢視相關作業]](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

您通常需要注意這 2 個作業之間較大的差異，因為這類差異可能是造成效能問題的原因，您也可以依照下列步驟進行進一步檢查。

![Data Lake Analytics 工具的 [檢視相關作業]](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>後續步驟

* [如何偵錯並解決資料扭曲問題](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [如何針對 U-SQL 作業失敗進行偵錯找出使用者定義程式碼錯誤](data-lake-analytics-debug-u-sql-jobs.md)