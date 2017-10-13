---
title: "對 U-SQL 作業進行偵錯 | Microsoft Docs"
description: "了解如何使用 Visual Studio 對失敗的 U-SQL Vertex 進行偵錯。"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: saveenr
ms.openlocfilehash: 2a77c72d3062272305208934d6406d040266c753
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>對 U-SQL 失敗作業的使用者定義 C# 程式碼進行偵錯

U-SQL 提供使用 C# 的擴充性模型，所以您可以撰寫程式碼以新增功能，例如自訂擷取程式或減壓器。 若要深入了解，請參閱 [U-SQL 程式設計指南](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf)。 在實務上，任何程式碼都可能需要偵錯，而且巨量資料系統可能僅提供有限的執行階段偵錯資訊，例如記錄檔。

Azure Data Lake Tools for Visual Studio 提供稱為**頂點失敗偵錯**的功能，可讓您將失敗的作業從雲端複製到本機電腦進行偵錯。 本機複本會擷取整個雲端環境，包括任何輸入資料和使用者程式碼。

下列影片示範 Azure Data Lake Tools for Visual Studio 中的頂點失敗偵錯。

> [!VIDEO https://e0d1.wpc.azureedge.net/80E0D1/OfficeMixProdMediaBlobStorage/asset-d3aeab42-6149-4ecc-b044-aa624901ab32/b0fc0373c8f94f1bb8cd39da1310adb8.mp4?sv=2012-02-12&sr=c&si=a91fad76-cfdd-4513-9668-483de39e739c&sig=K%2FR%2FdnIi9S6P%2FBlB3iLAEV5pYu6OJFBDlQy%2FQtZ7E7M%3D&se=2116-07-19T09:27:30Z&rscd=attachment%3B%20filename%3DDebugyourcustomcodeinUSQLADLA.mp4]
>

> [!NOTE]
> 如果尚未安裝，Visual Studio 需要下列兩項更新：[Microsoft Visual C++ 2015 可轉散發套件 Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) 和 [Windows 通用 C 執行階段](https://www.microsoft.com/download/details.aspx?id=50410)。

## <a name="download-failed-vertex-to-local-machine"></a>將失敗的頂點下載至本機電腦

當您在 Azure Data Lake Tools for Visual Studio 中開啟失敗的作業時，您會看到一個黃色的警示列，[錯誤] 索引標籤中有詳細的錯誤訊息。

1. 按一下 [下載]  以下載所有必要的資源和輸入資料流。 如果下載未完成，請按一下 [重試]。

2. 在下載完成後按一下 [開啟]，以產生本機的偵錯環境。 隨即會自動建立並開啟具有偵錯方案的新 Visual Studio 執行個體。

![Azure Data Lake Analytics U-SQL 偵錯 Visual Studio 下載 Vertex](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

作業可能會包含程式碼後置原始程式檔或已註冊的組件，且這兩種類型有不同的偵錯案例。

- [偵錯程式碼後置失敗的作業](#debug-job-failed-with-code-behind)
- [偵錯組件失敗的作業](#debug-job-failed-with-assemblies)


## <a name="debug-job-failed-with-code-behind"></a>偵錯程式碼後置失敗的作業

如果 U-SQL 作業失敗，而且作業包含使用者程式碼 (U-SQL 專案中通常命名為 `Script.usql.cs`)，原始程式碼會匯入偵錯方案。  您可從這裡使用 Visual Studio 偵錯工具 (監看式、變數等等) 對問題進行疑難排解。

> [!NOTE]
> 在進行偵錯之前，請確定核取 [例外狀況設定] 視窗中的 [Common Language Runtime 例外狀況] (**Ctrl + Alt + E**)。

![Azure Data Lake Analytics U-SQL 偵錯 Visual Studio 設定](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

1. 按 **F5** 執行程式碼後置程式碼。 它會一直執行到被例外狀況停止為止。

2. 開啟 `ADLTool_Codebehind.usql.cs` 檔案並設定中斷點，然後按 **F5** 逐步偵錯程式碼。

    ![Azure Data Lake Analytics U-SQL 偵錯例外狀況](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

## <a name="debug-job-failed-with-assemblies"></a>偵錯組件失敗的作業

如果在 U-SQL 指令碼中使用已註冊的組件，系統就無法自動取得原始程式碼。 在此情況下，請手動將組件的原始程式碼檔案新增至方案。

### <a name="configure-the-solution"></a>設定方案

1. 以滑鼠右鍵按一下 [方案 'VertexDebug'] > [新增] > [現有專案...]，以尋找組件的原始程式碼，並將專案新增至偵錯方案。

    ![Azure Data Lake Analytics U-SQL 偵錯新增專案](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. 在方案中，以滑鼠右鍵按一下 [LocalVertexHost] > [屬性]，並複製**工作目錄**路徑。

3. 以滑鼠右鍵按一下 [組件原始程式碼專案] > [屬性]，選取位於左側的 [建置] 索引標籤，將複製的路徑貼到 [輸出] > [輸出路徑]。

    ![Azure Data Lake Analytics U-SQL 偵錯設定 pdb 路徑](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

4. 按 **Ctrl + Alt + E**，核取 [例外狀況設定] 視窗中的 [Common Language Runtime 例外狀況]。

### <a name="start-debug"></a>開始偵錯

1. 以滑鼠右鍵按一下 [組件原始程式碼專案] > [重建]，將 .pdb 檔案輸出至 `LocalVertexHost` 工作目錄。

2. 按 **F5**，專案就會一直執行到被例外狀況停止為止。 您會看到下列警告訊息，可以放心忽略。 可能需要一分鐘左右才會移至 [偵錯] 畫面。

    ![Azure Data Lake Analytics U-SQL 偵錯 Visual Studio 警告](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-visual-studio-u-sql-debug-warning.png)

3. 開啟您的原始程式碼並設定中斷點，然後按 **F5** 逐步偵錯程式碼。

您也可以使用 Visual Studio 偵錯工具 (監看式、變數等等) 對問題進行疑難排解。

> [!NOTE]
> 每次修改程式碼以產生更新的 .pdb 檔案之後，都會重建組件原始程式碼專案。

偵錯之後，如果專案成功完成，[輸出] 視窗就會顯示下列訊息：

```
The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).
```

![Azure Data Lake Analytics U-SQL 偵錯成功](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

## <a name="resubmit-the-job"></a>重新提交作業

完成偵錯之後，請重新提交失敗的作業。

1. 對於有程式碼後置解決方案的作業，請將 C# 程式碼複製到程式碼後置來源檔 (通常是 `Script.usql.cs`)。
2. 對於有組件的作業，請將更新的 .dll 組件登入到 ADLA 資料庫：
    1. 從伺服器總管或 Cloud Explorer 展開 [ADLA 帳戶] > [資料庫] 節點。
    2. 以滑鼠右鍵按一下 [組件] 並向 ADLA 資料庫註冊新的 .dll 組件：![Azure Data Lake Analytics U-SQL 偵錯註冊組件](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-register-assembly.png)。
3. 重新提交您的作業。

## <a name="next-steps"></a>後續步驟

- [U-SQL 可程式性指南](data-lake-analytics-u-sql-programmability-guide.md)
- [針對 Azure Data Lake Analytics 作業開發 U-SQL 使用者定義運算子](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [教學課程：使用適用於 Visual Studio 的資料湖工具開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)
