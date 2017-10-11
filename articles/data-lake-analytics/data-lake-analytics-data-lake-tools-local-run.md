---
title: "使用本機執行和 Azure Data Lake U-SQL SDK 對 U-SQL 作業進行測試和偵錯 | Microsoft Docs"
description: "了解如何使用 Azure Data Lake Tools for Visual Studio 和 Azure Data Lake U-SQL SDK 在本機工作站上對 U-SQL 作業進行測試和偵錯。"
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: yanacai
ms.openlocfilehash: 771a96df5cc66bac46e7144785be8cc072b57b31
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="test-and-debug-u-sql-jobs-by-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>使用本機執行和 Azure Data Lake U-SQL SDK 對 U-SQL 作業進行測試和偵錯

您可以使用 Azure Data Lake Tools for Visual Studio 和 Azure Data Lake U-SQL SDK，和在 Azure Data Lake 服務中一樣地在工作站上執行 U-SQL 作業。 這兩個本機執行功能可節省您對 U-SQL 作業進行測試和偵錯的時間。

## <a name="understand-the-data-root-folder-and-the-file-path"></a>了解資料根資料夾和檔案路徑

本機執行和 U-SQL SDK 兩者都需要資料根資料夾。 資料根資料夾是本機計算帳戶的「本機存放區」。 它就相當於 Data Lake Analytics 帳戶的 Azure Data Lake Store 帳戶。 切換到不同的資料根資料夾，就如同切換到不同的存放區帳戶。 如果您想要存取具有不同資料根資料夾的常用共用資料，便必須在指令碼中使用絕對路徑。 或者，您可以在資料根資料夾下建立檔案系統符號連結 (例如 NTFS 上的 **mklink**)，來指向共用資料。

資料根資料夾是用來︰

- 儲存中繼資料，包括資料庫、資料表，資料表值函式 (TVF)，以及組件。
- 查詢在 U-SQL 中定義為相對路徑的輸入和輸出路徑。 使用相對路徑能夠更容易將 U-SQL 專案部署至 Azure。

您可以在 U-SQL 指令碼中使用相對路徑和本機絕對路徑。 相對路徑是相對於指定的資料根資料夾路徑。 我們建議您使用 "/" 做為路徑分隔符號，讓您的指令碼與伺服器端相容。 以下是一些相對路徑及其對等絕對路徑的範例。 在這些範例中，C:\LocalRunDataRoot 是資料根資料夾。

|相對路徑|絕對路徑|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>從 Visual Studio 使用本機執行

Data Lake Tools for Visual Studio 提供 Visual Studio 中的 U-SQL 本機執行體驗。 透過使用這項功能，您可以︰

- 在本機執行 U-SQL 指令碼及 C# 組件。
- 在本機對 C# 組件進行偵錯。
- 從伺服器總管建立、檢視和刪除 U-SQL 目錄 (本機資料庫、組件、結構描述和資料表)。 您也可以從伺服器總管中找到本機目錄。

    ![Data Lake Tools for Visual Studio 本機執行的本機目錄](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

Data Lake Tools 安裝程式會建立 C:\LocalRunRoot 資料夾，做為預設的資料根資料夾。 預設的本機執行平行處理原則是 1。

### <a name="to-configure-local-run-in-visual-studio"></a>在 Visual Studio 中設定本機執行

1. 開啟 Visual Studio。
2. 開啟 [伺服器總管]。
3. 展開 [Azure]  >  [Data Lake Analytics]。
4. 按一下 [Data Lake] 功能表，然後按一下 [選項和設定]。
5. 在左邊的樹狀目錄中，展開 [Azure Data Lake]，然後展開 [一般]。

    ![Data Lake Tools for Visual Studio 本機執行的組態設定](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

必須要有 Visual Studio U-SQL 專案才能執行本機執行。 這與從 Azure 執行 U-SQL 指令碼不同。

### <a name="to-run-a-u-sql-script-locally"></a>在本機執行 U-SQL 指令碼
1. 從 Visual Studio 開啟您的 U-SQL 專案。   
2. 在 [方案總管] 中的 U-SQL 指令碼上按一下滑鼠右鍵，然後按一下 [提交指令碼]。
3. 選取 [(本機)] 做為要在本機執行指令碼的 Analytics 帳戶。
您也可以按一下指令碼視窗頂端的 [(本機)] 帳戶，然後按一下 [提交] \(或使用 Ctrl + F5 鍵盤快速鍵)。

    ![Data Lake Tools for Visual Studio 本機執行的提交作業](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)

### <a name="debug-scripts-and-c-assemblies-locally"></a>在本機偵錯指令碼和 C# 組件

您可以在無需將 C# 組件提交並註冊至 Azure Data Lake Analytics 服務的情況下，對它進行偵錯。 您可以在這兩個程式碼後置檔案和參考的 C# 專案中設定中斷點。

#### <a name="to-debug-local-code-in-code-behind-file"></a>如何為程式碼後置檔案中的本機程式碼偵錯

1. 在程式碼後置檔案中設定中斷點。
2. 按下 F5 以便在本機為指令碼偵錯。

> [!NOTE]
   > 下列程序僅適用於 Visual Studio 2015。 在舊版 Visual Studio 中，您可能需要手動加入 pdb 檔案。  
   >
   >

#### <a name="to-debug-local-code-in-a-referenced-c-project"></a>如何為參考的 C# 專案中的本機程式碼偵錯

1. 建立 C# 組件專案，並建置它來產生輸出 dll。
2. 使用 U-SQL 陳述式來註冊 dll：

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. 在 C# 程式碼中設定中斷點。
4. 按下 F5 以便在本機為參考 C# dll 的指令碼偵錯。

## <a name="use-local-run-from-the-data-lake-u-sql-sdk"></a>從 Data Lake U-SQL SDK 使用本機執行

除了使用 Visual Studio 在本機執行 U-SQL 指令碼之外，您可以利用 Azure Data Lake U-SQL SDK，使用命令列和程式設計介面在本機執行 U-SQL 指令碼。 這能讓您調整 U-SQL 本機測試。

深入了解 [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md)。


## <a name="next-steps"></a>後續步驟

* 若要了解更複雜的查詢，請參閱 [使用 Azure Data Lake Analytics 來分析網站記錄檔](data-lake-analytics-analyze-weblogs.md)。
* 若要檢視作業詳細資料，請參閱[針對 Azure Data Lake Analytics 作業使用作業瀏覽器和作業檢視](data-lake-analytics-data-lake-tools-view-jobs.md)。
* 若要使用頂點執行檢視，請參閱[在 Data Lake Tools for Visual Studio 中使用頂點執行檢視](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)。
