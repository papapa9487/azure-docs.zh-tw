---
title: "Azure Data Lake Analytics 配額限制 | Microsoft Docs"
description: "了解如何調整並提高 Azure Data Lake Analytics (ADLA) 帳戶中的配額限制。"
services: data-lake-analytics
keywords: Azure Data Lake Analytics
documentationcenter: 
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: omidm
ms.openlocfilehash: 957f306ea0e80b5830ad64e5ef06c6d122d9eccc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-lake-analytics-quota-limits"></a>Azure Data Lake Analytics 配額限制

了解如何調整並提高 Azure Data Lake Analytics (ADLA) 帳戶中的配額限制。 了解這些限制可幫助您了解 U-SQL 作業的行為。 所有配額限制並非強制，因此您可以與我們連絡來增加上限。

## <a name="azure-subscriptions-limits"></a>Azure 訂用帳戶限制

**每個訂用帳戶的 ADLA 帳戶最大數目︰**5

 這是每個訂用帳戶您可以建立的 ADLA 帳戶的數目上限。 如果您嘗試建立第六個 ADLA 帳戶，會收到「您已達到訂用帳戶名稱下區域中允許的 Data Lake Analytics 帳戶的數目上限 (5)」錯誤。 在此情況下，您可刪除任何未使用的 ADLA 帳戶，或[建立支援票證](#increase-maximum-quota-limits)與我們聯絡。

## <a name="adla-account-limits"></a>ADLA 帳戶限制

**每個帳戶分析單位 (AU) 的最大數目︰**250

這是可以同時在您的帳戶中執行的 AU 的最大數目。 如果您的所有作業加起來的執行中 AU 總數超過此限制，系統會自動將較新的工作排入佇列。 例如：

* 如果您只有一個作業使用 250 個 AU 在執行，當您提交第二個作業時，在第一個作業完成之前，第二個作業會在作業佇列中等待。
* 如果您已經有五個執行的作業，而且每個都使用 50 AU，當您送出第六個需要 20 AU 的作業時它會在佇列中等到有 20 AU 可使用。

**每個帳戶的並行 U-SQL 作業最大數目︰**20

這是可以同時在您的帳戶中執行之工作的最大數目。 超過此值，新的工作會自動排入佇列。

## <a name="adjust-adla-quota-limits-per-account"></a>調整每個帳戶的 ADLA 配額限制

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選擇現有的 ADLA 帳戶。
3. 按一下 [內容] 。
4. 調整 [平行處理原則] 和 [並行工作] 以符合您的需求。

    ![Azure Data Lake Analytics 入口網站刀鋒視窗](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="increase-maximum-quota-limits"></a>增加配額限制上限

1. 在 Azure 入口網站中開啟支援要求。

    ![Azure Data Lake Analytics 入口網站刀鋒視窗](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Azure Data Lake Analytics 入口網站刀鋒視窗](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. 選取 [配額] 問題類型。
3. 選取您的 [訂用帳戶] \(請確定它不是「試用」的訂用帳戶\)。
4. 選取 [Data Lake Analytics] 配額類型。

    ![Azure Data Lake Analytics 入口網站刀鋒視窗](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. 在問題刀鋒視窗中，說明您所要增加的限制，以及您為何需要這個額外的容量的**詳細資料**。

    ![Azure Data Lake Analytics 入口網站刀鋒視窗](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. 確認您的連絡資訊，建立支援要求。

Microsoft 會檢閱您的要求，並嘗試盡速符合您的業務需求。

## <a name="next-steps"></a>後續步驟

* [Microsoft Azure Data Lake Analytics 概觀](data-lake-analytics-overview.md)
* [使用 Azure PowerShell 管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-powershell.md)
* [使用 Azure 入口網站監視和疑難排解 Azure Data Lake Analytics 作業](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
