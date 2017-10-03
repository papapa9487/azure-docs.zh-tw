---
title: "整合 Azure Service Fabric 與 API 管理 | Microsoft Docs"
description: "了解如何快速開始使用 Azure API 管理與 Service Fabric。"
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
ms.date: 09/13/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 1cadb543660b636ce7f0285973e6fb4141841b99
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="deploy-api-management-with-service-fabric"></a>使用 Service Fabric 部署 API 管理
本教學課程是一個系列的第二部分。 本教學課程示範如何使用 Service Fabric 來設定 [Azure API 管理](../api-management/api-management-key-concepts.md)，以將流量路由傳送至 Service Fabric 中的後端服務。  當您完成時，就已將 API 管理部署至 VNET，已設定 API 作業來將流量傳送到後端無狀態服務。 若要深入了解搭配 Service Fabric 的「Azure API 管理」案例，請參閱[概觀](service-fabric-api-management-overview.md)一文。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 部署 API 管理
> * 設定 API 管理
> * 建立 API 作業
> * 設定後端原則
> * 將 API 新增至產品

在本教學課程系列中，您將了解如何：
> [!div class="checklist"]
> * 在 Azure 上，使用範本建立安全的 [Windows 叢集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)或 [Linux 叢集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
> * 使用 Service Fabric 部署 API 管理

## <a name="prerequisites"></a>必要條件
開始進行本教學課程之前：
- 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 安裝 [Azure PowerShell 模組 4.1 版或更新版本](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)或 [Azure CLI 2.0](/cli/azure/install-azure-cli)。
- 在 Azure 上，建立安全的 [Windows 叢集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)或 [Linux 叢集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)

## <a name="sign-in-to-azure-and-select-your-subscription"></a>登入 Azure 帳戶並選取您的訂用帳戶
本教學課程使用 [Azure PowerShell][azure-powershell]。 開始新的 PowerShell 工作階段時，請先登入您的 Azure 帳戶並選取您的訂用帳戶，然後再執行 Azure 命令。
 
登入您的 Azure 帳戶並選取您的訂用帳戶：

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-api-management"></a>部署 API 管理
雲端應用程式通常需要前端閘道來為使用者、裝置或其他應用程式提供單一輸入點。 在 Service Fabric 中，閘道可以是任何無狀態服務 (例如 ASP.NET Core 應用程式)，或是為流量輸入設計的另一個服務 (例如，事件中樞、IoT 中樞或 Azure API 管理)。 本教學課程是使用 Azure API 管理作為 Service Fabric 應用程式閘道的簡介。 「API 管理」直接與 Service Fabric 整合，可讓您將具有一組豐富路由規則的 API 發佈至後端 Service Fabric 服務。 

現在您在 Azure 上已具有安全的 [Windows 叢集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)或 [Linux 叢集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)，請將 API 管理部署至子網路中的虛擬網路 (VNET) 以及針對 API 管理指定的 NSG。 本教學課程已將 API 管理 Resource Manager 範本預先設定為使用您在先前的 [Windows 叢集教學課程](service-fabric-tutorial-create-vnet-and-windows-cluster.md)或 [Linux 叢集教學課程](service-fabric-tutorial-create-vnet-and-linux-cluster.md)中設定之 VNET、子網路及 NSG 的名稱。 

下載下列 Resource Manager 範本和參數檔：
 
- [apim.json][apim-arm]
- [apim.parameters.json][apim-parameters-arm]

填寫用於您部署之 `apim.parameters.json` 中的空白參數。

使用下列指令碼來部署用於 API 管理的 Resource Manager 範本和參數檔：

```powershell
$ResourceGroupName = "tutorialgroup"
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
```

```azurecli
ResourceGroupName="tutorialgroup"
az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="configure-api-management"></a>設定 API 管理

部署您的 API 管理與 Service Fabric 叢集之後，就可以在 API 管理中設定安全性設定和 Service Fabric 後端。 這可讓您建立將流量傳送到 Service Fabric 叢集的後端服務原則。

### <a name="configure-api-management-security"></a>設定 API 管理安全性

若要設定 Service Fabric 後端，您必須先設定「API 管理」安全性設定。 若要設定安全性設定，請移至您在 Azure 入口網站中的「API 管理」服務。

#### <a name="enable-the-api-management-rest-api"></a>啟用 API 管理 REST API

「API 管理」REST API 是目前設定後端服務的唯一方式。 第一個步驟是啟用「API 管理」REST API 並保護它。

 1. 在 API 管理服務中，選取 [安全性] 底下的 [管理 API]。
 2. 選取 [啟用 API 管理 REST API] 核取方塊。
 3. 記下**管理 API URL**，我們稍後將用它來設定 Service Fabric 後端。
 4. 選取到期日和金鑰，然後按一下靠近頁面底部的 [產生] 按鈕來產生**存取權杖**。
 5. 複製**存取權杖**並將其儲存。  我們將在後續步驟中使用此存取權杖。 請注意，這與主要金鑰和次要金鑰不同。

#### <a name="upload-a-service-fabric-client-certificate"></a>上傳 Service Fabric 用戶端憑證

「API 管理」必須使用能夠存取您叢集的用戶端憑證來向 Service Fabric 叢集進行驗證，才能探索服務。 為了簡單起見，本教學課程會使用先前建立 [Windows 叢集](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor)或 [Linux 叢集](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor)時所指定的相同憑證，此憑證預設可用來存取您的叢集。

 1. 在 API 管理服務中，選取 [安全性] 底下的 [用戶端憑證]。
 2. 按一下 [+新增] 按鈕。
 2. 選取您建立 Service Fabric 叢集時所指定叢集憑證的私密金鑰檔案 (.pfx)、為它命名，然後提供私密金鑰密碼。

> [!NOTE]
> 本教學課程會將相同的憑證用於用戶端驗證和叢集節點對節點安全性。 如果您有一個已設定來存取 Service Fabric 叢集的個別用戶端憑證，則您可以使用該憑證。

### <a name="configure-the-backend"></a>設定後端

既然已設定「API 管理」安全性，您現在便可以設定 Service Fabric 後端。 就 Service Fabric 後端而言，作為後端的是 Service Fabric 叢集，而不是特定的 Service Fabric 服務。 這可讓單一原則路由傳送到叢集內的多個服務。

此步驟需要您稍早產生的存取權杖，以及您先前上傳到 API 管理之叢集憑證的指紋。

> [!NOTE]
> 如果您在上一個步驟中針對「API 管理」使用個別的用戶端憑證，則在此步驟中，除了叢集憑證指紋之外，您還需要用戶端憑證的指紋。

請將下列 HTTP PUT 要求傳送到您稍早在啟用「API 管理」REST API 以設定 Service Fabric 後端服務時所記下的「API 管理」API URL。 當命令成功時，您應該會看到 `HTTP 201 Created` 回應。 如需有關每個欄位的詳細資訊，請參閱「API 管理」[後端 API 參考文件](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend)。

HTTP 命令和 URL：
```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
```

要求標頭：
```http
Authorization: SharedAccessSignature <your access token>
Content-Type: application/json
```

要求本文：
```http
{
    "description": "<description>",
    "url": "<fallback service name>",
    "protocol": "http",
    "resourceId": "<cluster HTTP management endpoint>",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "<cluster HTTP management endpoint>" ],
            "clientCertificateThumbprint": "<client cert thumbprint>",
            "serverCertificateThumbprints": [ "<cluster cert thumbprint>" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
```

這裡的 **url** 參數是當後端原則中未指定任何服務名稱時，您叢集內作為所有要求路由傳送目的地之服務的完整服務名稱。 如果您不打算有後援服務，則可以使用假的服務名稱 (例如 "fabric:/fake/service")。

如需有關每個欄位的詳細其他詳細資料，請參閱「API 管理」[後端 API 參考文件](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend)。

```python
#import requests library for making REST calls
import requests
import json

#specify url
url = 'https://ryanwiapimgmt.management.azure-api.net/backends/servicefabric?api-version=2016-10-10'

token = "SharedAccessSignature integration&201710140514&Lqnbei7n2Sot6doiNtxMFPUi/m9LsNa+1ZK/PdxqFl49JFWjXh1wW5Gd99r/tIOeHp6dU8UV5iZUdOPfcrm5hg=="

payload = {
    "description": "My Service Fabric backend",
    "url": "fabric:/ApiApplication/ApiWebService",
    "protocol": "http",
    "resourceId": "https://tutorialcluster.eastus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "https://tutorialcluster.eastus.cloudapp.azure.com:19080" ],
            "clientCertificateThumbprint": "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80",
            "serverCertificateThumbprints": [ "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}

headers = {'Authorization': token, "Content-Type": "application/json"}

#Call REST API
response = requests.put(url, data=json.dumps(payload), headers=headers)

#Print Response
print(response.status_code)
print(response.text)
```

## <a name="deploy-a-service-fabric-back-end-service"></a>部署 Service Fabric 後端服務

既然您已將 Service Fabric 設定為「API 管理」的後端，現在便可為 API 撰寫將流量傳送到 Service Fabric 服務的後端原則。 但是您必須先有一個在 Service Fabric 中執行的服務來接受要求。

### <a name="create-a-service-fabric-service-with-an-http-endpoint"></a>建立具有 HTTP 端點的 Service Fabric 服務

針對本教學課程，我們將使用預設的 Web API 專案範本來建立一個基本的無狀態「ASP.NET Core 可靠服務」。 這會為您的服務建立一個 HTTP 端點，而您將會透過「Azure API 管理」來公開此服務：

```
/api/values
```

請從[為您的 ASP.NET Core 開發設定開發環境](service-fabric-add-a-web-frontend.md#set-up-your-environment-for-aspnet-core)開始著手。

設定完開發環境之後，請以「系統管理員」身分啟動 Visual Studio，然後建立 ASP.NET Core 服務：

 1. 在 Visual Studio 中，選取 [檔案] -> [新增專案]。
 2. 選取 [雲端] 底下的 [Service Fabric 應用程式] 範本，然後將它命名為 **"ApiApplication"**。
 3. 選取 [ASP.NET Core] 服務範本，然後將專案命名為 **"WebApiService"**。
 4. 選取 [Web API ASP.NET Core 1.1] 專案範本。
 5. 建立專案之後，開啟 `PackageRoot\ServiceManifest.xml`，然後從端點資源組態中移除 `Port` 屬性：
 
    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    這可讓 Service Fabric 從應用程式連接埠範圍動態指定連接埠，這些是我們透過叢集 Resource Manager 範本中的「網路安全性群組」開啟的連接埠，可允許流量從「API 管理」流到 Service Fabric。
 
 6. 在 Visual Studio 中按 F5 以確認本機有提供 Web API。 

    開啟 Service Fabric Explorer，然後向下切入到特定的 ASP.NET Core 服務執行個體，以查看此服務所接聽的基底位址。 將 `/api/values` 新增到基底位址，然後在瀏覽器中開啟它。 這會叫用 Web API 範本中 ValuesController 上的 Get 方法。 它會傳回範本所提供的預設回應，也就是包含兩個字串的 JSON 陣列：

    ```json
    ["value1", "value2"]`
    ```

    這是您將透過 Azure 中的「API 管理」公開的端點。

 7. 最後，將應用程式部署至您在 Azure 中的叢集。 [使用 Visual Studio](service-fabric-publish-app-remote-cluster.md#to-publish-an-application-using-the-publish-service-fabric-application-dialog-box)，在 [應用程式] 專案上按一下滑鼠右鍵，然後選取 [發行]。 提供您的叢集端點 (例如 `mycluster.westus.cloudapp.azure.com:19000`) 以將應用程式部署至您在 Azure 中的 Service Fabric 叢集。

一個名為 `fabric:/ApiApplication/WebApiService` 的 ASP.NET Core 無狀態服務現在應該正在 Azure 中的 Service Fabric 叢集內執行。

## <a name="create-an-api-operation"></a>建立 API 作業

現在我們已經準備好在「API 管理」中建立作業，供外部用戶端用來與在 Service Fabric 叢集內執行的 ASP.NET Core 無狀態服務進行通訊。

1. 登入 Azure 入口網站，然後瀏覽至您的「API 管理」服務部署。
2. 在 API 管理服務刀鋒視窗中，選取 [API]
3. 依序按一下 [+API] 和 [空白 API] 方塊，然後在對話方塊中填入資訊，藉以新增 API：

    - **Web 服務 URL**：就 Service Fabric 後端而言，並不使用此 URL 值。 您可以在這裡輸入任何值。 本教學課程使用："http://servicefabric"。
    - **顯示名稱**：為您的 API 提供任意名稱。 本教學課程使用 "Service Fabric App"。
    - **名稱**：輸入 "service-fabric-app"。
    - **URL 配置**：選取 [HTTP]、[HTTPS] 或 [both]。 本教學課程使用 [both]。
    - **API URL 尾碼**：為 API 提供一個尾碼。 本教學課程使用 "myapp"。
 
4. 從 API 清單中選取 [Service Fabric 應用程式]，然後按一下 [+ 新增作業] 以新增前端 API 作業。 填寫值：
    
    - **URL**：選取 [GET] 並提供 API 的 URL 路徑。 本教學課程使用 "/api/values"。  根據預設，這裡指定的 URL 路徑是傳送到後端 Service Fabric 服務的 URL 路徑。 如果您在這裡使用與您服務所用相同的 URL 路徑 (在此例中為 "/api/values")，則無須進一步修改，作業即可運作。 您也可以在這裡指定與您後端 Service Fabric 服務所用不同的 URL 路徑，在此情況下，您稍後也將需要在作業原則中指定路徑重寫。
    - **顯示名稱**：為 API 提供任何名稱。 本教學課程使用 "Values"。

5. 按一下 [儲存] 。

## <a name="configure-a-backend-policy"></a>設定後端原則

後端原則會將所有項目繫結在一起。 您需在此原則中設定作為要求路由傳送目的地的後端 Service Fabric 服務。 您可以將此原則套用至任何 API 作業。 [Service Fabric 的後端組態](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService)提供下列要求路由控制： 
 - 服務執行個體選取：方法是以硬式編碼 (例如 `"fabric:/myapp/myservice"`) 或從 HTTP 要求產生 (例如 `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`) 來指定 Service Fabric 服務執行個體名稱。
 - 分割區解析：方法是使用任何 Service Fabric 資料分割配置來產生分割區索引鍵。
 - 無狀態服務的複本選取。
 - 解析重試條件：可讓您指定重新解析服務位置及重新傳送要求的條件。

針對本教學課程，請建立一個後端原則，以將要求直接路由傳送到稍早部署的 ASP.NET Core 無狀態服務：

 1. 按一下編輯圖示，然後選取 [程式碼檢視]，以選取和編輯 Values 作業的**輸入原則**。
 2. 在原則程式碼編輯器中，於輸入原則底下新增 `set-backend-service` 原則 (如下所示)，然後按一下 [儲存] 按鈕：
    
    ```xml
    <policies>
      <inbound>
        <base/>
        <set-backend-service 
           backend-id="servicefabric"
           sf-service-instance-name="fabric:/ApiApplication/WebApiService"
           sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
      </inbound>
      <backend>
        <base/>
      </backend>
      <outbound>
        <base/>
      </outbound>
    </policies>
    ```

如需完整的一組 Service Fabric 後端原則屬性，請參考 [API 管理後端文件](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService)

### <a name="add-the-api-to-a-product"></a>將 API 新增至產品 

您必須先將 API 新增至產品以將存取權授與使用者，才能呼叫該 API。 

 1. 在 API 管理服務中，選取 [產品]。
 2. 「API 管理」預設會提供兩種產品：[入門] 和 [無限制]。 請選取 [無限制] 產品。
 3. 選取 [+新增 API]。
 4. 選取您在先前步驟中建立的 `Service Fabric App` API，然後按一下 [選取] 按鈕。

### <a name="test-it"></a>進行測試

您現在可以嘗試直接從 Azure 入口網站透過「API 管理」，將要求傳送到 Service Fabric 中的後端服務。

 1. 在 API 管理服務中，選取 [API]。
 2. 在您於先前步驟中建立的 **Service Fabric 應用程式** API 中，依序選取 [測試] 索引標籤和 [Values] 作業。
 3. 按一下 [傳送] 按鈕以將測試要求傳送到後端服務。  您應該會看到如下所示的 HTTP 回應：

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Access-Control-Allow-Origin: https://apimanagement.hosting.portal.azure.net

    Access-Control-Allow-Credentials: true

    Access-Control-Expose-Headers: Transfer-Encoding,Date,Server,Vary,Ocp-Apim-Trace-Location

    Ocp-Apim-Trace-Location: https://apimgmtstuvyx3wa3oqhdbwy.blob.core.windows.net/apiinspectorcontainer/RaVVuJBQ9yxtdyH55BAsjQ2-1?sv=2015-07-08&sr=b&sig=Ab6dPyLpTGAU6TdmlEVu32DMfdCXTiKAASUlwSq3jcY%3D&se=2017-09-15T05%3A49%3A53Z&sp=r&traceId=ed9f1f4332e34883a774c34aa899b832

    Date: Thu, 14 Sep 2017 05:49:56 GMT


    [
    "value1",
    "value2"
    ]
    ```

## <a name="clean-up-resources"></a>清除資源

叢集是由叢集資源本身和其他 Azure 資源所構成。 刪除叢集及其取用之所有資源的最簡單方式，就是刪除資源群組。

登入 Azure 並選取您要移除叢集的訂用帳戶識別碼。  您可以登入[Azure 入口網站](http://portal.azure.com)找到您的訂用帳戶識別碼。 使用 [Remove-AzureRMResourceGroup Cmdlet](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup) 刪除資源群組和所有叢集資源。

```powershell
$ResourceGroupName = "tutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="tutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 部署 API 管理
> * 設定 API 管理
> * 建立 API 作業
> * 設定後端原則
> * 將 API 新增至產品


[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[network-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json
