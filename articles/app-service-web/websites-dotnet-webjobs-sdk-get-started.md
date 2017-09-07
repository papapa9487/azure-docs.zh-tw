---
title: "在 Azure App Service 中建立 .NET WebJob | Microsoft Docs"
description: "使用 ASP.NET MVC 和 Azure 建立多層式應用程式。 前端是在 Azure App Service 的 Web 應用程式中執行，後端則是以 WebJob 形式執行。 該應用程式使用 Entity Framework、SQL Database 及 Azure 儲存體佇列和 Blob。"
services: app-service
documentationcenter: .net
author: tdykstra
manager: erikre
editor: mollybos
ms.assetid: 99cb9917-483a-45f8-a98d-07d19c68c753
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 6/14/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: a20b13058caecff75af14957468f20e63a3325c9
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="create-a-net-webjob-in-azure-app-service"></a>在 Azure App Service 中建立 .NET WebJob
本教學指導示範如何為使用 [WebJobs SDK](websites-dotnet-webjobs-sdk.md)的簡單多層次 ASP.NET MVC 5 應用程式撰寫程式碼。

[!INCLUDE [app-service-web-webjobs-corenote](../../includes/app-service-web-webjobs-corenote.md)]

[WebJobs SDK](websites-webjobs-resources.md) 的目的是為了簡化您對 WebJob 可執行的一般工作 (例如，映像處理、佇列處理、RSS 彙總、檔案維護和傳送電子郵件) 所撰寫的程式碼。 WebJobs SDK 具有內建功能，用於處理 Azure 儲存體和服務匯流排、工作排程和處理錯誤，以及許多其他常見案例。 此外，它的設計具有擴充性，而且有 [擴充功能的開放原始碼儲存機制](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)。

此範例應用程式是廣告看板。 使用者可以上傳廣告的影像，然後後端程序會將影像轉換成縮圖。 廣告清單頁面會顯示縮圖，而廣告詳細資料頁面則會顯示完整大小的影像。 以下為螢幕擷取畫面：

![Ad list](./media/websites-dotnet-webjobs-sdk-get-started/list.png)

此範例應用程式能夠與 [Azure 佇列](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) 和 [Azure Blob](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage) 搭配使用。 本教學課程顯示如何將應用程式部署至 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 和 [Azure SQL Database](http://msdn.microsoft.com/library/azure/ee336279)。

## <a id="prerequisites"></a>必要條件
本教學課程假設您知道如何在 Visual Studio 中使用 [ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started) 專案。

本教學課程一開始是針對 Visual Studio 2013 所撰寫，但可以與更新版本的 Visual Studio 搭配使用。 如果您要使用 Visual Studio 2015 或 2017，請注意，在本機執行應用程式之前，您必須將 Web.config 和 App.config 檔案中 SQL Server LocalDB 連接字串的 `Data Source` 部分，從 `Data Source=(localdb)\v11.0` 變更為 `Data Source=(LocalDb)\MSSQLLocalDB`。

> [!NOTE]
> <a name="note"></a>您必須具備 Azure 帳戶，才能完成此教學課程：
>
> * 您可以[免費申請 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)：您將取得可試用付費 Azure 服務的額度，即使在額度用完後，您仍可保留帳戶，並使用免費的 Azure 服務，例如「網站」。 除非您明確變更您的設定且同意付費，否則我們將不會從您的信用卡收取任何費用。
> * 您可以[啟用 Visual Studio 訂閱者的每月 Azure 額度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)：您的訂用帳戶每個月都會提供額度，供您用在付費 Azure 服務。
>
> 如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](https://azure.microsoft.com/try/app-service/)，即可在 App Service 中立即建立短期入門 Web 應用程式。 不需要信用卡；無需承諾。
>
>

## <a id="learn"></a>您將學到什麼
本教學課程說明如何執行下列工作：

* 安裝 Azure SDK 好讓電腦適合用於進行 Azure 開發 (僅適用於 Visual Studio 2013 和 2015 使用者)。
* 建立會在您部署相關的 Web 專案時，自動部署成為 Azure WebJob 的主控台應用程式專案。
* 在開發電腦上本機測試 WebJobs SDK 後端。
* 將具有 WebJob 後端的應用程式發佈至 App Service 中的 Web 應用程式。
* 上傳檔案，並將檔案儲存在 Azure Blob 服務。
* 運用 Azure WebJobs SDK 來使用 Azure 儲存體佇列和 Blob。

## <a id="contosoads"></a>應用程式架構
此範例應用程式會使用 [以佇列為中心的工作模式](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) ，將建立縮圖這個需要大量 CPU 的工作轉變為後端程序。

本應用程式會將廣告儲存在 SQL 資料庫中，使用 Entity Framework Code First 來建立表格和存取資料。 針對每個廣告，資料庫會儲存兩個 URL：一個用於完整大小的影像，而另一個用於縮圖。

![Ad table](./media/websites-dotnet-webjobs-sdk-get-started/adtable.png)

當使用者上傳影像時，Web 應用程式會將影像儲存在 [Azure Blob](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage)，並將廣告資訊 (內含指向該 Blob 的 URL) 儲存在資料庫。 同時會將訊息寫入 Azure 佇列。 在以 Azure WebJob 形式執行的後端處理中，WebJobs SDK 會輪詢佇列以尋找新訊息。 出現新訊息時，WebJob 便會建立該影像的縮圖，並更新該廣告的縮圖 URL 資料庫欄位。 以下圖表顯示應用程式的這些部分的互動情況：

![Contoso Ads architecture](./media/websites-dotnet-webjobs-sdk-get-started/apparchitecture.png)

[!INCLUDE [install-sdk](../../includes/install-sdk-2017-2015-2013.md)]  
本教學課程的指示適用於 Azure SDK for.NET 2.7.1 或更新版本。

## <a id="storage"></a>建立 Azure 儲存體帳戶
Azure 儲存體帳戶可提供在雲端中儲存佇列和 Blob 資料的資源。 WebJobs SDK 也會使用該帳戶來儲存儀表板的記錄資料。

在實際應用程式中，您通常會為應用程式資料與記錄資料建立不同的帳戶，並為測試資料與生產資料建立不同的帳戶。 在本教學課程中，您只會使用一個帳戶。

1. 在 Visual Studio 中開啟 [伺服器總管]  視窗。
2. 以滑鼠右鍵按一下 **Azure** 節點，然後按一下 [連線至 Microsoft Azure 訂用帳戶...]。
   
   ![連接到 Azure](./media/websites-dotnet-webjobs-sdk-get-started/connaz.png)

3. 使用您的 Azure 認證登入。
4. 以滑鼠右鍵按一下 Azure 節點下的 [儲存體]，然後按一下 [建立儲存體帳戶]。
   
   ![建立儲存體帳戶](./media/websites-dotnet-webjobs-sdk-get-started/createstor.png)
   
5. 在 [建立儲存體帳戶]  對話方塊中，輸入儲存體帳戶的名稱。

    這個名稱必須是唯一的 (其他 Azure 儲存體帳戶不可以有相同的名稱)。 如果您輸入的名稱已在使用中，則可以變更此名稱。

    存取儲存體帳戶的 URL 會是 *{name}*.core.windows.net。
6. 將 [區域或同質群組]  下拉式清單設為離您最近的區域。

    此設定會指定哪個 Azure 資料中心將會主控您的儲存體帳戶。 對於本教學課程，您的選擇將不會造成顯著的差異。 不過，對於生產 Web 應用程式，您會希望 Web 伺服器和儲存體帳戶都位於相同區域，以減少延遲和資料輸出費用。 您稍後將建立的 Web 應用程式資料中心應該盡可能接近可存取您 Web 應用程式的瀏覽器，以便將延遲降至最低。
7. 將 [複寫] 下拉式清單設為 [本機備援]。

    對儲存體帳戶啟用地理區域複寫時，儲存內容會複寫至次要資料中心，以便能在主要位置發生嚴重災難時容錯移轉至該位置。 地理區域複寫會引發額外成本。 對於測試和開發帳戶，您通常不會想要付費使用地理區域複寫功能。 如需詳細資訊，請參閱 [建立、管理或刪除儲存體帳戶](../storage/common/storage-create-storage-account.md)。
8. 按一下 [建立] 。

    ![New storage account](./media/websites-dotnet-webjobs-sdk-get-started/newstorage.png)

## <a id="download"></a>下載應用程式
1. 下載並解壓縮 [已完成的方案](http://code.msdn.microsoft.com/Simple-Azure-Website-with-b4391eeb)(英文)。
2. 啟動 Visual Studio。
3. 從 [檔案] 功能表中，依序選擇 [開啟] > [專案/方案]，並導覽至您下載方案的位置，然後開啟方案檔案。
4. 按 CTRL+SHIFT+B 建置解決方案。

    根據預設，Visual Studio 會自動還原未包含在 *.zip* 檔案中的 NuGet 封裝內容。 如果封裝未還原，請移至 [管理方案的 NuGet 套件] 對話方塊，然後按一下右上方的 [還原] 按鈕來手動安裝。
5. 在 [方案總管] 中，確定已選取 **ContosoAdsWeb** 作為啟始專案。

## <a id="configurestorage"></a>設定應用程式以使用儲存體帳戶
1. 開啟 ContosoAdsWeb 專案中的應用程式 *Web.config* 檔案。

    此檔案包含 SQL 連接字串和用來使用 Blob 和佇列的 Azure 儲存體連接字串。

    SQL 連接字串會指向 [SQL Server Express LocalDB](http://msdn.microsoft.com/library/hh510202.aspx) 資料庫。

    儲存體連接字串是具有儲存體帳戶名稱和存取金鑰預留位置的範例。 您將會使用具有您儲存體帳戶名稱和金鑰的連接字串來進行取代。  

    ```
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" />
        <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
    </connectionStrings>
    ```
    儲存體連接字串的名稱是 AzureWebJobsStorage，因為這是 WebJobs SDK 預設使用的名稱。 這裡會使用相同的名稱，因此在 Azure 環境中您只需要設定一個連接字串值。
2. 在 [伺服器總管] 中，在 [儲存體] 節點下方的儲存體帳戶上按一下滑鼠右鍵，然後按一下 [屬性]。

    ![按一下 [儲存體帳戶] 屬性](./media/websites-dotnet-webjobs-sdk-get-started/storppty.png)
3. 在 [屬性] 視窗中，按一下 [儲存體帳戶金鑰]，然後按一下省略符號。

    ![儲存體帳戶金鑰](./media/websites-dotnet-webjobs-sdk-get-started/stor-account-keys.png)
4. 複製 [連接字串] 。

    ![[儲存體帳戶金鑰] 對話方塊](./media/websites-dotnet-webjobs-sdk-get-started/cpak.png)
5. 使用您剛才複製的連接字串來取代 *Web.config* 檔案中的儲存體連接字串。 在貼上之前請確定您選取引號內的所有項目，但不包括引號。
6. 開啟 ContosoAdsWebJob 專案中的 *App.config* 檔案。

    此檔案有兩個儲存體連接字串，一個供應用程式使用，另一個供記錄使用。 您可以對應用程式資料和記錄使用不同的儲存體帳戶，以及您可以 [對資料使用多個儲存體帳戶](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs)。 在本教學課程中，您將使用單一儲存體帳戶。 連接字串具有儲存體帳戶金鑰的預留位置。

    ```
    <configuration>
        <connectionStrings>
            <add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;"/>
    </connectionStrings>
        <startup>
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    </configuration>

    ```

    依預設，WebJobs SDK 會尋找名為 AzureWebJobsStorage 和 AzureWebJobsDashboard 的連接字串。 另一種方式是，您可以[任意儲存您要的連接字串，並將它明確傳遞至 `JobHost` 物件](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#config)。
7. 使用您先前複製的連接字串來取代這兩個儲存體連接字串。
8. 儲存您的變更。

## <a id="run"></a>在本機執行應用程式
1. 若要啟動應用程式的 Web 前端，請按 CTRL+F5。

    預設瀏覽器便會開啟到首頁。 (系統即會執行 Web 專案，這是因為您已將它設定為啟始專案。)

    ![Contoso Ads home page](./media/websites-dotnet-webjobs-sdk-get-started/home.png)
2. 若要啟動應用程式的 WebJob 後端，以滑鼠右鍵按一下 [方案總管] 中的 ContosoAdsWebJob 專案，然後依序按一下 [偵錯]  >  [開始新執行個體]。

    主控台應用程式視窗隨即開啟，並顯示指出 WebJobs SDK JobHost 物件已開始執行的記錄訊息。

    ![Console application window showing that the backend is running](./media/websites-dotnet-webjobs-sdk-get-started/backendrunning.png)
3. 在您的瀏覽器中按一下 [建立廣告]。
4. 輸入部分測試資料，並選取要上傳的影像，然後按一下 [建立]。

    ![Create page](./media/websites-dotnet-webjobs-sdk-get-started/create.png)

    應用程式會進入索引頁面，但不會顯示新廣告的縮圖，因為處理尚未發生。

    同時，不久之後，主控台應用程式視窗中的記錄訊息會顯示已收到佇列訊息並已開始處理。

    ![Console application window showing that a queue message has been processed](./media/websites-dotnet-webjobs-sdk-get-started/backendlogs.png)
5. 在看到主控台應用程式視窗中的記錄訊息後，您可以重新整理索引頁面以查看縮圖。

    ![索引頁面](./media/websites-dotnet-webjobs-sdk-get-started/list.png)
6. 按一下廣告的 [詳細資料]  以查看完整大小的影像。

    ![Details page](./media/websites-dotnet-webjobs-sdk-get-started/details.png)

您一直在本機電腦上執行應用程式，並使用位於電腦上的 SQL Server 資料庫，但它會使用雲端中的佇列和 Blob。 在下一節中，您將會使用雲端資料庫以及雲端 Blob 和佇列，在雲端中執行應用程式。  

## <a id="runincloud"></a>在雲端中執行應用程式
您將執行下列步驟，在雲端中執行應用程式：

* 部署至 Web Apps。 Visual Studio 會在 App Service 和 SQL 資料庫執行個體中自動建立新的 Web 應用程式。
* 設定 Web 應用程式來使用您的 Azure SQL Database 和儲存體帳戶。

在建立一些廣告並在雲端中執行後，您將檢視 WebJobs SDK 儀表板，以查看儀表板所提供的豐富監視功能。

### <a name="deploy-to-web-apps"></a>部署至 Web Apps

1. 關閉瀏覽器和主控台應用程式視窗。
2. 完成[發佈至含有 SQL Database 的 Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-tutorial-dotnet-sqldatabase#publish-to-azure-with-sql-database) 一節中的步驟。
3. 在您完成部署步驟之後，請繼續進行本文中的其餘工作。

### <a name="configure-the-web-app-to-use-your-azure-sql-database-and-storage-account"></a>設定 Web 應用程式來使用您的 Azure SQL Database 和儲存體帳戶
[避免將敏感資訊 (例如連接字串) 放在儲存於原始程式碼儲存機制的檔案](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets)(英文) 會是安全性最佳作法。 Azure 提供實作上述最佳做法的方式：您可以在 Azure 環境中設定連接字串和其他設定值，當應用程式在 Azure 中執行時，ASP.NET 組態 API 便會自動挑選這些值。 您可以使用 [伺服器總管] 、Azure 入口網站、Windows PowerShell，或跨平台的命令列介面，在 Azure 中設定這些值。 如需詳細資訊，請參閱 [應用程式字串與連接字串的運作方式](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)。

在本節中，您會使用 [伺服器總管] 在 Azure 中設定連接字串值。

1. 在 [伺服器總管] 中，於 [Azure] > [App Service] > {您的資源群組} 下的 Web 應用程式上按一下滑鼠右鍵，然後按一下 [檢視設定]。

    隨即會在 [設定] 索引標籤中開啟 [Azure Web 應用程式] 視窗。
2. 將 DefaultConnection 連接字串的名稱變更為您在[發佈至含有 SQL Database 的 Azure](https://docs.microsoft.com/azure/app-service-web/app-service-web-tutorial-dotnet-sqldatabase#publish-to-azure-with-sql-database) 文章中設定 SQL Database 時所選擇的名稱。

    Azure 便會在您建立 Web 應用程式和相關資料庫時自動建立此連接字串，因此它已包含正確的連接字串值。 您只是單純將名稱變更為程式碼要尋找的名稱。
3. 新增兩個新的連接字串，將他們命名為 AzureWebJobsStorage 和 AzureWebJobsDashboard。 將資料庫類型設定為 [自訂]，並將連接字串值設定為您稍早在 *Web.config* 和 *App.config* 檔案中所用的相同值 (請確定您包含整個連接字串，而不只是存取金鑰而已，並且不要包含引號)。

    WebJobs SDK 會使用這些連接字串，一個供應用程式資料使用，一個供記錄使用。 如稍早所看到的，供應用程式資料使用的連接字串也會提供給 Web 前端程式碼使用。
4. 按一下 [儲存] 。

    ![Azure 入口網站中的連接字串](./media/websites-dotnet-webjobs-sdk-get-started/azconnstr.png)
5. 在 [伺服器總管] 中，於 Web 應用程式上按一下滑鼠右鍵，然後按一下 [停止]。
6. 當 Web 應用程式停止之後，再次以滑鼠右鍵按一下該 Web 應用程式，然後按一下 [啟動] 。

   發行時，WebJob 便會自動啟動，但在您進行組態變更時便會停止。 若要重新啟動它，您可以重新啟動 Web 應用程式，或在 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=529715)中重新啟動 WebJob。 通常建議您在進行設定變更之後重新啟動 Web 應用程式。
7. 重新整理網址列中具有 Web 應用程式 URL 的瀏覽器視窗。

    首頁便會隨即出現。
8. 建立廣告，就像您在[本機執行應用程式](https://docs.microsoft.com/azure/app-service-web/websites-dotnet-webjobs-sdk-get-started#a-idrunarun-the-application-locally)時所做的一樣。

   一開始，顯示的索引頁面沒有縮圖。
9. 在幾秒後重新整理頁面，縮圖便會隨即出現。

   如果未出現縮圖，您可能必須等候一分鐘左右，讓 WebJob 重新啟動。 在一段時間後，如果您重新整理頁面時仍未看到縮圖，則 WebJob 可能未自動啟動。 在該情況下，請移至 [Azure 入口網站](https://portal.azure.com/)中的 [應用程式服務] 刀鋒視窗，並找到您的 Web 應用程式，然後按一下 [啟動]。

### <a name="view-the-webjobs-sdk-dashboard"></a>檢視 WebJobs SDK 儀表板
1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取 [應用程式服務] 刀鋒視窗，並找到您的 Web 應用程式，然後選取 [WebJob]。
3. 選取 [記錄] 索引標籤。

    ![記錄索引標籤](./media/websites-dotnet-webjobs-sdk-get-started/log-tab.png)

    新的瀏覽器索引標籤即會開啟到 WebJobs SDK 儀表板。 儀表板會顯示 WebJob 正在執行中，並顯示在 WebJobs SDK 所觸發之程式碼中的函數清單。
4. 按一下其中一個函式，以查看其執行的詳細資料

    ![WebJobs SDK dashboard](./media/websites-dotnet-webjobs-sdk-get-started/wjdashboardhome.png)

    ![WebJobs SDK dashboard](./media/websites-dotnet-webjobs-sdk-get-started/wjfunctiondetails.png)

    此頁面上的 [轉送函數]  按鈕會造成 WebJobs SDK 架構再次呼叫此函數，這可提供您一個機會來變更首先傳送至函數的資料。

> [!NOTE]
> 當您完成測試時，請考慮刪除 Web 應用程式、儲存體帳戶和您的 SQL Database 執行個體。 Web 應用程式是免費提供的，但 SQL 儲存體帳戶和資料庫執行個體則會累算費用 (儘管是小規模，還是會收取基本費用)。 另外，如果您持續執行 Web 應用程式，那麼，找到您 URL 的任何人都可以建立和檢視廣告。 
>
>

### <a name="delete-your-web-app"></a>刪除 Web 應用程式
在入口網站中，移至 [應用程式服務] 刀鋒視窗，並找到和選取您的 Web 應用程式，然後按一下 [刪除]。 如果您只想暫時避免他人存取 Web 應用程式，請改為按一下 [停止]  。 在此情況下，將會繼續累算 SQL Database 和儲存體帳戶的費用。

### <a name="delete-your-storage-account"></a>刪除儲存體帳戶
若要刪除儲存體帳戶，請參閱[刪除儲存體帳戶](https://docs.microsoft.com/azure/storage/storage-create-storage-account#delete-a-storage-account)。 

### <a name="delete-your-database"></a>刪除資料庫
若要刪除您的 SQL Database，請參閱 [Azure SQL Database REST API](https://docs.microsoft.com/rest/api/sql/) 文件。

## <a id="create"></a>從頭開始建立應用程式
您將在本節執行下列工作：

* 使用 Web 專案建立 Visual Studio 方案。
* 在前端與後端之間共用的資料存取層中，新增類別庫專案。
* 在啟用 WebJobs 部署的後端中新增主控台應用程式專案。
* 新增 NuGet 封裝。
* 設定專案參考。
* 從您在前一節的教學課程中使用的所下載應用程式，複製其應用程式程式碼和組態檔。
* 針對使用 Azure Blob 和佇列及 WebJobs SDK 該部分的程式碼進行審查。

### <a name="create-a-visual-studio-solution-with-a-web-project-and-class-library-project"></a>使用 Web 專案和類別庫專案建立 Visual Studio 方案
1. 在 Visual Studio 中，選擇 [檔案] > [新增] > [專案]。
2. 在 [新增專案] 對話方塊中，依序選擇 [Visual C#] > [Web] > [ASP.NET Web 應用程式 (.NET Framework)]。
3. 將專案命名為 ContosoAdsWeb，將方案命名為 ContosoAdsWebJobsSDK (如果您要將方案放入與所下載方案相同的資料夾中，則請變更方案名稱)，然後按一下 [確定] 。

    ![New Project](./media/websites-dotnet-webjobs-sdk-get-started/newproject.png)
4. 在 [新增 ASP.NET Web 應用程式] 對話方塊中，選擇 MVC 範本，然後選取 [變更驗證]。

    ![變更驗證](./media/websites-dotnet-webjobs-sdk-get-started/chgauth.png)
5. 在 [變更驗證] 對話方塊中，選擇 [不需要驗證]，然後按一下 [確定]。

    ![不需要驗證](./media/websites-dotnet-webjobs-sdk-get-started/noauth.png)
6. 在 [新增 ASP.NET Web 應用程式] 對話方塊中，按一下 [確定]。

    Visual Studio 便會建立方案和 Web 專案。
7. 在 [方案總管] 中，以滑鼠右鍵按一下方案 (不是專案)，並依序選擇 [新增]  >  [新專案]。
8. 在 [新增專案] 對話方塊中，選擇 [Visual C#] > [Windows 傳統桌面] > [類別庫 (.NET Framework)] 範本。  
9. 將專案命名為 *ContosoAdsCommon*，然後按一下 [確定]。

    此專案將包含由前端與後端使用的 Entity Framework 內容和資料模型。 作為替代方式，您可以在 Web 專案中定義 EF 相關的類別，並從 WebJob 專案參考該專案。 但之後您的 WebJob 專案會有不需要的 Web 組件參考。

### <a name="add-a-console-application-project-that-has-webjobs-deployment-enabled"></a>新增已啟用 WebJobs 部署的主控台應用程式專案
1. 以滑鼠右鍵按一下 Web 專案 (不是方案或類別庫專案)，然後依序按一下 [新增]  > 的簡單多層次 ASP.NET MVC 5 應用程式撰寫程式碼。

    ![New Azure WebJob Project menu selection](./media/websites-dotnet-webjobs-sdk-get-started/newawjp.png)
2. 在 [新增 Azure WebJob] 對話方塊中，在 [專案名稱] 和 [WebJob 名稱] 中輸入 ContosoAdsWebJob。 保留 [WebJob 執行模式] 的 [連續執行] 設定。
3. 按一下 [確定] 。

   Visual Studio 便會建立設定為每次部署 Web 專案時，便會部署成為 WebJob 的主控台應用程式。 若要執行此動作，應用程式在建立專案後執行了下列工作：

   * 在 WebJob 專案的 Properties 資料夾中新增了 *webjob-publish-settings.json* 檔案。
   * 在 Web 專案的 Properties 資料夾中新增了 *webjobs-list.json* 檔案。
   * 在 WebJob 專案中安裝了 Microsoft.Web.WebJobs.Publish NuGet 封裝。

   如需這些變更的詳細資訊，請參閱 [如何使用 Visual Studio 部署 WebJobs](websites-dotnet-deploy-webjobs.md)。

### <a name="add-nuget-packages"></a>新增 NuGet 封裝
WebJob 專案的新專案範本會自動安裝 WebJobs SDK NuGet 封裝 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) 及其相依項目。

在 WebJob 專案自動安裝的 WebJobs SDK 相依性中，其中一個相依性是 Azure 儲存體用戶端程式庫 (SCL)。 不過，您需要將它新增至 Web 專案才能使用 Blob 和佇列。

1. 開啟方案的 [管理 NuGet 封裝]  對話方塊。
2. 在左側窗格中，選取 [Installed packages] 。
3. 尋找 Azure 儲存體套件，然後按一下 [管理]。
4. 在 [選取專案] 方塊中，勾選 **ContosoAdsWeb** 核取方塊，然後按一下 [確定]。

    這三個專案都會透過 Entity Framework 來使用 SQL Database 中的資料。
5. 在左側窗格中，選取 [線上] 。
6. 尋找 *EntityFramework* NuGet 封裝，並將它安裝在這三個專案中。

### <a name="set-project-references"></a>設定專案參考
Web 和 WebJob 專案都將使用 SQL Database，因此兩者都會需要 ContosoAdsCommon 專案的參考。

1. 在 ContosoAdsWeb 專案中，設定 ContosoAdsCommon 專案的參考。 (以滑鼠右鍵按一下 ContosoAdsWeb 專案，然後依序按一下 [新增]  > **K**的簡單多層次 ASP.NET MVC 5 應用程式撰寫程式碼。 
2. 在 [參考管理員] 對話方塊中，選取 [專案] > [方案] > [ContosoAdsCommon]，然後按一下 [確定]。)
   
    WebJob 專案需要參考，才能使用映像及存取連接字串。

4. 在 ContosoAdsWebJob 專案中，設定 `System.Drawing` 和 `System.Configuration` 的參考。

### <a name="add-code-and-configuration-files"></a>新增程式碼和組態檔
本教學檔案未說明如何[使用 Scaffolding 建立 MVC 控制器和檢視](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)、如何[編寫能與 SQL Server 資料庫搭配使用的 Entity Framework 程式碼](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc)或 [ASP.NET 4.5 中非同步程式設計的基本概念](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async)。 因此剩下要進行的作業就是，從所下載的方案將程式碼和設定檔複製到新方案。 在執行該作業後，下一節將示範和說明程式碼的重要部分。

若要加入檔案到專案或資料夾，請以滑鼠右鍵按一下專案或資料夾，然後按一下 [加入]  > 的簡單多層次 ASP.NET MVC 5 應用程式撰寫程式碼。 選取您需要的檔案，然後按一下 [加入] 。 如果詢問您是否要取代現有的檔案，請按一下 [是] 。

1. 在 ContosoAdsCommon 專案中，刪除 *Class1.cs* 檔案，並在其位置加入所下載專案的下列檔案。

   * *Ad.cs*
   * *ContosoAdscontext.cs*
   * BlobInformation.cs
2. 在 ContosoAdsWeb 專案中，從所下載的專案加入下列檔案。

   * *Web.config*
   * *Global.asax.cs*  
   * 在 Controllers 資料夾中，新增檔案︰AdController.cs
   * 在 Views\Shared 資料夾中：_Layout.cshtml 檔案
   * 在 Views\Home 資料夾中：Index.cshtml
   * 在 Views\Ad 資料夾中 (請先建立此資料夾)：五個 .cshtml 檔案
3. 在 ContosoAdsWebJob 專案中，從所下載的專案加入下列檔案。

   * App.config (將檔案類型篩選變更為 [全部檔案])
   * *Program.cs*
   * *Functions.cs*

您現在可以如本教學課程中稍早所指示般建置、執行及部署應用程式。 不過，在您開始執行此動作之前，請先將仍在您部署的第一個 Web 應用程式中執行的 WebJob 停止。 否則，WebJob 將會處理在本機建立或由在新 Web 應用程式中執行之應用程式所建立的佇列訊息，因為它們全都使用相同的儲存體帳戶。

## <a id="code"></a>檢閱應用程式程式碼
以下小節將說明 WebJobs SDK、Azure 儲存體 Blob 和佇列相關的程式碼。

> [!NOTE]
> 如需 WebJobs SDK 特定的程式碼，請移至 [Program.cs 和 Functions.cs](#programcs) 區段。
>
>

### <a name="contosoadscommon---adcs"></a>ContosoAdsCommon - Ad.cs
Ad.cs 檔案可定義廣告類別列舉，以及廣告資訊的 POCO 實體類別。

        public enum Category
        {
            Cars,
            [Display(Name="Real Estate")]
            RealEstate,
            [Display(Name = "Free Stuff")]
            FreeStuff
        }

        public class Ad
        {
            public int AdId { get; set; }

            [StringLength(100)]
            public string Title { get; set; }

            public int Price { get; set; }

            [StringLength(1000)]
            [DataType(DataType.MultilineText)]
            public string Description { get; set; }

            [StringLength(1000)]
            [DisplayName("Full-size Image")]
            public string ImageURL { get; set; }

            [StringLength(1000)]
            [DisplayName("Thumbnail")]
            public string ThumbnailURL { get; set; }

            [DataType(DataType.Date)]
            [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
            public DateTime PostedDate { get; set; }

            public Category? Category { get; set; }
            [StringLength(12)]
            public string Phone { get; set; }
        }

### <a name="contosoadscommon---contosoadscontextcs"></a>ContosoAdsCommon - ContosoAdsContext.cs
ContosoAdsContext 類別可指定廣告類別用於 DbSet 集合，Entity Framework 會儲存在 SQL 資料庫中。

        public class ContosoAdsContext : DbContext
        {
            public ContosoAdsContext() : base("name=ContosoAdsContext")
            {
            }
            public ContosoAdsContext(string connString)
                : base(connString)
            {
            }
            public System.Data.Entity.DbSet<Ad> Ads { get; set; }
        }

類別含兩個建構函式。 第一個是由 Web 專案所使用，指定儲存在 Web.config 檔案或 Azure 執行階段環境中的連接字串名稱。 第二個建構函式可讓您傳入實際的連接字串。 WebJob 專案會需要該資訊，因為它沒有 Web.config 檔案。 您稍早已看見儲存此連接字串的位置，之後您會看到程式碼如何在具現化 DbContext 類別時擷取連接字串。

### <a name="contosoadscommon---blobinformationcs"></a>ContosoAdsCommon - BlobInformation.cs
`BlobInformation` 類別可用來在佇列訊息中儲存影像 Blob 的相關資訊。

        public class BlobInformation
        {
            public Uri BlobUri { get; set; }

            public string BlobName
            {
                get
                {
                    return BlobUri.Segments[BlobUri.Segments.Length - 1];
                }
            }
            public string BlobNameWithoutExtension
            {
                get
                {
                    return Path.GetFileNameWithoutExtension(BlobName);
                }
            }
            public int AdId { get; set; }
        }


### <a name="contosoadsweb---globalasaxcs"></a>ContosoAdsWeb - Global.asax.cs
自 `Application_Start` 方法呼叫的程式碼會建立 images Blob 容器和 images 佇列 (如果尚不存在)。 這可確保每當使用新的儲存體帳戶啟動時，系統便會自動建立必要的 Blob 容器和佇列。

此程式碼會使用 *Web.config* 檔案或 Azure 執行階段環境中的儲存體連接字串，來取得儲存體帳戶的存取權限。

        var storageAccount = CloudStorageAccount.Parse
            (ConfigurationManager.ConnectionStrings["AzureWebJobsStorage"].ToString());

之後會取得 images Blob 容器的參考、建立容器 (如果尚不存在)，並設定新容器的存取權限。 依預設，新的容器只允許具有儲存體帳戶認證的用戶端存取 Blob。 Web 應用程式需要 Blob 處於公用狀態，才能使用指向影像 Blob 的 URL 來顯示影像。

        var blobClient = storageAccount.CreateCloudBlobClient();
        var imagesBlobContainer = blobClient.GetContainerReference("images");
        if (imagesBlobContainer.CreateIfNotExists())
        {
            imagesBlobContainer.SetPermissions(
                new BlobContainerPermissions
                {
                    PublicAccess = BlobContainerPublicAccessType.Blob
                });
        }

類似的程式碼可取得 *thumbnailrequest* 佇列的參考，並建立新佇列。 在此情況下，即不需要變更權限。

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
        var imagesQueue = queueClient.GetQueueReference("thumbnailrequest");
        imagesQueue.CreateIfNotExists();

### <a name="contosoadsweb---layoutcshtml"></a>ContosoAdsWeb - _Layout.cshtml
*_Layout.cshtml* 檔案可設定頁首與頁尾中的應用程式名稱，並建立 "Ads" 功能表項目。

### <a name="contosoadsweb---viewshomeindexcshtml"></a>ContosoAdsWeb - Views\Home\Index.cshtml
Views\Home\Index.cshtml 檔案在首頁上顯示類別連結。 連結會將查詢字串變數中 `Category` 列舉的整數值傳遞至 [廣告索引] 頁面。

        <li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
        <li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
        <li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
        <li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>

### <a name="contosoadsweb---adcontrollercs"></a>ContosoAdsWeb - AdController.cs
在 AdController.cs 檔案中，建構子會呼叫 `InitializeStorage` 方法來建立 Azure 儲存體用戶端程式庫物件，該物件可提供用於處理 Blob 和佇列的 API。

之後，程式碼可取得 images Blob 容器的參考，如您稍早在 Global.asax.cs 中所見。 在執行該動作時，它會設定適用 Web 應用程式的預設 [重試原則](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling) 。 預設指數輪詢重試原則，可能會因為對暫時性的錯誤進行反覆重試，使得 Web 應用程式停止回應超過一分鐘。 此處指定的重試原則會在每次嘗試後等候 3 秒，最多嘗試 3 次。

        var blobClient = storageAccount.CreateCloudBlobClient();
        blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
        imagesBlobContainer = blobClient.GetContainerReference("images");

類似的程式碼可取得 *images* 佇列的參考。

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
        queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
        imagesQueue = queueClient.GetQueueReference("blobnamerequest");

多數的控制器程式碼通常用於使用 DbContext 類別來處理 Entity Framework 資料模型。 例外狀況為 HttpPost `Create` 方法，它會上傳檔案，並將檔案儲存在 Blob 儲存體。 模型繫結器可為方法提供 [HttpPostedFileBase](http://msdn.microsoft.com/library/system.web.httppostedfilebase.aspx) 物件。

        [HttpPost]
        [ValidateAntiForgeryToken]
        public async Task<ActionResult> Create(
            [Bind(Include = "Title,Price,Description,Category,Phone")] Ad ad,
            HttpPostedFileBase imageFile)

如果使用者選取要上傳的檔案，程式碼會上傳檔案、將檔案儲存在 Blob，並以指向 Blob 的 URL 更新廣告資料庫記錄。

        if (imageFile != null && imageFile.ContentLength != 0)
        {
            blob = await UploadAndSaveBlobAsync(imageFile);
            ad.ImageURL = blob.Uri.ToString();
        }

執行上傳的程式碼位於 `UploadAndSaveBlobAsync` 方法。 它會為 Blob 建立 GUID 名稱、上傳並儲存檔案，然後傳回參考至儲存的 Blob。

        private async Task<CloudBlockBlob> UploadAndSaveBlobAsync(HttpPostedFileBase imageFile)
        {
            string blobName = Guid.NewGuid().ToString() + Path.GetExtension(imageFile.FileName);
            CloudBlockBlob imageBlob = imagesBlobContainer.GetBlockBlobReference(blobName);
            using (var fileStream = imageFile.InputStream)
            {
                await imageBlob.UploadFromStreamAsync(fileStream);
            }
            return imageBlob;
        }

在 HttpPost `Create` 方法建立 Blob 並更新資料庫之後，它會建立佇列訊息，以通知後端程序，有一個影像已備妥可供轉換成縮圖。

        BlobInformation blobInfo = new BlobInformation() { AdId = ad.AdId, BlobUri = new Uri(ad.ImageURL) };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        await thumbnailRequestQueue.AddMessageAsync(queueMessage);

HttpPost `Edit` 方法的程式碼也是類似的，不同的是如果使用者選取一個新影像檔案，則必須刪除此廣告的任何現有 Blob。

        if (imageFile != null && imageFile.ContentLength != 0)
        {
            await DeleteAdBlobsAsync(ad);
            imageBlob = await UploadAndSaveBlobAsync(imageFile);
            ad.ImageURL = imageBlob.Uri.ToString();
        }

以下是當您刪除廣告時會刪除 Blob 的程式碼：

        private async Task DeleteAdBlobsAsync(Ad ad)
        {
            if (!string.IsNullOrWhiteSpace(ad.ImageURL))
            {
                Uri blobUri = new Uri(ad.ImageURL);
                await DeleteAdBlobAsync(blobUri);
            }
            if (!string.IsNullOrWhiteSpace(ad.ThumbnailURL))
            {
                Uri blobUri = new Uri(ad.ThumbnailURL);
                await DeleteAdBlobAsync(blobUri);
            }
        }
        private static async Task DeleteAdBlobAsync(Uri blobUri)
        {
            string blobName = blobUri.Segments[blobUri.Segments.Length - 1];
            CloudBlockBlob blobToDelete = imagesBlobContainer.GetBlockBlobReference(blobName);
            await blobToDelete.DeleteAsync();
        }

### <a name="contosoadsweb---viewsadindexcshtml-and-detailscshtml"></a>ContosoAdsWeb - Views\Ad\Index.cshtml 和 Details.cshtml
*Index.cshtml* 檔案會顯示縮圖與其他廣告資料：

        <img  src="@Html.Raw(item.ThumbnailURL)" />

*Details.cshtml* 檔案會顯示完整大小的影像：

        <img src="@Html.Raw(Model.ImageURL)" />

### <a name="contosoadsweb---viewsadcreatecshtml-and-editcshtml"></a>ContosoAdsWeb - Views\Ad\Create.cshtml 和 Edit.cshtml
Create.cshtml 和 Edit.cshtml 檔案可指定表單編碼，供控制器取得 `HttpPostedFileBase` 物件。

        @using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))

`<input>` 元素告知瀏覽器提供檔案選取對話方塊。

        <input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />

### <a id="programcs"></a>ContosoAdsWebJob - Program.cs
當 WebJob 啟動時，`Main` 方法會呼叫 WebJobs SDK `JobHost.RunAndBlock` 方法，開始執行目前執行緒上所觸發的函數。

        static void Main(string[] args)
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

### <a id="generatethumbnail"></a>ContosoAdsWebJob - Functions.cs - GenerateThumbnail 方法
WebJobs SDK 會在收到佇列訊息時呼叫此方法。 此方法會建立縮圖，並將縮圖 URL 放入資料庫。

        public static void GenerateThumbnail(
        [QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,
        [Blob("images/{BlobName}", FileAccess.Read)] Stream input,
        [Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)
        {
            using (Stream output = outputBlob.OpenWrite())
            {
                ConvertImageToThumbnailJPG(input, output);
                outputBlob.Properties.ContentType = "image/jpeg";
            }

            // Entity Framework context class is not thread-safe, so it must
            // be instantiated and disposed within the function.
            using (ContosoAdsContext db = new ContosoAdsContext())
            {
                var id = blobInfo.AdId;
                Ad ad = db.Ads.Find(id);
                if (ad == null)
                {
                    throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", id.ToString()));
                }
                ad.ThumbnailURL = outputBlob.Uri.ToString();
                db.SaveChanges();
            }
        }

* 當 thumbnailrequest 佇列收到新訊息時，`QueueTrigger` 屬性便會指示 WebJobs SDK 呼叫此方法。

        [QueueTrigger("thumbnailrequest")] BlobInformation blobInfo,

    佇列訊息中的 `BlobInformation` 物件會自動還原序列化成 `blobInfo` 參數。 方法完成時，佇列訊息便會被刪除。 如果方法在完成前失敗，則佇列訊息不會被刪除；在 10 分鐘的租用到期後，訊息便會被釋放並等待被再次挑選與處理。 如果某個訊息總是導致例外狀況，則不會無限制地重複此順序。 在嘗試處理訊息失敗 5 次之後，訊息便會被移至名為 {queuename}-poison 的佇列。 您可以設定嘗試的數目上限。
* 這兩個 `Blob` 屬性提供繫結到 Blob 的物件：一個繫結到現有影像的 Blob，一個繫結到方法建立的新縮圖 Blob。

        [Blob("images/{BlobName}", FileAccess.Read)] Stream input,
        [Blob("images/{BlobNameWithoutExtension}_thumbnail.jpg")] CloudBlockBlob outputBlob)

    Blob 名稱取自佇列訊息 (`BlobName` 和 `BlobNameWithoutExtension`) 中所收到 `BlobInformation` 物件的屬性。 若要取得儲存體用戶端程式庫的完整功能，您可以利用 `CloudBlockBlob` 類別來使用 Blob。 如果您想要重複使用為使用 `Stream` 物件所撰寫的程式碼，您可以使用 `Stream` 類別。

如需如何撰寫使用 WebJobs SDK 屬性的函式詳細資訊，請參閱下列資源：

* [如何透過 WebJobs SDK 使用 Azure 佇列儲存體 (英文)](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)
* [如何透過 WebJobs SDK 使用 Azure Blob 儲存體 (英文)](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)
* [如何透過 WebJobs SDK 使用 Azure 資料表儲存體 (英文)](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)
* [如何搭配使用 Azure 服務匯流排與 WebJobs SDK (英文)](websites-dotnet-webjobs-sdk-service-bus.md)

> [!NOTE]
> * 如果您的 Web 應用程式在多個 VM 上執行，則系統將同時執行多個 WebJobs，且在某些情況下，這會導致相同的資料多次進行處理。 如果您使用內建佇列、blob 和服務匯流排觸發程序，這不會造成問題。 SDK 可確保您的函式針對每個訊息或 blob 僅會處理一次。
> * 如需如何實作順利關機的詳細資訊，請參閱 [順利關機](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#graceful)。
> * 為求簡化，`ConvertImageToThumbnailJPG` 方法 (未顯示) 中的程式碼會使用 `System.Drawing` 命名空間中的類別。 不過，此命名空間中類別的設計原意是要與 Windows Form 搭配使用。 不支援將它們用於 Windows 或 ASP.NET 服務。 如需映像處理選項的詳細資訊，請參閱[動態映像產生](http://www.hanselman.com/blog/BackToBasicsDynamicImageGenerationASPNETControllersRoutingIHttpHandlersAndRunAllManagedModulesForAllRequests.aspx)和[深入調整映像大小](http://www.hanselminutes.com/313/deep-inside-image-resizing-and-scaling-with-aspnet-and-iis-with-imageresizingnet-author-na)。
>
>

## <a name="next-steps"></a>後續步驟
在本教學課程中，您看到使用 WebJobs SDK 進行後端處理的簡單多層次應用程式。 如要進一步了解 ASP.NET 多層式應用程式和 WebJobs，請參考本節所提供的一些建議。

### <a name="missing-features"></a>遺漏的功能
此應用程式設計得很簡單，以做為入門的教學課程。 在真實世界中，您將實作[相依性插入](http://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection)和[存放庫和工作單位模式](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo)的應用程式會使用[介面來記錄](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log)、使用 [EF Code First 移轉](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application)來管理資料模型變更，以及使用 [EF 連線復原](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application)來管理暫時性網路錯誤。

### <a name="scaling-webjobs"></a>調整 WebJob 的規模
WebJob 會在 Web 應用程式的內容中執行，且無法單獨調整。 例如，如果您擁有一個標準的 Web 應用程式執行個體，則您的背景程序只有一個執行中的執行個體，而且它會使用亦可用來提供 Web 內容的部分伺服器資源 (CPU、記憶體等)。

如果流量會因為一天中的某個時段或一週中的某天而有所不同，而且如果必須執行的後端處理可以暫緩的話，您可以排程 WebJobs 在低流量時段中執行。 如果該解決方案的負載仍然太高，您可以在針對該用途專用的 Web 應用程式中以 WebJob 形式執行後端。 接著您可以擴充後端 Web 應用程式，而不會影響到前端 Web 應用程式。

如需詳細資訊，請參閱 [調整 WebJob](websites-webjobs-resources.md#scale)。

### <a name="avoiding-web-app-timeout-shut-downs"></a>避免關機時 Web 應用程式逾時
若要確保您的 WebJobs 持續不斷地在 Web 應用程式的所有執行個體上執行，您必須啟用 [AlwaysOn](http://weblogs.asp.net/scottgu/archive/2014/01/16/windows-azure-staging-publishing-support-for-web-sites-monitoring-improvements-hyper-v-recovery-manager-ga-and-pci-compliance.aspx) 功能。

### <a name="using-the-webjobs-sdk-outside-of-webjobs"></a>在 WebJobs 外部使用 WebJobs SDK
使用 WebJobs SDK 的程式無需在 Azure 的 WebJob 中執行。 它可以在本機執行，也可以在如雲端服務背景工作角色或 Windows 服務等其他環境中執行。 不過，您僅能透過 Azure Web 應用程式來存取 WebJobs SDK 儀表板。 若要使用儀表板，您必須將 Web 應用程式連線到您所使用的儲存體帳戶，方法是在傳統入口網站的 [設定]  索引標籤上設定 AzureWebJobsDashboard 連接字串。 然後，您可以使用下列 URL 來進入儀表板：

https://{webappname}.scm.azurewebsites.net/azurejobs/#/functions

如需詳細資訊，請參閱 [使用 WebJobs SDK 來取得本機開發的儀表板](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx)(英文)，但請注意，它會顯示舊的連接字串名稱。

### <a name="more-webjobs-documentation"></a>其他 WebJobs 文件
如需詳細資訊，請參閱 [Azure WebJobs 文件資源](http://go.microsoft.com/fwlink/?LinkId=390226)。

