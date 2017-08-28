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
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: ef50adf3af19bce494c3256308b443c8eaccdcea
ms.contentlocale: zh-tw
ms.lasthandoff: 08/17/2017

---

# <a name="create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>建立和部署含有 ASP.NET Core Web API 前端服務和具狀態後端服務的應用程式
本教學課程是一個系列的第一部分。  您將了解如何建立含有 ASP.NET Core Web API 前端和具狀態後端服務的 Azure Service Fabric 應用程式來儲存您的資料。 當您完成時，您會有一個投票應用程式，其 ASP.NET Core Web 前端會將投票結果儲存在叢集中具狀態的後端服務。 如果您不需要以手動建立投票應用程式，可以[下載已完成應用程式的原始程式碼](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/)並直接前往[逐步解說投票範例應用程式](#walkthrough_anchor)。

![應用程式圖表](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

在系列的第一部分中，您將了解如何：

> [!div class="checklist"]
> * 建立 ASP.NET Core Web API 服務成為具狀態可靠服務
> * 建立 ASP.NET Core Web 應用程式服務成為具狀態可靠服務
> * 使用反向 proxy 來與具狀態服務進行通訊

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
首先，使用 ASP.NET Core 建立投票應用程式的 web 前端。 ASP.NET Core 是輕量型、跨平台的 Web 開發架構，可供您用來建立新式 Web UI 和 Web API。 若要完整了解 ASP.NET Core 如何與 Service Fabric 整合，強烈建議您仔細閱讀 [Service Fabric Reliable Services 中的 ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) 文章。 現在，您可以依照本教學課程來快速上手。 若要深入了解 ASP.NET Core，請參閱 [ASP.NET Core 文件](https://docs.microsoft.com/aspnet/core/)。

> [!NOTE]
> 本教學課程係根據[適用於 Visual Studio 2017 的 ASP.NET Core 工具](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/start-mvc)。 適用於 Visual Studio 2015 的 .NET Core 工具不再進行更新。

1. 以**系統管理員**身分啟動 Visual Studio。

2. 使用**檔案**->**新增**->**專案**建立專案

3. 在 [新增專案]  對話方塊中，選擇 [雲端] > [Service Fabric 應用程式]。

4. 將應用程式命名為 **Voting**，然後按 [確定]。

   ![Visual Studio 中的新增專案對話方塊](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. 在**新增 Service Fabric 服務**頁面上，選擇**無狀態 ASP.NET Core**，並將服務命名為 **VotingWeb**。
   
   ![在新服務對話方塊中選擇 ASP.NET Web 服務](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. 下一頁會提供一組 ASP.NET Core 專案範本。 在本教學課程中，選擇 [Web 應用程式]。 
   
   ![選擇 ASP.NET 專案類型](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio 會建立應用程式和服務專案，並顯示在 [方案總管] 中。

   ![使用 ASP.NET Core Web API 服務建立應用程式後的方案總管]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="add-angularjs-to-the-votingweb-service"></a>將 AngularJS 新增至 VotingWeb 服務
使用內建 [Bower 支援](/aspnet/core/client-side/bower)將 [AngularJS](http://angularjs.org/) 新增至您的服務。 開啟 bower.json 並新增 Angular 和 Angular 啟動程序的項目，然後儲存您的變更。

```json
{
  "name": "asp.net",
  "private": true,
  "dependencies": {
    "bootstrap": "3.3.7",
    "jquery": "2.2.0",
    "jquery-validation": "1.14.0",
    "jquery-validation-unobtrusive": "3.2.6",
    "angular": "v1.6.5",
    "angular-bootstrap": "v1.1.0"
  }
}
```
在儲存 bower.json 檔案時，Angular 會安裝在您專案的 wwwroot/lib 資料夾中。 此外，它會列在 Dependencies/Bower 資料夾內。

### <a name="update-the-sitejs-file"></a>更新 site.js 檔案
開啟 wwwroot/js/site.js 檔案。  將其內容取代為 [首頁] 檢視所使用的 JavaScript：

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>更新 Index.cshtml 檔案
開啟 Views/Home/Index.cshtml 檔案，檢視為 [首頁] 控制器特定。  將其內容取代為下列項目，然後儲存變更。

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item" />
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr />

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.key)">
                            <span class="pull-left">
                                {{vote.key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-layoutcshtml-file"></a>更新 _Layout.cshtml 檔案
開啟 Views/Shared/_Layout.cshtml 檔案，ASP.NET 應用程式的預設版面配置。  將其內容取代為下列項目，然後儲存變更。

```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="http://angularjs.org">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.min.css" rel="stylesheet" />
    <link href="~/css/site.css" rel="stylesheet" />

</head>
<body>
    <div class="container body-content">
        @RenderBody()
    </div>

    <script src="~/lib/jquery/dist/jquery.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
    <script src="~/lib/angular/angular.js"></script>
    <script src="~/lib/angular-bootstrap/ui-bootstrap-tpls.js"></script>
    <script src="~/js/site.js"></script>

    @RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>更新 VotingWeb.cs 檔案
開啟 VotingWeb.cs 檔案，該檔案會使用 WebListener web 伺服器，在無狀態服務內建立 ASP.NET Core WebHost。  將 `using System.Net.Http;` 指示詞新增至檔案開頭處。  將 `CreateServiceInstanceListeners()` 函式取代為下列項目，然後儲存變更。

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new WebListenerCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
            {
                ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting WebListener on {url}");

                return new WebHostBuilder().UseWebListener()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<StatelessServiceContext>(serviceContext)
                                    .AddSingleton<HttpClient>())
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseApplicationInsights()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
            }))
    };
}
```

### <a name="add-the-votescontrollercs-file"></a>新增 VotesController.cs 檔案
新增定義投票動作的控制器。 以滑鼠右鍵按一下 [控制器] 資料夾，然後選取 [新增 -> 新增項目 -> 類別]。  將檔案命名為 "VotesController.cs"，然後按一下 [新增]。  將檔案內容取代為下列項目，然後儲存變更。  稍後，在[更新 VotesController.cs 檔案](#updatevotecontroller_anchor)中，將會修改這個檔案，以從後端服務讀取和寫入投票資料。  現在，控制器會將靜態字串資料傳回至檢視。

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using System.Collections.Generic;
using Newtonsoft.Json;
using System.Text;
using System.Net.Http;
using System.Net.Http.Headers;

namespace VotingWeb.Controllers
{
    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```



### <a name="deploy-and-run-the-application-locally"></a>在本機部署和執行應用程式
您現在可以繼續執行應用程式。 在 Visual Studio 中，按 `F5` 部署應用程式以供偵錯。 如果您先前並未以**系統管理員**身分開啟 Visual Studio，`F5` 將失敗。

> [!NOTE]
> 您第一次在本機執行及部署應用程式時，Visual Studio 會建立本機叢集以供偵錯。  建立叢集可能需要一些時間。 叢集建立狀態會顯示在 Visual Studio 輸出視窗中。

此時，您的 web 應用程式看起來應該像這樣：

![ASP.NET Core 前端](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

若要停止應用程式偵錯，請返回 Visual Studio 並且按**Shift+F5**。

## <a name="add-a-stateful-back-end-service-to-your-application"></a>將具狀態後端服務新增到應用程式
現在，應用程式中有 ASP.NET Web API 服務正在執行，讓我們繼續新增具狀態可靠服務，將一些資料儲存於應用程式中。

Service Fabric 可讓您使用可靠集合，直接在服務內以一致且可靠的方式儲存資料。 可靠集合是一組高可用性和可靠的集合類別，用過 C# 集合的任何人都會很熟悉。

在本教學課程中，您建立服務，將計數器值儲存於可靠集合中。

1. 在 [方案總管] 中，以滑鼠右鍵按一下應用程式專案中的 [服務]，然後選擇 [新增] > [新增 Service Fabric Explorer]。
   
    ![將新服務加入至現有的應用程式](./media/service-fabric-tutorial-create-dotnet-app/vs-add-new-service.png)

2. 在 [新增 Service Fabric 服務] 對話方塊中，選擇 [具狀態 ASP.NET Core]，並將服務命名為 **VotingData**，然後按 [確定]。

    ![Visual Studio 中的新增服務對話方塊](./media/service-fabric-tutorial-create-dotnet-app/add-stateful-service.png)

    建立服務專案後，您的應用程式中會有兩個服務。 隨著您繼續組建應用程式，您可以用相同的方式新增更多服務。 每個服務都可以獨立設定版本和升級。

3. 下一頁會提供一組 ASP.NET Core 專案範本。 在本教學課程中，選擇 [Web API]。

    ![選擇 ASP.NET 專案類型](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog2.png)

    Visual Studio 會建立服務專案，並顯示在 [方案總管] 中。

    ![Solution Explorer](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>新增 VoteDataController.cs 檔案

在 **VotingData** 專案中，以滑鼠右鍵按一下 [控制器] 資料夾，然後選取 [新增 -> 新增項目 -> 類別]。 將檔案命名為 "VoteDataController.cs"，然後按一下 [新增]。 將檔案內容取代為下列項目，然後儲存變更。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.ServiceFabric.Data;
using System.Threading;
using Microsoft.ServiceFabric.Data.Collections;

namespace VotingData.Controllers
{
    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            var ct = new CancellationToken();

            var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                var list = await votesDictionary.CreateEnumerableAsync(tx);

                var enumerator = list.GetAsyncEnumerator();

                var result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```


## <a name="connect-the-services"></a>連接服務
在下一個步驟中，我們會將這兩項服務連線，並讓前端 Web 應用程式從後端服務取得和設定投票資訊。

對於您與可靠服務通訊的方式，Service Fabric 會提供完整的彈性。 在單一應用程式中，您可能有可透過 TCP 存取的服務。 可透過 HTTP 的 REST API 存取的其他服務以及其他任何服務可以透過 Web 通訊端存取。 如需可用選項和相關權衡取捨的背景，請參閱 [與服務進行通訊](service-fabric-connect-and-communicate-with-services.md)。

在本教學課程中，我們會使用 [ASP.NET Core Web API](service-fabric-reliable-services-communication-aspnetcore.md)。

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>更新 VotesController.cs 檔案
在 **VotingWeb** 專案中，開啟 Controllers/VotesController.cs 檔案。  將 `VotesController` 類別定義內容取代為下列項目，然後儲存變更。

```csharp
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;
        string serviceProxyUrl = "http://localhost:19081/Voting/VotingData/api/VoteData";
        string partitionKind = "Int64Range";
        string partitionKey = "0";

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            IEnumerable<KeyValuePair<string, int>> votes;

            HttpResponseMessage response = await this.httpClient.GetAsync($"{serviceProxyUrl}?PartitionKind={partitionKind}&PartitionKey={partitionKey}");

            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int)response.StatusCode);
            }

            votes = JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync());

            return Json(votes);
        }

        // PUT: api/Votes/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            string payload = $"{{ 'name' : '{name}' }}";
            StringContent putContent = new StringContent(payload, Encoding.UTF8, "application/json");
            putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

            string proxyUrl = $"{serviceProxyUrl}/{name}?PartitionKind={partitionKind}&PartitionKey={partitionKey}";

            HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent);

            return new ContentResult()
            {
                StatusCode = (int)response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }

        // DELETE: api/Votes/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            HttpResponseMessage response = await this.httpClient.DeleteAsync($"{serviceProxyUrl}/{name}?PartitionKind={partitionKind}&PartitionKey={partitionKey}");

            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int)response.StatusCode);
            }

            return new OkResult();

        }
    }
```
<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>逐步解說投票範例應用程式
投票應用程式包含兩個服務：
- Web 前端服務 (VotingWeb) 是 ASP.NET Core Web 前端服務，可作為網頁，並公開 Web API 來與後端服務通訊。
- 後端服務 (VotingData) 是 ASP.NET Core Web 服務，會公開 API 來將投票結果儲存在磁碟上所保存的可靠字典中。

![應用程式圖表](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

當您在應用程式中投票時，會發生下列事件：
1. JavaScript 會將投票要求當做 HTTP PUT 要求，傳送至 Web 前端服務中的 Web API。

2. Web 前端服務使用 Proxy 來尋找 HTTP PUT 要求，並將其轉送至後端服務。

3. 後端服務會接受傳入要求，並將更新的結果儲存在可靠的字典中，以複寫至叢集中的多個節點並保存在磁碟上。 應用程式的所有資料都會儲存在叢集中，因此不需要資料庫。

## <a name="debug-in-visual-studio"></a>在 Visual Studio 中偵錯
在 Visual Studio 中偵錯應用程式時，您會使用本機 Service Fabric 開發叢集。 您可以根據自己的情況選擇調整偵錯體驗。 在此應用程式中，我們將資料儲存在使用可靠字典的後端服務中。 當您停止偵錯工具時，Visual Studio 預設會移除應用程式。 移除應用程式也會導致移除後端服務中的資料。 若要保存偵錯工作階段之間的資料，您可以在 Visual Studio 中，將 [應用程式偵錯模式] 當做 [投票] 專案上的屬性來變更。

若要查看對程式碼的影響，請完成下列步驟：
1. 開啟 **VotesController.cs** 檔案，並在 Web API 的 **Put** 方法 (第 47 行) 中設定中斷點 - 您可以在 Visual Studio 的方案總管中搜尋此檔案。

2. 開啟 **VoteDataController.cs** 檔案，並在此 Web API 的 **Put** 方法 (第 50 行) 中設定中斷點。

3. 返回到瀏覽器，並按一下投票選項或新增投票選項。 您到達 Web 前端之 API 控制器的第一個中斷點。
    
    1. 瀏覽器中的 JavaScript 會在此位置，將要求傳送至前端服務中的 Web API 控制器。
    
    ![新增投票前端服務](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

    2. 首先，我們會針對後端服務 **(1)** 建構 ReverseProxy 的 URL。
    3. 接著，我們會將 HTTP PUT 要求傳送至 ReverseProxy **(2)**。
    4. 最後我們會將後端服務的回應傳回至用戶端 **(3)**。

4. 按 **F5** 繼續
    1. 您現在位於後端服務的中斷點。
    
    ![新增投票後端服務](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

    2. 在方法的第一行 **(1)** 中，我們使用 `StateManager` 取得或新增名為 `counts` 的可靠字典。
    3. 與可靠字典中的值進行的所有互動，都需要交易，這會使用陳述式 **(2)** 建立該交易。
    4. 在交易中，我們會接著更新投票選項的相關索引鍵值，然後認可作業 **(3)**。 一旦傳回認可方法，字典中的資料會更新並複寫至叢集中的其他節點。 資料現在會安全地儲存在叢集中，而且後端服務可以容錯移轉到仍有可用資料的其他節點。
5. 按 **F5** 繼續

若要停止偵錯工作階段，請按 **Shift+F5**。


## <a name="next-steps"></a>後續步驟
在教學課程的這個部分中，您已了解如何：

> [!div class="checklist"]
> * 建立 ASP.NET Core Web API 服務成為具狀態可靠服務
> * 建立 ASP.NET Core Web 應用程式服務成為具狀態可靠服務
> * 使用反向 proxy 來與具狀態服務進行通訊

前進到下一個教學課程：
> [!div class="nextstepaction"]
> [將應用程式部署至 Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)
