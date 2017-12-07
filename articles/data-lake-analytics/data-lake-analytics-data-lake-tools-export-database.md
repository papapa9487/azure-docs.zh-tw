---
title: "如何使用 Azure Data Lake Tools for Visual Studio 匯出 U-SQL 資料庫 | Microsoft Docs"
description: "了解如何使用 Azure Data Lake Tools for Visual Studio 來匯出 U-SQL 資料庫，並同時將其匯入本機帳戶。"
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
ms.date: 11/27/2017
ms.author: yanacai
ms.openlocfilehash: 9f11e07f7fbaf2b708d6fbc8a746238745132bda
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-export-u-sql-database"></a>如何匯出 U-SQL 資料庫

在本文中，您將學習如何使用 [Azure Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs)，以單一 U-SQL 指令碼的形式匯出 U SQL 資料庫並下載資源。 相同的程序也支援將匯出的資料庫匯入到本機帳戶。

客戶通常會維護多個開發、測試和實際執行環境。 這些環境會裝載在開發人員的本機電腦上的本機帳戶，以及 Azure 上的 Azure Data Lake Analytics 帳戶中。 在開發和測試環境下開發和微調 U-SQL 查詢時，開發人員常會需要重現實際執行資料庫中的所有項目。 **資料庫匯出精靈**可協助加速此流程。 開發人員可使用精靈，將現有的資料庫環境和範例資料複製到其他 Azure Data Lake Analytics 帳戶中。

## <a name="export-steps"></a>匯出步驟

### <a name="step-1-right-click-the-database-in-server-explorer-and-click-export"></a>步驟 1：在伺服器總管中以滑鼠右鍵按一下資料庫，然後按一下 [匯出...]

您具有權限的所有 Azure Data Lake Analytics 帳戶，都會在伺服器總管中列出。 請展開包含想匯出資料庫的帳戶，並以滑鼠右鍵按一下資料庫以選擇 [匯出...]。若找不到內容功能表，您會需要[將工具更新到最新版本](http://aka.ms/adltoolsvs)。

![Data Lake Analytics 工具匯出資料庫](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

### <a name="step-2-configure-the-objects-you-want-to-export"></a>步驟 2：設定想要匯出的物件

有時候資料庫很大而您只想匯出其中一小部分，就可以在匯出精靈中設定想匯出物件的子集。 請注意，執行 U-SQL 作業時已完成匯出動作，因此從 Azure 帳戶匯出會產生一些成本。

![Data Lake Analytics 工具匯出資料庫精靈](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-more-configurations"></a>步驟 3：檢查物件清單和更多設定

在此步驟中，您可以在對話頂端仔細檢查所選物件。 若有錯誤，您可以按一下 [上一步] 以返回，並再次設定想要匯出的物件。

您也可以執行有關匯出目標的其他設定，下表中列出了這些設定的說明：

|組態|說明|
|-------------|-----------|
|目的地名稱|此名稱表示匯出的資料庫資源儲存位置，例如組件、其他檔案和範例資料。 在您的本機資料根資料夾底下，將建立具有此名稱的資料夾。|
|專案目錄|此路徑會定義匯出的 U-SQL 指令碼 (其中包含所有資料庫物件定義) 儲存位置。|
|僅限結構描述|選取此選項則只會匯出資料庫定義和資源 (例如組件和其他檔案)。|
|結構描述和資料|選取此選項則資料庫定義、資源和資料都會匯出。 已匯出資料表最頂端的 N 列。|
|自動匯入本機資料庫|勾選此設定代表匯出的資料庫在匯出完成後，將自動匯入本機資料庫。|

![Data Lake Analytics 工具匯出資料庫精靈設定](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>步驟 4：檢查匯出結果

在所有設定和匯出進度完成後，您可以在精靈的記錄視窗中找到匯出的結果。 透過下方螢幕擷取畫面中以紅色矩形標示的記錄檔，您可以找到匯出的 U-SQL 指令碼和資料庫資源所在位置，其中包括組件、其他檔案和範例資料。

![Data Lake Analytics 工具匯出資料庫精靈已完成](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="how-to-import-the-exported-database-to-local-account"></a>如何將匯出的資料庫匯入到本機帳戶

進行此匯入最方便的方式，是在步驟 3 中的匯出進度期間勾選 [自動匯入本機資料庫]。 若您忘了這麼做，可以透過匯出記錄檔尋找匯出的 U-SQL 指令碼，並在本機執行 U-SQL 指令碼，將資料庫匯入到您的本機帳戶中。

## <a name="how-to-import-the-exported-database-to-azure-data-lake-analytics-account"></a>如何將匯出的資料庫匯入到 Azure Data Lake Analytics 帳戶

若要將資料庫匯入到其他 Azure Data Lake Analytics 帳戶中，您需要執行兩個步驟：

1. 將匯出的資源 (包括組件、其他檔案和範例資料)，上傳到要匯入的目標 Azure Data Lake Analytics 帳戶預設的 Azure Data Lake Store 帳戶。 在本機資料根資料夾底下，可以找到匯出的資源資料夾，然後將整個資料夾上傳到預設儲存體帳戶的根目錄。
2. 上傳完成之後，將匯出的 U-SQL 指令碼提交到想要匯入資料庫的目標 Azure Data Lake Analytics 帳戶。

## <a name="known-limitation"></a>已知的限制

目前，若在精靈中選取 [結構描述和資料]，此工具會執行 U-SQL 作業，匯出儲存在資料表中的資料。 這就是為什麼資料匯出流程可能會很慢，而且會產生成本。 

## <a name="next-steps"></a>後續步驟

* [了解 U-SQL 資料庫](https://msdn.microsoft.com/library/azure/mt621299.aspx) 
* [如何使用本機執行和 Azure Data Lake U-SQL SDK 對 U-SQL 作業進行測試和偵錯](data-lake-analytics-data-lake-tools-local-run.md)


