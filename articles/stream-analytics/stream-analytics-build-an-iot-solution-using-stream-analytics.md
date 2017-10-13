---
title: "利用串流分析來建置 IoT 解決方案 | Microsoft Docs"
description: "串流分析收費亭案例 IoT 解決方案的入門教學課程"
keywords: "iot 解決方案, 視窗函數"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: a93693ef7d40025fa96846594a8eb525a50b6885
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>利用串流分析來建置 IoT 解決方案
## <a name="introduction"></a>簡介
在本教學課程中，您將學習如何利用 Azure 串流分析來獲得您資料的即時深入解析。 開發人員可以很容易地結合資料串流，例如點選流、記錄和裝置產生的事件，並且包含歷程記錄或參考資料，以衍生商務深入解析。 Azure 串流分析是由 Microsoft Azure 代管、可完全管理的即時串流計算服務，它提供內建的備援、低延遲及延展性功能，可讓您在幾分鐘之內就立刻上手。

完成本教學課程之後，您將能夠：

* 熟悉 Azure 串流分析入口網站。
* 設定及部署串流工作。
* 使用串流分析查詢語言來表達真實世界的問題，並加以解決。
* 有自信地使用串流分析來為客戶開發串流解決方案。
* 利用監視和記錄的經驗來排解問題。

## <a name="prerequisites"></a>必要條件
您需要下列必要條件來完成本教學課程：

* 最新版的 [Azure PowerShell](/powershell/azure/overview)
* Visual Studio 2017、2015 或免費的 [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
* [Azure 訂用帳戶](https://azure.microsoft.com/pricing/free-trial/)
* 電腦的系統管理員權限
* 從 Microsoft 下載中心下載 [TollApp.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TollApp/TollApp.zip)
* 選擇性： [GitHub](https://aka.ms/azure-stream-analytics-toll-source)

## <a name="scenario-introduction-hello-toll"></a>案例簡介：收費站，你好！
收費站是常見的設施。 您可以在世界各地的許多快速道路、橋樑及隧道中看到它們。 每個收費站都有多個收費亭。 在人工收費亭中，您需要停車來向服務員付費。 在自動收費亭中，位於每個收費亭最上方的感應器會在您通過收費亭時掃描黏貼在您車輛擋風玻璃上的 RFID 卡。 我們可以輕易地把車輛通過這些收費站的情況，想像成其中能執行許多有趣行動的事件串流。

![收費亭中車輛的圖片](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>傳入的資料
本教學課程搭配兩個資料串流來教學。 安裝在收費亭入口和出口處的感應器會產生第一個串流。 第二個串流是擁有車輛登記資料的靜態查詢資料集。

### <a name="entry-data-stream"></a>入口資料流
入口資料流包含車輛進入收費站的相關資訊。

| TollID | EntryTime | LicensePlate | State | Make | 模型 | VehicleType | VehicleWeight | Toll | Tag |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

以下是每個資料欄的簡短說明：

| 資料欄 | 說明 |
| --- | --- |
| TollID |唯一識別收費亭的收費亭識別碼 |
| EntryTime |車輛進入收費亭的日期及時間 (國際標準時間) |
| LicensePlate |車輛的車牌號碼 |
| State |美國的某個洲 |
| Make |車輛的製造商 |
| 模型 |車輛的型號 |
| VehicleType |1 代表載客車或 2 代表商用車 |
| WeightType |車輛的重量，單位為噸；0 代表客車 |
| Toll |通行費，單位為美元 |
| Tag |車輛上可用來自動付費的 e-Tag，空白則代表手動付費 |

### <a name="exit-data-stream"></a>出口資料流
出口資料流包含車輛離開收費站的相關資訊。

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

以下是每個資料欄的簡短說明：

| 資料欄 | 說明 |
| --- | --- |
| TollID |唯一識別收費亭的收費亭識別碼 |
| ExitTime |車輛離開收費亭的日期及時間 (國際標準時間) |
| LicensePlate |車輛的車牌號碼 |

### <a name="commercial-vehicle-registration-data"></a>商用車的登記資料
本教學課程將使用商用車登記資料庫的靜態快照。

| LicensePlate | RegistrationId | 已過期 |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

以下是每個資料欄的簡短說明：

| 資料欄 | 說明 |
| --- | --- |
| LicensePlate |車輛的車牌號碼 |
| RegistrationId |車輛的登記識別碼 |
| 已過期 |車輛的登記狀態：0 代表車輛登記仍有效，1 代表車輛登記已過期 |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>設定 Azure 串流分析的環境
若要完成此教學課程，您需要 Microsoft Azure 訂用帳戶。 Microsoft 能讓您免費試用 Microsoft Azure 服務。

如果您沒有 Azure 帳戶，您可以 [要求獲得免費試用版](http://azure.microsoft.com/pricing/free-trial/)。

> [!NOTE]
> 若要註冊免費試用，您必須擁有可接收簡訊的行動裝置，以及有效的信用卡。
> 
> 

請務必依照這篇文章結尾處＜清理您的 Azure 帳戶＞一節中的步驟來進行，讓您能充分利用您的免費 Azure 點數。

## <a name="provision-azure-resources-required-for-the-tutorial"></a>佈建本教學課程所需的 Azure 資源
本教學課程將需要 2 個事件中樞，來接收 *entry* 及 *exit* 資料流。 Azure SQL Database 會輸出串流分析工作的結果。 Azure 儲存體會儲存車輛登記的相關參考資料。

您可以使用 GitHub 上 TollApp 資料夾中的 Setup.ps1 指令碼來建立所有必要的資源。 為了節省時間，我們建議您執行這個指令碼。 如果您想要深入了解如何在 Azure 入口網站中設定這些資源，請參閱＜在 Azure 入口網站中設定教學課程資源＞附錄。

請下載並儲存支援的 [TollApp](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TollApp/TollApp.zip) 資料夾和檔案。

請*以系統管理員的身分*開啟 [Microsoft Azure PowerShell]。 如果您沒有 Azure PowerShell，請依照 [安裝和設定 Azure PowerShell](/powershell/azure/overview) 中的指示來安裝。

因為 Windows 會自動封鎖 .ps1、.dll 及 .exe 檔案，所以您需要先設定執行原則，才能執行指令碼。 請確認您是「以系統管理員的身分」 執行 Azure PowerShell 視窗。 請執行 **Set-ExecutionPolicy unrestricted**。 在出現提示時按下 Y 鍵 。

!["Set-ExecutionPolicy unrestricted" 正在 Azure PowerShell 視窗中執行的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

請執行 **Get-ExecutionPolicy** 來確認命令已正確執行。

!["Get-ExecutionPolicy" 正在 Azure PowerShell 視窗中執行的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

請前往擁有指令碼和產生器應用程式的目錄。

!["cd .\TollApp\TollApp" 正在 Azure PowerShell 視窗中執行的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

請輸入 **.\\Setup.ps1** 來設定您的 Azure 帳戶、建立及設定所有必要的資源，然後開始產生事件。 指令碼會隨機挑選一個區域來建立您的資源。 若要明確指定區域，您可以傳遞 **-location** 參數，如以下範例所示：

**.\\Setup.ps1 -location “Central US”**

![Azure 登入頁面的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

指令碼將會開啟 Microsoft Azure 的 [登入]  頁面。 請輸入您的帳戶認證。

> [!NOTE]
> 如果您的帳戶能夠存取多個訂用帳戶，系統將會要求您輸入您要用於本教學課程的訂用帳戶名稱。
> 
> 

指令碼可能需要幾分鐘的時間來執行。 完成之後，輸出結果應該看起來像下方的螢幕擷取畫面。

![Azure PowerShell 視窗中指令碼輸出的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

您也會看到類似下方螢幕擷取畫面的另一個視窗。 這個應用程式會把事件傳送至 Azure 事件中樞，您需要它來執行教學課程。 所以在您完成本教學課程之前，請不要停止這個應用程式，或是關閉這個視窗。

![「正在傳送事件中樞資料」的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

現在，您應該能夠在 Azure 入口網站中看到您的資源。 請前往 <https://portal.azure.com>，並使用您的帳戶認證登入。 請注意，目前有些功能會利用傳統入口網站。 將會清楚指出這些步驟。

### <a name="azure-event-hubs"></a>Azure 事件中心
在 Azure 入口網站中，按一下左邊管理窗格底部的 [更多服務]。 在提供的欄位中輸入**事件中樞**，然後按一下 [事件中樞]。 這會啟動新的瀏覽器視窗，以顯示**傳統入口網站**中的 [服務匯流排] 區域。 您可以在此發現 Setup.ps1 指令碼所建立的事件中樞。

![服務匯流排](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image8.png)

按一下開頭為 tolldata 的項目。 按一下 [事件中樞] 索引標籤。您會在這個命名空間中看到 2 個已建立的事件中樞，分別名為 *entry* 和 *exit*。

![傳統入口網站中的事件中樞索引標籤](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image9.png)

### <a name="azure-storage-container"></a>Azure 儲存體容器
1. 回到瀏覽器中已開啟 Azure 入口網站的索引標籤。 請按一下 Azure 入口網站左側的 [儲存體]  ，來查看在教學課程中使用的 Azure 儲存體容器。
   
    ![儲存體功能表項目](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image11.png)
2. 按一下開頭為 tolldata 的項目。 請按一下 [容器] 索引標籤來查看已建立的容器。
   
    ![Azure 入口網站中的 [容器] 索引標籤](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image10.png)
3. 請按一下 [tolldata]  容器來查看已上傳，且擁有車輛登記資料的 JSON 檔案。
   
    ![容器中 registration.json 檔案的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image12.png)

### <a name="azure-sql-database"></a>Azure SQL Database
1. 回到瀏覽器中開啟的第一個索引標籤上的 Azure 入口網站。 按一下 Azure 入口網站左側的 [SQL DATABASE]，以查看將在教學課程中使用的 SQL 資料庫，然後按一下 [tolldatadb]。
   
    ![所建立 SQL Database 的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)
2. 請複製伺服器名稱，但不用複製連接埠號碼 (例如 [伺服器名稱].database.windows.net)。
    ![已建立 SQL 資料庫 db 的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15a.png)

## <a name="connect-to-the-database-from-visual-studio"></a>從 Visual Studio 連線至資料庫
使用 Visual Studio 來存取輸出資料庫中的查詢結果。

如何從 Visual Studio 連線到 SQL Database (目的地)：

1. 開啟 Visual Studio，然後按一下工具 > 連接到資料庫。
2. 如果畫面出現提示，請按一下 [Microsoft SQL Server]  來做為資料來源。
   
    ![[變更資料來源] 對話方塊](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)
3. 在 [伺服器名稱] 欄位中，貼上您在上一節從 Azure 入口網站複製的名稱 (也就是 [伺服器名稱].database.windows.net)。
4. 按一下 [使用 SQL Server 驗證] 。
5. 在 [使用者名稱] 欄位中輸入 **tolladmin**，以及在 [密碼] 欄位中輸入 **123toll!** 。
6. 按一下 [選取或輸入資料庫名稱]，然後選取 [TollDataDB] 做為資料庫。
   
    ![[新增連線] 對話方塊](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)
7. 按一下 [確定] 。
8. 開啟 [伺服器總管]。
   
    ![Server Explorer](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)
9. 在 TollDataDB 資料庫中看到 4 個資料表。
   
    ![TollDataDB 資料庫中的資料表](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator-tollapp-sample-project"></a>事件產生器：TollApp 範例專案
PowerShell 指令碼會自動利用 TollApp 範例應用程式來開始傳送事件， 因此您不需要執行任何額外的步驟。

然而，如果您對實作的細節有興趣，可以在 GitHub 的 [samples/TollApp](https://aka.ms/azure-stream-analytics-toll-source)中找到 TollApp 應用程式的原始程式碼。

![Visual Studio 中所顯示範例程式碼的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>建立串流分析工作
1. 在 Azure 入口網站中，按一下頁面左上角的綠色加號，以建立新的串流分析作業。 選取 智慧 + 分析，然後按一下串流分析作業。
   
    ![New button](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)
2. 提供作業名稱，驗證訂用帳戶正確，然後在與事件中樞儲存體相同的區域中，建立新的資源群組 (指令碼的預設值為美國中南部)。
3. 按一下 [釘選到儀表板]，然後按一下頁面底部的 [建立]。
   
    ![[建立串流分析工作] 選項](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>定義輸入來源
1. 將會建立作業，並開啟作業頁面。 或者，您可以在入口網站儀表板上按一下已建立的分析作業。

2. 開啟 [輸入]  索引標籤以定義來源資料。
   
    ![[輸入] 索引標籤](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.png)
3. 按一下 [加入輸入] 。
   
    ![[加入輸入] 選項](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)
4. 輸入 **EntryStream** 做為 [輸入別名]。
5. [來源類型] 是 [資料流]
6. [來源] 是 [事件中樞]。
7. [服務匯流排命名空間] 應該是下拉式清單中的 TollData。
8. [事件中樞名稱] 應該設定為**進入**。
9. [事件中樞原則名稱]* *是* **RootManageSharedAccessKey**  (預設值)。
10. 選取 [JSON] 做為 [事件序列化格式]，並選取 [UTF8] 做為 [編碼] 格式。
   
    您的設定將看起來會像是：
   
    ![[事件中樞] 設定](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

10. 按一下頁面底部的 [建立] 來完成精靈。
    
    現在您已經建立了入口資料流，您將可以依照相同步驟建立出口資料流。 請務必輸入如下面螢幕擷取畫面所示的值。
    
    ![出口資料流的設定](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)
    
    您已經定義了兩個輸入串流：
    
    ![Azure 入口網站中定義的輸入資料流](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.png)
    
    接下來，您將為包含車輛登記資料的 Blob 檔案新增參考資料輸入。
11. 按一下 [新增]，然後依照串流輸入的相同程序，但選取 [參考資料] 而不是 [資料流]，[輸入別名] 是 **註冊**。

12. 按一下開頭為 **tolldata** 的儲存體帳戶。 容器名稱應該是 **tolldata**，[路徑格式] 應該是 **registration.json**。 這個檔案名稱會區分大小寫，且應該是**小寫**。
    
    ![[部落格儲存體] 設定](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)
13. 按一下 [建立] 完成精靈。

現在，所有輸入都已經定義了。

## <a name="define-output"></a>定義輸出
1. 在串流分析作業概觀窗格中，選取 [輸出]。
   
    ![[輸出] 索引標籤和 [加入輸出] 選項](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.png)
2. 按一下 [新增] 。
3. 將 [輸出別名] 設定為「輸出」，然後將 [接受] 設定為 [SQL 資料庫]。
3. 請選取在文章的＜從 Visual Studio 連線至資料庫＞一節中使用的伺服器名稱。 資料庫名稱應該是 **TollDataDB**。
4. 在 [使用者名稱] 欄位中輸入 **tolladmin**，在 [密碼] 欄位中輸入 **123toll!** ，在 [資料表] 欄位中輸入 **TollDataRefJoin**。
   
    ![SQL Database 設定](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.png)
5. 按一下 [建立] 。

## <a name="azure-stream-analytics-query"></a>Azure 串流分析查詢
[查詢]  索引標籤包含會轉換傳入資料的 SQL 查詢。

![已新增到 [查詢] 索引標籤的查詢](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.png)

本教學課程會嘗試回答和幾個通行費資料相關的業務問題，並建構可在 Azure 串流分析中使用的串流分析查詢來提供相關的答案。

在您開始第一個串流分析工作之前，讓我們先來探索幾個案例和查詢語法。

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Azure 串流分析查詢語言簡介
- - -
假設您需要計算進入某個收費亭的車輛數目。 由於這是連續的事件串流，因此您必須定義一個「時段」。 我們來將問題修改為「每三分鐘有多少車輛進入收費亭」。 這通常稱為輪轉計數。

我們來看看回答這個問題的 Azure 串流分析查詢：

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

如您所見，Azure 串流分析會使用類似 SQL 的查詢語言，並新增幾個擴充功能來指定和時間有關的查詢層面。

如需詳細資料，請參閱 MSDN 中的[時間管理](https://msdn.microsoft.com/library/azure/mt582045.aspx)，以及查詢中所用的[時段](https://msdn.microsoft.com/library/azure/dn835019.aspx)建構。

## <a name="testing-azure-stream-analytics-queries"></a>測試 Azure 串流分析查詢
您已經將第一個 Azure 串流分析查詢編寫完成，現在該利用位於下列路徑中您 TollApp 資料夾中的範例資料檔案來測試查詢了：

**..\\TollApp\\TollApp\\Data**

這個資料夾包含下列檔案：

* Entry.json
* Exit.json
* registration.json

## <a name="question-1-number-of-vehicles-entering-a-toll-booth"></a>問題 1：進入某個收費亭的車輛數目
1. 開啟 Azure 入口網站，然後移至您建立的 Azure 串流分析工作。 按一下 [查詢]  索引標籤，然後貼上在上一節中複製的查詢。

2. 若要根據範例資料來驗證此查詢，請按一下 [...] 符號，然後選取 [從檔案上傳範例資料]，將資料上傳至 EntryStream 輸入。

    ![Entry.json 檔案的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)
3. 在出現的窗格中，選取本機電腦上的檔案 (Entry.json)，然後按一下 [確定]。 [測試] 圖示現在亮起，可以點選。
   
    ![Entry.json 檔案的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.png)
3. 請驗證查詢的輸出與下列預期的結果相同：
   
    ![測試結果](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.png)

## <a name="question-2-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>問題 2：回報每輛車通過收費亭的總時數
車輛通過收費亭所需的平均時間有助於評估程序效率和客戶經驗。

若要尋找總時間，您需要聯結 EntryTime 串流和 ExitTime 串流。 您將會聯結 TollId 和 LicencePlate 資料欄的串流。 **JOIN** 運算子需要您指定彈性時間，來說明已聯結事件之間可接受的時間差。 您將使用 **DATEDIFF** 函式來指定事件之間的時間差不能超過 15 分鐘。 我們也會將 **DATEDIFF** 函式套用到入口和出口時間，以便計算車輛經過收費亭的實際時間。 請注意在 **SELECT** 陳述式中 (而非在 **JOIN** 中) 使用 **DATEDIFF** 時，其使用方式的差異。

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. 若要測試此查詢，請在作業的 [查詢] 上更新查詢。 新增 **ExitStream** 的測試檔，如同上面輸入的 **EntryStream** 一樣。
   
2. 按一下 [ **測試**]。

3. 選取核取方塊來測試查詢並檢視輸出：
   
    ![測試輸出](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3-report-all-commercial-vehicles-with-expired-registration"></a>問題 3：回報所有車輛登記已過期的商用車
Azure 串流分析可使用靜態的資料快照，來與時間資料流聯結。 若要示範此功能，請使用下面的範例問題。

如果商用車已經向收費公司登記，就可以直接通過收費亭，不用停車接受檢查。 您將使用商用車登記查詢資料表，來辨識所有車輛登記已過期的商用車。

```
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

若要使用參考資料測試查詢，您需要定義參考資料的輸入來源，而且您已經這樣做了。

若要測試此查詢，請將查詢貼到 [查詢] 索引標籤，按一下 [測試]，並指定兩個輸入來源和註冊範例資料，然後按一下 [測試]。  
   
![測試輸出](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

## <a name="start-the-stream-analytics-job"></a>啟動串流分析工作
現在可以完成設定並啟動工作。 儲存問題 3 的查詢，這會產生符合我們 **TollDataRefJoin** 輸出資料表結構描述的輸出。

請前往工作 [儀表板]，然後按一下 [啟動]。

![工作儀表板中 [開始] 按鈕的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.png)

在開啟的對話方塊上，把 [開始輸出] 時間變更為 [自訂時間]。 將小時變更為目前時間的前一個小時。 此變更可確保在您於教學課程開始時開始產生事件之後，來自事件中樞的所有事件都會被處理。 現在按一下 [啟動] 按鈕以啟動作業。

![自訂時間的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

啟動工作可能需要幾分鐘的時間， 您可以在串流分析最上層的頁面看到狀態。

![工作狀態的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.png)

## <a name="check-results-in-visual-studio"></a>在 Visual Studio 中查看結果
1. 開啟 Visual Studio 伺服器總管，然後用滑鼠右鍵按一下 [TollDataRefJoin]  資料表。
2. 按一下 [顯示資料表資料]  可查看您工作的輸出。
   
    ![伺服器總管中 [顯示資料表資料] 的選取項目](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>相應放大 Azure 串流分析工作
Azure 串流分析是設計成能進行彈性調整，以便於處理大量資料。 Azure 串流分析查詢可以使用 **PARTITION BY** 子句，來告訴系統此步驟將會相應放大。**PartitionId** 是系統新增的特殊資料欄，且它與輸入 (事件中樞) 的分割識別碼相符。

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. 停止目前的作業，在 [查詢] 索引標籤中更新查詢，然後開啟作業儀表板中的 [設定] 齒輪。 按一下 [調整] 。
   
     會定義工作能夠接收之計算能力的量。
2. 將下拉式清單從 1 變更為 6。
   
    ![選取 6 個串流處理單位的螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.png)
3. 前往 [輸出] 索引標籤，然後把 SQL 資料表名稱變更為 **TollDataTumblingCountPartitioned**。

如果您現在啟動工作，Azure 串流分析將能夠把工作分散到更多的計算資源上，並達到更高的輸送量。 請注意，TollApp 應用程式也會同時傳送已依照 TollId 來分割的事件。

## <a name="monitor"></a>監視
[監視] 區域包含執行中作業的相關統計資料。 第一次設定需要使用相同區域中的儲存體帳戶 (如同本文的其餘部分，也就是收費)。   

![監視螢幕擷取畫面](media/stream-analytics-build-an-iot-solution-using-stream-analytics/monitoring.png)

您也可以從作業儀表板的 [設定] 區域存取 [活動記錄]。


## <a name="conclusion"></a>結論
本教學課程向您介紹了 Azure 串流分析服務。 課程中示範了如何為串流分析工作設定輸入和輸出。 課程中還利用付費資料案例，來解釋在資料空間會不斷變化時所引發的常見問題類型，以及如何在 Azure 串流分析中利用類似 SQL 的簡單查詢來解決這些問題。 課程說明了要用來處理時間資料的 SQL 延伸模組建構。 課程示範了如何聯結不同的資料流，以及如何利用靜態參考資料來豐富資料流的內容，以及如何相應放大查詢來達到更高的輸送量。

雖然本教學課程提供了詳盡的簡介，但也絕對不算是完整的說明。 您可以使用 SAQL 語言在 [一般串流分析使用模式的查詢範例](stream-analytics-stream-analytics-query-patterns.md)中找到更多查詢模式。
若要深入了解 Azure 串流分析，請參閱 [線上文件](https://azure.microsoft.com/documentation/services/stream-analytics/) 。

## <a name="clean-up-your-azure-account"></a>清理您的 Azure 帳戶
1. 從 Azure 入口網站停止串流分析工作。
   
    Setup.ps1 指令碼會建立 2 個事件中樞，以及 1 個 SQL Database。 下列指示將協助您在本教學課程結束時清理資源。
2. 請在 PowerShell 視窗中輸入 **.\\Cleanup.ps1**，來啟動會刪除本教學課程所使用資源的指令碼。
   
   > [!NOTE]
   > 資源是依照名稱來識別。 請確認您在確認刪除之前，已仔細檢閱每個項目。
   > 
   > 


