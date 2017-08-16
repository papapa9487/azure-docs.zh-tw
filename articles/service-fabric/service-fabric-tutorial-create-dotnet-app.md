---
title: "建立 Service Fabric 的 .NET 應用程式 |Microsoft Docs"
description: "了解如何建立含有 ASP.NET Core 前端和可靠服務具狀態後端的應用程式，並將應用程式部署到叢集。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: ryanwi, mikhegn
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 71d910bc0e459528805521ba991e5291396a3b8d
ms.contentlocale: zh-tw
ms.lasthandoff: 08/10/2017

---

# <a name="create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>建立和部署含有 ASP.NET Core Web API 前端服務和具狀態後端服務的應用程式
本教學課程是系列中的第一部分，示範如何建立含有 ASP.NET Core Web API 前端和具狀態後端服務的 Azure Service Fabric 應用程式來儲存您的資料。 

![應用程式圖表](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

在系列的第一部分中，您將了解如何：

> [!div class="checklist"]
> * 建立 ASP.NET Core Web API 服務成為可靠服務
> * 建立具狀態可靠服務
> * 實作服務遠端和使用服務 Proxy

在本教學課程系列中，您將了解如何：
> [!div class="checklist"]
> * 建置 .NET Service Fabric 應用程式
> * [將應用程式部署到遠端叢集](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [使用 Visual Studio Team Services 設定 CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)

## <a name="prerequisites"></a>必要條件
開始進行本教學課程之前：
- 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [安裝 Visual Studio 2017](https://www.visualstudio.com/) 並安裝 **Azure 開發**以及 **ASP.NET 和 Web 開發**工作負載。
- [安裝 Service Fabric SDK](service-fabric-get-started.md)

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>建立 ASP.NET Web API 服務成為可靠的服務
ASP.NET Core 是輕量型、跨平台的 Web 開發架構，可供您用來建立新式 Web UI 和 Web API。 若要完整了解 ASP.NET Core 如何與 Service Fabric 整合，強烈建議您仔細閱讀 [Service Fabric Reliable Services 中的 ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) 文章。 現在您可以依照本教學課程來快速上手。 若要深入了解 ASP.NET Core，請參閱 [ASP.NET Core 文件](https://docs.microsoft.com/aspnet/core/)。

> [!NOTE]
> 本教學課程係根據[適用於 Visual Studio 2017 的 ASP.NET Core 工具](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/start-mvc)。 適用於 Visual Studio 2015 的 .NET Core 工具不再進行更新。

1. 以**系統管理員**身分啟動 Visual Studio。

2. 使用**檔案**->**新增**->**專案**建立專案

3. 在 [新增專案]  對話方塊中，選擇 [雲端] > [Service Fabric 應用程式]。

4. 將應用程式命名為 **MyApplication**，然後按 [確定]。

   ![Visual Studio 中的新增專案對話方塊](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. 在**新增 Service Fabric 服務**頁面上，選擇**無狀態 ASP.NET Core**，並將服務命名為 **MyWebAPIFrontEnd**。
   
   ![在新服務對話方塊中選擇 ASP.NET Web 服務](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. 下一頁會提供一組 ASP.NET Core 專案範本。 在本教學課程中，選擇 [Web API]。 但您可以將相同的概念套用於建置完整的 Web 應用程式。
   
   ![選擇 ASP.NET 專案類型](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio 會建立應用程式和服務專案，並顯示在 [方案總管] 中。

   ![使用 ASP.NET Core Web API 服務建立應用程式後的方案總管]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="deploy-and-debug-the-application-locally"></a>在本機部署和偵錯應用程式
您現在可以繼續進行應用程式偵錯，觀察預設 ASP.NET Core Web API 範本提供的預設行為。

在 Visual Studio 中，按 `F5` 部署應用程式以供偵錯。 如果您先前並未以**系統管理員**身分開啟 Visual Studio，`F5` 將失敗。

> [!NOTE]
> 您第一次在本機執行及部署應用程式時，Visual Studio 會建立本機叢集以供偵錯，這可能需要一些時間。 叢集建立狀態會顯示在 Visual Studio 輸出視窗中。

備妥叢集時，系統匣會顯示來自 Service Fabric 本機叢集管理員的通知。

1. 若要進行應用程式偵錯，請在 Visual Studio 按 F5。
2. 部署完成時，Visual Studio 會啟動瀏覽器並瀏覽至 ASP.NET Web API 服務的根目錄。 ASP.NET Core Web API 範本不會提供根目錄的預設行為，因此您將在瀏覽器中收到錯誤。
3. 將 `/api/values` 新增至瀏覽器中的 URL。 此要求會叫用 Web API 範本中 ValuesController 上的 `Get` 方法。 它會傳回範本所提供的預設回應，也就是包含兩個字串的 JSON 陣列：
   
![從 ASP.NET Core Web API 範本傳回的預設值](./media/service-fabric-tutorial-create-dotnet-app/browser-aspnet-template-values.png)

> [!NOTE]
> 進行應用程式偵錯時，若要變更預設的 Visual Studio 2017 行為，可以變更 Service Fabric 應用程式專案 **MyApplication** 的屬性。
> 對於此教學課程，將應用程式偵錯模式設定為 [重新整理應用程式]，並將 **'/api/values'** 新增到應用程式 URL 屬性，這麼做可以達到最佳的偵錯效果。
> 

若要停止應用程式偵錯，請返回 Visual Studio 並且按**Shift+F5**。

### <a name="understanding-the-service-fabric-application-and-service"></a>了解 Service Fabric 應用程式和服務
Visual Studio 解決方案現在包含兩個專案。
1. Service Fabric 應用程式專案 - **MyApplication**
    - 此專案未直接包含任何程式碼。 它反而會參考一組服務專案。 此外，其中包含其他類型的內容，可用來指定如何撰寫和部署應用程式。
2. 服務專案 - **MyWebAPIFrontEnd**
    - 此專案是您的 ASP.NET Core Web API 專案，其中包含您服務的程式碼和組態。 查看 [控制器] 資料夾中的 ValuesController.cs 程式碼檔案時，您會發現它是一般的 ASP.NET Core Web API 控制器。 在 Service Fabric 中執行 ASP.NET Core Web API 成為以 Reliable Service 時，在控制器撰寫程式碼沒有特定的需求。

如需 Service Fabric 應用程式模型的詳細資訊，請參閱[在 Service Fabric 中模型化應用程式](service-fabric-application-model.md)。

如需服務專案內容的詳細資訊，請參閱[可靠服務使用者入門](service-fabric-reliable-services-quick-start.md)。

## <a name="add-a-stateful-back-end-service-to-your-application"></a>將具狀態後端服務新增到應用程式
現在，應用程式中有 ASP.NET Web API 服務正在執行，讓我們繼續新增具狀態可靠服務，將一些資料儲存於應用程式中。

Service Fabric 可讓您使用可靠集合，直接在服務內以一致且可靠的方式儲存資料。 可靠集合是一組簡單的高可用性和可靠的集合類別，用過 C# 集合的任何人都會很熟悉。

在本教學課程中，您建立服務，將計數器值儲存於可靠集合中。

1. 在 [方案總管] 中，以滑鼠右鍵按一下應用程式專案中的 [服務]，然後選擇 [新增] > [新增 Service Fabric Explorer]。
   
    ![將新服務加入至現有的應用程式](./media/service-fabric-tutorial-create-dotnet-app/vs-add-new-service.png)

2. 在 [新增 Service Fabric 服務] 對話方塊中，選擇 [具狀態服務]，並將服務命名為 **MyStatefulService**，然後按 [確定]。

    ![Visual Studio 中的新增服務對話方塊](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    建立服務專案後，您的應用程式中會有兩個服務。 隨著您繼續組建應用程式，您可以用相同的方式新增更多服務。 每個服務都可以獨立設定版本和升級。

### <a name="deploy-and-debug-the-application-locally"></a>在本機部署和偵錯應用程式
將新的服務新增到應用程式後，讓我們對於整個應用程式進行偵錯，並查看新服務的預設行為。

若要進行應用程式偵錯，請在 Visual Studio 按 F5。

> [!NOTE]
> 如果您選擇使用**重新整理應用程式**作為應用程式偵錯模式，系統會提示您授與本機 Service Fabric 叢集存取權，以便建立應用程式的輸出資料夾。
> 

在本機 Service Fabric 叢集中，將組建、部署和宣告您的應用程式中的這兩個服務。 服務啟動後，Visual Studio 仍然會啟動瀏覽器，但是也會自動顯示 [診斷事件檢視器]，以便查看服務的追蹤輸出。
   
![診斷事件檢視器](./media/service-fabric-tutorial-create-dotnet-app/diagnostic-events-viewer.png)

診斷事件檢視器會顯示偵錯中的 Visual Studio 解決方案所屬的全部服務發出的追蹤訊息。 暫停診斷事件檢視器，即可展開其中一個服務訊息檢查其屬性。 如此一來，即可查看訊息由叢集中哪個服務發出、服務執行個體在哪個節點上執行，以及其他資訊。

![診斷事件檢視器](./media/service-fabric-tutorial-create-dotnet-app/expanded-diagnostics-viewer.png)

您可看出服務訊息來自我們建立的具狀態服務，因為 **serviceTypeName** 是 **MyStatefulServiceType**。 您也可以看出它傳送的訊息顯示「目前的計數器是...。」。 如下列的螢幕擷取畫面所示，此訊息是由 **MyStatefulService.cs** 的 `RunAsync` 方法中反白顯示的一行程式碼發出。

![服務訊息程式碼](./media/service-fabric-tutorial-create-dotnet-app/service-message-code.png)

如需服務和應用程式發出診斷資訊的詳細資訊，請參閱[對 Azure Service Fabric 進行監視和診斷](service-fabric-diagnostics-overview.md)。

若要停止應用程式偵錯，請返回 Visual Studio 並且按**Shift+F5**。

### <a name="understanding-the-mystatefulservice-code"></a>了解 MyStatefulService 程式碼
為了更了解它如何使用可靠的字典將資料儲存於叢集中，讓我們花一些時間查看 **MyStatefulService** 服務中的程式碼。

服務中有五行程式碼與建立、更新和讀取可靠的字典有關。

![可靠的字典程式碼](./media/service-fabric-tutorial-create-dotnet-app/reliable-dictionary-code.png)

1. 每當服務的 `RunAsync` 方法執行 (這會在服務啟動時發生) 時，這行程式碼會取得或新增與服務的 `myDictionary` 同名的可靠字典。
2. 與可靠字典中的值進行的所有互動，都需要交易，這會使用在這行程式碼中輸入的陳述式建立該交易。
3. 這行程式碼會取得與在方法呼叫中指定的索引鍵相關聯的值，例如 `Counter`。
4. 這行程式碼會將值遞增，以便更新與索引鍵 `Counter` 相關聯的值。
5. 這個方法呼叫會認可交易，並且在叢集中的節點仲裁儲存更新的值時傳回。

如需可靠字典和可靠集合的詳細資訊，請參閱 [Azure Service Fabric 具狀態服務中可靠的集合簡介](service-fabric-reliable-services-reliable-collections.md)。

## <a name="connect-the-services"></a>連接服務
在下一個步驟中，我們將連接這兩項服務，並設定前端 Web API 傳回後端服務可靠字典的目前值。

對於您與可靠服務通訊的方式，Service Fabric 會提供完整的彈性。 在單一應用程式中，您可能有可透過 TCP 存取的服務。 可透過 HTTP 的 REST API 存取的其他服務以及其他任何服務可以透過 Web 通訊端存取。 如需可用選項和相關權衡取捨的背景，請參閱 [與服務進行通訊](service-fabric-connect-and-communicate-with-services.md)。

在此教學課程中，我們會使用[使用 Reliable Services 的遠端服務](service-fabric-reliable-services-communication-remoting.md)。

在服務遠端的做法中 (模仿遠端程序呼叫 (RPC))，您會定義一個介面以作為服務的公用合約。 然後使用該介面來產生 Proxy 類別，以便與服務進行互動。

### <a name="create-the-remoting-interface"></a>建立遠端介面
我們會先建立介面做為兩個服務之間的中介。 介面必須由使用該介面的所有服務參考，因此我們會在個別的類別庫專案中建立該介面。

1. 在 [方案總管] 中，以滑鼠右鍵按一下您的解決方案，並選擇 [新增]  >  [新增專案]。
2. 在左側導覽窗格中選擇 [Visual C#] 項目，然後選取 [類別庫 (.NET Framework)] 範本。 確定 .NET Framework 版本已設定為 **4.5.2** 或更新版本。
   
    ![為具狀態服務建立介面專案](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-project.png)

3. 將類別庫 **MyStatefulService.Interface** 命名，並按一下 [確定]。

4. 在 [方案總管] 中，以滑鼠右鍵按一下解決方案，並選擇 [管理解決方案的 NuGet 封裝]。

5. 選擇 [瀏覽]，並搜尋 **Microsoft.ServiceFabric.Services.Remoting**。 選擇將對於解決方案中的三個服務專案安裝它： 
   
    ![新增服務 NuGet 封裝](./media/service-fabric-tutorial-create-dotnet-app/add-nuget.png)

6. 在類別庫中，透過單一方法 `GetCountAsync` 建立介面，並從 `Microsoft.ServiceFabric.Services.Remoting.IService` 擴充介面。 遠端介面必須衍生自這個介面，以指出它是服務遠端的介面。 
   
    ```c#
    using Microsoft.ServiceFabric.Services.Remoting;  
    using System.Threading.Tasks;
    ...
    namespace MyStatefulService.Interface
    {           
        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```
7. 在 [方案總管] 中以滑鼠右鍵按一下 **MyStatefulService.Interface** 專案，然後選取 [屬性]。 選取 [組建] 索引標籤，然後選取 [平台目標] 下拉式清單中的 **x64** 值。 

8. 儲存您的所有變更。

### <a name="implement-the-interface-in-your-stateful-service"></a>在具狀態服務中實作介面
既然我們已定義介面，我們必須在具狀態服務中實作該介面。

1. 將參考新增到包含 **MyStatefulService** 專案介面的類別庫。
   
    ![新增具狀態服務中的類別庫專案的參考](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-reference.png)

    ![新增具狀態服務中的類別庫專案的參考](./media/service-fabric-tutorial-create-dotnet-app/vs-add-class-library-reference-2.png)

2. 開啟 `MyStatefulService.cs` 檔案，將其延伸來實作我們建立的 `ICounter` 介面。
   
    ```c#
    using MyStatefulService.Interface;
    ...
   
    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. 現在實作 `ICounter` 介面中所定義的單一方法，即 `GetCountAsync`。
   
    ```c#
    public async Task<long> GetCountAsync()
    {
        var myDictionary =
          await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
   
        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```

    - 這個方法會傳回名稱為 `myDictionary` 可靠字典中儲存的 `Counter` 索引鍵值。 

### <a name="expose-the-stateful-service-using-service-remoting"></a>使用服務遠端處理公開具狀態服務
實作 `ICounter` 介面後，最後一個步驟是開啟服務遠端端點。 對於具狀態服務，Service Fabric 會提供稱為 `CreateServiceReplicaListeners`的可覆寫方法。 透過此方法，您可以根據想要為服務啟用的通訊類型，指定一或多個通訊接聽程式。

在此案例中，我們取代現有的 `CreateServiceReplicaListeners` 方法，並提供 `ServiceRemotingListener` 的執行個體，此執行個體會透過 `ServiceProxy` 建立可從用戶端呼叫的 RPC 端點。  

變更 **MyStatefulService.cs** 檔案中的 **CreateServiceReplicaListeners** 方法，並將 using 陳述式新增到 `Microsoft.ServiceFabric.Services.Remoting.Runtime` 命名空間。

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```

現在，具狀態服務已經準備好使用服務遠端接收透過 RPC 來自其他服務的流量。

### <a name="call-the-stateful-back-end-service-from-the-front-end-service"></a>從前端服務呼叫具狀態後端服務
我們的後端服務已公開介面，現在只需要從 ASP.NET Web API 服務新增與介面進行通訊的程式碼。 為了使用服務遠端進行通訊，我們將使用值控制器的服務 Proxy。

1. 在 [方案總管] 中，展開 **MyWebAPIFrontEnd**，並且以滑鼠右鍵按一下 [相依性]，然後選取 [新增參考]。  選取 **MyStatefulService.Interface**，並按一下 [確定]。
   
2. 在 **Controllers** 資料夾中，開啟 `ValuesController.cs` 檔案。 將下列 using 陳述式新增到檔案中：

    ```c#
    using MyStatefulService.Interface;
    using Microsoft.ServiceFabric.Services.Client;
    using Microsoft.ServiceFabric.Services.Remoting.Client;
    ```
    
3. `Get` 方法目前只會傳回 "value1" 和 "value2" 的硬式編碼字串陣列，這符合我們稍早在瀏覽器中所見的內容。 使用下列程式碼來取代此實作：
   
    ```
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));
   
        long count = await counter.GetCountAsync();
   
        return new string[] { count.ToString() };
    }
    ```
 
    這個方法的第一行程式碼會使用 ICounter 介面建立具狀態服務的 ServiceProxy 物件。 建立 ServiceProxy 時，您必須提供兩項資訊：資料分割識別碼和服務名稱。
   
    具狀態服務可分割為不同的級別，方法是按照客戶識別碼或郵遞區號之類的索引鍵，將資料劃分到不同貯體。 在我們的簡單應用程式中，具狀態服務只有一個資料分割，所以索引鍵並不重要。 您提供的任何索引鍵都會放在相同的分割區。 如深入了解分割服務，請參閱 [如何分割 Service Fabric 可靠的服務](service-fabric-concepts-partitioning.md)。
   
    服務名稱是 fabric:/&lt;application_name&gt;/&lt;service_name&gt; 形式的 URI。
   
    利用這兩項資訊，Service Fabric 即可唯一識別要求應傳送至的電腦。 `ServiceProxy` 類別也會順暢地處理裝載具狀態服務分割區的電腦發生失敗的情況，而另一部電腦則必須進行升級才能取而代之。 此概念讓撰寫用戶端程式碼來處理其他服務變得簡單許多。
   
    一旦擁有 Proxy，我們將叫用 `GetCountAsync` 方法並傳回其結果。

4. 再次按 F5 以執行修改過的應用程式。 像之前一樣，Visual Studio 會自動啟動瀏覽器並瀏覽至 Web 專案的根目錄。 新增 "api/values" 路徑，您應該會看到傳回的目前計數器值。
   
    ![在瀏覽器中顯示的具狀態計數器值](./media/service-fabric-tutorial-create-dotnet-app/browser-aspnet-counter-value.png)
   
    定期重新整理瀏覽器，以查看計數器值更新。

若要停止應用程式偵錯，請返回 Visual Studio 並且按**Shift+F5**。

## <a name="next-steps"></a>後續步驟
在教學課程的這個部分中，您已了解如何：

> [!div class="checklist"]
> * 建立 ASP.NET Core Web API 服務成為可靠服務
> * 建立具狀態可靠服務
> * 實作服務遠端和使用服務 Proxy

前進到下一個教學課程：
> [!div class="nextstepaction"]
> [將應用程式部署至 Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)
