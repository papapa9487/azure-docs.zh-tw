---
title: "Azure Data Lake Tools：使用 Azure Data Lake Tools for Visual Studio Code | Microsoft Docs"
description: "了解如何使用 Azure Data Lake Tools for Visual Studio Code 來建立、測試和執行 U-SQL 指令碼。 "
Keywords: VScode,Azure Data Lake Tools,Local run,Local debug,Local Debug,preview file,upload to storage path,download,upload
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/10/2017
ms.author: jejiang
ms.openlocfilehash: 60307b8b16718fdc947bde7616532fa6a0920cf0
ms.sourcegitcommit: 21a58a43ceceaefb4cd46c29180a629429bfcf76
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2017
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>使用 Azure Data Lake Tools for Visual Studio Code

了解 Azure Data Lake Tools for Visual Studio Code (VS Code) 以建立、測試和執行 U-SQL 指令碼。 下列影片中也涵蓋此資訊︰

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>必要條件

Azure Data Lake Tools for VSCode 支援 Windows、Linux 及 MacOS。  

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)。

若為 MacOS 和 Linux：
- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core)。 
- [Mono 5.2.x](http://www.mono-project.com/download/)。

## <a name="install-data-lake-tools"></a>安裝 Data Lake Tools

安裝完必要條件之後，您就可以安裝 Data Lake Tools for VS Code。

**安裝 Data Lake Tools**

1. 開啟 Visual Studio Code。
2. 按一下左窗格的 [擴充功能]。 在搜尋方塊中輸入 **Azure Data Lake**。
3. 按一下 [Azure Data Lake Tools] 旁邊的 [安裝]。 幾秒鐘後，[安裝] 按鈕就會變為 [重新載入]。
4. 按一下 [重新載入] 來啟動 **Azure Data Lake Tools** 擴充功能。
5. 按一下 [重新載入視窗] 進行確認。 您會在 [擴充功能] 窗格中看到 [Azure Data Lake Tools]。

    ![Data Lake Tools for Visual Studio Code 擴充功能窗格](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

 
## <a name="activate-azure-data-lake-tools"></a>啟動 Azure Data Lake Tools
建立一個新的 .usql 檔案或開啟現有的 .usql 檔案，以啟動擴充功能。 

## <a name="open-the-sample-script"></a>開啟範例指令碼
開啟命令選擇區 (Ctrl+Shift+P)，然後輸入 **ADL: Open Sample Script**。 它會開啟此範例的另一個執行個體。 您也可以在此執行個體上編輯、設定及提交指令碼。

## <a name="work-with-u-sql"></a>使用 U-SQL

您需要開啟 U-SQL 檔案或資料夾以使用 U-SQL。

**開啟 U-SQL 專案的資料夾**

1. 從 Visual Studio Code 選取 [檔案] 功能表，然後選取 [開啟資料夾]。
2. 指定資料夾，然後選取 [選取資料夾]。
3. 選取 [檔案] 功能表，然後選取 [新增]。 專案中就會加入一個 Untitled-1 檔案。
4. 在 Untitled-1 檔案中輸入以下程式碼：

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    這個指令碼會在 /output 資料夾中建立 departments.csv 檔案並納入一些資料。

5. 在開啟的資料夾中，將檔案儲存為 **myUSQL.usql**。 xxx_settings.json 組態檔也會新增到資料夾。
6. 開啟 xxx_settings.json 並使用下列屬性設定：

    - 帳戶：編譯及執行 U-SQL 作業時需使用的 Azure 訂用帳戶下之 Data Lake Analytics 帳戶，因此需要先設定電腦帳戶，然後再編譯及執行 U-SQL 作業。
    - 資料庫：您帳戶底下的資料庫。 預設值為 **master**。
    - 結構描述：您資料庫底下的結構描述。 預設值為 **dbo**。
    - 選擇性設定︰
        - 優先順序︰優先順序範圍是從 1 到 1000，1 是最高的優先順序。 預設值為 **1000**。
        - 平行處理原則︰平行處理原則的範圍是從 1 到 150。 預設值為您 Azure Data Lake Analytics 帳戶中允許的平行處理原則上限。 
        
        ![Data Lake Tools for Visual Studio Code 組態檔](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)
      
        > [!NOTE] 
        > 在儲存組態後，帳戶、資料庫和結構描述資訊就會出現在對應之 .usql 檔案左下角的狀態列上。

**編譯 U-SQL 指令碼**

1. 選取 Ctrl+Shift+P 以開啟命令選擇區。 
2. 輸入 **ADL: Compile Script**。 編譯結果會出現在 [輸出] 視窗中。 您可以也在指令碼檔案上按一下滑鼠右鍵，然後選取 [ADL: Compile Script] 來編譯 U-SQL 作業。 編譯結果會出現在 [輸出] 窗格中。
 

**提交 U-SQL 指令碼**

1. 選取 Ctrl+Shift+P 以開啟命令選擇區。 
2. 輸入 **ADL: Submit Job**。  您也可以在指令碼檔案上按一下滑鼠右鍵，然後選取 [ADL: Submit Job]。 

在提交 U-SQL 作業後，提交記錄會出現在 VS Code 的 [輸出] 視窗中。 如果提交成功，則作業 URL 也會出現。 您可以在網頁瀏覽器中開啟作業 URL 來追蹤即時的作業狀態。

若要能夠輸出作業詳細資料，請在 **vs code for the u-sql_settings.json** 檔案中設定 **jobInformationOutputPath**。
 
## <a name="use-python-r-and-csharp-code-behind-file"></a>使用 Python、R 和 CSharp 程式碼後置檔案
Azure Data Lake Tool 支援多個自訂程式碼，如需指示，請參閱[針對 VSCode 中的 Azure Data Lake Analytics 使用 Python、R、CSharp 開發 U-SQL](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md) (英文)。

## <a name="use-assemblies"></a>使用組件

如需有關開發組件的資訊，請參閱[針對 Azure Data Lake Analytics 作業開發 U-SQL 組件](data-lake-analytics-u-sql-develop-assemblies.md)。

您可以使用 Data Lake Tools 在 Data Lake Analytics 目錄中註冊自訂程式碼組件。

**註冊組件**

您可以透過 **ADL: Register Assembly** 或 **ADL: Register Assembly through Configuration** 命令來註冊組件。

**透過 ADL: Register Assembly 命令來進行註冊**
1.  選取 Ctrl+Shift+P 以開啟命令選擇區。
2.  輸入 **ADL: Register Assembly**。 
3.  指定本機組件路徑。 
4.  選取 Data Lake Analytics 帳戶。
5.  選取資料庫。

結果：入口網站會在瀏覽器中開啟，並顯示組件註冊程序。  

另一個可供觸發 **ADL: Register Assembly** 命令的便利方式，是對檔案總管中的 .dll 檔案按一下滑鼠右鍵。 

**透過 ADL: Register Assembly through Configuration 命令來進行註冊**
1.  選取 Ctrl+Shift+P 以開啟命令選擇區。
2.  輸入 **ADL: Register Assembly through Configuration**。 
3.  指定本機組件路徑。 
4.  隨即會顯示 JSON 檔案。 請視需要檢閱並編輯組件相依性及資源參數。 指示會顯示在 [輸出] 視窗中。 若要繼續進行組件註冊，請儲存 (Ctrl + S) JSON 檔案。

![Data Lake Tools for Visual Studio Code 程式碼後置](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
>[!NOTE]
>- 組件相依性：Azure Data Lake Tools 會自動偵測 DLL 是否有任何相依項目。 系統偵測到相依項目後，就會將其顯示在 JSON 檔案中。 
>- 資源：您可以在註冊組件時上傳 DLL 資源 (例如 .txt、.png 和 .csv)。 

另一個可供觸發 **ADL: Register Assembly through Configuration** 命令的方式，是對檔案總管中的 .dll 檔案按一下滑鼠右鍵。 

下列 U-SQL 程式碼示範如何呼叫組件。 在此範例中，組件名稱是 *test*。

```
REFERENCE ASSEMBLY [test];

@a = 
    EXTRACT 
        Iid int,
    Starts DateTime,
    Region string,
    Query string,
    DwellTime int,
    Results string,
    ClickedUrls string 
    FROM @"Sample/SearchLog.txt" 
    USING Extractors.Tsv();

@d =
    SELECT DISTINCT Region 
    FROM @a;

@d1 = 
    PROCESS @d
    PRODUCE 
        Region string,
    Mkt string
    USING new USQLApplication_codebehind.MyProcessor();

OUTPUT @d1 
    TO @"Sample/SearchLogtest.txt" 
    USING Outputters.Tsv();
```

## <a name="connect-to-azure"></a>連接到 Azure

您必須先連線到 Azure 帳戶，才能在 Data Lake Analytics 中編譯和執行 U-SQL 指令碼。

**連接到 Azure**

1.  選取 Ctrl+Shift+P 以開啟命令選擇區。 
2.  輸入 **ADL: Login**。 登入資訊便會出現在 [輸出] 窗格。

    ![Data Lake Tools for Visual Studio Code 命令選擇區](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![Data Lake Tools for Visual Studio Code 裝置登入資訊](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3. 在登入 URL (https://aka.ms/devicelogin) 上選取 Ctrl 鍵再按一下滑鼠左鍵，以開啟登入網頁。 在文字方塊中輸入代碼 **G567LX42V**，然後選取 [繼續]。

   ![Data Lake Tools for Visual Studio Code 登入貼上代碼](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  依照網頁上的指示登入。 當您連線時，您的 Azure 帳戶名稱會出現在 [VS Code] 視窗左下角的狀態列中。 

    > [!NOTE] 
    > 如果您的帳戶已啟用雙因素驗證，建議您使用電話驗證而非使用 PIN 碼。

若要登出，請輸入命令 **ADL: Logout**。

## <a name="list-your-data-lake-analytics-accounts"></a>列出 Data Lake Analytics 帳戶

若要測試連線，請取得 Data Lake Analytics 帳戶的清單。

**列出您的 Azure 訂用帳戶下的 Data Lake Analytics 帳戶**

1. 選取 Ctrl+Shift+P 以開啟命令選擇區。
2. 輸入 **ADL: List Accounts**。 帳戶會出現在 [輸出] 窗格中。


## <a name="access-the-data-lake-analytics-catalog"></a>存取 Data Lake Analytics 目錄

連線至 Azure 之後，您可以使用下列步驟來存取 U-SQL 目錄。

**存取 Azure Data Lake Analytics 中繼資料**

1.  選取 Ctrl+Shift+P，然後輸入 **ADL: List Tables**。
2.  選取其中一個 Data Lake Analytics 帳戶。
3.  選取其中一個 Data Lake Analytics 資料庫。
4.  選取其中一個結構描述。 您就會看到資料表清單。

## <a name="view-data-lake-analytics-jobs"></a>檢視 Data Lake Analytics 作業

**檢視 Data Lake Analytics 作業**
1.  開啟命令選擇區 (Ctrl+Shift+P)，然後選取 [ADL: Show Job]。 
2.  選取 Data Lake Analytics 帳戶或本機帳戶。 
3.  等候帳戶的作業清單出現。
4.  從作業清單中選取一個作業，Data Lake Tools 就會在 Azure 入口網站中開啟作業的詳細資料，並在 VS Code 中顯示 JobInfo 檔案。

    ![Data Lake Tools for Visual Studio Code IntelliSense 物件類型](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-integration"></a>Azure Data Lake Storage 整合

您可以使用 Azure Data Lake Storage 相關命令來進行下列作業：
 - 瀏覽 Azure Data Lake Storage 資源。 [列出儲存體路徑](#list-the-storage-path)。 
 - 預覽 Azure Data Lake Storage 檔案。 [預覽儲存體檔案](#preview-the-storage-file)。 
 - 在 VS Code 中直接將檔案上傳至 Azure Data Lake Storage。 [上傳檔案或資料夾](#upload-file-or-folder)。
 - 在 VS Code 中直接從 Azure Data Lake Storage 下載檔案。 [下載檔案](#download-file)。

## <a name="list-the-storage-path"></a>列出儲存體路徑 

**透過命令選擇區來列出儲存體路徑**

以滑鼠右鍵按一下指令碼編輯器，然後選取 [ADL: List Path]。

在清單中選擇資料夾，或按一下 [輸入路徑] 或 [從根路徑瀏覽] (以 [輸入路徑] 為例)。 -> 選取您的 **ADLA 帳戶**。 -> 瀏覽或輸入儲存體資料夾路徑 (例如：/output/)。 -> 命令選擇區會根據您的輸入列出路徑資訊。

![Data Lake Tools for Visual Studio Code 列出儲存體路徑結果](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

可供列出相對路徑的更便利方式為透過右鍵快顯功能表。

**透過按一下滑鼠右鍵來列出儲存體路徑**

以滑鼠右鍵按一下路徑字串，然後選取 [清單路徑] 以繼續。

![Data Lake Tools for Visual Studio Code 右鍵快顯功能表](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


## <a name="preview-the-storage-file"></a>預覽儲存體檔案

以滑鼠右鍵按一下指令碼編輯器，然後選取 [ADL: Preview File]。

選取您的 **ADLA 帳戶**。 -> 輸入 Azure 儲存體檔案路徑 (例如，/output/SearchLog.txt)。 -> 結果：檔案在 VSCode 中開啟。

   ![Data Lake Tools for Visual Studio Code 預覽檔案結果](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

還有另一個預覽檔案的方式，就是在指令碼編輯器中的檔案完整路徑或檔案相對路徑上，透過右鍵操作功能表來進行。 

## <a name="upload-file-or-folder"></a>上傳檔案或資料夾

1. 以滑鼠右鍵按一下指令碼編輯器，然後選取 [上傳檔案] 或 [上傳資料夾]。

2. 如果選擇要上傳檔案，請選擇一或多個檔案，如果選擇上傳資料夾，請選擇整個資料夾，然後按一下 [上傳]。 -> 在清單中選擇儲存體資料夾，或按一下 [輸入路徑] 或 [從根路徑瀏覽] (以 [輸入路徑] 為例)。 -> 選取您的 **ADLA 帳戶**。 -> 瀏覽或輸入儲存體資料夾路徑 (例如：/output/)。 -> 按一下 [Choose current folder] 來指定您的上傳目的地。

   ![Data Lake Tools for Visual Studio Code 上傳狀態](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    


   還有另一個將檔案上傳到儲存體的方式，就是在指令碼編輯器中的檔案完整路徑或檔案相對路徑上，透過右鍵操作功能表來上傳。

同時，您可以監視[上傳狀態](#check-storage-tasks-status)。


## <a name="download-file"></a>下載檔案 
您可以輸入 **ADL: Download File** 或 **ADL: Download File (Advanced)** 命令來下載檔案。

**透過 ADL: Download File (Advanced) 下載檔案**
1. 以滑鼠右鍵按一下指令碼編輯器，然後選取 [Download File (Advanced)]。
2. VS Code 會顯示一個 JSON 檔案。 您可以輸入檔案路徑，然後同時下載多個檔案。 指示會顯示在 [輸出] 視窗中。 若要繼續下載檔案，請儲存 (Ctrl+S) JSON 檔案。

    ![Data Lake Tools for Visual Studio Code 下載檔案與設定](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

3.  結果：[輸出] 視窗會顯示檔案上傳狀態。

    ![Data Lake Tools for Visual Studio Code 下載多個檔案結果](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

同時，您可以監視[下載狀態](#check-storage-tasks-status)。

**透過 ADL: Download File 下載檔案**

1. 以滑鼠右鍵按一下指令碼編輯器，選取 [下載檔案]，然後從 [選取資料夾] 對話方塊選取目的地資料夾。

2. 在清單中選擇資料夾，或按一下 [輸入路徑] 或 [從根路徑瀏覽] (以 [輸入路徑] 為例)。 -> 選取您的 **ADLA 帳戶**。 -> 瀏覽或輸入儲存體資料夾路徑 (例如：/output/) -> 選擇要下載的檔案。

   ![Data Lake Tools for Visual Studio Code 下載狀態](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

   
   還有另一個下載儲存體檔案的方式，就是在指令碼編輯器中的檔案完整路徑或檔案相對路徑上，透過右鍵操作功能表來上傳。

同時，您可以監視[下載狀態](#check-storage-tasks-status)。

## <a name="check-storage-tasks-status"></a>檢查儲存體工作的狀態
在下載和上傳完成時，狀態會顯示在狀態列底部。
1. 按一下下方的狀態列，然後下載和上傳狀態就會顯示在 [輸出] 面板中。

   ![Data Lake Tools for Visual Studio Code 檢查儲存體狀態](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="vscode-explorer-integration-with-azure-data-lake"></a>VSCode Explorer 與 Azure Data Lake 整合
1. 登入之後，您會看到所有 Azure 帳戶都列在 **DataLake 總管**的左側面板中。 展開一個資料庫，即可檢視節點下的 [結構描述]、[資料表]、[組件] 等等。

   ![DataLake 總管](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

2. 您可以用滑鼠右鍵按一下 [組件] 節點，以執行 [註冊組件] 命令。

    ![DataLake 總管](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

3. 瀏覽至 [儲存體帳戶]，您可以用滑鼠右鍵按一下資料夾或檔案，以上傳或下載檔案。 也可以透過操作功能表，使用**預覽**檔案、**下載**、**複製相對路徑**、**複製完整路徑**等功能。

   ![DataLake 總管](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-download-preview-file.png)

## <a name="open-adl-storage-explorer-in-portal"></a>在入口網站中開啟 ADL 儲存體總管
1. 選取 Ctrl+Shift+P 以開啟命令選擇區。
2. 輸入 **Open Web Azure Storage Explorer**，或在指令碼編輯器中的相對路徑或完整路徑上按一下滑鼠右鍵，然後選取 **Open Web Azure Storage Explorer**。
3. 選取 Data Lake Analytics 帳戶。

Data Lake Tools 會在 Azure 入口網站中開啟 Azure 儲存體路徑。 您可以找到該路徑，並從網路預覽檔案。

## <a name="local-run-and-local-debug-for-windows-users"></a>Windows 使用者的本機執行和本機偵錯
U-SQL 本機執行會先測試您的本機資料並在本機驗證您的指令碼，然後才將您的程式碼發行至 Data Lake Analytics。 本機偵錯功能可讓您先完成下列工作，再將您的程式碼提交給 Data Lake Analytics： 
- 偵錯您的 C# 程式碼後置。 
- 逐步執行程式碼。 
- 在本機驗證您的指令碼。

如需本機執行和本機偵錯的指示，請參閱[使用 Visual Studio Code 來進行 U-SQL 本機執行和本機偵錯](data-lake-tools-for-vscode-local-run-and-debug.md)。

## <a name="additional-features"></a>其他功能

Data Lake Tools for VSCode 支援下列功能︰

-   IntelliSense 自動完成：關鍵字、方法和變數等項目周圍的快顯視窗會出現建議。 不同圖示代表不同類型的物件︰

    - Scala 資料類型
    - 複雜資料類型
    - 內建 UDT
    - .NET 集合和類別
    - C# 運算式
    - 內建 C# UDF、UDO 和 UDAAG 
    - U-SQL 函式
    - U-SQL 時間範圍函式
 
    ![Data Lake Tools for Visual Studio Code IntelliSense 物件類型](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Data Lake Analytics 中繼資料的 IntelliSense 自動完成：Data Lake Tools 會將 Data Lake Analytics 中繼資料資訊下載到本機。 IntelliSense 功能會從 Data Lake Analytics 中繼資料自動填入物件，包括資料庫、結構描述、資料表、檢視、資料表值函式、程序和 C# 組件。
 
    ![Data Lake Tools for Visual Studio Code IntelliSense 中繼資料](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   IntelliSense 錯誤標記：Data Lake Tools 會為 U-SQL 和 C# 的編輯錯誤加上底線。 
-   語法醒目提示：Data Lake Tools 會使用不同顏色來區分項目，例如變數、關鍵字、資料類型和函式。 

    ![Data Lake Tools for Visual Studio Code 語法醒目顯示](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>後續步驟
- [針對 VSCode 中的 Azure Data Lake Analytics 使用 Python、R、CSharp 開發 U-SQL](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [使用 Visual Studio Code 來進行 U-SQL 本機執行和本機偵錯](data-lake-tools-for-vscode-local-run-and-debug.md)
- [教學課程：開始使用 Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
- [教學課程：使用 Data Lake Tools for Visual Studio 開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)
- [針對 Azure Data Lake Analytics 作業開發 U-SQL 組件](data-lake-analytics-u-sql-develop-assemblies.md)




