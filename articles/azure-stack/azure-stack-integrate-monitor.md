---
title: "整合外部監視解決方案與 Azure Stack | Microsoft Docs"
description: "了解如何將 Azure Stack 與您的資料中心的外部監視解決方案整合。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: mabrigg
ms.openlocfilehash: 76499ac959b77e83494bc4f9593c20a99da5c147
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>整合外部監視解決方案與 Azure Stack

適用於：Azure Stack 整合系統

針對外部監視 Azure Stack 基礎結構，您需要監視 Azure Stack 軟體、實體電腦和實體網路交換器。 每個區域都會提供方法來擷取健康情況和警示資訊。

- Azure Stack 軟體提供以 REST 為基礎的 API 來擷取健康情況和警示。 (藉由使用儲存空間直接存取之類的軟體定義技術，儲存裝置健康情況和警示是軟體監視的一部分。)
- 實體電腦可讓健康情況和警示資訊透過基礎板管理控制器 (BMC) 提供。
- 實體網路裝置可讓健康情況和警示資訊透過 SNMP 通訊協定提供。

每個 Azure Stack 解決方案隨附硬體生命週期主機。 此主機會針對實體伺服器及網路裝置執行原始設備製造商 (OEM) 硬體廠商的監視軟體。 如有需要，您可以略過這些監視解決方案，並直接與資料中心內現有的監視解決方案整合。

> [!IMPORTANT]
> 您使用的外部監視解決方案必須是無代理程式。 您無法在 Azure Stack 元件內部安裝協力廠商代理程式。

下圖顯示整合 Azure Stack 整合系統、硬體生命週期主機、外部監視解決方案和外部票證/資料收集系統之間的流量。

![顯示 Azure Stack、監視以及票證解決方案之間流量的圖表。](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

本文說明如何將 Azure Stack 與外部監視解決方案 (例如 System Center Operations Manager 和 Nagios) 整合。 也包含如何使用 PowerShell 或 REST API 呼叫，以程式設計方式處理警示的資訊。

## <a name="integrate-with-operations-manager"></a>與 Operations Manager 整合

您可以將 Operations Manager 用於 Azure Stack 的外部監視。 Microsoft Azure Stack 適用的 System Center 管理組件可讓您使用單一 Operations Manager 執行個體來監視多個 Azure Stack 部署。 管理組件會使用健康情況資源提供者和更新資源提供者 REST API 來與 Azure Stack 進行通訊。 如果您打算略過硬體生命週期主機上執行的 OEM 監視軟體，您可以安裝廠商管理組件來監視實體伺服器。 您也可以使用 Operations Manager 網路裝置探索來監視網路交換器。

Azure Stack 的管理組件提供下列功能：

- 您可以管理多個 Azure Stack 部署。
- 支援 Azure Active Directory (Azure AD) 和 Active Directory 同盟服務 (AD FS)。
- 您可以擷取和關閉警示。
- 提供健康情況和容量儀表板。
- 包含當修補和更新 (P&U) 進行時的自動維護模式偵測。
- 包含針對部署和區域的強制更新工作。
- 您可以將自訂資訊新增至區域。
- 支援通知與報告。

您可以在[這裡](https://www.microsoft.com/en-us/download/details.aspx?id=55184)或直接從 Operations Manager 下載 Microsoft Azure Stack 適用的 System Center 管理組件和相關聯的使用者指南。

如需票證解決方案，您可以將 Operations Manager 與 System Center Service Manager 整合。 整合的產品連接器會啟用雙向通訊，可讓您在解決 Service Manager 中的服務要求之後關閉 Azure Stack 和 Operations Manager 中的警示。

下圖顯示 Azure Stack 與現有的 System Center 部署的整合。 您可以進一步使用 System Center Orchestrator 或 Service Management Automation (SMA) 來將 Service Manager 自動化，以在 Azure Stack 中執行作業。

![顯示與 OM、Service Manager 和 SMA 整合的圖表。](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>與 Nagios 整合

Nagios 監視外掛程式隨著協力廠商 Cloudbase 解決方案開發，其提供是依據寬鬆免費軟體授權 - MIT (麻省理工學院)。

外掛程式是使用 Python 撰寫，並利用健康情況資源提供者 REST API。 可提供在 Azure Stack 中擷取並關閉警示的基本功能。 類似 System Center 管理組件，它可讓您新增多個 Azure Stack 部署，以及傳送通知。

此外掛程式可與 Nagios Enterprise 和 Nagios Core 搭配使用。 您可以從 [這裡](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)下載。 下載網站也包含安裝和設定詳細資料。

### <a name="plugin-parameters"></a>外掛程式參數

使用下列參數來設定外掛程式檔案 "Azurestack_plugin.py"：

| 參數 | 說明 | 範例 |
|---------|---------|---------|
| *arm_endpoint* | Azure Resource Manager (系統管理員) 端點 |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Azure Resource Manager (系統管理員) 端點  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | 管理訂用帳戶識別碼 | 透過系統管理員入口網站或 PowerShell 擷取 |
| *User_name* | 操作員訂用帳戶使用者名稱 | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | 操作員訂用帳戶密碼 | mypassword |
| *Client_id* | 用戶端 | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Azure Stack 區域名稱 | local |
|  |  |

*提供的 PowerShell GUID 是通用的。 您可以對每個部署使用它。

## <a name="use-powershell-to-monitor-health-and-alerts"></a>使用 PowerShell 來監視健康情況和警示

如果您不是使用 Operations Manager、Nagios 或以 Nagios 基礎的解決方案，可以使用 PowerShell 來啟用廣泛的監視解決方案，以與 Azure Stack 整合。
 
1. 若要使用 PowerShell，請確定您已針對 Azure Stack 操作員環境[安裝和設定 PowerShell ](azure-stack-powershell-configure-quickstart.md)。 在可以連線 Resource Manager (系統管理員) 端點的本機電腦上安裝 PowerShell (https://adminmanagement.[region].[External_FQDN])。

2. 以 Azure Stack 操作員身分執行下列命令以連線到 Azure Stack 環境：

   ```PowerShell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```
3. 切換至隨著 PowerShell 安裝而安裝 [Azure Stack 工具](https://github.com/Azure/AzureStack-Tools)的所在目錄，例如 c:\azurestack-tools-master。 然後，切換至基礎結構目錄，然後執行下列命令以匯入基礎結構模組：

   ```PowerShell
   Import-Module .\AzureStack.Infra.psm1
    ```
4. 使用如下列範例的命令來處理警示：
   ```PowerShell
   #Retrieve all alerts
   Get-AzsAlert -location [Region]

   #Filter for active alerts
   $Active=Get-AzsAlert -location [Region] | Where {$_.State -eq "active"}
   $Active

   #Close alert
   Close-AzsAlert -location [Region] -AlertID "ID"

   #Retrieve resource provider health
   Get-AzsResourceProviderHealths -location [Region]

   #Retrieve infrastructure role instance health
   Get-AzsInfrastructureRoleHealths -location [Region]
   ```

## <a name="use-the-rest-api-to-monitor-health-and-alerts"></a>使用 REST API 來監視健康情況和警示

您可以使用 REST API 呼叫來取得警示、關閉警示，以及取得資源提供者的健康情況。

### <a name="get-alert"></a>取得警示

**要求**

此要求會取得預設提供者訂用帳戶的所有作用中和已關閉警示。 沒有要求主體。


|方法  |要求 URI  |
|---------|---------|
|GET     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts?api-version=2016-05-01"      |
|     |         |

**引數**

|引數  |說明  |
|---------|---------|
|armendpoint     |  您的 Azure Stack 環境的 Azure Resource Manager 端點，採用格式：https://adminmanagement.{RegionName}.{External FQDN}。 例如，如果外部 FQDN 是 *azurestack.external*，而區域名稱為 *local*，那麼 Resource Manager 端點為 https://adminmanagement.local.azurestack.external。       |
|subid     |   正在進行呼叫之使用者的訂用帳戶識別碼。 您僅可以透過具有預設提供者訂用帳戶權限的使用者來使用此 API 進行查詢。      |
|RegionName     |    Azure Stack 部署的區域名稱。     |
|api-version     |  用來提出此要求的通訊協定版本。 您必須使用 2016-05-01。      |
|     |         |

**回應**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/Alerts?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Active",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},

…
```

**回應詳細資料**


|  引數  |說明  |
|---------|---------|
|*id*     |      警示的唯一識別碼。   |
|*name*     |     警示的內部名稱。   |
|*type*     |     資源定義。    |
|*位置*     |       區域名稱。     |
|*「標記」*     |   資源標籤。     |
|*closedtimestamp*    |  關閉警示時的 UTC 時間。    |
|*createdtimestamp*     |     建立警示時的 UTC 時間。   |
|*description*     |    警示的描述。     |
|*faultid*     | 受影響的元件。        |
|*alertid*     |  警示的唯一識別碼。       |
|*faulttypeid*     |  發生故障元件的唯一類型。       |
|*lastupdatedtimestamp*     |   上次更新警示資訊的 UTC 時間。    |
|*healthstate*     | 整體健全情況狀態。        |
|*name*     |   特定警示的名稱。      |
|*fabricname*     |    發生錯誤元件已註冊的網狀架構名稱。   |
|*description*     |  已註冊網狀架構元件的描述。   |
|*servicetype*     |   已註冊網狀架構服務的類型。   |
|*remediation*     |   建議的補救步驟。    |
|*type*     |   警示類型。    |
|*resourceRegistrationid*    |     受影響的已註冊資源的識別碼。    |
|*resourceProviderRegistrationID*   |    受影響元件的已註冊資源提供者的識別碼。  |
|*serviceregistrationid*     |    已註冊服務的識別碼。   |
|*severity*     |     警示嚴重性。  |
|*state*     |    警示狀態。   |
|*title*     |    警示標題。   |
|*impactedresourceid*     |     受影響資源的識別碼。    |
|*ImpactedresourceDisplayName*     |     受影響資源的名稱。  |
|*closedByUserAlias*     |   關閉警示的使用者。      |

### <a name="close-alert"></a>關閉警示

**要求**

此要求會依警示的唯一識別碼關閉警示。

|方法    |要求 URI  |
|---------|---------|
|PUT     |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/Alerts/alertid?api-version=2016-05-01"    |

**引數**


|引數  |說明  |
|---------|---------|
|*armendpoint*     |   您的 Azure Stack 環境的 Resource Manager 端點，採用格式：https://adminmanagement.{RegionName}.{External FQDN}。 例如，如果外部 FQDN 是 *azurestack.external*，而區域名稱為 *local*，那麼 Resource Manager 端點為 https://adminmanagement.local.azurestack.external。      |
|*subid*     |    正在進行呼叫之使用者的訂用帳戶識別碼。 您僅可以透過具有預設提供者訂用帳戶權限的使用者來使用此 API 進行查詢。     |
|*RegionName*     |   Azure Stack 部署的區域名稱。      |
|*api-version*     |    用來提出此要求的通訊協定版本。 您必須使用 2016-05-01。     |
|*alertid*     |    警示的唯一識別碼。     |

**內文**

```json

{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"2017-08-10T20:18:58.1584868Z",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```
**回應**

```http
PUT https://adminmanagement.local.azurestack.external//subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/alerts/71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"name":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/alerts",
"location":"local",
"tags":{},
"properties":
{
"closedTimestamp":"",
"createdTimestamp":"2017-08-10T20:13:57.4398842Z",
"description":[{"text":"The infrastructure role (Updates) is experiencing issues.",
"type":"Text"}],
"faultId":"ServiceFabric:/UpdateResourceProvider/fabric:/AzurestackUpdateResourceProvider",
"alertId":"71dbd379-1d1d-42e2-8439-6190cc7aa80b",
"faultTypeId":"ServiceFabricApplicationUnhealthy",
"lastUpdatedTimestamp":"2017-08-10T20:18:58.1584868Z",
"alertProperties":
{
"healthState":"Warning",
"name":"Updates",
"fabricName":"fabric:/AzurestackUpdateResourceProvider",
"description":null,
"serviceType":"UpdateResourceProvider"},
"remediation":[{"text":"1. Navigate to the (Updates) and restart the role. 2. If after closing the alert the issue persists, please contact support.",
"type":"Text"}],
"resourceRegistrationId":null,
"resourceProviderRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"serviceRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"severity":"Warning",
"state":"Closed",
"title":"Infrastructure role is unhealthy",
"impactedResourceId":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/UpdateResourceProvider",
"impactedResourceDisplayName":"UpdateResourceProvider",
"closedByUserAlias":null
}
},
```

**回應詳細資料**


|  引數  |說明  |
|---------|---------|
|*id*     |      警示的唯一識別碼。   |
|*name*     |     警示的內部名稱。   |
|*type*     |     資源定義。    |
|*位置*     |       區域名稱。     |
|*「標記」*     |   資源標籤。     |
|*closedtimestamp*    |  關閉警示時的 UTC 時間。    |
|*createdtimestamp*     |     建立警示時的 UTC 時間。   |
|*說明*     |    警示的描述。     |
|*faultid*     | 受影響的元件。        |
|*alertid*     |  警示的唯一識別碼。       |
|*faulttypeid*     |  發生故障元件的唯一類型。       |
|*lastupdatedtimestamp*     |   上次更新警示資訊的 UTC 時間。    |
|*healthstate*     | 整體健全情況狀態。        |
|*name*     |   特定警示的名稱。      |
|*fabricname*     |    發生錯誤元件已註冊的網狀架構名稱。   |
|*description*     |  已註冊網狀架構元件的描述。   |
|*servicetype*     |   已註冊網狀架構服務的類型。   |
|*remediation*     |   建議的補救步驟。    |
|*type*     |   警示類型。    |
|*resourceRegistrationid*    |     受影響的已註冊資源的識別碼。    |
|*resourceProviderRegistrationID*   |    受影響元件的已註冊資源提供者的識別碼。  |
|*serviceregistrationid*     |    已註冊服務的識別碼。   |
|*severity*     |     警示嚴重性。  |
|*state*     |    警示狀態。   |
|*title*     |    警示標題。   |
|*impactedresourceid*     |     受影響資源的識別碼。    |
|*ImpactedresourceDisplayName*     |     受影響資源的名稱。  |
|*closedByUserAlias*     |   關閉警示的使用者。      |

### <a name="get-resource-provider-health"></a>取得資源提供者健康情況

**要求**

此要求會取得所有已註冊資源提供者的健康情況狀態。


|方法  |要求 URI  |
|---------|---------|
|GET    |   https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths?api-version=2016-05-01"   |


**引數**


|引數  |說明  |
|---------|---------|
|*armendpoint*     |    您的 Azure Stack 環境的 Resource Manager 端點，採用格式：https://adminmanagement.{RegionName}.{External FQDN}。 例如，如果外部 FQDN 是 azurestack.external，而區域名稱為 local，那麼 Resource Manager 端點為 https://adminmanagement.local.azurestack.external。     |
|*subid*     |     正在進行呼叫之使用者的訂用帳戶識別碼。 您僅可以透過具有預設提供者訂用帳戶權限的使用者來使用此 API 進行查詢。    |
|*RegionName*     |     Azure Stack 部署的區域名稱。    |
|*api-version*     |   用來提出此要求的通訊協定版本。 您必須使用 2016-05-01。      |


**回應**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths?api-version=2016-05-01
```

```json
{
"value":[
{
"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"name":"03ccf38f-f6b1-4540-9dc8ec7b6389ecca",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths",
"location":"local",
"tags":{},
"properties":{
"registrationId":"03ccf38f-f6b1-4540-9dc8-ec7b6389ecca",
"displayName":"Key Vault",
"namespace":"Microsoft.KeyVault.Admin",
"routePrefix":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local",
"serviceLocation":"local",
"infraURI":"/subscriptions/4aa97de3-6b83-4582-86e1-65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.KeyVault.Admin/locations/local/infraRoles/Key Vault",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},
"healthState":"Healthy"
}
}

…
```
**回應詳細資料**


|引數  |說明  |
|---------|---------|
|*Id*     |   警示的唯一識別碼。      |
|*name*     |  警示的內部名稱。       |
|*type*     |  資源定義。       |
|*位置*     |  區域名稱。       |
|*「標記」*     |     資源標籤。    |
|*registrationId*     |   資源提供者的唯一註冊。      |
|displayName     |資源提供者顯示名稱。        |
|*namespace*     |   資源提供者實作的 API 命名空間。       |
|*routePrefix*     |    與資源提供者互動的 URI。     |
|*serviceLocation*     |   此資源提供者所註冊的區域。      |
|*infraURI*     |   列為基礎結構角色的資源提供者的 URI。      |
|*alertSummary*     |   與資源提供者相關聯的重大和警告警示的摘要。      |
|*healthState*     |    資源提供者的健康情況狀態。     |


### <a name="get-resource-health"></a>取得資源健康情況

此要求會取得特定已註冊資源提供者的健康情況狀態。

**要求**

|方法  |要求 URI  |
|---------|---------|
|GET     |     https://{armendpoint}/subscriptions/{subId}/resourceGroups/system.{RegionName}/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/{RegionName}/serviceHealths/{RegistrationID}/resourceHealths?api-version=2016-05-01"    |

**引數**

|引數  |說明  |
|---------|---------|
|*armendpoint*     |    您的 Azure Stack 環境的 Resource Manager 端點，採用格式：https://adminmanagement.{RegionName}.{External FQDN}。 例如，如果外部 FQDN 是 azurestack.external，而區域名稱為 local，那麼 Resource Manager 端點為 https://adminmanagement.local.azurestack.external。     |
|*subid*     |正在進行呼叫之使用者的訂用帳戶識別碼。 您僅可以透過具有預設提供者訂用帳戶權限的使用者來使用此 API 進行查詢。         |
|*RegionName*     |  Azure Stack 部署的區域名稱。       |
|*api-version*     |  用來提出此要求的通訊協定版本。 您必須使用 2016-05-01。       |
|*RegistrationID* |特定的資源提供者的註冊識別碼。 |

**回應**

```http
GET https://adminmanagement.local.azurestack.external/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/03ccf38f-f6b1-4540-9dc8-ec7b6389ecca /resourceHealths?api-version=2016-05-01 HTTP/1.1
```

```json
{
"value":
[
{"id":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.InfrastructureInsights.Admin/regionHealths/local/serviceHealths/472aaaa6-3f63-43fa-a489-4fd9094e235f/resourceHealths/028c3916-ab86-4e7f-b5c2-0468e607915c",
"name":"028c3916-ab86-4e7f-b5c2-0468e607915c",
"type":"Microsoft.InfrastructureInsights.Admin/regionHealths/serviceHealths/resourceHealths",
"location":"local",
"tags":{},
"properties":
{"registrationId":"028c3916-ab86-4e7f-b5c2 0468e607915c","namespace":"Microsoft.Fabric.Admin","routePrefix":"/subscriptions/4aa97de3-6b83-4582-86e1 65a5e4d1295b/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local",
"resourceType":"infraRoles",
"resourceName":"Privileged endpoint",
"usageMetrics":[],
"resourceLocation":"local",
"resourceURI":"/subscriptions/<Subscription_ID>/resourceGroups/system.local/providers/Microsoft.Fabric.Admin/fabricLocations/local/infraRoles/Privileged endpoint",
"rpRegistrationId":"472aaaa6-3f63-43fa-a489-4fd9094e235f",
"alertSummary":{"criticalAlertCount":0,"warningAlertCount":0},"healthState":"Unknown"
}
}
…
```

**回應詳細資料**

|引數  |說明  |
|---------|---------|
|*Id*     |   警示的唯一識別碼。      |
|*name*     |  警示的內部名稱。       |
|*type*     |  資源定義。       |
|*位置*     |  區域名稱。       |
|*「標記」*     |     資源標籤。    |
|*registrationId*     |   資源提供者的唯一註冊。      |
|*resourceType*     |資源類型。        |
|*resourceName*     |   資源名稱。   |
|*usageMetrics*     |    資源的使用量計量。     |
|*resourceLocation*     |   部署所在的區域名稱。      |
|*resourceURI*     |   資源的 URI。   |
|*alertSummary*     |   重大和警告警示、健康情況狀態的摘要。     |

## <a name="next-steps"></a>後續步驟

- 如需內建的健康情況監視的相關資訊，請參閱[在 Azure Stack 中監視健康情況和警示](azure-stack-monitor-health.md)。


