---
title: "Azure Service Fabric 搭配 API 管理快速入門 | Microsoft Docs"
description: "本指南示範如何快速開始使用 Azure API 管理與 Service Fabric。"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/01/2017
ms.author: vturecek
ms.openlocfilehash: 2969834713fc7c2f1a2e281a6c988158d803dc45
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-with-azure-api-management-quick-start"></a>Service Fabric 搭配 Azure API 管理快速入門

本指南示範如何設定搭配 Service Fabric 的「Azure API 管理」，並設定您的第一個 API 作業以將流量傳送到 Service Fabric 中的後端服務。 若要深入了解搭配 Service Fabric 的「Azure API 管理」案例，請參閱[概觀](service-fabric-api-management-overview.md)一文。 

## <a name="deploy-api-management-and-service-fabric-to-azure"></a>將 API 管理與 Service Fabric 部署至 Azure

第一個步驟是將「API 管理」與 Service Fabric 叢集部署至共用「虛擬網路」中的 Azure。 這可讓「API 管理」與 Service Fabric 直接進行通訊，以便執行服務探索、服務分割區解析，以及將流量直接轉送到 Service Fabric 中的任何後端服務。

### <a name="topology"></a>拓撲

本指南會將下列拓撲部署至 Azure，其中「API 管理」與 Service Fabric 位於相同「虛擬網路」的子網路中：

 ![圖片標題][sf-apim-topology-overview]

為了快速開始使用，針對每個部署步驟都提供了 Resource Manager 範本：

 - 網路拓撲：
    - [network.json][network-arm]
    - [network.parameters.json][network-parameters-arm]
 - Service Fabric 叢集：
    - [cluster.json][cluster-arm]
    - [cluster.parameters.json][cluster-parameters-arm]
 - API 管理：
    - [apim.json][apim-arm]
    - [apim.parameters.json][apim-parameters-arm]

### <a name="sign-in-to-azure-and-select-your-subscription"></a>登入 Azure 帳戶並選取您的訂用帳戶

本指南使用 [Azure PowerShell][azure-powershell]。 開始新的 PowerShell 工作階段時，請先登入您的 Azure 帳戶並選取您的訂用帳戶，然後再執行 Azure 命令。
 
登入您的 Azure 帳戶：

```powershell
Login-AzureRmAccount
```

選取您的訂用帳戶：

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

### <a name="create-a-resource-group"></a>建立資源群組

為您的部署建立新的新資源群組。 為其命名並提供位置。

```powershell
New-AzureRmResourceGroup -Name <my-resource-group> -Location westus
```

### <a name="deploy-the-network-topology"></a>部署網路拓撲

第一個步驟是設定將作為「API 管理」與 Service Fabric 叢集部署位置的網路拓撲。 [network.json][network-arm] Resource Manager 範本已設定為建立一個含有兩個子網路和兩個「網路安全性群組」(NSG) 的「虛擬網路」(VNET)。 

[network.parameters.json][network-parameters-arm] 參數檔包含將作為「API 管理」與 Service Fabric 部署位置的子網路名稱和 NSG。 就這份指南而言，參數值無須變更。 「API 管理」與 Service Fabric Resource Manager 範本會使用這些值，因此如果您在這裡修改它們，就必須相應地在其他 Resource Manager 範本中也修改它們。 

 1. 下載下列 Resource Manager 範本和參數檔：

    - [network.json][network-arm]
    - [network.parameters.json][network-parameters-arm]

 2. 使用下列 PowerShell 命令來部署用於網路設定的 Resource Manager 範本和參數檔：

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
    ```

### <a name="deploy-the-service-fabric-cluster"></a>部署 Service Fabric 叢集

部署完網路資源之後，下一個步驟是將 Service Fabric 叢集部署至為 Service Fabric 叢集指定之子網路與 NSG 中的 VNET。 針對本教學課程，Service Fabric Resource Manager 範本已預先設定為使用您在上一個步驟中設定之 VNET、子網路及 NSG 的名稱。 

Service Fabric 叢集 Resource Manager 範本已設定為建立具有憑證安全性的安全叢集。 此憑證可用來保護您叢集的節點對節點通訊，以及管理使用者對您 Service Fabric 叢集的存取。 「API 管理」會使用此憑證來存取「Service Fabric 命名服務」以探索服務。

基於叢集安全性考量，此步驟需要您在 Key Vault 中有憑證。 如需有關使用 Key Vault 來設定安全叢集的詳細資訊，請參閱[這份關於使用 Resource Manager 在 Azure 中建立叢集的指南](service-fabric-cluster-creation-via-arm.md)

> [!NOTE]
> 您可以在除了用於存取叢集的憑證之外，再新增 Azure Active Directory 驗證。 Azure Active Directory 是建議用來管理使用者對您 Service Fabric 叢集之存取的方式，但並非完成本教學課程所需的項目。 不論是叢集的節點對節點安全性，還是「Azure API 管理」驗證，都需要憑證，後者目前不支援向 Service Fabric 後端的 Azure Active Directory 進行驗證。

 1. 下載下列 Resource Manager 範本和參數檔：
 
    - [cluster.json][cluster-arm]
    - [cluster.parameters.json][cluster-parameters-arm]

 2. 填寫用於您部署之 `cluster.parameters.json` 檔案中的空白參數，包括您叢集憑證的 [Key Vault 資訊](service-fabric-cluster-creation-via-arm.md#set-up-a-key-vault)。

 3. 使用下列 PowerShell 命令來部署 Resource Manager 範本和參數檔，以建立 Service Fabric 叢集：

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\cluster.json -TemplateParameterFile .\cluster.parameters.json -Verbose
    ```

### <a name="deploy-api-management"></a>部署 API 管理

最後，將「API 管理」部署至為「API 管理」指定之子網路和 NSG 中的 VNET。 您不需要等到 Service Fabric 叢集部署完成再部署「API 管理」。 

針對本教學課程，「API 管理」Resource Manager 範本已預先設定為使用您在上一個步驟中設定之 VNET、子網路及 NSG 的名稱。 

 1. 下載下列 Resource Manager 範本和參數檔：
 
    - [apim.json][apim-arm]
    - [apim.parameters.json][apim-parameters-arm]

 2. 填寫用於您部署之 `apim.parameters.json` 中的空白參數。

 3. 使用下列 PowerShell 命令來部署用於「API 管理」的 Resource Manager 範本和參數檔：

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
    ```

## <a name="configure-api-management"></a>設定 API 管理

部署完您的「API 管理」與 Service Fabric 叢集之後，您可以在「API 管理」中設定 Service Fabric 後端。 這可讓您建立將流量傳送到 Service Fabric 叢集的後端服務原則。

### <a name="api-management-security"></a>API 管理安全性

若要設定 Service Fabric 後端，您必須先設定「API 管理」安全性設定。 若要設定安全性設定，請移至您在 Azure 入口網站中的「API 管理」服務。

#### <a name="enable-the-api-management-rest-api"></a>啟用 API 管理 REST API

「API 管理」REST API 是目前設定後端服務的唯一方式。 第一個步驟是啟用「API 管理」REST API 並保護它。

 1. 在「API 管理」服務中，選取 [安全性] 底下的 [管理 API - 預覽]。
 2. 選取 [啟用 API 管理 REST API] 核取方塊。
 3. 記下「管理 API」URL - 這是我們稍後將用來設定 Service Fabric 後端的 URL
 4. 選取到期日和金鑰，然後按一下靠近頁面底部的 [產生] 按鈕來產生「存取權杖」。
 5. 複製該「存取權杖」並儲存它 - 我們將在接下來的步驟中使用此權杖。 請注意，這與主要金鑰和次要金鑰不同。

#### <a name="upload-a-service-fabric-client-certificate"></a>上傳 Service Fabric 用戶端憑證

「API 管理」必須使用能夠存取您叢集的用戶端憑證來向 Service Fabric 叢集進行驗證，才能探索服務。 為了簡單起見，本教學課程會使用建立 Service Fabric 叢集時所指定的相同憑證，此憑證預設即可用來存取您的叢集。

 1. 在「API 管理」服務中，選取 [安全性] 底下的 [用戶端憑證 - 預覽]。
 2. 按一下 [+ 新增] 按鈕
 2. 選取您建立 Service Fabric 叢集時所指定叢集憑證的私密金鑰檔案 (.pfx)、為它命名，然後提供私密金鑰密碼。

> [!NOTE]
> 本教學課程會將相同的憑證用於用戶端驗證和叢集節點對節點安全性。 如果您有一個已設定來存取 Service Fabric 叢集的個別用戶端憑證，則您可以使用該憑證。

### <a name="configure-the-backend"></a>設定後端

既然已設定「API 管理」安全性，您現在便可以設定 Service Fabric 後端。 就 Service Fabric 後端而言，作為後端的是 Service Fabric 叢集，而不是特定的 Service Fabric 服務。 這可讓單一原則路由傳送到叢集內的多個服務。

此步驟需要您稍早產生的存取權杖，以及您在上一個步驟中上傳到「API 管理」之叢集憑證的指紋。

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

這裡的 **url** 參數是當後端原則中未指定任何服務名稱時，您叢集內作為所有要求路由傳送目的地之服務的完整服務名稱。 如果您不打算有後援服務，則可以使用假的服務名稱 (例如 "fabric:/fake/service")。 請注意，即使是假的後援服務，**url** 也必須採用 "fabric:/app/service" 的格式。

如需有關每個欄位的詳細其他詳細資料，請參閱「API 管理」[後端 API 參考文件](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend)。

#### <a name="example"></a>範例

```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
Authorization: SharedAccessSignature 230948023984&Ld93cRGcNU6KZ4uVz7JlfTec4eX43Q9Nu8ndatOgBzs6+f559Pkf3iHX2cSge+r42pn35qGY3TitjrIl13hwcQ==
Content-Type: application/json

{
    "description": "My Service Fabric backend",
    "url": "fabric:/myapp/myservice",
    "protocol": "http",
    "resourceId": "https://your-cluster.westus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": ["https://your-cluster.westus.cloudapp.azure.com:19080"],
            "clientCertificateThumbprint": "57bc463aba3aea3a12a18f36f44154f819f0fe32",
            "serverCertificateThumbprints": ["57bc463aba3aea3a12a18f36f44154f819f0fe32"],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
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
 2. 在 [API 管理] 服務刀鋒視窗中，選取 [API - 預覽]
 3. 按一下 [空白 API] 方塊，然後在對話方塊中填入資訊：

     - **Web 服務 URL**：就 Service Fabric 後端而言，並不使用此 URL 值。 您可以在這裡輸入任何值。 針對本教學課程，請使用：`http://servicefabric`。
     - **名稱**：為您的 API 提供任何名稱。 針對本教學課程，請使用 `Service Fabric App`。
     - **URL 配置**：選取 [HTTP]、[HTTPS] 或 [both]。 針對本教學課程，請使用 `both`。
     - **API URL 尾碼**：為 API 提供一個尾碼。 針對本教學課程，請使用 `myapp`。
 
 4. 建立 API 之後，按一下 [+ 新增作業] 來新增前端 API 作業。 填寫值：
    
     - **URL**：選取 `GET` 並提供 API 的 URL 路徑。 針對本教學課程，請使用 `/api/values`。
     
       根據預設，這裡指定的 URL 路徑是傳送到後端 Service Fabric 服務的 URL 路徑。 如果您在這裡使用與您服務所用相同的 URL 路徑 (在此例中為 `/api/values`)，則無須進一步修改，作業即可運作。 您也可以在這裡指定與您後端 Service Fabric 服務所用不同的 URL 路徑，在此情況下，您稍後也將需要在作業原則中指定路徑重寫。
     - **顯示名稱**：為 API 提供任何名稱。 針對本教學課程，請使用 `Values`。

## <a name="configure-a-backend-policy"></a>設定後端原則

後端原則會將所有項目繫結在一起。 您需在此原則中設定作為要求路由傳送目的地的後端 Service Fabric 服務。 您可以將此原則套用至任何 API 作業。 [Service Fabric 的後端組態](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService)提供下列要求路由控制： 
 - 服務執行個體選取：方法是以硬式編碼 (例如 `"fabric:/myapp/myservice"`) 或從 HTTP 要求產生 (例如 `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`) 來指定 Service Fabric 服務執行個體名稱。
 - 分割區解析：方法是使用任何 Service Fabric 資料分割配置來產生分割區索引鍵。
 - 無狀態服務的複本選取。
 - 解析重試條件：可讓您指定重新解析服務位置及重新傳送要求的條件。

針對本教學課程，請建立一個後端原則，以將要求直接路由傳送到稍早部署的 ASP.NET Core 無狀態服務：

 1. 按一下編輯圖示，然後選取 [程式碼檢視]，以選取和編輯 `Values` 作業的「輸入原則」。
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

### <a name="add-the-api-to-a-product"></a>將 API 新增至產品。 

您必須先將 API 新增至產品以將存取權授與使用者，才能呼叫該 API。 

 1. 在 [API 管理] 服務中，選取 [產品 - 預覽]。
 2. 「API 管理」預設會提供兩種產品：[入門] 和 [無限制]。 請選取 [無限制] 產品。
 3. 選取 API。
 4. 按一下 [+新增] 按鈕。
 5. 選取您在先前步驟中建立的 `Service Fabric App` API，然後按一下 [選取] 按鈕。

### <a name="test-it"></a>進行測試

您現在可以嘗試直接從 Azure 入口網站透過「API 管理」，將要求傳送到 Service Fabric 中的後端服務。

 1. 在 [API 管理] 服務中，選取 [API - 預覽]。
 2. 在您於先前步驟中建立的 `Service Fabric App` API 中，選取 [測試] 索引標籤。
 3. 按一下 [傳送] 按鈕以將測試要求傳送到後端服務。

## <a name="next-steps"></a>後續步驟

此時，您應該已在 Service Fabric 與「API 管理」做好基本設定。

本教學課程針對您的 Service Fabric 叢集使用基本憑證型使用者驗證，以便讓您快速進行設定。 建議您使用 [Azure Active Directory 驗證](service-fabric-cluster-creation-via-arm.md#set-up-azure-active-directory-for-client-authentication)，以針對 Service Fabric 叢集提供更進階的使用者驗證。 

接著，[在 Azure API 管理中建立和設定進階產品設定](https://docs.microsoft.com/azure/api-management/api-management-howto-product-with-rules)，以準備好您的應用程式來應付真實世界流量。

<!-- links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json


<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-api-management-quickstart/sf-apim-topology-overview.png
