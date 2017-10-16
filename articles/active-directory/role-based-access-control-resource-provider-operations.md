---
title: "Azure Resource Manager 提供者作業 | Microsoft Docs"
description: "Microsoft Azure Resource Manager 資源提供者中可用作業的詳細資料"
services: active-directory
documentationcenter: 
author: jboeshart
manager: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2017
ms.author: jaboes
ms.openlocfilehash: 95c6a1e9cfadeb19a0ec5a53fffb15ef220bfec0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Azure Resource Manager 資源提供者作業

本文件會列出每個 Microsoft Azure Resource Manager 資源提供者可用的作業。 這些作業可用在自訂角色中，以對 Azure 中的資源提供細微的角色型存取控制 (RBAC) 權限。 請注意，這份清單並不完整，而且這些作業可能會隨著每個提供者的更新而有所新增或移除。 作業字串遵循 `Microsoft.<ProviderName>/<ChildResourceType>/<action>` 的格式。 如需完整的現行清單，請使用 `Get-AzureRmProviderOperation` (在 PowerShell 中) 或 `azure provider operations show` (在 Azure CLI 中) 來列出 Azure 資源提供者的作業。

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| 作業 | 說明 |
|---|---|
|/configuration/action|更新租用戶組態。|
|/services/action|更新租用戶中的服務執行個體。|
|/configuration/write|建立租用戶組態。|
|/configuration/read|讀取租用戶組態。|
|/services/write|建立租用戶中的服務執行個體。|
|/services/read|讀取租用戶中的服務執行個體。|
|/services/delete|刪除租用戶中的服務執行個體。|
|/services/servicemembers/action|建立服務中的服務成員執行個體。|
|/services/servicemembers/read|讀取服務中的服務成員執行個體。|
|/services/servicemembers/delete|刪除服務中的服務成員執行個體。|
|/services/servicemembers/alerts/read|讀取服務成員的警示。|
|/services/alerts/read|讀取服務的警示。|
|/services/alerts/read|讀取服務的警示。|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| 作業 | 說明 |
|---|---|
|/generateRecommendations/action|產生建議|
|/suppressions/action|建立/更新隱藏項目|
|/register/action|針對 Microsoft Advisor 註冊訂用帳戶|
|/generateRecommendations/read|取得產生建議狀態|
|/recommendations/read|讀取建議|
|/suppressions/read|取得隱藏項目|
|/suppressions/delete|刪除隱藏項目|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| 作業 | 說明 |
|---|---|
|/servers/read|擷取所指定 Analysis Server 的資訊。|
|/servers/write|建立或更新所指定的 Analysis Server。|
|/servers/delete|刪除 Analysis Server。|
|/servers/suspend/action|暫止 Analysis Server。|
|/servers/resume/action|繼續 Analysis Server。|
|/servers/checkNameAvailability<br>/action|確認給定的 Analysis Server 名稱有效，且並非使用中。|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| 作業 | 說明 |
|---|---|
|/checkNameAvailability/action|檢查所提供的服務名稱是否可用|
|/register/action|針對 Microsoft.ApiManagement 資源提供者註冊訂用帳戶|
|/unregister/action|針對 Microsoft.ApiManagement 資源提供者取消註冊訂用帳戶|
|/service/write|建立 API 管理服務的新執行個體|
|/service/read|讀取 API 管理服務執行個體的中繼資料|
|/service/delete|刪除 API 管理服務執行個體|
|/service/updatehostname/action|設定、更新或移除 API 管理服務的自訂網域名稱|
|/service/uploadcertificate/action|上傳 API 管理服務的 SSL 憑證|
|/service/backup/action|將 API 管理服務備份到使用者所提供之儲存體帳戶中的指定容器|
|/service/restore/action|從使用者所提供之儲存體帳戶中的指定容器來還原 API 管理服務|
|/service/managedeployments/action|變更 SKU/單位、新增/移除 API 管理服務的區域部署|
|/service/getssotoken/action|取得 SSO 權杖，以供用來登入 API 管理服務舊版入口網站 (登入身分為系統管理員)|
|/service/applynetworkconfigurationupdates/action|更新虛擬網路中執行的 Microsoft.ApiManagement 資源，以挑選更新後的網路設定。|
|/service/operationresults/read|取得長時間執行之作業的目前狀態|
|/service/networkStatus/read|取得資源的網路存取狀態。|
|/service/loggers/read|取得記錄器的清單，或取得記錄器的詳細資料|
|/service/loggers/write|新增記錄器，或更新現有的記錄器詳細資料|
|/service/loggers/delete|移除現有的記錄器|
|/service/users/read|取得已註冊的使用者清單，或取得使用者的帳戶詳細資料|
|/service/users/write|註冊新的使用者，或更新現有使用者的帳戶詳細資料|
|/service/users/delete|移除使用者帳戶|
|/service/users/generateSsoUrl/action|產生 SSO URL。 URL 可用來存取管理入口網站|
|/service/users/subscriptions/read|取得使用者訂用帳戶的清單|
|/service/users/keys/read|取得使用者金鑰的清單|
|/service/users/groups/read|取得使用者群組的清單|
|/service/tenant/operationResults/read|取得作業結果的清單，或取得特定作業的結果|
|/service/tenant/policy/read|取得租用戶的原則組態|
|/service/tenant/policy/write|設定租用戶的原則組態|
|/service/tenant/policy/delete|移除租用戶的原則組態|
|/service/tenant/configuration/save/action|在存放庫的指定分支中建立具有組態快照集的認可|
|/service/tenant/configuration/deploy/action|執行部署工作，以將所指定 git 分支的變更套用至資料庫中的組態。|
|/service/tenant/configuration/validate/action|驗證所指定 git 分支的變更|
|/service/tenant/configuration/operationResults/read|取得作業結果的清單，或取得特定作業的結果|
|/service/tenant/configuration/syncState/read|取得上次 git 同步處理的狀態|
|/service/tenant/access/read|取得租用戶存取資訊的詳細資料|
|/service/tenant/access/write|更新租用戶存取資訊的詳細資料|
|/service/tenant/access/regeneratePrimaryKey/action|重新產生主要存取金鑰|
|/service/tenant/access/regenerateSecondaryKey/action|重新產生次要存取金鑰|
|/service/identityProviders/read|取得識別提供者清單，或取得識別提供者的詳細資料|
|/service/identityProviders/write|建立新的識別提供者，或更新現有識別提供者的詳細資料|
|/service/identityProviders/delete|移除現有的識別提供者|
|/service/subscriptions/read|取得產品訂用帳戶清單，或取得產品訂用帳戶的詳細資料|
|/service/subscriptions/write|讓現有使用者訂閱現有產品，或更新現有的訂用帳戶詳細資料。 這項作業可用來續訂訂用帳戶|
|/service/subscriptions/delete|刪除訂用帳戶。 這項作業可用來刪除訂用帳戶|
|/service/subscriptions/regeneratePrimaryKey/action|重新產生訂用帳戶的主要金鑰|
|/service/subscriptions/regenerateSecondaryKey/action|重新產生訂用帳戶的次要金鑰|
|/service/backends/read|取得後端清單，或取得後端的詳細資料|
|/service/backends/write|新增後端，或更新現有的後端詳細資料|
|/service/backends/delete|移除現有後端|
|/service/apis/read|取得所有已註冊之 API 的清單，或取得 API 的詳細資料|
|/service/apis/write|建立新的 API，或更新現有 API 的詳細資料|
|/service/apis/delete|移除現有 API|
|/service/apis/policy/read|取得 API 的原則組態詳細資料|
|/service/apis/policy/write|設定 API 的原則組態詳細資料|
|/service/apis/policy/delete|從 API 中移除原則組態|
|/service/apis/operations/read|取得現有 API 作業的清單，或取得 API 作業的詳細資料|
|/service/apis/operations/write|建立新的 API 作業，或更新現有的 API 作業|
|/service/apis/operations/delete|移除現有的 API 作業|
|/service/apis/operations/policy/read|取得作業的原則組態詳細資料|
|/service/apis/operations/policy/write|設定作業的原則組態詳細資料|
|/service/apis/operations/policy/delete|從作業中移除原則組態|
|/service/products/read|取得產品清單，或取得產品的詳細資料|
|/service/products/write|建立新的產品，或更新現有產品的詳細資料|
|/service/products/delete|移除現有產品|
|/service/products/subscriptions/read|取得產品訂用帳戶的清單|
|/service/products/apis/read|取得新增至現有產品之 API 的清單|
|/service/products/apis/write|將現有 API 新增至現有產品|
|/service/products/apis/delete|從現有產品中移除現有 API|
|/service/products/policy/read|取得現有產品的原則組態|
|/service/products/policy/write|設定現有產品的原則組態|
|/service/products/policy/delete|從現有產品中移除原則組態|
|/service/products/groups/read|取得與產品相關聯的開發人員群組清單|
|/service/products/groups/write|將現有開發人員群組與現有產品相關聯|
|/service/products/groups/delete|刪除現有開發人員群組與現有產品的關聯|
|/service/openidConnectProviders/read|取得 OpenID Connect 提供者的清單，或取得 OpenID Connect 提供者的詳細資料|
|/service/openidConnectProviders/write|建立新的 OpenID Connect 提供者，或更新現有 OpenID Connect 提供者的詳細資料|
|/service/openidConnectProviders/delete|移除現有的 OpenID Connect 提供者|
|/service/certificates/read|取得憑證清單，或取得憑證的詳細資料|
|/service/certificates/write|新增憑證|
|/service/certificates/delete|移除現有憑證|
|/service/properties/read|取得所有屬性的清單，或取得指定之屬性的詳細資料|
|/service/properties/write|建立新的屬性，或更新指定之屬性的值|
|/service/properties/delete|移除現有屬性|
|/service/groups/read|取得群組清單，或取得群組的詳細資料|
|/service/groups/write|建立新的群組，或更新現有群組的詳細資料|
|/service/groups/delete|移除現有群組|
|/service/groups/users/read|取得群組使用者的清單|
|/service/groups/users/write|將現有使用者新增至現有群組|
|/service/groups/users/delete|從現有群組移除現有使用者|
|/service/authorizationServers/read|取得授權伺服器的清單，或取得授權伺服器的詳細資料|
|/service/authorizationServers/write|建立新的授權伺服器，或更新現有授權伺服器的詳細資料|
|/service/authorizationServers/delete|移除現有的授權伺服器|
|/service/reports/bySubscription/read|取得依訂用帳戶來彙總的報告。|
|/service/reports/byRequest/read|取得報告資料的要求|
|/service/reports/byOperation/read|取得依作業來彙總的報告|
|/service/reports/byGeo/read|取得依地理區域來彙總的報告|
|/service/reports/byUser/read|取得依開發人員來彙總的報告。|
|/service/reports/byTime/read|取得依時間週期來彙總的報告|
|/service/reports/byApi/read|取得依 API 來彙總的報告|
|/service/reports/byProduct/read|取得依產品來彙總的報告。|

## <a name="microsoftappservice"></a>Microsoft.AppService

| 作業 | 說明 |
|---|---|
|/appidentities/Read|傳回已向閘道器註冊的資源 (網站)。|
|/appidentities/Write|建立新的應用程式身分識別。|
|/appidentities/Delete|刪除現有的應用程式身分識別。|
|/deploymenttemplates/listMetadata/Action|列出與 API 應用程式套件相關聯的 UI 中繼資料。|
|/deploymenttemplates/generate/Action|傳回用來佈建 API 應用程式執行個體的部署範本。|
|/gateways/Read|傳回閘道器執行個體。|
|/gateways/Write|建立新的閘道器，或更新現有閘道器。|
|/gateways/Delete|刪除現有的閘道器執行個體。|
|/gateways/listLoginUris/Action|填入權杖存放區，並傳回 OAuth 登入 URI。|
|/gateways/listKeys/Action|傳回閘道器祕密。|
|/gateways/tokens/Write|使用給定名稱來建立新的 Zumo 權杖。|
|/gateways/registrations/Read|傳回已向閘道器註冊的資源 (網站)。|
|/gateways/registrations/Write|向閘道器註冊資源 (網站)。|
|/gateways/registrations/Delete|從閘道器取消註冊資源 (網站)。|
|/apiapps/Read|傳回 API 應用程式執行個體。|
|/apiapps/Write|建立新的 API 應用程式，或更新現有 API 應用程式。|
|/apiapps/Delete|刪除現有的 API 應用程式執行個體。|
|/apiapps/listStatus/Action|傳回 API 應用程式的狀態。|
|/apiapps/listKeys/Action|傳回 API 應用程式的祕密。|
|/apiapps/apidefinitions/Read|傳回 API 應用程式的 API 定義。|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| 作業 | 說明 |
|---|---|
|/elevateAccess/action|對呼叫者授與租用戶範圍的使用者存取系統管理員存取權|
|/classicAdministrators/read|讀取訂用帳戶的系統管理員。|
|/classicAdministrators/write|對訂用帳戶新增或修改系統管理員。|
|/classicAdministrators/delete|從訂用帳戶中移除系統管理員。|
|/locks/read|取得指定範圍的鎖定。|
|/locks/write|新增指定範圍的鎖定。|
|/locks/delete|刪除指定範圍的鎖定。|
|/policyAssignments/read|取得關於原則指派的資訊。|
|/policyAssignments/write|建立指定範圍的原則指派。|
|/policyAssignments/delete|刪除指定範圍的原則指派。|
|/permissions/read|列出呼叫者在給定範圍內所具有的所有權限。|
|/roleDefinitions/read|取得關於角色定義的資訊。|
|/roleDefinitions/write|以指定權限和可指派的範圍來建立或更新自訂角色定義。|
|/roleDefinitions/delete|刪除指定的自訂角色定義。|
|/providerOperations/read|針對所有資源提供者取得可在角色定義中使用的作業。|
|/policyDefinitions/read|取得關於原則定義的資訊。|
|/policyDefinitions/write|建立自訂的原則定義。|
|/policyDefinitions/delete|刪除原則定義。|
|/roleAssignments/read|取得關於角色指派的資訊。|
|/roleAssignments/write|建立指定範圍的角色指派。|
|/roleAssignments/delete|刪除指定範圍內的角色指派。|

## <a name="microsoftautomation"></a>Microsoft.Automation

| 作業 | 說明 |
|---|---|
|/automationAccounts/read|取得 Azure 自動化帳戶|
|/automationAccounts/write|建立或更新 Azure 自動化帳戶|
|/automationAccounts/delete|刪除 Azure 自動化帳戶|
|/automationAccounts/configurations/readContent/action|取得 Azure Automation DSC 的內容|
|/automationAccounts/hybridRunbookWorkerGroups/read|讀取混合式 Runbook 背景工作角色資源|
|/automationAccounts/hybridRunbookWorkerGroups/delete|刪除混合式 Runbook 背景工作角色資源|
|/automationAccounts/jobSchedules/read|取得 Azure 自動化作業排程|
|/automationAccounts/jobSchedules/write|建立 Azure 自動化作業排程|
|/automationAccounts/jobSchedules/delete|刪除 Azure 自動化作業排程|
|/automationAccounts/connectionTypes/read|取得 Azure 自動化連線類型資產|
|/automationAccounts/connectionTypes/write|建立 Azure 自動化連線類型資產|
|/automationAccounts/connectionTypes/delete|刪除 Azure 自動化連線類型資產|
|/automationAccounts/modules/read|取得 Azure 自動化模組|
|/automationAccounts/modules/write|建立或更新 Azure 自動化模組|
|/automationAccounts/modules/delete|刪除 Azure 自動化模組|
|/automationAccounts/credentials/read|取得 Azure 自動化認證資產|
|/automationAccounts/credentials/write|建立或更新 Azure 自動化認證資產|
|/automationAccounts/credentials/delete|刪除 Azure 自動化認證資產|
|/automationAccounts/certificates/read|取得 Azure 自動化憑證資產|
|/automationAccounts/certificates/write|建立或更新 Azure 自動化憑證資產|
|/automationAccounts/certificates/delete|刪除 Azure 自動化憑證資產|
|/automationAccounts/schedules/read|取得 Azure 自動化排程資產|
|/automationAccounts/schedules/write|建立或更新 Azure 自動化排程資產|
|/automationAccounts/schedules/delete|刪除 Azure 自動化排程資產|
|/automationAccounts/jobs/read|取得 Azure 自動化作業|
|/automationAccounts/jobs/write|建立 Azure 自動化作業|
|/automationAccounts/jobs/stop/action|停止 Azure 自動化作業|
|/automationAccounts/jobs/suspend/action|暫止 Azure 自動化作業|
|/automationAccounts/jobs/resume/action|繼續 Azure 自動化作業|
|/automationAccounts/jobs/runbookContent/action|在作業執行時取得 Azure 自動化 Runbook 的內容|
|/automationAccounts/jobs/output/action|取得作業的輸出|
|/automationAccounts/jobs/read|取得 Azure 自動化作業|
|/automationAccounts/jobs/write|建立 Azure 自動化作業|
|/automationAccounts/jobs/stop/action|停止 Azure 自動化作業|
|/automationAccounts/jobs/suspend/action|暫止 Azure 自動化作業|
|/automationAccounts/jobs/resume/action|繼續 Azure 自動化作業|
|/automationAccounts/jobs/streams/read|取得 Azure 自動化作業串流|
|/automationAccounts/connections/read|取得 Azure 自動化連線資產|
|/automationAccounts/connections/write|建立或更新 Azure 自動化連線資產|
|/automationAccounts/connections/delete|刪除 Azure 自動化連線資產|
|/automationAccounts/variables/read|讀取 Azure 自動化變數資產|
|/automationAccounts/variables/write|建立或更新 Azure 自動化變數資產|
|/automationAccounts/variables/delete|刪除 Azure 自動化變數資產|
|/automationAccounts/runbooks/readContent/action|取得 Azure 自動化 Runbook 的內容|
|/automationAccounts/runbooks/read|取得 Azure 自動化 Runbook|
|/automationAccounts/runbooks/write|建立或更新 Azure 自動化 Runbook|
|/automationAccounts/runbooks/delete|刪除 Azure 自動化 Runbook|
|/automationAccounts/runbooks/draft/readContent/action|取得 Azure 自動化 Runbook 草稿的內容|
|/automationAccounts/runbooks/draft/writeContent/action|建立 Azure 自動化 Runbook 草稿的內容|
|/automationAccounts/runbooks/draft/read|取得 Azure 自動化 Runbook 草稿|
|/automationAccounts/runbooks/draft/publish/action|發佈 Azure 自動化 Runbook 草稿|
|/automationAccounts/runbooks/draft/undoEdit/action|復原對 Azure 自動化 Runbook 草稿所做的編輯|
|/automationAccounts/runbooks/draft/testJob/read|取得 Azure 自動化 Runbook 草稿測試作業|
|/automationAccounts/runbooks/draft/testJob/write|建立 Azure 自動化 Runbook 草稿測試作業|
|/automationAccounts/runbooks/draft/testJob/stop/action|停止 Azure 自動化 Runbook 草稿測試作業|
|/automationAccounts/runbooks/draft/testJob/suspend/action|暫止 Azure 自動化 Runbook 草稿測試作業|
|/automationAccounts/runbooks/draft/testJob/resume/action|繼續 Azure 自動化 Runbook 草稿測試作業|
|/automationAccounts/webhooks/read|讀取 Azure 自動化 Webhook|
|/automationAccounts/webhooks/write|建立或更新 Azure 自動化 Webhook|
|/automationAccounts/webhooks/delete|刪除 Azure 自動化 Webhook |
|/automationAccounts/webhooks/generateUri/action|產生 Azure 自動化 Webhook 的 URI|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

此提供者不是完整的 ARM 提供者，因此不會提供任何 ARM 作業。

## <a name="microsoftbatch"></a>Microsoft.Batch

| 作業 | 說明 |
|---|---|
|/register/action|針對 Batch 資源提供者註冊訂用帳戶，並讓您能夠建立 Batch 帳戶|
|/batchAccounts/write|建立新的 Batch 帳戶，或更新現有的 Batch 帳戶|
|/batchAccounts/read|列出 Batch 帳戶，或取得 Batch 帳戶的屬性|
|/batchAccounts/delete|刪除 Batch 帳戶|
|/batchAccounts/listkeys/action|列出 Batch 帳戶的存取金鑰|
|/batchAccounts/regeneratekeys/action|重新產生 Batch 帳戶的存取金鑰|
|/batchAccounts/syncAutoStorageKeys/action|針對為 Batch 帳戶所設定的自動儲存體帳戶同步處理存取金鑰|
|/batchAccounts/applications/read|列出應用程式，或取得應用程式的屬性|
|/batchAccounts/applications/write|建立新的應用程式，或更新現有應用程式|
|/batchAccounts/applications/delete|刪除應用程式|
|/batchAccounts/applications/versions/read|取得應用程式套件的屬性|
|/batchAccounts/applications/versions/write|建立新的應用程式套件，或更新現有的應用程式套件|
|/batchAccounts/applications/versions/activate/action|啟動應用程式套件|
|/batchAccounts/applications/versions/delete|刪除應用程式套件|
|/locations/quotas/read|取得指定訂用帳戶在指定 Azure 區域內的 Batch 配額|

## <a name="microsoftbilling"></a>Microsoft.Billing

| 作業 | 說明 |
|---|---|
|/invoices/read|列出可用的發票|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| 作業 | 說明 |
|---|---|
|/mapApis/Read|讀取作業|
|/mapApis/Write|寫入作業|
|/mapApis/Delete|刪除作業|
|/mapApis/regenerateKey/action|重新產生金鑰|
|/mapApis/listSecrets/action|列出祕密|
|/mapApis/listSingleSignOnToken/action|讀取資源的單一登入授權權杖|
|/Operations/read|作業的描述。|

## <a name="microsoftcache"></a>Microsoft.Cache

| 作業 | 說明 |
|---|---|
|/checknameavailability/action|檢查名稱是否可用於新的 Redis 快取|
|/register/action|向訂用帳戶註冊 'Microsoft.Cache' 資源提供者|
|/unregister/action|向訂用帳戶取消註冊 'Microsoft.Cache' 資源提供者|
|/redis/write|在管理入口網站中修改 Redis 快取的設定和組態|
|/redis/read|在管理入口網站中檢視 Redis 快取的設定和組態|
|/redis/delete|刪除整個 Redis 快取|
|/redis/listKeys/action|在管理入口網站中檢視 Redis 快取存取金鑰的值|
|/redis/regenerateKey/action|在管理入口網站中變更 Redis 快取存取金鑰的值|
|/redis/import/action|從多個 Blob 將指定格式的資料匯入 Redis|
|/redis/export/action|以指定格式將 Redis 資料匯出至有前置詞的儲存體 Blob|
|/redis/forceReboot/action|強制重新啟動快取執行個體，但可能會造成資料遺失。|
|/redis/stop/action|停止快取執行個體。|
|/redis/start/action|啟動快取執行個體。|
|/redis/metricDefinitions/read|取得 Redis 快取可用的計量|
|/redis/firewallRules/read|取得 Redis 快取的 IP 防火牆規則|
|/redis/firewallRules/write|編輯 Redis 快取的 IP 防火牆規則|
|/redis/firewallRules/delete|刪除 Redis 快取的 IP 防火牆規則|
|/redis/listUpgradeNotifications/read|列出快取租用戶的最新升級通知。|
|/redis/linkedservers/read|取得與 Redis 快取相關聯的連結伺服器。|
|/redis/linkedservers/write|對 Redis 快取新增連結伺服器|
|/redis/linkedservers/delete|從 Redis 快取中刪除連結伺服器|
|/redis/patchSchedules/read|取得 Redis 快取的修補排程|
|/redis/patchSchedules/write|修改 Redis 快取的修補排程|
|/redis/patchSchedules/delete|刪除 Redis 快取的修補排程|

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| 作業 | 說明 |
|---|---|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|為服務應用程式主體佈建服務主體|
|/validateCertificateRegistrationInformation/Action|驗證憑證購買物件，但不將其提交出去|
|/register/action|針對訂用帳戶註冊 Microsoft 憑證資源提供者|
|/certificateOrders/Write|新增 certificateOrder 或更新現有 certificateOrder|
|/certificateOrders/Delete|刪除現有的 AppServiceCertificate|
|/certificateOrders/Read|取得 CertificateOrders 的清單|
|/certificateOrders/reissue/Action|重新發行現有的 certificateorder|
|/certificateOrders/renew/Action|更新現有的 certificateorder|
|/certificateOrders/retrieveCertificateActions/Action|擷取憑證動作的清單|
|/certificateOrders/retrieveEmailHistory/Action|擷取憑證電子郵件的歷程記錄|
|/certificateOrders/resendEmail/Action|重新傳送憑證電子郵件|
|/certificateOrders/verifyDomainOwnership/Action|確認網域擁有權|
|/certificateOrders/resendRequestEmails/Action|將要求電子郵件重新傳送至其他電子郵件地址|
|/certificateOrders/resendRequestEmails/Action|擷取已發行之 App Service 憑證的網站密封|
|/certificateOrders/certificates/Write|新增憑證或更新現有憑證|
|/certificateOrders/certificates/Delete|刪除現有憑證|
|/certificateOrders/certificates/Read|取得憑證清單|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| 作業 | 說明 |
|---|---|
|/register/action|傳統計算的暫存器|
|/checkDomainNameAvailability/action|檢查給定網域名稱的可用性。|
|/moveSubscriptionResources/action|將所有傳統資源移動到不同訂用帳戶。|
|/validateSubscriptionMoveAvailability/action|驗證訂用帳戶是否可進行傳統移動作業。|
|/operatingSystemFamilies/read|列出 Microsoft Azure 中可用的客體作業系統系列，並且也會列出適用於每個 f 的作業系統版本
|/capabilities/read|顯示功能|
|/operatingSystems/read|列出 Microsoft Azure 中目前可用的客體作業系統版本。|
|/resourceTypes/skus/read|取得受支援之資源類型的 SKU 清單。|
|/domainNames/read|傳回資源的網域名稱。|
|/domainNames/write|新增或修改資源的網域名稱。|
|/domainNames/delete|移除資源的網域名稱。|
|/domainNames/swap/action|將預備位置切換到生產位置。|
|/domainNames/serviceCertificates/read|傳回所使用的服務憑證。|
|/domainNames/serviceCertificates/write|新增或修改所使用的服務憑證。|
|/domainNames/serviceCertificates/delete|刪除所使用的服務憑證。|
|/domainNames/serviceCertificates/operationStatuses/read|讀取網域名稱服務憑證的作業狀態。|
|/domainNames/capabilities/read|顯示網域名稱功能|
|/domainNames/extensions/read|傳回網域名稱副檔名。|
|/domainNames/extensions/write|新增網域名稱副檔名。|
|/domainNames/extensions/delete|移除網域名稱副檔名。|
|/domainNames/extensions/operationStatuses/read|讀取網域名稱副檔名的作業狀態。|
|/domainNames/active/write|設定作用中的網域名稱。|
|/domainNames/slots/read|顯示部署位置。|
|/domainNames/slots/write|建立或更新部署。|
|/domainNames/slots/delete|刪除給定的部署位置。|
|/domainNames/slots/start/action|啟動部署位置。|
|/domainNames/slots/stop/action|暫止部署位置。|
|/domainNames/slots/operationStatuses/read|讀取網域名稱位置的作業狀態。|
|/domainNames/slots/roles/read|取得部署位置的角色。|
|/domainNames/slots/roles/extensionReferences/read|傳回部署位置角色的擴充功能參考。|
|/domainNames/slots/roles/extensionReferences/write|新增或修改部署位置角色的擴充功能參考。|
|/domainNames/slots/roles/extensionReferences/delete|移除部署位置角色的擴充功能參考。|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|讀取網域名稱位置角色擴充功能參考的作業狀態。|
|/domainNames/slots/roles/roleInstances/read|取得角色執行個體。|
|/domainNames/slots/roles/roleInstances/restart/action|重新啟動角色執行個體。|
|/domainNames/slots/roles/roleInstances/reimage/action|針對角色執行個體重新安裝映像。|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|讀取網域名稱位置角色之角色執行個體的作業狀態。|
|/domainNames/slots/state/start/write|將部署位置狀態變更為停止。|
|/domainNames/slots/state/stop/write|將部署位置狀態變更為啟動。|
|/domainNames/slots/upgradeDomain/write|逐步升級網域。|
|/domainNames/internalLoadBalancers/read|取得內部負載平衡器。|
|/domainNames/internalLoadBalancers/write|建立新的內部負載平衡。|
|/domainNames/internalLoadBalancers/delete|移除新的內部負載平衡。|
|/domainNames/internalLoadBalancers/operationStatuses/read|讀取網域名稱內部負載平衡器的作業狀態。|
|/domainNames/loadBalancedEndpointSets/read|顯示已負載平衡的端點集|
|/domainNames/loadBalancedEndpointSets/operationStatuses/read|讀取網域名稱之已負載平衡端點集的作業狀態。|
|/domainNames/availabilitySets/read|顯示資源的可用性設定組。|
|/quotas/read|取得訂用帳戶配額。|
|/virtualMachines/read|擷取虛擬機器清單。|
|/virtualMachines/write|新增或修改虛擬機器。|
|/virtualMachines/delete|移除虛擬機器。|
|/virtualMachines/start/action|啟動虛擬機器。|
|/virtualMachines/redeploy/action|重新部署虛擬機器。|
|/virtualMachines/restart/action|重新啟動虛擬機器。|
|/virtualMachines/stop/action|停止虛擬機器。|
|/virtualMachines/shutdown/action|關閉虛擬機器。|
|/virtualMachines/attachDisk/action|將資料磁碟連結至虛擬機器。|
|/virtualMachines/detachDisk/action|中斷資料磁碟對虛擬機器的連結。|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|下載虛擬機器的 RDP 檔案。|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/read|取得與網路介面相關聯的網路安全性群組。|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/write|新增與網路介面相關聯的網路安全性群組。|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/delete|刪除與網路介面相關聯的網路安全性群組。|
|/virtualMachines/networkInterfaces/<br>associatedNetworkSecurityGroups/operationStatuses/read|讀取與網路安全性群組相關聯之虛擬機器的作業狀態。|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|取得計量定義。|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|取得診斷設定。|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|新增或修改診斷設定。|
|/virtualMachines/metrics/read|取得計量。|
|/virtualMachines/operationStatuses/read|讀取虛擬機器的作業狀態。|
|/virtualMachines/extensions/read|取得虛擬機器擴充功能。|
|/virtualMachines/extensions/write|放置虛擬機器擴充功能。|
|/virtualMachines/extensions/operationStatuses/read|讀取虛擬機器擴充功能的作業狀態。|
|/virtualMachines/asyncOperations/read|取得可能的非同步作業|
|/virtualMachines/disks/read|擷取資料磁碟清單|
|/virtualMachines/associatedNetworkSecurityGroups/read|取得與虛擬機器相關聯的網路安全性群組。|
|/virtualMachines/associatedNetworkSecurityGroups/write|新增與虛擬機器相關聯的網路安全性群組。|
|/virtualMachines/associatedNetworkSecurityGroups/delete|刪除與虛擬機器相關聯的網路安全性群組。|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|讀取與網路安全性群組相關聯之虛擬機器的作業狀態。|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| 作業 | 說明 |
|---|---|
|/register/action|傳統網路的暫存器|
|/gatewaySupportedDevices/read|擷取支援裝置清單。|
|/reservedIps/read|取得保留的 IP|
|/reservedIps/write|新增保留的 IP|
|/reservedIps/delete|刪除保留的 IP。|
|/reservedIps/link/action|連結保留的 IP|
|/reservedIps/join/action|加入保留的 IP|
|/reservedIps/operationStatuses/read|讀取保留之 IP 的作業狀態。|
|/virtualNetworks/read|取得虛擬網路。|
|/virtualNetworks/write|新增虛擬網路。|
|/virtualNetworks/delete|刪除虛擬網路。|
|/virtualNetworks/peer/action|讓某個虛擬網路與另一個虛擬網路對等互連。|
|/virtualNetworks/join/action|加入虛擬網路。|
|/virtualNetworks/checkIPAddressAvailability/action|檢查虛擬網路中給定 IP 位址的可用性。|
|/virtualNetworks/capabilities/read|顯示功能|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/read|取得與子網路相關聯的網路安全性群組。|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/write|新增與子網路相關聯的網路安全性群組。|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/delete|刪除與子網路相關聯的網路安全性群組。|
|/virtualNetworks/subnets/<br>associatedNetworkSecurityGroups/operationStatuses/read|讀取與網路安全性群組相關聯之虛擬網路子網路的作業狀態。|
|/virtualNetworks/operationStatuses/read|讀取虛擬網路的作業狀態。|
|/virtualNetworks/gateways/read|取得虛擬網路閘道。|
|/virtualNetworks/gateways/write|新增虛擬網路閘道。|
|/virtualNetworks/gateways/delete|刪除虛擬網路閘道。|
|/virtualNetworks/gateways/startDiagnostics/action|啟動虛擬網路閘道的診斷。|
|/virtualNetworks/gateways/stopDiagnostics/action|停止虛擬網路閘道的診斷。|
|/virtualNetworks/gateways/downloadDiagnostics/action|下載閘道診斷。|
|/virtualNetworks/gateways/listCircuitServiceKey/action|擷取迴路服務金鑰。|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|下載裝置組態指令碼。|
|/virtualNetworks/gateways/listPackage/action|列出虛擬網路閘道套件。|
|/virtualNetworks/gateways/operationStatuses/read|讀取虛擬網路閘道的作業狀態。|
|/virtualNetworks/gateways/packages/read|取得虛擬網路閘道套件。|
|/virtualNetworks/gateways/connections/read|擷取連線清單。|
|/virtualNetworks/gateways/connections/connect/action|建立網站對網站閘道連線。|
|/virtualNetworks/gateways/connections/disconnect/action|中斷網站對網站閘道連線。|
|/virtualNetworks/gateways/connections/test/action|測試網站對網站閘道連線。|
|/virtualNetworks/gateways/clientRevokedCertificates/read|讀取已撤銷的用戶端憑證。|
|/virtualNetworks/gateways/clientRevokedCertificates/write|撤銷用戶端憑證。|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|取消撤銷用戶端憑證。|
|/virtualNetworks/gateways/clientRootCertificates/read|尋找用戶端根憑證。|
|/virtualNetworks/gateways/clientRootCertificates/write|上傳新的用戶端根憑證。|
|/virtualNetworks/gateways/clientRootCertificates/delete|刪除虛擬網路閘道用戶端憑證。|
|/virtualNetworks/gateways/clientRootCertificates/download/action|依指紋下載憑證。|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|列出虛擬網路閘道憑證套件。|
|/networkSecurityGroups/read|取得網路安全性群組。|
|/networkSecurityGroups/write|新增網路安全性群組。|
|/networkSecurityGroups/delete|刪除網路安全性群組。|
|/networkSecurityGroups/operationStatuses/read|讀取網路安全性群組的作業狀態。|
|/networkSecurityGroups/securityRules/read|取得安全性規則。|
|/networkSecurityGroups/securityRules/write|新增或更新安全性規則。|
|/networkSecurityGroups/securityRules/delete|刪除安全性規則。|
|/networkSecurityGroups/securityRules/operationStatuses/read|讀取網路安全性群組安全性規則的作業狀態。|
|/quotas/read|取得訂用帳戶配額。|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| 作業 | 說明 |
|---|---|
|/register/action|傳統儲存體的暫存器|
|/checkStorageAccountAvailability/action|檢查儲存體帳戶的可用性。|
|/capabilities/read|顯示功能|
|/publicImages/read|取得公用虛擬機器映像。|
|/images/read|傳回映像。|
|/storageAccounts/read|傳回具有給定帳戶的儲存體帳戶。|
|/storageAccounts/write|新增儲存體帳戶。|
|/storageAccounts/delete|刪除儲存體帳戶。|
|/storageAccounts/listKeys/action|列出儲存體帳戶的存取金鑰。|
|/storageAccounts/regenerateKey/action|重新產生儲存體帳戶的現有存取金鑰。|
|/storageAccounts/operationStatuses/read|讀取資源的作業狀態。|
|/storageAccounts/images/read|傳回儲存體帳戶映像。|
|/storageAccounts/images/delete|刪除給定的儲存體帳戶映像。|
|/storageAccounts/disks/read|傳回儲存體帳戶磁碟。|
|/storageAccounts/disks/write|新增儲存體帳戶磁碟。|
|/storageAccounts/disks/delete|刪除給定的儲存體帳戶磁碟。|
|/storageAccounts/disks/operationStatuses/read|讀取資源的作業狀態。|
|/storageAccounts/osImages/read|傳回儲存體帳戶的作業系統映像。|
|/storageAccounts/osImages/delete|刪除給定之儲存體帳戶的作業系統映像。|
|/storageAccounts/services/read|取得可用服務。|
|/storageAccounts/services/metricDefinitions/read|取得計量定義。|
|/storageAccounts/services/metrics/read|取得計量。|
|/storageAccounts/services/diagnosticSettings/read|取得診斷設定。|
|/storageAccounts/services/diagnosticSettings/write|新增或修改診斷設定。|
|/disks/read|傳回儲存體帳戶磁碟。|
|/osImages/read|傳回作業系統映像。|
|/quotas/read|取得訂用帳戶配額。|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| 作業 | 說明 |
|---|---|
|/accounts/read|讀取 API 帳戶。|
|/accounts/write|寫入 API 帳戶。|
|/accounts/delete|刪除 API 帳戶|
|/accounts/listKeys/action|列出金鑰|
|/accounts/regenerateKey/action|重新產生金鑰|
|/accounts/skus/read|讀取現有資源的可用 SKU。|
|/accounts/usages/read|取得現有資源的配額使用情況。|
|/Operations/read|作業的描述。|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| 作業 | 說明 |
|---|---|
|/RateCard/read|傳回給定訂用帳戶的優惠資料、資源/計量中繼資料和速率。|
|/UsageAggregates/read|擷取訂用帳戶的 Microsoft Azure 耗用量。 結果會包含特定時間範圍內的彙總使用量資料、訂用帳戶和資源相關資訊。|

## <a name="microsoftcompute"></a>Microsoft.Compute

| 作業 | 說明 |
|---|---|
|/register/action|向 Microsoft.Compute 資源提供者註冊訂用帳戶|
|/restorePointCollections/read|取得還原點集合的屬性|
|/restorePointCollections/write|建立新的還原點集合，或更新現有還原點集合|
|/restorePointCollections/delete|刪除還原點集合和內含的還原點|
|/restorePointCollections/restorePoints/read|取得還原點的屬性|
|/restorePointCollections/restorePoints/write|建立新的還原點|
|/restorePointCollections/restorePoints/delete|刪除還原點|
|/restorePointCollections/restorePoints/retrieveSasUris/action|取得還原點屬性以及 Blob SAS URI|
|/virtualMachineScaleSets/read|取得虛擬機器擴展集的屬性|
|/virtualMachineScaleSets/write|建立新的虛擬機器擴展集，或更新現有的虛擬機器擴展集|
|/virtualMachineScaleSets/delete|刪除虛擬機器擴展集|
|/virtualMachineScaleSets/start/action|啟動虛擬機器擴展集的執行個體|
|/virtualMachineScaleSets/powerOff/action|關閉虛擬機器擴展集執行個體的電源|
|/virtualMachineScaleSets/restart/action|重新啟動虛擬機器擴展集的執行個體|
|/virtualMachineScaleSets/deallocate/action|關閉虛擬機器擴展集之執行個體的電源，並將其計算資源釋出 |
|/virtualMachineScaleSets/manualUpgrade/action|將虛擬機器擴展集的執行個體手動更新至最新模型|
|/virtualMachineScaleSets/scale/action|相應縮小/相應放大現有虛擬機器擴展集的執行個體計數|
|/virtualMachineScaleSets/instanceView/read|取得虛擬機器擴展集的執行個體檢視|
|/virtualMachineScaleSets/skus/read|列出現有虛擬機器擴展集的有效 SKU|
|/virtualMachineScaleSets/virtualMachines/read|擷取 VM 擴展集內的虛擬機器屬性|
|/virtualMachineScaleSets/virtualMachines/delete|刪除 VM 擴展集內的特定虛擬機器。|
|/virtualMachineScaleSets/virtualMachines/start/action|啟動 VM 擴展集內的虛擬機器執行個體。|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|將 VM 擴展集內的虛擬機器執行個體關閉電源。|
|/virtualMachineScaleSets/virtualMachines/restart/action|重新啟動 VM 擴展集內的虛擬機器執行個體。|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|關閉 VM 擴展集之虛擬機器的電源，並將其計算資源釋出。|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|擷取 VM 擴展集內的虛擬機器執行個體檢視。|
|/images/read|取得映像的屬性|
|/images/write|建立新的映像，或更新現有映像|
|/images/delete|刪除映像|
|/operations/read|列出 Microsoft.Compute 資源提供者的可用作業|
|/disks/read|取得磁碟的屬性|
|/disks/write|建立新的磁碟，或更新現有磁碟|
|/disks/delete|刪除磁碟|
|/disks/beginGetAccess/action|取得磁碟用於 Blob 存取的 SAS URI|
|/disks/endGetAccess/action|撤銷磁碟的 SAS URI|
|/snapshots/read|取得快照集的屬性|
|/snapshots/write|建立新的快照集，或更新現有快照集|
|/snapshots/delete|刪除快照集|
|/availabilitySets/read|取得可用性設定組的屬性|
|/availabilitySets/write|建立新的可用性設定組，或更新現有的可用性設定組|
|/availabilitySets/delete|刪除可用性設定組|
|/availabilitySets/vmSizes/read|列出在可用性設定組中建立或更新虛擬機器時可以使用的大小|
|/virtualMachines/read|取得虛擬機器的屬性|
|/virtualMachines/write|建立新的虛擬機器，或更新現有虛擬機器|
|/virtualMachines/delete|刪除虛擬機器|
|/virtualMachines/start/action|啟動虛擬機器|
|/virtualMachines/powerOff/action|關閉虛擬機器的電源。 請注意，您會繼續收到虛擬機器的帳單。|
|/virtualMachines/redeploy/action|重新部署虛擬機器|
|/virtualMachines/restart/action|重新啟動虛擬機器|
|/virtualMachines/deallocate/action|關閉虛擬機器的電源，並將計算資源釋出|
|/virtualMachines/generalize/action|將虛擬機器的狀態設定為「已一般化」，並準備虛擬機器以供擷取|
|/virtualMachines/capture/action|藉由複製虛擬硬碟來擷取虛擬機器，並產生可用來建立類似虛擬機器的範本|
|/virtualMachines/convertToManagedDisks/action|將虛擬機器的 Blob 型磁碟轉換為受控磁碟|
|/virtualMachines/vmSizes/read|列出虛擬機器所能更新成的大小|
|/virtualMachines/instanceView/read|取得虛擬機器和其資源的詳細執行階段狀態|
|/virtualMachines/extensions/read|取得虛擬機器擴充功能的屬性|
|/virtualMachines/extensions/write|建立新的虛擬機器擴充功能，或更新現有的虛擬機器擴充功能|
|/virtualMachines/extensions/delete|刪除虛擬機器擴充功能|
|/locations/vmSizes/read|列出某個位置可用的虛擬機器大小|
|/locations/usages/read|取得訂用帳戶在某個位置之計算資源的服務限制和目前的使用量|
|/locations/operations/read|取得非同步作業的狀態|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| 作業 | 說明 |
|---|---|
|/register/action|針對容器登錄資源提供者註冊訂用帳戶，並讓您能夠建立容器登錄。|
|/checknameavailability/read|確認登錄名稱有效，且並非使用中。|
|/registries/read|傳回容器登錄的清單，或取得指定容器登錄的屬性。|
|/registries/write|使用指定參數建立容器登錄，或更新指定容器登錄的屬性或標記。|
|/registries/delete|刪除現有容器登錄。|
|/registries/listCredentials/action|列出指定容器登錄的登入認證。|
|/registries/regenerateCredential/action|重新產生指定容器登錄的登入認證。|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| 作業 | 說明 |
|---|---|
|/containerServices/subscriptions/read|根據訂用帳戶取得指定的容器服務|
|/containerServices/resourceGroups/read|根據資源群組取得指定的容器服務|
|/containerServices/resourceGroups/ContainerServiceName/read|取得指定的容器服務|
|/containerServices/resourceGroups/ContainerServiceName/write|放置或更新指定的容器服務|
|/containerServices/resourceGroups/ContainerServiceName/delete|刪除指定的容器服務|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| 作業 | 說明 |
|---|---|
|/updateCommunicationPreference/action|更新通訊喜好設定|
|/listCommunicationPreference/action|列出通訊喜好設定|
|/applications/read|讀取作業|
|/applications/write|寫入作業|
|/applications/write|寫入作業|
|/applications/delete|刪除作業|
|/applications/listSecrets/action|列出祕密|
|/applications/listSingleSignOnToken/action|讀取單一登入權杖|
|/operations/read|讀取作業|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| 作業 | 說明 |
|---|---|
|/hubs/read|讀取任何 Azure Customer Insights 中樞|
|/hubs/write|建立或更新任何 Azure Customer Insights 中樞|
|/hubs/delete|刪除任何 Azure Customer Insights 中樞|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|取得資源的可用計量|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|取得資源的診斷設定|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|建立或更新資源的診斷設定|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|取得資源的可用記錄|
|/hubs/authorizationPolicies/read|讀取任何 Azure Customer Insights 共用存取簽章原則|
|/hubs/authorizationPolicies/write|建立或更新任何 Azure Customer Insights 共用存取簽章原則|
|/hubs/authorizationPolicies/delete|刪除任何 Azure Customer Insights 共用存取簽章原則|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|重新產生 Azure Customer Insights 共用存取簽章原則的主要金鑰|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|重新產生 Azure Customer Insights 共用存取簽章原則的次要金鑰|
|/hubs/profiles/read|讀取任何 Azure Customer Insights 設定檔|
|/hubs/profiles/write|寫入任何 Azure Customer Insights 設定檔|
|/hubs/kpi/read|讀取任何 Azure Customer Insights 關鍵效能指標|
|/hubs/kpi/write|建立或更新任何 Azure Customer Insights 關鍵效能指標|
|/hubs/kpi/delete|刪除任何 Azure Customer Insights 關鍵效能指標|
|/hubs/views/read|讀取任何 Azure Customer Insights 應用程式檢視|
|/hubs/views/write|建立或更新任何 Azure Customer Insights 應用程式檢視|
|/hubs/views/delete|刪除任何 Azure Customer Insights 應用程式檢視|
|/hubs/interactions/read|讀取任何 Azure Customer Insights 互動|
|/hubs/interactions/write|建立或更新任何 Azure Customer Insights 互動|
|/hubs/roleAssignments/read|讀取任何 Azure Customer Insights RBAC 指派|
|/hubs/roleAssignments/write|建立或更新任何 Azure Customer Insights RBAC 指派|
|/hubs/roleAssignments/delete|刪除任何 Azure Customer Insights RBAC 指派|
|/hubs/connectors/read|讀取任何 Azure Customer Insights 連接器|
|/hubs/connectors/write|建立或更新任何 Azure Customer Insights 連接器|
|/hubs/connectors/delete|刪除任何 Azure Customer Insights 連接器|
|/hubs/connectors/mappings/read|讀取任何 Azure Customer Insights 連接器對應|
|/hubs/connectors/mappings/write|建立或更新任何 Azure Customer Insights 連接器對應|
|/hubs/connectors/mappings/delete|刪除任何 Azure Customer Insights 連接器對應|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| 作業 | 說明 |
|---|---|
|/checkNameAvailability/action|檢查租用戶的目錄名稱可用性。|
|/catalogs/read|取得訂用帳戶或資源群組下一或多個目錄的屬性。|
|/catalogs/write|建立目錄，或更新目錄的標記和屬性。|
|/catalogs/delete|刪除目錄。|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| 作業 | 說明 |
|---|---|
|/datafactories/read|讀取 Data Factory。|
|/datafactories/write|建立或更新 Data Factory|
|/datafactories/delete|刪除 Data Factory。|
|/datafactories/datapipelines/read|讀取管線。|
|/datafactories/datapipelines/delete|刪除管線。|
|/datafactories/datapipelines/pause/action|暫停管線。|
|/datafactories/datapipelines/resume/action|繼續管線。|
|/datafactories/datapipelines/update/action|更新管線。|
|/datafactories/datapipelines/write|建立或更新管線|
|/datafactories/linkedServices/read|讀取連結的服務。|
|/datafactories/linkedServices/delete|刪除連結的服務。|
|/datafactories/linkedServices/write|建立或更新連結的服務|
|/datafactories/tables/read|讀取資料表。|
|/datafactories/tables/delete|刪除資料表。|
|/datafactories/tables/write|建立或更新資料表|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| 作業 | 說明 |
|---|---|
|/accounts/read|取得 DataLakeAnalytics 帳戶的相關資訊。|
|/accounts/write|建立或更新 DataLakeAnalytics 帳戶。|
|/accounts/delete|刪除 DataLakeAnalytics 帳戶。|
|/accounts/firewallRules/read|取得防火牆規則的相關資訊。|
|/accounts/firewallRules/write|建立或更新防火牆規則。|
|/accounts/firewallRules/delete|刪除防火牆規則。|
|/accounts/storageAccounts/read|取得 DataLakeAnalytics 帳戶所連結的儲存體帳戶。|
|/accounts/storageAccounts/write|將儲存體帳戶連結至 DataLakeAnalytics 帳戶。|
|/accounts/storageAccounts/delete|讓儲存體帳戶與 DataLakeAnalytics 帳戶取消連結。|
|/accounts/storageAccounts/Containers/read|取得儲存體帳戶下的容器|
|/accounts/storageAccounts/Containers/listSasTokens/action|列出儲存體容器的 SAS 權杖|
|/accounts/dataLakeStoreAccounts/read|取得 DataLakeAnalytics 帳戶所連結的 DataLakeStore 帳戶。|
|/accounts/dataLakeStoreAccounts/write|將 DataLakeStore 帳戶連結至 DataLakeAnalytics 帳戶。|
|/accounts/dataLakeStoreAccounts/delete|讓 DataLakeStore 帳戶與 DataLakeAnalytics 帳戶取消連結。|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| 作業 | 說明 |
|---|---|
|/accounts/read|取得現存 DataLakeStore 帳戶的相關資訊。|
|/accounts/write|建立新的 DataLakeStore 帳戶，或更新現存 DataLakeStore 帳戶。|
|/accounts/delete|刪除現存的 DataLakeStore 帳戶。|
|/accounts/firewallRules/read|取得防火牆規則的相關資訊。|
|/accounts/firewallRules/write|建立或更新防火牆規則。|
|/accounts/firewallRules/delete|刪除防火牆規則。|
|/accounts/trustedIdProviders/read|取得所信任識別提供者的相關資訊。|
|/accounts/trustedIdProviders/write|建立或更新所信任的識別提供者。|
|/accounts/trustedIdProviders/delete|刪除所信任的識別提供者。|

## <a name="microsoftdevices"></a>Microsoft.Devices

| 作業 | 說明 |
|---|---|
|/register/action|針對 IotHub 資源提供者註冊訂用帳戶，並讓您能夠建立 IotHub 資源|
|/checkNameAvailability/Action|檢查 IotHub 名稱是否可供使用|
|/usages/Read|取得此提供者的訂用帳戶使用量詳細資料。|
|/operations/Read|取得所有 ResourceProvider 作業|
|/iotHubs/Read|取得 IotHub 資源|
|/iotHubs/Write|建立或更新 IotHub 資源|
|/iotHubs/Delete|刪除 IotHub 資源|
|/iotHubs/listkeys/Action|取得所有 IotHub 金鑰|
|/iotHubs/exportDevices/Action|匯出裝置|
|/iotHubs/importDevices/Action|匯入裝置|
|/IotHubs/metricDefinitions/read|取得 IotHub 服務的可用計量|
|/iotHubs/iotHubKeys/listkeys/Action|取得給定名稱的 IotHub 金鑰|
|/iotHubs/iotHubStats/Read|取得 IotHub 統計資料|
|/iotHubs/quotaMetrics/Read|取得配額計量|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|建立 EventHub 取用者群組|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|取得 EventHub 取用者群組|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|刪除 EventHub 取用者群組|
|/iotHubs/routing/routes/$testall/Action|針對所有現有路由來測試訊息|
|/iotHubs/routing/routes/$testnew/Action|針對所提供的測試路由來測試訊息|
|/IotHubs/diagnosticSettings/read|取得資源的診斷設定|
|/IotHubs/diagnosticSettings/write|建立或更新資源的診斷設定|
|/iotHubs/skus/Read|取得有效的 IotHub SKU|
|/iotHubs/jobs/Read|取得在給定的 IotHub 上所提交的作業詳細資料|
|/iotHubs/routingEndpointsHealth/Read|取得 IotHub 之所有路由端點的健康狀態|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| 作業 | 說明 |
|---|---|
|/Subscription/register/action|註冊訂用帳戶|
|/labs/delete|刪除實驗室。|
|/labs/read|讀取實驗室。|
|/labs/write|新增或修改實驗室。|
|/labs/ListVhds/action|列出可供建立自訂映像的磁碟映像。|
|/labs/GenerateUploadUri/action|產生 URI 以用來將自訂磁碟映像上傳到實驗室。|
|/labs/CreateEnvironment/action|在實驗室中建立虛擬機器。|
|/labs/ClaimAnyVm/action|在實驗室中宣告隨機的可宣告虛擬機器。|
|/labs/ExportResourceUsage/action|將實驗室資源使用狀況匯出到儲存體帳戶|
|/labs/users/delete|刪除使用者設定檔。|
|/labs/users/read|讀取使用者設定檔。|
|/labs/users/write|新增或修改使用者設定檔。|
|/labs/users/secrets/delete|刪除祕密。|
|/labs/users/secrets/read|讀取祕密。|
|/labs/users/secrets/write|新增或修改祕密。|
|/labs/users/environments/delete|刪除環境。|
|/labs/users/environments/read|讀取環境。|
|/labs/users/environments/write|新增或修改環境。|
|/labs/users/disks/delete|刪除磁碟。|
|/labs/users/disks/read|讀取磁碟。|
|/labs/users/disks/write|新增或修改磁碟。|
|/labs/users/disks/Attach/action|將磁碟連結至虛擬機器並建立磁碟租用。|
|/labs/users/disks/Detach/action|將連結至虛擬機器的磁碟中斷連結並中斷磁碟租用。|
|/labs/customImages/delete|刪除自訂映像。|
|/labs/customImages/read|讀取自訂映像。|
|/labs/customImages/write|新增或修改自訂映像。|
|/labs/serviceRunners/delete|刪除服務執行器。|
|/labs/serviceRunners/read|讀取服務執行器。|
|/labs/serviceRunners/write|新增或修改服務執行器。|
|/labs/artifactSources/delete|刪除構件來源。|
|/labs/artifactSources/read|讀取構件來源。|
|/labs/artifactSources/write|新增或修改構件來源。|
|/labs/artifactSources/artifacts/read|讀取構件。|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|產生給定構件的 ARM 範本，將所需的檔案上傳至儲存體帳戶，然後驗證所產生的構件。|
|/labs/artifactSources/armTemplates/read|讀取 Azure Resource Manager 範本。|
|/labs/costs/read|讀取成本。|
|/labs/costs/write|新增或修改成本。|
|/labs/virtualNetworks/delete|刪除虛擬網路。|
|/labs/virtualNetworks/read|讀取虛擬網路。|
|/labs/virtualNetworks/write|新增或修改虛擬網路。|
|/labs/formulas/delete|刪除公式。|
|/labs/formulas/read|讀取公式。|
|/labs/formulas/write|新增或修改公式。|
|/labs/schedules/delete|刪除排程。|
|/labs/schedules/read|讀取排程。|
|/labs/schedules/write|新增或修改排程。|
|/labs/schedules/Execute/action|執行排程。|
|/labs/schedules/ListApplicable/action|列出所有適用排程|
|/labs/galleryImages/read|讀取資源庫映像。|
|/labs/policySets/EvaluatePolicies/action|評估實驗室原則。|
|/labs/policySets/policies/delete|刪除原則。|
|/labs/policySets/policies/read|讀取原則。|
|/labs/policySets/policies/write|新增或修改原則。|
|/labs/virtualMachines/delete|刪除虛擬機器。|
|/labs/virtualMachines/read|讀取虛擬機器。|
|/labs/virtualMachines/write|新增或修改虛擬機器。|
|/labs/virtualMachines/Start/action|啟動虛擬機器。|
|/labs/virtualMachines/Stop/action|停止虛擬機器|
|/labs/virtualMachines/ApplyArtifacts/action|將構件套用至虛擬機器。|
|/labs/virtualMachines/AddDataDisk/action|將新的或現有的資料磁碟連結至虛擬機器。|
|/labs/virtualMachines/DetachDataDisk/action|中斷指定磁碟與虛擬機器的連結。|
|/labs/virtualMachines/Claim/action|取得現有虛擬機器的擁有權|
|/labs/virtualMachines/ListApplicableSchedules/action|列出所有適用排程|
|/labs/virtualMachines/schedules/delete|刪除排程。|
|/labs/virtualMachines/schedules/read|讀取排程。|
|/labs/virtualMachines/schedules/write|新增或修改排程。|
|/labs/virtualMachines/schedules/Execute/action|執行排程。|
|/labs/notificationChannels/delete|刪除 notificationchannels。|
|/labs/notificationChannels/read|讀取 notificationchannels。|
|/labs/notificationChannels/write|新增或修改 notificationchannels。|
|/labs/notificationChannels/Notify/action|對所提供的通道傳送通知。|
|/schedules/delete|刪除排程。|
|/schedules/read|讀取排程。|
|/schedules/write|新增或修改排程。|
|/schedules/Execute/action|執行排程。|
|/schedules/Retarget/action|更新排程的目標資源識別碼。|
|/locations/operations/read|讀取作業。|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| 作業 | 說明 |
|---|---|
|/databaseAccountNames/read|檢查名稱可用性。|
|/databaseAccounts/read|讀取資料庫帳戶。|
|/databaseAccounts/write|更新資料庫帳戶。|
|/databaseAccounts/listKeys/action|列出資料庫帳戶的金鑰|
|/databaseAccounts/regenerateKey/action|輪替資料庫帳戶的金鑰|
|/databaseAccounts/listConnectionStrings/action|取得資料庫帳戶的連接字串|
|/databaseAccounts/changeResourceGroup/action|變更資料庫帳戶的資源群組|
|/databaseAccounts/failoverPriorityChange/action|變更資料庫帳戶區域的容錯移轉優先順序。 這會用來執行手動容錯移轉作業|
|/databaseAccounts/delete|刪除資料庫帳戶。|
|/databaseAccounts/metricDefinitions/read|讀取資料庫帳戶的計量定義。|
|/databaseAccounts/metrics/read|讀取資料庫帳戶的計量。|
|/databaseAccounts/usages/read|讀取資料庫帳戶的使用量。|
|/databaseAccounts/databases/collections/metricDefinitions/read|讀取集合計量定義。|
|/databaseAccounts/databases/collections/metrics/read|讀取集合計量。|
|/databaseAccounts/databases/collections/usages/read|讀取集合使用量。|
|/databaseAccounts/databases/metricDefinitions/read|讀取資料庫計量定義|
|/databaseAccounts/databases/metrics/read|讀取資料庫計量。|
|/databaseAccounts/databases/usages/read|讀取資料庫使用量。|
|/databaseAccounts/readonlykeys/read|讀取資料庫帳戶的唯讀金鑰。|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| 作業 | 說明 |
|---|---|
|/generateSsoRequest/Action|產生要求以便登入網域控制中心。|
|/validateDomainRegistrationInformation/Action|驗證網域購買物件，但不將其提交出去|
|/checkDomainAvailability/Action|檢查網域是否可供購買|
|/listDomainRecommendations/Action|根據關鍵字來擷取清單網域建議|
|/register/action|針對訂用帳戶註冊 Microsoft 網域資源提供者|
|/domains/Read|取得網域清單|
|/domains/Write|新增網域或更新現有網域|
|/domains/Delete|刪除現有網域。|
|/domains/operationresults/Read|取得網域作業|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| 作業 | 說明 |
|---|---|
|/lcsprojects/read|顯示屬於某位使用者的 Microsoft Dynamics 週期服務專案|
|/lcsprojects/write|建立並更新屬於某位使用者的 Microsoft Dynamics 週期服務專案。 您只能更新名稱和描述屬性。 專案建立後，您就無法更新與其相關聯的訂用帳戶和位置|
|/lcsprojects/delete|刪除屬於某位使用者的 Microsoft Dynamics 週期服務專案|
|/lcsprojects/clouddeployments/read|顯示屬於某位使用者之 Microsoft Dynamics 週期服務專案內的 Microsoft Dynamics AX 2012 R3 評估部署|
|/lcsprojects/clouddeployments/write|在屬於某位使用者的 Microsoft Dynamics 週期服務專案內，建立 Microsoft Dynamics AX 2012 R3 評估部署。 您可以從 Azure 管理入口網站欄管理部署|
|/lcsprojects/connectors/read|讀取屬於 Microsoft Dynamics 週期服務專案的連接器|
|/lcsprojects/connectors/write|建立和更新屬於 Microsoft Dynamics 週期服務專案的連接器|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| 作業 | 說明 |
|---|---|
|/checkNameAvailability/action|檢查給定訂用帳戶下的命名空間是否可用。|
|/register/action|針對 EventHub 資源提供者註冊訂用帳戶，並讓您能夠建立 EventHub 資源|
|/namespaces/write|建立命名空間資源，並更新其屬性。 命名空間的標記和狀態是可以更新的屬性。|
|/namespaces/read|取得命名空間資源描述的清單|
|/namespaces/Delete|刪除命名空間資源|
|/namespaces/metricDefinitions/read|取得命名空間計量資源描述的清單|
|/namespaces/authorizationRules/read|取得命名空間授權規則描述的清單。|
|/namespaces/authorizationRules/write|建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。|
|/namespaces/authorizationRules/delete|刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。 |
|/namespaces/authorizationRules/listkeys/action|取得命名空間的連接字串|
|/namespaces/authorizationRules/regenerateKeys/action|對資源重新產生主要或次要金鑰|
|/namespaces/eventhubs/write|建立或更新 EventHub 屬性。|
|/namespaces/eventhubs/read|取得 EventHub 資源描述的清單|
|/namespaces/eventhubs/Delete|用來刪除 EventHub 資源的作業|
|/namespaces/eventHubs/consumergroups/write|建立或更新 ConsumerGroup 屬性。|
|/namespaces/eventHubs/consumergroups/read|取得 ConsumerGroup 資源描述的清單|
|/namespaces/eventHubs/consumergroups/Delete|用來刪除 ConsumerGroup 資源的作業|
|/namespaces/eventhubs/authorizationRules/read| 取得 EventHub 授權規則的清單|
|/namespaces/eventhubs/authorizationRules/write|建立 EventHub 授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。|
|/namespaces/eventhubs/authorizationRules/delete|用來刪除 EventHub 授權規則的作業|
|/namespaces/eventhubs/authorizationRules/listkeys/action|取得 EventHub 的連接字串|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|對資源重新產生主要或次要金鑰|
|/namespaces/diagnosticSettings/read|取得命名空間診斷設定資源描述的清單|
|/namespaces/diagnosticSettings/write|取得命名空間診斷設定資源描述的清單|
|/namespaces/logDefinitions/read|取得命名空間記錄資源描述的清單|

## <a name="microsoftfeatures"></a>Microsoft.Features

| 作業 | 說明 |
|---|---|
|/providers/features/read|取得給定資源提供者中某個訂用帳戶的功能。|
|/providers/features/register/action|註冊給定資源提供者中某個訂用帳戶的功能。|
|/features/read|取得訂用帳戶的功能。|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| 作業 | 說明 |
|---|---|
|/clusters/write|建立或更新 HDInsight 叢集|
|/clusters/read|取得 HDInsight 叢集的相關詳細資料|
|/clusters/delete|刪除 HDInsight 叢集|
|/clusters/changerdpsetting/action|變更 HDInsight 叢集的 RDP 設定|
|/clusters/configurations/action|更新 HDInsight 叢集組態|
|/clusters/configurations/read|取得 HDInsight 叢集組態|
|/clusters/roles/resize/action|調整 HDInsight 叢集的大小|
|/locations/capabilities/read|取得訂用帳戶功能|
|/locations/checkNameAvailability/read|檢查名稱可用性|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| 作業 | 說明 |
|---|---|
|/register/action|針對匯入/匯出資源提供者註冊訂用帳戶，並讓您能夠建立匯入/匯出作業。|
|/jobs/write|使用指定參數建立作業，或更新指定作業的屬性或標記。|
|/jobs/read|取得指定作業的屬性，或傳回作業清單。|
|/jobs/listBitLockerKeys/action|取得指定作業的 BitLocker 金鑰。|
|/jobs/delete|刪除現有作業。|
|/locations/read|取得指定位置的屬性，或傳回位置清單。|

## <a name="microsoftinsights"></a>Microsoft.Insights

| 作業 | 說明 |
|---|---|
|/Register/Action|註冊 Microsoft Insights 提供者|
|/AlertRules/Write|寫入到警示規則組態|
|/AlertRules/Delete|刪除警示規則組態|
|/AlertRules/Read|讀取警示規則組態|
|/AlertRules/Activated/Action|警示規則已啟用|
|/AlertRules/Resolved/Action|警示規則已解析|
|/AlertRules/Throttled/Action|警示規則已節流|
|/AlertRules/Incidents/Read|讀取警示規則事件組態|
|/MetricDefinitions/Read|讀取計量定義|
|/eventtypes/values/Read|讀取管理事件類型值|
|/eventtypes/digestevents/Read|讀取管理事件類型摘要|
|/Metrics/Read|讀取計量|
|/LogProfiles/Write|寫入到記錄設定檔組態|
|/LogProfiles/Delete|刪除記錄設定檔組態|
|/LogProfiles/Read|讀取記錄設定檔|
|/AutoscaleSettings/Write|寫入到自動調整設定組態|
|/AutoscaleSettings/Delete|刪除自動調整設定組態|
|/AutoscaleSettings/Read|讀取自動調整設定組態|
|/AutoscaleSettings/Scaleup/Action|自動調整相應增加作業|
|/AutoscaleSettings/Scaledown/Action|自動調整相應減少作業|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|讀取計量定義|
|/ActivityLogAlerts/Activated/Action|已觸發活動記錄警示|
|/DiagnosticSettings/Write|寫入到診斷設定組態|
|/DiagnosticSettings/Delete|刪除診斷設定組態|
|/DiagnosticSettings/Read|讀取診斷設定組態|
|/LogDefinitions/Read|讀取記錄定義|
|/ExtendedDiagnosticSettings/Write|寫入到延伸的診斷設定組態|
|/ExtendedDiagnosticSettings/Delete|刪除延伸的診斷設定組態|
|/ExtendedDiagnosticSettings/Read|讀取延伸的診斷設定組態|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| 作業 | 說明 |
|---|---|
|/register/action|註冊訂用帳戶|
|/checkNameAvailability/read|確認 Key Vault 名稱有效，且並非使用中|
|/vaults/read|檢視 Key Vault 的屬性|
|/vaults/write|建立新的 Key Vault，或更新現有 Key Vault 的屬性|
|/vaults/delete|刪除 Key Vault|
|/vaults/deploy/action|在部署 Azure 資源時啟用 Key Vault 中祕密的存取權|
|/vaults/secrets/read|檢視祕密的屬性而非其值|
|/vaults/secrets/write|建立新的祕密，或更新現有祕密的值|
|/vaults/accessPolicies/write|藉由合併或取代來更新現有存取原則，或在保存庫中新增存取原則。|
|/deletedVaults/read|檢視虛刪除之 Key Vault 的屬性|
|/locations/operationResults/read|檢查長時間執行之作業的結果|
|/locations/deletedVaults/read|檢視虛刪除之 Key Vault 的屬性|
|/locations/deletedVaults/purge/action|清除虛刪除的 Key Vault|

## <a name="microsoftlogic"></a>Microsoft.Logic

| 作業 | 說明 |
|---|---|
|/workflows/read|讀取工作流程。|
|/workflows/write|建立或更新工作流程。|
|/workflows/delete|刪除工作流程。|
|/workflows/run/action|啟動工作流程的執行。|
|/workflows/disable/action|停用工作流程。|
|/workflows/enable/action|啟用工作流程。|
|/workflows/validate/action|驗證工作流程。|
|/workflows/move/action|將工作流程從其現有的訂用帳戶識別碼、資源群組和/或名稱改為不同的訂用帳戶識別碼、資源群組和/或名稱。|
|/workflows/listSwagger/action|取得工作流程 Swagger 定義。|
|/workflows/regenerateAccessKey/action|重新產生存取金鑰祕密。|
|/workflows/listCallbackUrl/action|取得工作流程的回呼 URL。|
|/workflows/versions/read|讀取工作流程版本。|
|/workflows/versions/triggers/listCallbackUrl/action|取得觸發程序的回呼 URL。|
|/workflows/runs/read|讀取工作流程的執行。|
|/workflows/runs/cancel/action|取消工作流程的執行。|
|/workflows/runs/actions/read|讀取工作流程的執行動作。|
|/workflows/runs/operations/read|讀取工作流程的執行作業狀態。|
|/workflows/triggers/read|讀取觸發程序。|
|/workflows/triggers/run/action|執行觸發程序。|
|/workflows/triggers/listCallbackUrl/action|取得觸發程序的回呼 URL。|
|/workflows/triggers/histories/read|讀取觸發程序歷程記錄。|
|/workflows/triggers/histories/resubmit/action|重新提交工作流程的觸發程序。|
|/workflows/accessKeys/read|讀取存取金鑰。|
|/workflows/accessKeys/write|建立或更新存取金鑰。|
|/workflows/accessKeys/delete|刪除存取金鑰。|
|/workflows/accessKeys/list/action|列出存取金鑰祕密。|
|/workflows/accessKeys/regenerate/action|重新產生存取金鑰祕密。|
|/locations/workflows/validate/action|驗證工作流程。|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| 作業 | 說明 |
|---|---|
|/register/action|針對 Machine Learning Web 服務資源提供者註冊訂用帳戶，並讓您能夠建立 Web 服務。|
|/webServices/action|建立所支援區域的區域性 Web 服務屬性|
|/commitmentPlans/read|讀取任何 Machine Learning 承諾用量方案|
|/commitmentPlans/write|建立或更新任何 Machine Learning 承諾用量方案|
|/commitmentPlans/delete|刪除任何 Machine Learning 承諾用量方案|
|/commitmentPlans/join/action|加入任何 Machine Learning 承諾用量方案|
|/commitmentPlans/commitmentAssociations/read|讀取任何 Machine Learning 承諾用量方案關聯|
|/commitmentPlans/commitmentAssociations/move/action|移動任何 Machine Learning 承諾用量方案關聯|
|/Workspaces/read|讀取任何 Machine Learning 工作區|
|/Workspaces/write|建立或更新任何 Machine Learning 工作區|
|/Workspaces/delete|刪除任何 Machine Learning 工作區|
|/Workspaces/listworkspacekeys/action|列出 Machine Learning 工作區的金鑰|
|/Workspaces/resyncstoragekeys/action|重新同步處理針對 Machine Learning 工作區所設定之儲存體帳戶的金鑰|
|/webServices/read|讀取任何 Machine Learning Web 服務|
|/webServices/write|建立或更新任何 Machine Learning Web 服務|
|/webServices/delete|刪除任何 Machine Learning Web 服務|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| 作業 | 說明 |
|---|---|
|/agreements/offers/plans/read|傳回給定 Marketplace 項目的合約|
|/agreements/offers/plans/sign/action|簽署給定 Marketplace 項目的合約|
|/agreements/offers/plans/cancel/action|取消給定 Marketplace 項目的合約|

## <a name="microsoftmedia"></a>Microsoft.Media

| 作業 | 說明 |
|---|---|
|/mediaservices/read||
|/mediaservices/write||
|/mediaservices/delete||
|/mediaservices/regenerateKey/action||
|/mediaservices/listKeys/action||
|/mediaservices/syncStorageKeys/action||

## <a name="microsoftnetwork"></a>Microsoft.Network

| 作業 | 說明 |
|---|---|
|/register/action|註冊訂用帳戶|
|/unregister/action|取消註冊訂用帳戶|
|/checkTrafficManagerNameAvailability/action|檢查流量管理員相對 DNS 名稱的可用性。|
|/dnszones/read|取得 JSON 格式的 DNS 區域。 區域屬性包含標記、etag、numberOfRecordSets 和 maxNumberOfRecordSets。 請注意，此命令不會擷取區域內所包含的記錄集。|
|/dnszones/write|在資源群組內建立或更新 DNS 區域。  用來更新 DNS 區域資源上的標記。 請注意，此命令無法用來在區域內建立或更新記錄集。|
|/dnszones/delete|刪除 JSON 格式的 DNS 區域。 區域屬性包含標記、etag、numberOfRecordSets 和 maxNumberOfRecordSets。|
|/dnszones/MX/read|取得 JSON 格式的 'MX' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。|
|/dnszones/MX/write|在 DNS 區域內建立或更新 'MX' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。|
|/dnszones/MX/delete|從 DNS 區域中移除名稱已給定且類型為 'MX' 的記錄集。|
|/dnszones/NS/read|取得 NS 類型的 DNS 記錄集|
|/dnszones/NS/write|建立或更新 NS 類型的 DNS 記錄集|
|/dnszones/NS/delete|刪除 NS 類型的 DNS 記錄集|
|/dnszones/AAAA/read|取得 JSON 格式的 'AAAA' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。|
|/dnszones/AAAA/write|在 DNS 區域內建立或更新 'AAAA' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。|
|/dnszones/AAAA/delete|從 DNS 區域中移除名稱已給定且類型為 'AAAA' 的記錄集。|
|/dnszones/CNAME/read|取得 JSON 格式的 'CNAME' 類型記錄集。 記錄集包含 TTL、標記和 etag。|
|/dnszones/CNAME/write|在 DNS 區域內建立或更新 'CNAME' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。|
|/dnszones/CNAME/delete|從 DNS 區域中移除名稱已給定且類型為 'CNAME' 的記錄集。|
|/dnszones/SOA/read|取得 SOA 類型的 DNS 記錄集|
|/dnszones/SOA/write|建立或更新 SOA 類型的 DNS 記錄集|
|/dnszones/SRV/read|取得 JSON 格式的 'SRV' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。|
|/dnszones/SRV/write|建立或更新 SRV 類型的記錄集|
|/dnszones/SRV/delete|從 DNS 區域中移除名稱已給定且類型為 'SRV' 的記錄集。|
|/dnszones/PTR/read|取得 JSON 格式的 'PTR' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。|
|/dnszones/PTR/write|在 DNS 區域內建立或更新 'PTR' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。|
|/dnszones/PTR/delete|從 DNS 區域中移除名稱已給定且類型為 'PTR' 的記錄集。|
|/dnszones/A/read|取得 JSON 格式的 'A' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。|
|/dnszones/A/write|在 DNS 區域內建立或更新 'A' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。|
|/dnszones/A/delete|從 DNS 區域中移除名稱已給定且類型為 'A' 的記錄集。|
|/dnszones/TXT/read|取得 JSON 格式的 'TXT' 類型記錄集。 記錄集包含記錄清單以及 TTL、標記和 etag。|
|/dnszones/TXT/write|在 DNS 區域內建立或更新 'TXT' 類型的記錄集。 指定的記錄將會取代記錄集內的目前記錄。|
|/dnszones/TXT/delete|從 DNS 區域中移除名稱已給定且類型為 'TXT' 的記錄集。|
|/dnszones/recordsets/read|取得跨類型的 DNS 記錄集|
|/networkInterfaces/read|取得網路介面定義。 |
|/networkInterfaces/write|建立網路介面，或更新現有的網路介面。 |
|/networkInterfaces/join/action|將虛擬機器加入網路介面|
|/networkInterfaces/delete|刪除網路介面|
|/networkInterfaces/effectiveRouteTable/action|取得 VM 網路介面上所設定的路由表|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|取得 VM 網路介面上所設定的網路安全性群組|
|/networkInterfaces/loadBalancers/read|取得網路介面所屬的所有負載平衡器|
|/networkInterfaces/ipconfigurations/read|取得網路介面 IP 組態定義。 |
|/publicIPAddresses/read|取得公用 IP 位址定義。|
|/publicIPAddresses/write|建立公用 IP 位址，或更新現有的公用 IP 位址。 |
|/publicIPAddresses/delete|刪除公用 IP 位址。|
|/publicIPAddresses/join/action|加入公用 IP 位址|
|/routeFilters/read|取得路由篩選的定義|
|/routeFilters/join/action|加入路由篩選|
|/routeFilters/delete|刪除路由篩選的定義|
|/routeFilters/write|建立路由篩選，或更新現有的路由篩選|
|/routeFilters/rules/read|取得路由篩選規則的定義|
|/routeFilters/rules/write|建立路由篩選規則，或更新現有的路由篩選規則|
|/routeFilters/rules/delete|刪除路由篩選規則的定義|
|/networkWatchers/read|取得網路監看員定義|
|/networkWatchers/write|建立網路監看員，或更新現有的網路監看員|
|/networkWatchers/delete|刪除網路監看員|
|/networkWatchers/configureFlowLog/action|設定目標資源的流程記錄。|
|/networkWatchers/ipFlowVerify/action|傳回是要允許還是拒絕特定目的地的往返封包。|
|/networkWatchers/nextHop/action|針對指定的目標和目的地 IP 位址，傳回下一個躍點的類型和 IP 位址。|
|/networkWatchers/queryFlowLogStatus/action|取得資源的流程記錄狀態。|
|/networkWatchers/queryTroubleshootResult/action|取得上一次執行的疑難排解結果或目前執行的疑難排解作業。|
|/networkWatchers/securityGroupView/action|檢視對 VM 套用之已設定且生效的網路安全性群組規則。|
|/networkWatchers/topology/action|取得資源的網路層級檢視和資源在資源群組中的關聯性。|
|/networkWatchers/troubleshoot/action|開始針對 Azure 中的網路資源進行疑難排解。|
|/networkWatchers/packetCaptures/queryStatus/action|取得屬性的相關資訊和封包擷取資源的狀態。|
|/networkWatchers/packetCaptures/stop/action|停止正在執行的封包擷取工作階段。|
|/networkWatchers/packetCaptures/read|取得封包擷取定義|
|/networkWatchers/packetCaptures/write|建立封包擷取|
|/networkWatchers/packetCaptures/delete|刪除封包擷取|
|/loadBalancers/read|取得負載平衡器定義|
|/loadBalancers/write|建立負載平衡器，或更新現有的負載平衡器|
|/loadBalancers/delete|刪除負載平衡器|
|/loadBalancers/networkInterfaces/read|取得負載平衡器下所有網路介面的參考|
|/loadBalancers/loadBalancingRules/read|取得負載平衡器的負載平衡規則定義|
|/loadBalancers/backendAddressPools/read|取得負載平衡器的後端位址集區定義|
|/loadBalancers/backendAddressPools/join/action|加入負載平衡器的後端位址集區|
|/loadBalancers/inboundNatPools/read|取得負載平衡器的輸入 NAT 集區定義|
|/loadBalancers/inboundNatPools/join/action|加入負載平衡器的輸入 NAT 集區|
|/loadBalancers/inboundNatRules/read|取得負載平衡器的輸入 NAT 規則定義|
|/loadBalancers/inboundNatRules/write|建立負載平衡器的輸入 NAT 規則，或更新現有的負載平衡器輸入 NAT 規則|
|/loadBalancers/inboundNatRules/delete|刪除負載平衡器的輸入 NAT 規則|
|/loadBalancers/inboundNatRules/join/action|加入負載平衡器的輸入 NAT 規則|
|/loadBalancers/outboundNatRules/read|取得負載平衡器的輸出 NAT 規則定義|
|/loadBalancers/probes/read|取得負載平衡器探查|
|/loadBalancers/virtualMachines/read|取得負載平衡器下所有虛擬機器的參考|
|/loadBalancers/frontendIPConfigurations/read|取得負載平衡器的前端 IP 組態定義|
|/trafficManagerGeographicHierarchies/read|取得流量管理員的地理階層，其所含的區域可用在地理流量路由方法中|
|/bgpServiceCommunities/read|取得 BGP 服務社群|
|/applicationGatewayAvailableWafRuleSets/read|取得應用程式閘道可用的 WAF 規則集|
|/virtualNetworks/read|取得虛擬網路定義|
|/virtualNetworks/write|建立虛擬網路，或更新現有的虛擬網路|
|/virtualNetworks/delete|刪除虛擬網路|
|/virtualNetworks/peer/action|讓某個虛擬網路與另一個虛擬網路對等互連|
|/virtualNetworks/virtualNetworkPeerings/read|取得虛擬網路對等互連定義|
|/virtualNetworks/virtualNetworkPeerings/write|建立虛擬網路對等互連，或更新現有的虛擬網路對等互連|
|/virtualNetworks/virtualNetworkPeerings/delete|刪除虛擬網路對等互連|
|/virtualNetworks/subnets/read|取得虛擬網路子網路定義|
|/virtualNetworks/subnets/write|建立虛擬網路子網路，或更新現有的虛擬網路子網路|
|/virtualNetworks/subnets/delete|刪除虛擬網路子網路|
|/virtualNetworks/subnets/join/action|加入虛擬網路|
|/virtualNetworks/subnets/joinViaServiceTunnel/action|將資源 (例如，儲存體帳戶或 SQL Database) 加入已啟用服務通道功能的子網路中。|
|/virtualNetworks/subnets/virtualMachines/read|取得虛擬網路子網路中所有虛擬機器的參考|
|/virtualNetworks/checkIpAddressAvailability/read|檢查指定的虛擬網路中是否有 IP 位址|
|/virtualNetworks/virtualMachines/read|取得虛擬網路中所有虛擬機器的參考|
|/expressRouteServiceProviders/read|取得 ExpressRoute 服務提供者|
|/dnsoperationresults/read|取得 DNS 作業的結果|
|/localnetworkgateways/read|取得 LocalNetworkGateway|
|/localnetworkgateways/write|建立或更新現有的 LocalNetworkGateway|
|/localnetworkgateways/delete|刪除 LocalNetworkGateway|
|/trafficManagerProfiles/read|取得流量管理員設定檔組態。 這包括 DNS 設定、流量路由設定、端點監視設定，以及此流量管理員設定檔所路由之端點的清單。|
|/trafficManagerProfiles/write|建立流量管理員設定檔，或修改現有流量管理員設定檔的組態。 這包括啟用或停用設定檔以及修改 DNS 設定、流量路由設定或端點監視設定。 您可以新增、移除、啟用或停用由流量管理員設定檔所路由傳送的端點。|
|/trafficManagerProfiles/delete|刪除流量管理員設定檔。 與流量管理員設定檔相關聯的所有設定都會遺失，而且您無法再使用設定檔來路由傳送流量。|
|/dnsoperationstatuses/read|取得 DNS 作業的狀態 |
|/operations/read|取得可用的作業|
|/expressRouteCircuits/read|取得 ExpressRouteCircuit|
|/expressRouteCircuits/write|建立或更新現有的 ExpressRouteCircuit|
|/expressRouteCircuits/delete|刪除 ExpressRouteCircuit|
|/expressRouteCircuits/stats/read|取得 ExpressRouteCircuit 統計資料|
|/expressRouteCircuits/peerings/read|取得 ExpressRouteCircuit 對等互連|
|/expressRouteCircuits/peerings/write|建立或更新現有的 ExpressRouteCircuit 對等互連|
|/expressRouteCircuits/peerings/delete|刪除 ExpressRouteCircuit 對等互連|
|/expressRouteCircuits/peerings/arpTables/action|取得 ExpressRouteCircuit 對等互連 ArpTable|
|/expressRouteCircuits/peerings/routeTables/action|取得 ExpressRouteCircuit 對等互連 RouteTable|
|/expressRouteCircuits/peerings/<br>routeTablesSummary/action|取得 ExpressRouteCircuit 對等互連 RouteTable 摘要|
|/expressRouteCircuits/peerings/stats/read|取得 ExpressRouteCircuit 對等互連統計資料|
|/expressRouteCircuits/authorizations/read|取得 ExpressRouteCircuit 授權|
|/expressRouteCircuits/authorizations/write|建立或更新現有的 ExpressRouteCircuit 授權|
|/expressRouteCircuits/authorizations/delete|刪除 ExpressRouteCircuit 授權|
|/connections/read|取得 VirtualNetworkGatewayConnection|
|/connections/write|建立或更新現有的 VirtualNetworkGatewayConnection|
|/connections/delete|刪除 VirtualNetworkGatewayConnection|
|/connections/sharedKey/read|取得 VirtualNetworkGatewayConnection SharedKey|
|/connections/sharedKey/write|建立或更新現有的 VirtualNetworkGatewayConnection SharedKey|
|/networkSecurityGroups/read|取得網路安全性群組定義|
|/networkSecurityGroups/write|建立網路安全性群組，或更新現有的網路安全性群組|
|/networkSecurityGroups/delete|刪除網路安全性群組|
|/networkSecurityGroups/join/action|加入網路安全性群組|
|/networkSecurityGroups/defaultSecurityRules/read|取得預設的安全性規則定義|
|/networkSecurityGroups/securityRules/read|取得安全性規則定義|
|/networkSecurityGroups/securityRules/write|建立安全性規則，或更新現有的安全性規則|
|/networkSecurityGroups/securityRules/delete|刪除安全性規則|
|/applicationGateways/read|取得應用程式閘道|
|/applicationGateways/write|建立或更新應用程式閘道|
|/applicationGateways/delete|刪除應用程式閘道|
|/applicationGateways/backendhealth/action|取得應用程式閘道後端的健康狀態|
|/applicationGateways/start/action|啟動應用程式閘道|
|/applicationGateways/stop/action|停止應用程式閘道|
|/applicationGateways/backendAddressPools/join/action|加入應用程式閘道後端位址集區|
|/routeTables/read|取得路由表定義|
|/routeTables/write|建立路由表，或更新現有的路由表|
|/routeTables/delete|刪除路由表定義|
|/routeTables/join/action|加入路由表|
|/routeTables/routes/read|取得路由定義|
|/routeTables/routes/write|建立路由，或更新現有路由|
|/routeTables/routes/delete|刪除路由定義|
|/locations/operationResults/read|取得非同步 POST 或 DELETE 作業的作業結果|
|/locations/checkDnsNameAvailability/read|檢查指定的位置是否有 DNS 標籤|
|/locations/usages/read|取得資源使用量計量|
|/locations/operations/read|取得代表非同步作業狀態的作業資源|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| 作業 | 說明 |
|---|---|
|/register/action|針對 NotifciationHubs 資源提供者註冊訂用帳戶，並讓您能夠建立命名空間和 NotificationHubs|
|/CheckNamespaceAvailability/action|檢查在 NotificationHub 服務內，給定的命名空間資源名稱是否可供使用。|
|/Namespaces/write|建立命名空間資源，並更新其屬性。 命名空間的標記和狀態是可以更新的屬性。|
|/Namespaces/read|取得命名空間資源描述的清單|
|/Namespaces/Delete|刪除命名空間資源|
|/Namespaces/authorizationRules/action|取得命名空間授權規則描述的清單。|
|/Namespaces/CheckNotificationHubAvailability/action|檢查在命名空間內，給定的 NotificationHub 名稱是否可供使用。|
|/Namespaces/authorizationRules/write|建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。|
|/Namespaces/authorizationRules/read|取得命名空間授權規則描述的清單。|
|/Namespaces/authorizationRules/delete|刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。 |
|/Namespaces/authorizationRules/listkeys/action|取得命名空間的連接字串|
|/Namespaces/authorizationRules/regenerateKeys/action|命名空間授權規則會重新產生主要/次要金鑰，指定需要重新產生的金鑰|
|/Namespaces/NotificationHubs/write|建立通知中樞，並更新其屬性。 其屬性主要包括 PNS 認證。 授權規則與 TTL|
|/Namespaces/NotificationHubs/read|取得通知中樞資源描述的清單|
|/Namespaces/NotificationHubs/Delete|刪除通知中樞資源|
|/Namespaces/NotificationHubs/authorizationRules/action|取得通知中樞授權規則的清單|
|/Namespaces/NotificationHubs/pnsCredentials/action|取得所有通知中樞的 PNS 認證。 這包括 WNS、MPNS、APNS、GCM 和百度認證|
|/Namespaces/NotificationHubs/debugSend/action|傳送測試推播通知。|
|/Namespaces/NotificationHubs/metricDefinitions/read|取得命名空間計量資源描述的清單|
|/Namespaces/NotificationHubs/<br>authorizationRules/write|建立通知中樞授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。|
|/Namespaces/NotificationHubs/<br>authorizationRules/read|取得通知中樞授權規則的清單|
|/Namespaces/NotificationHubs/<br>authorizationRules/delete|刪除通知中樞的授權規則|
|/Namespaces/NotificationHubs/<br>authorizationRules/listkeys/action|取得通知中樞的連接字串|
|/Namespaces/NotificationHubs/<br>authorizationRules/regenerateKeys/action|通知中樞授權規則會重新產生主要/次要金鑰，指定需要重新產生的金鑰|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| 作業 | 說明 |
|---|---|
|/register/action|向資源提供者註冊訂用帳戶。|
|/linkTargets/read|列出未與 Azure 訂用帳戶相關聯的現有帳戶。 若要將此 Azure 訂用帳戶連結到工作區，請在「建立工作區」作業的客戶識別碼屬性中，使用這項作業所傳回的客戶識別碼。|
|/workspaces/write|建立新的工作區，或藉由提供來自現有工作區的客戶識別碼來連結至現有工作區。|
|/workspaces/read|取得現有工作區|
|/workspaces/delete|刪除工作區。 如果工作區在建立時連結到現有工作區，則系統不會刪除其所連結的工作區。|
|/workspaces/generateregistrationcertificate/action|為工作區產生註冊憑證。 此憑證可用來將 Microsoft System Center Operation Manager 連線到工作區。|
|/workspaces/sharedKeys/action|擷取工作區的共用金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。|
|/workspaces/search/action|執行搜尋查詢|
|/workspaces/datasources/read|取得工作區下的資料來源。|
|/workspaces/datasources/write|在工作區下建立/更新資料來源。|
|/workspaces/datasources/delete|刪除工作區下的資料來源。|
|/workspaces/managementGroups/read|取得與此工作區連線之 System Center Operations Manager 管理群組的名稱和中繼資料。|
|/workspaces/schema/read|取得工作區的搜尋結構描述。  搜尋結構描述包括公開的欄位和其類型。|
|/workspaces/usages/read|取得工作區的使用量資料，包括工作區所讀取的資料量。|
|/workspaces/intelligencepacks/read|列出給定工作區可見的所有智慧套件，並且也會列出該工作區會啟用還是停用套件。|
|/workspaces/intelligencepacks/enable/action|為給定工作區啟用智慧套件。|
|/workspaces/intelligencepacks/disable/action|為給定工作區停用智慧套件。|
|/workspaces/sharedKeys/read|擷取工作區的共用金鑰。 這些金鑰可用來將 Microsoft Operational Insights 代理程式連線到工作區。|
|/workspaces/savedSearches/read|取得已儲存的搜尋查詢|
|/workspaces/savedSearches/write|建立已儲存的搜尋查詢|
|/workspaces/savedSearches/delete|刪除已儲存的搜尋查詢|
|/workspaces/storageinsightconfigs/write|建立新的儲存體組態。 這些組態可用來提取現有儲存體帳戶中來自某個位置的資料。|
|/workspaces/storageinsightconfigs/read|取得儲存體組態。|
|/workspaces/storageinsightconfigs/delete|刪除儲存體組態。 這會讓 Microsoft Operational Insights 停止從儲存體帳戶讀取資料。|
|/workspaces/configurationScopes/read|取得組態範圍|
|/workspaces/configurationScopes/write|設定組態範圍|
|/workspaces/configurationScopes/delete|刪除組態範圍|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| 作業 | 說明 |
|---|---|
|/register/action|向資源提供者註冊訂用帳戶。|
|/solutions/write|建立新的 OMS 解決方案|
|/solutions/read|取得現有的 OMS 解決方案|
|/solutions/delete|刪除現有的 OMS 解決方案|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| 作業 | 說明 |
|---|---|
|/Vaults/backupJobsExport/action|匯出作業|
|/Vaults/write|「建立保存庫」作業會建立 'vault' 類型的 Azure 資源|
|/Vaults/read|「取得保存庫」作業會取得物件，此物件代表 'vault' 類型的 Azure 資源|
|/Vaults/delete|「刪除保存庫」作業會刪除 'vault' 類型的指定 Azure 資源|
|/Vaults/refreshContainers/read|重新整理容器清單|
|/Vaults/backupJobsExport/operationResults/read|傳回匯出作業的作業結果。|
|/Vaults/backupOperationResults/read|傳回復原服務保存庫的備份作業結果。|
|/Vaults/monitoringAlerts/read|取得復原服務保存庫的警示。|
|/Vaults/monitoringAlerts/{uniqueAlertId}/read|取得警示的詳細資料。|
|/Vaults/backupSecurityPIN/read|傳回復原服務保存庫的安全性 PIN 碼資訊。|
|/vaults/replicationEvents/read|讀取任何事件|
|/Vaults/backupProtectableItems/read|傳回所有可保護項目的清單。|
|/vaults/replicationFabrics/read|讀取任何網狀架構|
|/vaults/replicationFabrics/write|建立或更新任何網狀架構|
|/vaults/replicationFabrics/remove/action|移除網狀架構|
|/vaults/replicationFabrics/checkConsistency/action|檢查網狀架構的一致性|
|/vaults/replicationFabrics/delete|刪除任何網狀架構|
|/vaults/replicationFabrics/renewcertificate/action||
|/vaults/replicationFabrics/deployProcessServerImage/action|部署處理序伺服器的映像|
|/vaults/replicationFabrics/reassociateGateway/action|重新關聯閘道|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>read|讀取任何復原服務提供者|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>remove/action|移除復原服務提供者|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>delete|刪除任何復原服務提供者|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/<br>refreshProvider/action|重新整理提供者|
|/vaults/replicationFabrics/replicationStorageClassifications/read|讀取任何存放裝置分類|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/read|讀取任何存放裝置分類對應|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/write|建立或更新任何存放裝置分類對應|
|/vaults/replicationFabrics/replicationStorageClassifications/<br>replicationStorageClassificationMappings/delete|刪除任何存放裝置分類對應|
|/vaults/replicationFabrics/replicationvCenters/read|讀取任何作業|
|/vaults/replicationFabrics/replicationvCenters/write|建立或更新任何作業|
|/vaults/replicationFabrics/replicationvCenters/delete|刪除任何作業|
|/vaults/replicationFabrics/replicationNetworks/read|讀取任何網路|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/read|讀取任何網路對應|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/write|建立或更新任何網路對應|
|/vaults/replicationFabrics/replicationNetworks/<br>replicationNetworkMappings/delete|刪除任何網路對應|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>read|讀取任何保護容器|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>discoverProtectableItem/action|探索可保護的項目|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>write|建立或更新任何保護容器|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>remove/action|移除保護容器|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>switchprotection/action|切換保護容器|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectableItems/read|讀取任何可保護的項目|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/read|讀取任何保護容器對應|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/write|建立或更新任何保護容器對應|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/remove/action|移除保護容器對應|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectionContainerMappings/delete|刪除任何保護容器對應|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/read|讀取任何受保護的項目|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/write|建立或更新任何受保護的項目|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/delete|刪除任何受保護的項目|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/remove/action|移除受保護的項目|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/plannedFailover/action|計劃性容錯移轉|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/unplannedFailover/action|容錯移轉|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/testFailover/action|Test Failover|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/testFailoverCleanup/action|測試容錯移轉清理|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/failoverCommit/action|容錯移轉認可|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/reProtect/action|重新保護受保護的項目|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/updateMobilityService/action|更新行動服務|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/repairReplication/action|修復複寫|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/applyRecoveryPoint/action|套用復原點|
|/vaults/replicationFabrics/replicationProtectionContainers/<br>replicationProtectedItems/recoveryPoints/read|讀取任何複寫復原點|
|/vaults/replicationPolicies/read|讀取任何原則|
|/vaults/replicationPolicies/write|建立或更新任何原則|
|/vaults/replicationPolicies/delete|刪除任何原則|
|/vaults/replicationRecoveryPlans/read|讀取任何復原方案|
|/vaults/replicationRecoveryPlans/write|建立或更新任何復原方案|
|/vaults/replicationRecoveryPlans/delete|刪除任何復原方案|
|/vaults/replicationRecoveryPlans/plannedFailover/action|計劃性容錯移轉復原方案|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|容錯移轉復原方案|
|/vaults/replicationRecoveryPlans/testFailover/action|測試容錯移轉復原方案|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|測試容錯移轉清理復原方案|
|/vaults/replicationRecoveryPlans/failoverCommit/action|容錯移轉認可復原方案|
|/vaults/replicationRecoveryPlans/reProtect/action|重新保護復原方案|
|/Vaults/extendedInformation/read|「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源|
|/Vaults/extendedInformation/write|「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源|
|/Vaults/extendedInformation/delete|「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源|
|/Vaults/backupManagementMetaData/read|傳回復原服務保存庫的備份管理中繼資料。|
|/Vaults/backupProtectionContainers/read|傳回屬於訂用帳戶的所有容器|
|/Vaults/backupFabrics/operationResults/read|傳回作業的狀態|
|/Vaults/backupFabrics/protectionContainers/read|傳回所有已註冊的容器|
|/Vaults/backupFabrics/protectionContainers/<br>operationResults/read|取得對保護容器執行之作業的結果。|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/read|傳回受保護項目的物件詳細資料|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/write|建立備用的受保護項目|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/delete|刪除受保護的項目|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/backup/action|對受保護的項目執行備份。|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/operationResults/read|取得對受保護項目執行之作業的結果。|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/operationStatus/read|傳回對受保護項目執行之作業的狀態。|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/read|取得受保護項目的復原點。|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>restore/action|還原受保護項目的復原點。|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>provisionInstantItemRecovery/action|為受保護的項目佈建即時項目復原|
|/Vaults/backupFabrics/protectionContainers/<br>protectedItems/recoveryPoints/<br>revokeInstantItemRecovery/action|為受保護的項目撤銷即時項目復原|
|/Vaults/usages/read|傳回復原服務保存庫的使用量詳細資料。|
|/vaults/usages/read|讀取任何保存庫使用量|
|/Vaults/certificates/write|「更新資源憑證」作業會更新資源/保存庫的認證憑證。|
|/Vaults/tokenInfo/read|傳回復原服務保存庫的權杖資訊。|
|/vaults/replicationAlertSettings/read|讀取任何警示設定|
|/vaults/replicationAlertSettings/write|建立或更新任何警示設定|
|/Vaults/backupOperations/read|傳回復原服務保存庫的備份作業狀態。|
|/Vaults/storageConfig/read|傳回復原服務保存庫的儲存體組態。|
|/Vaults/storageConfig/write|更新復原服務保存庫的儲存體組態。|
|/Vaults/backupUsageSummaries/read|傳回復原服務之受保護項目和受保護伺服器的摘要。|
|/Vaults/backupProtectedItems/read|傳回所有受保護項目的清單。|
|/Vaults/backupconfig/vaultconfig/read|傳回復原服務保存庫的組態。|
|/Vaults/backupconfig/vaultconfig/write|更新復原服務保存庫的組態。|
|/Vaults/registeredIdentities/write|「註冊服務容器」作業可用來向復原服務註冊容器。|
|/Vaults/registeredIdentities/read|「取得容器」作業可用來取得為資源註冊的容器。|
|/Vaults/registeredIdentities/delete|「取消註冊容器」作業可用來取消註冊容器。|
|/Vaults/registeredIdentities/operationResults/read|「取得作業結果」作業可用來取得以非同步方式提交之作業的作業狀態和結果|
|/vaults/replicationJobs/read|讀取任何作業|
|/vaults/replicationJobs/cancel/action|取消作業|
|/vaults/replicationJobs/restart/action|重新啟動作業|
|/vaults/replicationJobs/resume/action|繼續作業|
|/Vaults/backupPolicies/read|傳回所有保護原則|
|/Vaults/backupPolicies/write|建立保護原則|
|/Vaults/backupPolicies/delete|刪除保護原則|
|/Vaults/backupPolicies/operationResults/read|取得原則作業的結果。|
|/Vaults/backupPolicies/operationStatus/read|取得原則作業的狀態。|
|/Vaults/vaultTokens/read|「保存庫權杖」作業可用來取得保存庫層級後端作業的保存庫權杖。|
|/Vaults/monitoringConfigurations/notificationConfiguration/read|取得復原服務保存庫通知組態。|
|/Vaults/backupJobs/read|傳回所有作業物件|
|/Vaults/backupJobs/cancel/action|取消作業|
|/Vaults/backupJobs/operationResults/read|傳回作業的作業結果。|
|/locations/allocateStamp/action|AllocateStamp 是服務所使用的內部作業|
|/locations/allocatedStamp/read|GetAllocatedStamp 是服務所使用的內部作業|

## <a name="microsoftrelay"></a>Microsoft.Relay

| 作業 | 說明 |
|---|---|
|/checkNamespaceAvailability/action|檢查給定訂用帳戶下的命名空間是否可用。|
|/register/action|針對轉送資源提供者註冊訂用帳戶，並讓您能夠建立轉送資源|
|/namespaces/write|建立命名空間資源，並更新其屬性。 命名空間的標記和狀態是可以更新的屬性。|
|/namespaces/read|取得命名空間資源描述的清單|
|/namespaces/Delete|刪除命名空間資源|
|/namespaces/authorizationRules/write|建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。|
|/namespaces/authorizationRules/delete|刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。 |
|/namespaces/authorizationRules/listkeys/action|取得命名空間的連接字串|
|/namespaces/HybridConnections/write|建立或更新 HybridConnection 屬性。|
|/namespaces/HybridConnections/read|取得 HybridConnection 資源描述的清單|
|/namespaces/HybridConnections/Delete|用來刪除 HybridConnection 資源的作業|
|/namespaces/HybridConnections/authorizationRules/write|建立 HybridConnection 授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。|
|/namespaces/HybridConnections/authorizationRules/delete|用來刪除 HybridConnection 授權規則的作業|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|取得 HybridConnection 的連接字串|
|/namespaces/WcfRelays/write|建立或更新 WcfRelay 屬性。|
|/namespaces/WcfRelays/read|取得 WcfRelay 資源描述的清單|
|/namespaces/WcfRelays/Delete|用來刪除 WcfRelay 資源的作業|
|/namespaces/WcfRelays/authorizationRules/write|建立 WcfRelay 授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。|
|/namespaces/WcfRelays/authorizationRules/delete|用來刪除 WcfRelay 授權規則的作業|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|取得 WcfRelay 的連接字串|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| 作業 | 說明 |
|---|---|
|/AvailabilityStatuses/read|取得指定範圍中所有資源的可用性狀態|
|/AvailabilityStatuses/current/read|取得指定資源的可用性狀態|

## <a name="microsoftresources"></a>Microsoft.Resources

| 作業 | 說明 |
|---|---|
|/checkResourceName/action|檢查資源名稱是否有效。|
|/providers/read|取得提供者清單。|
|/subscriptions/read|取得訂用帳戶清單。|
|/subscriptions/operationresults/read|取得訂用帳戶作業結果。|
|/subscriptions/providers/read|取得或列出資源提供者。|
|/subscriptions/tagNames/read|取得或列出訂用帳戶標記。|
|/subscriptions/tagNames/write|新增訂用帳戶標記。|
|/subscriptions/tagNames/delete|刪除訂用帳戶標記。|
|/subscriptions/tagNames/tagValues/read|取得或列出訂用帳戶標記值。|
|/subscriptions/tagNames/tagValues/write|新增訂用帳戶標記值。|
|/subscriptions/tagNames/tagValues/delete|刪除訂用帳戶標記值。|
|/subscriptions/resources/read|取得訂用帳戶的資源。|
|/subscriptions/resourceGroups/read|取得或列出資源群組。|
|/subscriptions/resourceGroups/write|建立或更新資源群組。|
|/subscriptions/resourceGroups/delete|刪除資源群組和其所有資源。|
|/subscriptions/resourceGroups/moveResources/action|將資源從某個資源群組移到另一個資源群組。|
|/subscriptions/resourceGroups/validateMoveResources/action|驗證資源從某個資源群組移到另一個資源群組的動作。|
|/subscriptions/resourcegroups/resources/read|取得資源群組的資源。|
|/subscriptions/resourcegroups/deployments/read|取得或列出部署。|
|/subscriptions/resourcegroups/deployments/write|建立或更新部署。|
|/subscriptions/resourcegroups/deployments/operationstatuses/read|取得或列出部署作業狀態。|
|/subscriptions/resourcegroups/deployments/operations/read|取得或列出部署作業。|
|/subscriptions/locations/read|取得所支援位置的清單。|
|/links/read|取得或列出資源連結。|
|/links/write|建立或更新資源連結。|
|/links/delete|刪除資源連結。|
|/tenants/read|取得租用戶清單。|
|/resources/read|根據篩選來取得資源清單。|
|/deployments/read|取得或列出部署。|
|/deployments/write|建立或更新部署。|
|/deployments/delete|刪除部署。|
|/deployments/cancel/action|取消部署。|
|/deployments/validate/action|驗證部署。|
|/deployments/operations/read|取得或列出部署作業。|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| 作業 | 說明 |
|---|---|
|/jobcollections/read|取得作業集合|
|/jobcollections/write|建立或更新作業集合。|
|/jobcollections/delete|刪除作業集合。|
|/jobcollections/enable/action|啟用作業集合。|
|/jobcollections/disable/action|停用作業集合。|
|/jobcollections/jobs/read|取得作業。|
|/jobcollections/jobs/write|建立或更新作業。|
|/jobcollections/jobs/delete|刪除作業。|
|/jobcollections/jobs/run/action|執行作業。|
|/jobcollections/jobs/generateLogicAppDefinition/action|根據排程器作業來產生邏輯應用程式定義。|
|/jobcollections/jobs/jobhistories/read|取得工作歷程記錄。|

## <a name="microsoftsearch"></a>Microsoft.Search

| 作業 | 說明 |
|---|---|
|/register/action|針對搜尋資源提供者註冊訂用帳戶，並讓您能夠建立搜尋服務。|
|/checkNameAvailability/action|檢查服務名稱的可用性。|
|/searchServices/write|建立或更新搜尋服務。|
|/searchServices/read|讀取搜尋服務。|
|/searchServices/delete|刪除搜尋服務。|
|/searchServices/start/action|啟動搜尋服務。|
|/searchServices/stop/action|停止搜尋服務。|
|/searchServices/listAdminKeys/action|讀取系統管理金鑰。|
|/searchServices/regenerateAdminKey/action|重新產生系統管理金鑰。|
|/searchServices/createQueryKey/action|建立查詢金鑰。|
|/searchServices/queryKey/read|讀取查詢金鑰。|
|/searchServices/queryKey/delete|刪除查詢金鑰。|

## <a name="microsoftsecurity"></a>Microsoft.Security

| 作業 | 說明 |
|---|---|
|/jitNetworkAccessPolicies/read|取得 Just-in-Time 網路存取原則|
|/jitNetworkAccessPolicies/write|建立新的 Just-in-Time 網路存取原則，或更新現有的 Just-in-Time 網路存取原則|
|/jitNetworkAccessPolicies/initiate/action|起始 Just-in-Time 網路存取原則|
|/securitySolutionsReferenceData/read|取得安全性解決方案參考資料|
|/securityStatuses/read|取得 Azure 資源的安全性健康狀態|
|/webApplicationFirewalls/read|取得 Web 應用程式防火牆|
|/webApplicationFirewalls/write|建立新的 Web 應用程式防火牆，或更新現有的 Web 應用程式防火牆|
|/webApplicationFirewalls/delete|刪除 Web 應用程式防火牆|
|/securitySolutions/read|取得安全性解決方案|
|/securitySolutions/write|建立新的安全性解決方案，或更新現有的安全性解決方案|
|/securitySolutions/delete|刪除安全性解決方案|
|/tasks/read|取得所有可用的安全性建議|
|/tasks/dismiss/action|關閉安全性建議|
|/tasks/activate/action|啟用安全性建議|
|/policies/read|取得安全性原則|
|/policies/write|更新安全性原則|
|/applicationWhitelistings/read|取得應用程式白名單|
|/applicationWhitelistings/write|建立新的應用程式白名單，或更新現有的應用程式白名單|

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

| 作業 | 說明 |
|---|---|
|/subscriptions/write|建立或更新訂用帳戶|
|/gateways/write|建立或更新閘道器|
|/gateways/delete|刪除閘道器|
|/gateways/read|取得閘道器|
|/gateways/regenerateprofile/action|重新產生閘道器設定檔|
|/gateways/upgradetolatest/action|將閘道器升級為最新版本|
|/nodes/write|建立或更新節點|
|/nodes/delete|刪除節點|
|/nodes/read|取得節點|
|/sessions/write|建立或更新工作階段|
|/sessions/read|取得工作階段|
|/sessions/delete|刪除工作階段|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| 作業 | 說明 |
|---|---|
|/checkNameAvailability/action|檢查給定訂用帳戶下的命名空間是否可用。|
|/register/action|針對 ServiceBus 資源提供者註冊訂用帳戶，並讓您能夠建立 ServiceBus 資源|
|/namespaces/write|建立命名空間資源，並更新其屬性。 命名空間的標記和狀態是可以更新的屬性。|
|/namespaces/read|取得命名空間資源描述的清單|
|/namespaces/Delete|刪除命名空間資源|
|/namespaces/metricDefinitions/read|取得命名空間計量資源描述的清單|
|/namespaces/authorizationRules/write|建立命名空間層級的授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。|
|/namespaces/authorizationRules/read|取得命名空間授權規則描述的清單。|
|/namespaces/authorizationRules/delete|刪除命名空間授權規則。 您無法刪除預設的命名空間授權規則。 |
|/namespaces/authorizationRules/listkeys/action|取得命名空間的連接字串|
|/namespaces/authorizationRules/regenerateKeys/action|對資源重新產生主要或次要金鑰|
|/namespaces/diagnosticSettings/read|取得命名空間診斷設定資源描述的清單|
|/namespaces/diagnosticSettings/write|取得命名空間診斷設定資源描述的清單|
|/namespaces/queues/write|建立或更新佇列屬性。|
|/namespaces/queues/read|取得佇列資源描述的清單|
|/namespaces/queues/Delete|用來刪除佇列資源的作業|
|/namespaces/queues/authorizationRules/write|建立佇列授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。|
|/namespaces/queues/authorizationRules/read| 取得佇列授權規則的清單|
|/namespaces/queues/authorizationRules/delete|用來刪除佇列授權規則的作業|
|/namespaces/queues/authorizationRules/listkeys/action|取得佇列的連接字串|
|/namespaces/queues/authorizationRules/regenerateKeys/action|對資源重新產生主要或次要金鑰|
|/namespaces/logDefinitions/read|取得命名空間記錄資源描述的清單|
|/namespaces/topics/write|建立或更新主題屬性。|
|/namespaces/topics/read|取得主題資源描述的清單|
|/namespaces/topics/Delete|用來刪除主題資源的作業|
|/namespaces/topics/authorizationRules/write|建立主題授權規則，並更新其屬性。 您可以更新授權規則的存取權限、主要金鑰和次要金鑰。|
|/namespaces/topics/authorizationRules/read| 取得主題授權規則的清單|
|/namespaces/topics/authorizationRules/delete|用來刪除主題授權規則的作業|
|/namespaces/topics/authorizationRules/listkeys/action|取得主題的連接字串|
|/namespaces/topics/authorizationRules/regenerateKeys/action|對資源重新產生主要或次要金鑰|
|/namespaces/topics/subscriptions/write|建立或更新 TopicSubscription 屬性。|
|/namespaces/topics/subscriptions/read|取得 TopicSubscription 資源描述的清單|
|/namespaces/topics/subscriptions/Delete|用來刪除 TopicSubscription 資源的作業|
|/namespaces/topics/subscriptions/rules/write|建立或更新規則屬性。|
|/namespaces/topics/subscriptions/rules/read|取得規則資源描述的清單|
|/namespaces/topics/subscriptions/rules/Delete|用來刪除規則資源的作業|

## <a name="microsoftsql"></a>Microsoft.Sql

| 作業 | 說明 |
|---|---|
|/servers/read|傳回訂用帳戶之資源群組中的伺服器清單|
|/servers/write|建立新的伺服器，或修改訂用帳戶之資源群組中現有伺服器的屬性|
|/servers/delete|刪除伺服器和內含的所有資料庫與彈性集區|
|/servers/import/action|在伺服器上建立新的資料庫，並部署來自 DacPac 套件的結構描述和資料|
|/servers/upgrade/action|啟用最新版伺服器上可用的新功能，並指定資料庫版本轉換對應|
|/servers/VulnerabilityAssessmentScans/action|執行弱點評估伺服器掃描|
|/servers/operationResults/read|此作業可用來追蹤伺服器從較舊版本升級為更高版本時的進度|
|/servers/operationResults/delete|中止正在進行的伺服器版本升級|
|/servers/securityAlertPolicies/read|擷取給定伺服器上所設定的伺服器威脅偵測原則詳細資料|
|/servers/securityAlertPolicies/write|變更給定伺服器的伺服器威脅偵測|
|/servers/securityAlertPolicies/operationResults/read|擷取伺服器的「威脅偵測原則設定」作業結果|
|/servers/administrators/read|擷取伺服器管理員的詳細資料|
|/servers/administrators/write|建立或更新伺服器管理員|
|/servers/administrators/delete|從伺服器中刪除伺服器管理員|
|/servers/recoverableDatabases/read|這項作業可用於即時資料庫的災害復原，以將資料庫還原至最後一個已知良好的備份點。 它會傳回最後一個良好備份的相關資訊，但它實際上並不會還原資料庫。|
|/servers/serviceObjectives/read|擷取給定伺服器上可用的服務等級目標 (也稱為效能層級) 清單|
|/servers/firewallRules/read|擷取伺服器防火牆規則的詳細資料|
|/servers/firewallRules/write|建立或更新伺服器防火牆規則，此規則會控制能夠連線到伺服器的 IP 位址範圍|
|/servers/firewallRules/delete|從伺服器中刪除防火牆規則|
|/servers/administratorOperationResults/read|擷取伺服器管理員的作業結果|
|/servers/recommendedElasticPools/read|擷取彈性資料庫集區的建議，以根據資源的歷史使用量來降低成本或改善效能|
|/servers/recommendedElasticPools/metrics/read|擷取給定伺服器之建議彈性資料庫集區的計量|
|/servers/recommendedElasticPools/databases/read|擷取應該新增到給定伺服器之建議彈性資料庫集區的資料庫|
|/servers/elasticPools/read|擷取給定伺服器上的彈性資料庫集區詳細資料|
|/servers/elasticPools/write|建立新的彈性資料庫集區，或變更現有彈性資料庫集區的屬性|
|/servers/elasticPools/delete|刪除現有的彈性資料庫集區|
|/servers/elasticPools/operationResults/read|擷取給定彈性資料庫集區作業的詳細資料|
|/servers/elasticPools/providers/Microsoft.Insights/<br>metricDefinitions/read|傳回可供彈性資料庫集區使用之計量的類型|
|/servers/elasticPools/providers/Microsoft.Insights/<br>diagnosticSettings/read|取得資源的診斷設定|
|/servers/elasticPools/providers/Microsoft.Insights/<br>diagnosticSettings/write|建立或更新資源的診斷設定|
|/servers/elasticPools/metrics/read|傳回彈性資料庫集區的資源使用量計量|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|擷取屬於彈性資料庫集區之給定資料庫的活動和詳細資料|
|/servers/elasticPools/advisors/read|傳回彈性集區可用 Advisor 的清單|
|/servers/elasticPools/advisors/write|更新彈性集區層級上 Advisor 的自動執行狀態。|
|/servers/elasticPools/advisors/recommendedActions/read|傳回彈性集區之指定 Advisor 的建議動作清單|
|/servers/elasticPools/advisors/recommendedActions/write|對彈性集區套用建議動作|
|/servers/elasticPools/elasticPoolActivity/read|擷取給定彈性資料庫集區的活動和詳細資料|
|/servers/elasticPools/databases/read|擷取屬於給定伺服器之彈性資料庫集區的資料庫清單和詳細資料|
|/servers/auditingPolicies/read|擷取給定伺服器上所設定之預設伺服器資料表稽核原則的詳細資料|
|/servers/auditingPolicies/write|變更給定伺服器的預設伺服器資料表稽核|
|/servers/disasterRecoveryConfiguration/operationResults/read|取得災害復原設定作業的結果|
|/servers/advisors/read|傳回伺服器可用 Advisor 的清單|
|/servers/advisors/write|更新伺服器層級上 Advisor 的自動執行狀態。|
|/servers/advisors/recommendedActions/read|傳回伺服器之指定 Advisor 的建議動作清單|
|/servers/advisors/recommendedActions/write|對伺服器套用建議動作|
|/servers/usages/read|傳回伺服器內所有資料庫的伺服器 DTU 配額和目前的 DTU 耗用量|
|/servers/elasticPoolEstimates/read|傳回已針對此伺服器建立之彈性集區估計值的清單|
|/servers/elasticPoolEstimates/write|針對所提供的資料庫清單建立新的彈性集區估計值|
|/servers/auditingSettings/read|擷取給定伺服器上所設定之伺服器 Blob 稽核原則的詳細資料|
|/servers/auditingSettings/write|變更給定伺服器的伺服器 Blob 稽核|
|/servers/auditingSettings/operationResults/read|擷取伺服器的「Blob 稽核原則設定」作業結果|
|/servers/backupLongTermRetentionVaults/read|這項作業可用來取得備用的長期保留保存庫。 它會傳回向此伺服器註冊之保存庫的相關資訊。|
|/servers/backupLongTermRetentionVaults/write|註冊備用的長期保留保存庫|
|/servers/restorableDroppedDatabases/read|擷取給定伺服器上雖已捨棄但仍留在保留原則內的資料庫清單。 這項作業會傳回資料庫和相關聯中繼資料的清單，例如刪除日期。|
|/servers/databases/read|傳回訂用帳戶之資源群組中的伺服器清單|
|/servers/databases/write|建立新的伺服器，或修改訂用帳戶之資源群組中現有伺服器的屬性|
|/servers/databases/delete|刪除伺服器和內含的所有資料庫與彈性集區|
|/servers/databases/export/action|在伺服器上建立新的資料庫，並部署來自 DacPac 套件的結構描述和資料|
|/servers/databases/VulnerabilityAssessmentScans/action|執行弱點評估資料庫掃描。|
|/servers/databases/pause/action|暫停 DataWarehouse 版本的資料庫|
|/servers/databases/resume/action|繼續 DataWarehouse 版本的資料庫|
|/servers/databases/operationResults/read|此作業可用來追蹤長時間執行之資料庫作業 (例如調整) 的進度。|
|/servers/databases/replicationLinks/read|傳回為特定資料庫所建立之複寫連結的詳細資料|
|/servers/databases/replicationLinks/delete|強制終止複寫關係，但可能遺失資料|
|/servers/databases/replicationLinks/unlink/action|強制終止複寫關係，或在與合作夥伴進行同步處理之後終止|
|/servers/databases/replicationLinks/failover/action|從主要資料庫同步處理所有變更後進行容錯移轉，讓此資料庫變成複寫關係的主要資料庫，並讓遠端的主要資料庫變成次要資料庫|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|立即進行容錯移轉 (但可能遺失資料)，讓此資料庫變成複寫關係的主要資料庫，並讓遠端的主要資料庫變成次要資料庫|
|/servers/databases/replicationLinks/updateReplicationMode/action|將連結的複寫模式更新為同步或非同步模式|
|/servers/databases/replicationLinks/operationResults/read|取得資料庫複寫連結上長時間執行之作業的狀態|
|/servers/databases/dataMaskingPolicies/read|擷取給定資料庫上所設定之資料遮罩原則的詳細資料|
|/servers/databases/dataMaskingPolicies/write|變更給定伺服器的資料遮罩原則|
|/servers/databases/dataMaskingPolicies/rules/read|擷取給定資料庫上所設定之資料遮罩原則規則的詳細資料|
|/servers/databases/dataMaskingPolicies/rules/write|變更給定伺服器的資料遮罩原則規則|
|/servers/databases/securityAlertPolicies/read|擷取給定資料庫上所設定的威脅偵測原則詳細資料|
|/servers/databases/securityAlertPolicies/write|變更給定資料庫的威脅偵測原則|
|/servers/databases/providers/Microsoft.Insights/<br>metricDefinitions/read|傳回可供資料庫使用之計量的類型|
|/servers/databases/providers/Microsoft.Insights/<br>diagnosticSettings/read|取得資源的診斷設定|
|/servers/databases/providers/Microsoft.Insights/<br>diagnosticSettings/write|建立或更新資源的診斷設定|
|/servers/databases/providers/Microsoft.Insights/<br>logDefinitions/read|取得資料庫的可用記錄|
|/servers/databases/topQueries/read|傳回所選查詢在所選時段內彙總的執行階段統計資料|
|/servers/databases/topQueries/queryText/read|傳回所選查詢識別碼的 Transact-SQL 文字|
|/servers/databases/topQueries/statistics/read|傳回所選查詢在所選時段內彙總的執行階段統計資料|
|/servers/databases/connectionPolicies/read|擷取給定資料庫上所設定的連線原則詳細資料|
|/servers/databases/connectionPolicies/write|變更給定資料庫的連線原則|
|/servers/databases/metrics/read|傳回資料庫資源使用量計量|
|/servers/databases/auditRecords/read|擷取資料庫 Blob 稽核記錄|
|/servers/databases/transparentDataEncryption/read|擷取給定資料庫之透明資料加密安全性功能的狀態和詳細資料|
|/servers/databases/transparentDataEncryption/write|啟用或停用給定資料庫的透明資料加密|
|/servers/databases/transparentDataEncryption/operationResults/read|擷取給定資料庫之透明資料加密安全性功能的狀態和詳細資料|
|/servers/databases/auditingPolicies/read|擷取給定資料庫上所設定之資料表稽核原則的詳細資料|
|/servers/databases/auditingPolicies/write|變更給定資料庫的資料表稽核原則|
|/servers/databases/dataWarehouseQueries/read|傳回所選查詢識別碼的資料倉儲散發查詢資訊|
|/servers/databases/dataWarehouseQueries/<br>dataWarehouseQuerySteps/read|傳回所選步驟識別碼之資料倉儲查詢的分散式查詢步驟資訊|
|/servers/databases/serviceTierAdvisors/read|傳回相關建議，以讓您根據查詢執行統計資料來相應增加或減少資料庫，以提升效能或降低成本|
|/servers/databases/advisors/read|傳回資料庫可用 Advisor 的清單|
|/servers/databases/advisors/write|更新資料庫層級上 Advisor 的自動執行狀態。|
|/servers/databases/advisors/recommendedActions/read|傳回資料庫之指定 Advisor 的建議動作清單|
|/servers/databases/advisors/recommendedActions/write|對資料庫套用建議動作|
|/servers/databases/usages/read|傳回資料庫可以達到的大小上限和資料目前所佔用的大小|
|/servers/databases/queryStore/read|傳回資料庫之查詢存放區設定目前的值|
|/servers/databases/queryStore/write|更新資料庫的查詢存放區設定|
|/servers/databases/auditingSettings/read|擷取給定資料庫上所設定之 Blob 稽核原則的詳細資料|
|/servers/databases/auditingSettings/write|變更給定資料庫的 Blob 稽核原則|
|/servers/databases/schemas/tables/recommendedIndexes/read|擷取資料庫上的索引建議清單|
|/servers/databases/schemas/tables/recommendedIndexes/write|套用索引建議|
|/servers/databases/schemas/tables/columns/read|擷取資料表的資料行清單|
|/servers/databases/missingindexes/read|傳回相關建議，讓您知道若要提升查詢效能所應建立、修改或刪除的資料庫索引|
|/servers/databases/missingindexes/write|在特定資料庫中使用資料庫索引建議|
|/servers/databases/importExportOperationResults/read|傳回有關資料庫匯入或匯出作業的詳細資料，此作業是從位於儲存體帳戶的 DacPac 來進行|
|/servers/importExportOperationResults/read|傳回從給定伺服器的儲存體帳戶所進行之資料庫匯入作業的清單與詳細資料|

## <a name="microsoftstorage"></a>Microsoft.Storage

| 作業 | 說明 |
|---|---|
|/register/action|針對儲存體資源提供者註冊訂用帳戶，並讓您能夠建立儲存體帳戶。|
|/checknameavailability/read|確認帳戶名稱有效，且並非使用中。|
|/storageAccounts/write|使用指定參數來建立儲存體帳戶、更新指定儲存體帳戶的屬性或標記，或新增指定儲存體帳戶的自訂網域。|
|/storageAccounts/delete|刪除現有的儲存體帳戶。|
|/storageAccounts/listkeys/action|傳回指定儲存體帳戶的存取金鑰。|
|/storageAccounts/regeneratekey/action|重新產生指定儲存體帳戶的存取金鑰。|
|/storageAccounts/read|傳回儲存體帳戶清單，或取得指定儲存體帳戶的屬性。|
|/storageAccounts/listAccountSas/action|傳回指定儲存體帳戶的帳戶 SAS 權杖。|
|/storageAccounts/listServiceSas/action|儲存體服務的 SAS 權杖|
|/storageAccounts/services/diagnosticSettings/write|建立/更新儲存體帳戶的診斷設定。|
|/skus/read|列出 Microsoft.Storage 所支援的 SKU。|
|/usages/read|傳回指定訂用帳戶資源的限制和目前的使用量計數|
|/operations/read|輪詢非同步作業的狀態。|
|/locations/deleteVirtualNetworkOrSubnets/action|讓 Microsoft.Storage 知道虛擬網路或子網路即將刪除|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| 作業 | 說明 |
|---|---|
|/managers/clearAlerts/action|清除與裝置管理員相關聯的所有警示。|
|/managers/getActivationKey/action|取得裝置管理員的啟用金鑰。|
|/managers/regenerateActivationKey/action|重新產生裝置管理員的啟用金鑰。|
|/managers/regenarateRegistationCertificate/action|重新產生裝置管理員的註冊憑證。|
|/managers/getEncryptionKey/action|取得裝置管理員的加密金鑰。|
|/managers/read|列出或取得裝置管理員|
|/managers/delete|刪除裝置管理員|
|/managers/write|建立或更新裝置管理員|
|/managers/configureDevice/action|設定裝置|
|/managers/listActivationKey/action|取得 StorSimple 裝置管理員的啟用金鑰。|
|/managers/listPublicEncryptionKey/action|列出 StorSimple 裝置管理員的公用加密金鑰。|
|/managers/listPrivateEncryptionKey/action|取得 StorSimple 裝置管理員的私密加密金鑰。|
|/managers/provisionCloudAppliance/action|建立新的雲端應用裝置。|
|/Managers/write|「建立保存庫」作業會建立 'vault' 類型的 Azure 資源|
|/Managers/read|「取得保存庫」作業會取得物件，此物件代表 'vault' 類型的 Azure 資源|
|/Managers/delete|「刪除保存庫」作業會刪除 'vault' 類型的指定 Azure 資源|
|/managers/storageAccountCredentials/write|建立或更新儲存體帳戶認證|
|/managers/storageAccountCredentials/read|列出或取得儲存體帳戶認證|
|/managers/storageAccountCredentials/delete|刪除儲存體帳戶認證|
|/managers/storageAccountCredentials/listAccessKey/action|列出儲存體帳戶認證的存取金鑰|
|/managers/accessControlRecords/read|列出或取得存取控制記錄|
|/managers/accessControlRecords/write|建立或更新存取控制記錄|
|/managers/accessControlRecords/delete|刪除存取控制記錄|
|/managers/metrics/read|列出或取得計量|
|/managers/bandwidthSettings/read|列出頻寬設定 (僅限 8000 系列)|
|/managers/bandwidthSettings/write|新建或更新頻寬設定 (僅限 8000 系列)|
|/managers/bandwidthSettings/delete|刪除現有頻寬設定 (僅限 8000 系列)|
|/Managers/extendedInformation/read|「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源|
|/Managers/extendedInformation/write|「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源|
|/Managers/extendedInformation/delete|「取得延伸資訊」作業會取得物件的延伸資訊，此延伸資訊代表 'vault' 類型的 Azure 資源|
|/managers/alerts/read|列出或取得警示|
|/managers/storageDomains/read|列出或取得儲存體網域|
|/managers/storageDomains/write|建立或更新儲存體網域|
|/managers/storageDomains/delete|刪除儲存體網域|
|/managers/devices/scanForUpdates/action|掃描裝置的更新。|
|/managers/devices/download/action|下載裝置的更新。|
|/managers/devices/install/action|在裝置上安裝更新。|
|/managers/devices/read|列出或取得裝置|
|/managers/devices/write|建立或更新裝置|
|/managers/devices/delete|刪除裝置|
|/managers/devices/deactivate/action|停用裝置。|
|/managers/devices/publishSupportPackage/action|發佈裝置的支援套件以供進行 Microsoft 支援服務疑難排解。|
|/managers/devices/failover/action|裝置的容錯移轉。|
|/managers/devices/sendTestAlertEmail/action|傳送測試警示電子郵件給所設定的電子郵件收件者。|
|/managers/devices/installUpdates/action|在裝置上安裝更新|
|/managers/devices/listFailoverSets/action|列出現有裝置的容錯移轉集。|
|/managers/devices/listFailoverTargets/action|列出裝置的容錯移轉目標|
|/managers/devices/publicEncryptionKey/action|列出裝置管理員的公用加密金鑰|
|/managers/devices/hardwareComponentGroups/<br>read|列出硬體元件群組|
|/managers/devices/hardwareComponentGroups/<br>changeControllerPowerState/action|變更硬體元件群組的控制器電源狀態|
|/managers/devices/metrics/read|列出或取得計量|
|/managers/devices/chapSettings/write|建立或更新 Chap 設定|
|/managers/devices/chapSettings/read|列出或取得 Chap 設定|
|/managers/devices/chapSettings/delete|刪除 Chap 設定|
|/managers/devices/backupScheduleGroups/read|列出或取得備份排程群組|
|/managers/devices/backupScheduleGroups/write|建立或更新備份排程群組|
|/managers/devices/backupScheduleGroups/delete|刪除備份排程群組|
|/managers/devices/updateSummary/read|列出或取得更新摘要|
|/managers/devices/migrationSourceConfigurations/<br>import/action|匯入用於移轉的來源組態|
|/managers/devices/migrationSourceConfigurations/<br>startMigrationEstimate/action|啟動作業來預估移轉程序的持續時間。|
|/managers/devices/migrationSourceConfigurations/<br>startMigration/action|使用來源組態來開始移轉|
|/managers/devices/migrationSourceConfigurations/<br>confirmMigration/action|確認移轉成功，並加以認可。|
|/managers/devices/migrationSourceConfigurations/<br>fetchMigrationEstimate/action|提取移轉估計作業的狀態。|
|/managers/devices/migrationSourceConfigurations/<br>fetchMigrationStatus/action|提取移轉狀態。|
|/managers/devices/migrationSourceConfigurations/<br>fetchConfirmMigrationStatus/action|提取移轉的確認狀態。|
|/managers/devices/alertSettings/read|列出或取得警示設定|
|/managers/devices/alertSettings/write|建立或更新警示設定|
|/managers/devices/networkSettings/read|列出或取得網路設定|
|/managers/devices/networkSettings/write|新建或更新網路設定|
|/managers/devices/jobs/read|列出或取得作業|
|/managers/devices/jobs/cancel/action|取消正在執行的作業|
|/managers/devices/metricsDefinitions/read|列出或取得計量定義|
|/managers/devices/volumeContainers/write|新建或更新磁碟區容器 (僅限 8000 系列)|
|/managers/devices/volumeContainers/read|列出磁碟區容器 (僅限 8000 系列)|
|/managers/devices/volumeContainers/delete|刪除現有的磁碟區容器 (僅限 8000 系列)|
|/managers/devices/volumeContainers/listEncryptionKeys/action|列出磁碟區容器的加密金鑰|
|/managers/devices/volumeContainers/rolloverEncryptionKey/action|變換磁碟區容器的加密金鑰|
|/managers/devices/volumeContainers/metrics/read|列出計量|
|/managers/devices/volumeContainers/volumes/read|列出磁碟區|
|/managers/devices/volumeContainers/volumes/write|新建或更新磁碟區|
|/managers/devices/volumeContainers/volumes/delete|刪除現有磁碟區|
|/managers/devices/volumeContainers/volumes/metrics/read|列出計量|
|/managers/devices/volumeContainers/volumes/metricsDefinitions/read|列出計量定義|
|/managers/devices/volumeContainers/metricsDefinitions/read|列出計量定義|
|/managers/devices/iscsiservers/read|列出或取得 iSCSI 伺服器|
|/managers/devices/iscsiservers/write|建立或更新 iSCSI 伺服器|
|/managers/devices/iscsiservers/delete|刪除 iSCSI 伺服器|
|/managers/devices/iscsiservers/backup/action|製作 iSCSI 伺服器的備份。|
|/managers/devices/iscsiservers/metrics/read|列出或取得計量|
|/managers/devices/iscsiservers/disks/read|列出或取得磁碟|
|/managers/devices/iscsiservers/disks/write|建立或更新磁碟|
|/managers/devices/iscsiservers/disks/delete|刪除磁碟|
|/managers/devices/iscsiservers/disks/metrics/read|列出或取得計量|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|列出或取得計量定義|
|/managers/devices/iscsiservers/metricsDefinitions/read|列出或取得計量定義|
|/managers/devices/backups/read|列出或取得備份組|
|/managers/devices/backups/delete|刪除備份組|
|/managers/devices/backups/restore/action|從備份組還原所有磁碟區。|
|/managers/devices/backups/elements/clone/action|使用備份元素來複製共用或磁碟區。|
|/managers/devices/backupPolicies/write|新建或更新備份原則 (僅限 8000 系列)|
|/managers/devices/backupPolicies/read|列出備份原則 (僅限 8000 系列)|
|/managers/devices/backupPolicies/delete|刪除現有的備份原則 (僅限 8000 系列)|
|/managers/devices/backupPolicies/backup/action|製作手動備份，以針對原則所保護的所有磁碟區建立隨選備份。|
|/managers/devices/backupPolicies/schedules/write|新建或更新排程|
|/managers/devices/backupPolicies/schedules/read|列出排程|
|/managers/devices/backupPolicies/schedules/delete|刪除現有排程|
|/managers/devices/securitySettings/update/action|更新安全性設定。|
|/managers/devices/securitySettings/read|列出安全性設定|
|/managers/devices/securitySettings/<br>syncRemoteManagementCertificate/action|同步處理裝置的遠端管理憑證。|
|/managers/devices/securitySettings/write|新建或更新安全性設定|
|/managers/devices/fileservers/read|列出或取得檔案伺服器|
|/managers/devices/fileservers/write|建立或更新檔案伺服器|
|/managers/devices/fileservers/delete|刪除檔案伺服器|
|/managers/devices/fileservers/backup/action|製作檔案伺服器的備份。|
|/managers/devices/fileservers/metrics/read|列出或取得計量|
|/managers/devices/fileservers/shares/write|建立或更新共用|
|/managers/devices/fileservers/shares/read|列出或取得共用|
|/managers/devices/fileservers/shares/delete|刪除共用|
|/managers/devices/fileservers/shares/metrics/read|列出或取得計量|
|/managers/devices/fileservers/shares/metricsDefinitions/read|列出或取得計量定義|
|/managers/devices/fileservers/metricsDefinitions/read|列出或取得計量定義|
|/managers/devices/timeSettings/read|列出或取得時間設定|
|/managers/devices/timeSettings/write|新建或更新時間設定|
|/Managers/certificates/write|「更新資源憑證」作業會更新資源/保存庫的認證憑證。|
|/managers/cloudApplianceConfigurations/read|列出雲端應用裝置所支援的組態|
|/managers/metricsDefinitions/read|列出或取得計量定義|
|/managers/encryptionSettings/read|列出或取得加密設定|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| 作業 | 說明 |
|---|---|
|/streamingjobs/Start/action|啟動串流分析作業|
|/streamingjobs/Stop/action|停止串流分析作業|
|/streamingjobs/Read|讀取串流分析作業|
|/streamingjobs/Write|寫入串流分析作業|
|/streamingjobs/Delete|刪除串流分析作業|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|取得 streamingjobs 的可用計量|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|讀取診斷設定。|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|寫入診斷設定。|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|取得 streamingjobs 的可用記錄|
|/streamingjobs/transformations/Read|讀取串流分析作業轉換|
|/streamingjobs/transformations/Write|寫入串流分析作業轉換|
|/streamingjobs/transformations/Delete|刪除串流分析作業轉換|
|/streamingjobs/inputs/Read|讀取串流分析作業輸入|
|/streamingjobs/inputs/Write|寫入串流分析作業輸入|
|/streamingjobs/inputs/Delete|刪除串流分析作業輸入|
|/streamingjobs/outputs/Read|讀取串流分析作業輸出|
|/streamingjobs/outputs/Write|寫入串流分析作業輸出|
|/streamingjobs/outputs/Delete|刪除串流分析作業輸出|

## <a name="microsoftsupport"></a>Microsoft.Support

| 作業 | 說明 |
|---|---|
|/register/action|支援資源提供者的暫存器|
|/supportTickets/read|取得支援票證的詳細資料 (包括狀態、嚴重性、連絡人詳細資料和通訊)，或取得跨訂用帳戶之支援票證的清單。|
|/supportTickets/write|建立或更新支援票證。 您可以建立技術、帳單、配額或訂用帳戶管理相關問題的支援票證。 您可以更新現有支援票證的嚴重性、連絡人詳細資料和通訊。|

## <a name="microsoftweb"></a>Microsoft.Web

| 作業 | 說明 |
|---|---|
|/unregister/action|針對訂用帳戶取消註冊 Microsoft.Web 資源提供者。|
|/validate/action|驗證。|
|/register/action|針對訂用帳戶註冊 Microsoft.Web 資源提供者。|
|/hostingEnvironments/Read|取得 App Service 環境的屬性|
|/hostingEnvironments/Write|建立新的 App Service 環境，或更新現有的 App Service 環境|
|/hostingEnvironments/Delete|刪除 App Service 環境|
|/hostingEnvironments/reboot/Action|將 App Service 環境中的所有機器重新開機|
|/hostingenvironments/resume/action|繼續裝載環境。|
|/hostingenvironments/suspend/action|暫止裝載環境。|
|/hostingenvironments/metricdefinitions/read|取得裝載環境的計量定義。|
|/hostingEnvironments/workerPools/Read|取得 App Service 環境中背景工作集區的屬性|
|/hostingEnvironments/workerPools/Write|在 App Service 環境中建立新的背景工作集區，或更新 App Service 環境中現有的背景工作集區|
|/hostingenvironments/workerpools/metricdefinitions/read|取得裝載環境 Workerpools 計量定義。|
|/hostingenvironments/workerpools/metrics/read|取得裝載環境 Workerpools 計量。|
|/hostingenvironments/workerpools/skus/read|取得裝載環境 Workerpools SKU。|
|/hostingenvironments/workerpools/usages/read|取得裝載環境 Workerpools 使用量。|
|/hostingenvironments/sites/read|取得裝載環境 Web Apps。|
|/hostingenvironments/serverfarms/read|取得裝載環境 App Service 方案。|
|/hostingenvironments/usages/read|取得裝載環境使用量。|
|/hostingenvironments/capacities/read|取得裝載環境容量。|
|/hostingenvironments/operations/read|取得裝載環境作業。|
|/hostingEnvironments/multiRolePools/Read|取得 App Service 環境中前端集區的屬性|
|/hostingEnvironments/multiRolePools/Write|在 App Service 環境中建立新的前端集區，或更新 App Service 環境中現有的前端集區|
|/hostingenvironments/multirolepools/metricdefinitions/read|取得裝載環境 MultiRole 集區計量定義。|
|/hostingenvironments/multirolepools/metrics/read|取得裝載環境 MultiRole 集區計量。|
|/hostingenvironments/multirolepools/skus/read|取得裝載環境 MultiRole 集區 SKU。|
|/hostingenvironments/multirolepools/usages/read|取得裝載環境 MultiRole 集區使用量。|
|/hostingenvironments/diagnostics/read|取得裝載環境診斷。|
|/publishingusers/read|取得發佈使用者。|
|/publishingusers/write|更新發佈使用者。|
|/checknameavailability/read|檢查資源名稱是否可供使用。|
|/geoRegions/Read|取得地理區域清單。|
|/sites/Read|取得 Web 應用程式的屬性|
|/sites/Write|建立新的 Web 應用程式，或更新現有 Web 應用程式|
|/sites/Delete|刪除現有的 Web 應用程式|
|/sites/backup/Action|建立新的 Web 應用程式備份|
|/sites/publishxml/Action|取得 Web 應用程式的發行設定檔 xml|
|/sites/publish/Action|發佈 Web 應用程式|
|/sites/restart/Action|重新啟動 Web 應用程式|
|/sites/start/Action|啟動 Web 應用程式|
|/sites/stop/Action|停止 Web 應用程式|
|/sites/slotsswap/Action|交換 Web 應用程式部署位置|
|/sites/slotsdiffs/Action|取得 Web 應用程式和位置之間的組態差異|
|/sites/applySlotConfig/Action|將目標位置的 Web 應用程式位置組態套用到目前的 Web 應用程式|
|/sites/resetSlotConfig/Action|重設 Web 應用程式組態|
|/sites/functions/action|函式 Web Apps。|
|/sites/listsyncfunctiontriggerstatus/action|列出同步函式觸發程序狀態 Web Apps。|
|/sites/networktrace/action|網路追蹤 Web Apps。|
|/sites/newpassword/action|Newpassword Web Apps。|
|/sites/sync/action|同步處理 Web Apps。|
|/sites/operationresults/read|取得 Web Apps 的作業結果。|
|/sites/webjobs/read|取得 Web Apps WebJobs。|
|/sites/backup/read|取得 Web Apps 備份。|
|/sites/backup/write|更新 Web Apps 備份。|
|/sites/metricdefinitions/read|取得 Web Apps 計量定義。|
|/sites/metrics/read|取得 Web Apps 計量。|
|/sites/continuouswebjobs/delete|刪除 Web Apps 的連續 Web 作業。|
|/sites/continuouswebjobs/read|取得 Web Apps 的連續 Web 作業。|
|/sites/continuouswebjobs/start/action|啟動 Web Apps 的連續 Web 作業。|
|/sites/continuouswebjobs/stop/action|停止 Web Apps 的連續 Web 作業。|
|/sites/domainownershipidentifiers/read|取得 Web Apps 網域擁有權識別碼。|
|/sites/domainownershipidentifiers/write|更新 Web Apps 網域擁有權識別碼。|
|/sites/premieraddons/delete|刪除 Web Apps 的頂級附加元件。|
|/sites/premieraddons/read|取得 Web Apps 的頂級附加元件。|
|/sites/premieraddons/write|更新 Web Apps 的頂級附加元件。|
|/sites/triggeredwebjobs/delete|刪除 Web Apps 所觸發的 WebJobs。|
|/sites/triggeredwebjobs/read|取得 Web Apps 所觸發的 WebJobs。|
|/sites/triggeredwebjobs/run/action|執行 Web Apps 所觸發的 WebJobs。|
|/sites/hostnamebindings/delete|刪除 Web Apps 的主機名稱繫結。|
|/sites/hostnamebindings/read|取得 Web Apps 的主機名稱繫結。|
|/sites/hostnamebindings/write|更新 Web Apps 的主機名稱繫結。|
|/sites/virtualnetworkconnections/delete|刪除 Web Apps 的虛擬網路連線。|
|/sites/virtualnetworkconnections/read|取得 Web Apps 的虛擬網路連線。|
|/sites/virtualnetworkconnections/write|更新 Web Apps 的虛擬網路連線。|
|/sites/virtualnetworkconnections/gateways/read|取得 Web Apps 的虛擬網路連線閘道器。|
|/sites/virtualnetworkconnections/gateways/write|更新 Web Apps 的虛擬網路連線閘道器。|
|/sites/publishxml/read|取得 Web Apps 的發佈 XML。|
|/sites/hybridconnectionrelays/read|取得 Web Apps 的混合式連線轉送。|
|/sites/perfcounters/read|取得 Web Apps 的效能計數器。|
|/sites/usages/read|取得 Web Apps 使用量。|
|/sites/slots/Write|建立新的 Web 應用程式位置，或更新現有的 Web 應用程式位置|
|/sites/slots/Delete|刪除現有的 Web 應用程式位置|
|/sites/slots/backup/Action|建立新的 Web 應用程式位置備份。|
|/sites/slots/publishxml/Action|取得 Web 應用程式位置的發行設定檔 xml|
|/sites/slots/publish/Action|發佈 Web 應用程式位置|
|/sites/slots/restart/Action|重新啟動 Web 應用程式位置|
|/sites/slots/start/Action|啟動 Web 應用程式位置|
|/sites/slots/stop/Action|停止 Web 應用程式位置|
|/sites/slots/slotsswap/Action|交換 Web 應用程式部署位置|
|/sites/slots/slotsdiffs/Action|取得 Web 應用程式和位置之間的組態差異|
|/sites/slots/applySlotConfig/Action|將目標位置的 Web 應用程式位置組態套用到目前的位置。|
|/sites/slots/resetSlotConfig/Action|重設 Web 應用程式位置組態|
|/sites/slots/Read|取得 Web 應用程式部署位置的屬性|
|/sites/slots/newpassword/action|Newpassword Web Apps 位置。|
|/sites/slots/sync/action|同步處理 Web Apps 的位置。|
|/sites/slots/operationresults/read|取得 Web Apps 位置的作業結果。|
|/sites/slots/webjobs/read|取得 Web Apps 位置的 WebJobs。|
|/sites/slots/backup/write|更新 Web Apps 位置的備份。|
|/sites/slots/metricdefinitions/read|取得 Web Apps 位置的計量定義。|
|/sites/slots/metrics/read|取得 Web Apps 位置的計量。|
|/sites/slots/continuouswebjobs/delete|刪除 Web Apps 位置的連續 Web 作業。|
|/sites/slots/continuouswebjobs/read|取得 Web Apps 位置的連續 Web 作業。|
|/sites/slots/continuouswebjobs/start/action|啟動 Web Apps 位置的連續 Web 作業。|
|/sites/slots/continuouswebjobs/stop/action|停止 Web Apps 位置的連續 Web 作業。|
|/sites/slots/premieraddons/delete|刪除 Web Apps 位置的頂級附加元件。|
|/sites/slots/premieraddons/read|取得 Web Apps 位置的頂級附加元件。|
|/sites/slots/premieraddons/write|更新 Web Apps 位置的頂級附加元件。|
|/sites/slots/triggeredwebjobs/delete|刪除 Web Apps 位置所觸發的 WebJobs。|
|/sites/slots/triggeredwebjobs/read|取得 Web Apps 位置所觸發的 WebJobs。|
|/sites/slots/triggeredwebjobs/run/action|執行 Web Apps 位置所觸發的 WebJobs。|
|/sites/slots/hostnamebindings/delete|刪除 Web Apps 位置的主機名稱繫結。|
|/sites/slots/hostnamebindings/read|取得 Web Apps 位置的主機名稱繫結。|
|/sites/slots/hostnamebindings/write|更新 Web Apps 位置的主機名稱繫結。|
|/sites/slots/phplogging/read|取得 Web Apps 位置的 Phplogging。|
|/sites/slots/virtualnetworkconnections/delete|刪除 Web Apps 位置的虛擬網路連線。|
|/sites/slots/virtualnetworkconnections/read|取得 Web Apps 位置的虛擬網路連線。|
|/sites/slots/virtualnetworkconnections/write|更新 Web Apps 位置的虛擬網路連線。|
|/sites/slots/virtualnetworkconnections/gateways/write|更新 Web Apps 位置的虛擬網路連線閘道器。|
|/sites/slots/usages/read|取得 Web Apps 位置的使用量。|
|/sites/slots/hybridconnection/delete|刪除 Web Apps 位置的混合式連線。|
|/sites/slots/hybridconnection/read|取得 Web Apps 位置的混合式連線。|
|/sites/slots/hybridconnection/write|更新 Web Apps 位置的混合式連線。|
|/sites/slots/config/Read|取得 Web 應用程式位置的組態設定|
|/sites/slots/config/list/Action|列出 Web 應用程式位置的安全性機密設定，例如發佈認證、應用程式設定和連接字串|
|/sites/slots/config/Write|更新 Web 應用程式位置的組態設定|
|/sites/slots/config/delete|刪除 Web Apps 的位置設定。|
|/sites/slots/instances/read|取得 Web Apps 位置的執行個體。|
|/sites/slots/instances/processes/read|取得 Web Apps 位置的執行個體處理序。|
|/sites/slots/instances/deployments/read|取得 Web Apps 位置的執行個體部署。|
|/sites/slots/sourcecontrols/Read|取得 Web 應用程式位置的原始檔控制組態設定|
|/sites/slots/sourcecontrols/Write|更新 Web 應用程式位置的原始檔控制組態設定|
|/sites/slots/sourcecontrols/Delete|刪除 Web 應用程式位置的原始檔控制組態設定|
|/sites/slots/restore/read|取得 Web Apps 位置的還原。|
|/sites/slots/analyzecustomhostname/read|取得 Web Apps 位置的分析自訂主機名稱。|
|/sites/slots/backups/Read|取得 Web 應用程式位置備份的屬性|
|/sites/slots/backups/list/action|列出 Web Apps 位置的備份。|
|/sites/slots/backups/restore/action|還原 Web Apps 位置的備份。|
|/sites/slots/deployments/delete|刪除 Web Apps 的位置部署。|
|/sites/slots/deployments/read|取得 Web Apps 的位置部署。|
|/sites/slots/deployments/write|更新 Web Apps 的位置部署。|
|/sites/slots/deployments/log/read|取得 Web Apps 的位置部署記錄。|
|/sites/hybridconnection/delete|刪除 Web Apps 的混合式連線。|
|/sites/hybridconnection/read|取得 Web Apps 的混合式連線。|
|/sites/hybridconnection/write|更新 Web Apps 的混合式連線。|
|/sites/recommendationhistory/read|取得 Web Apps 的建議歷程記錄。|
|/sites/recommendations/Read|取得 Web 應用程式的建議清單。|
|/sites/recommendations/disable/action|停用 Web Apps 建議。|
|/sites/config/Read|取得 Web 應用程式的組態設定|
|/sites/config/list/Action|列出 Web 應用程式的安全性機密設定，例如發佈認證、應用程式設定和連接字串|
|/sites/config/Write|更新 Web 應用程式的組態設定|
|/sites/config/delete|刪除 Web Apps 設定。|
|/sites/instances/read|取得 Web Apps 執行個體。|
|/sites/instances/processes/delete|刪除 Web Apps 的執行個體處理序。|
|/sites/instances/processes/read|取得 Web Apps 的執行個體處理序。|
|/sites/instances/deployments/read|取得 Web Apps 的執行個體部署。|
|/sites/sourcecontrols/Read|取得 Web 應用程式的原始檔控制組態設定|
|/sites/sourcecontrols/Write|更新 Web 應用程式的原始檔控制組態設定|
|/sites/sourcecontrols/Delete|刪除 Web 應用程式的原始檔控制組態設定|
|/sites/restore/read|取得 Web Apps 還原。|
|/sites/analyzecustomhostname/read|分析自訂主機名稱。|
|/sites/backups/Read|取得 Web 應用程式備份的屬性|
|/sites/backups/list/action|列出 Web Apps 備份。|
|/sites/backups/restore/action|還原 Web Apps 備份。|
|/sites/snapshots/read|取得 Web Apps 快照集。|
|/sites/functions/delete|刪除 Web Apps 函式。|
|/sites/functions/listsecrets/action|列出祕密 Web Apps 函式。|
|/sites/functions/read|取得 Web Apps 函式。|
|/sites/functions/write|更新 Web Apps 函式。|
|/sites/deployments/delete|刪除 Web Apps 部署。|
|/sites/deployments/read|取得 Web Apps 部署。|
|/sites/deployments/write|更新 Web Apps 部署。|
|/sites/deployments/log/read|取得 Web Apps 部署記錄。|
|/sites/diagnostics/read|取得 Web Apps 診斷。|
|/sites/diagnostics/workerprocessrecycle/read|取得 Web Apps 診斷的背景工作處理序回收。|
|/sites/diagnostics/workeravailability/read|取得 Web Apps 診斷的 Workeravailability。|
|/sites/diagnostics/runtimeavailability/read|取得 Web Apps 診斷的執行階段可用性。|
|/sites/diagnostics/cpuanalysis/read|取得 Web Apps 診斷 Cpuanalysis。|
|/sites/diagnostics/servicehealth/read|取得 Web Apps 診斷的服務健康狀態。|
|/sites/diagnostics/frebanalysis/read|取得 Web Apps 診斷的 FREB 分析。|
|/availablestacks/read|取得可用堆疊。|
|/isusernameavailable/read|檢查使用者名稱是否可供使用。|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Read|取得 API 清單。|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Write|新增 API 或更新現有 API。|
|/Microsoft.Web/apiManagementAccounts/<br>apis/Delete|刪除現有 API。|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Read|取得連線清單。|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Write|儲存新的連線，或更新現有連線。|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/Delete|刪除現有連線。|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Read|讀取 ConnectionAcls|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Write|新增或更新 ConnectionAcl|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connections/connectionAcls/Delete|刪除 ConnectionAcl|
|/Microsoft.Web/apiManagementAccounts/<br>apis/connectionAcls/Read|讀取 API 的 ConnectionAcls|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Read|讀取 ConnectionAcls|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Write|建立或更新 API ACL|
|/Microsoft.Web/apiManagementAccounts/<br>apis/apiAcls/Delete|刪除 API ACL|
|/serverfarms/Read|取得 App Service 方案的屬性|
|/serverfarms/Write|建立新的 App Service 方案，或更新現有的 App Service 方案|
|/serverfarms/Delete|刪除現有的 App Service 方案|
|/serverfarms/restartSites/Action|重新啟動 App Service 方案中的所有 Web Apps|
|/serverfarms/operationresults/read|取得 App Service 方案的作業結果。|
|/serverfarms/capabilities/read|取得 App Service 方案的容量。|
|/serverfarms/metricdefinitions/read|取得 App Service 方案的計量定義。|
|/serverfarms/metrics/read|取得 App Service 方案的計量。|
|/serverfarms/hybridconnectionplanlimits/read|取得 App Service 方案的混合式連線方案限制。|
|/serverfarms/virtualnetworkconnections/read|取得 App Service 方案的虛擬網路連線。|
|/serverfarms/virtualnetworkconnections/routes/delete|刪除 App Service 方案的虛擬網路連線路由。|
|/serverfarms/virtualnetworkconnections/routes/read|取得 App Service 方案的虛擬網路連線路由。|
|/serverfarms/virtualnetworkconnections/routes/write|更新 App Service 方案的虛擬網路連線路由。|
|/serverfarms/virtualnetworkconnections/gateways/write|更新 App Service 方案的虛擬網路連線閘道器。|
|/serverfarms/firstpartyapps/settings/delete|刪除 App Service 方案的第一方應用程式設定。|
|/serverfarms/firstpartyapps/settings/read|取得 App Service 方案的第一方應用程式設定。|
|/serverfarms/firstpartyapps/settings/write|更新 App Service 方案的第一方應用程式設定。|
|/serverfarms/sites/read|取得 App Service 方案的 Web Apps。|
|/serverfarms/workers/reboot/action|重新啟動 App Service 方案的背景工作角色。|
|/serverfarms/hybridconnectionrelays/read|取得 App Service 方案的混合式連線轉送。|
|/serverfarms/skus/read|取得 App Service 方案 SKU。|
|/serverfarms/usages/read|取得 App Service 方案的使用量。|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|取得 App Service 方案的混合式連線命名空間轉送 Web Apps。|
|/ishostnameavailable/read|檢查主機名稱是否可供使用。|
|/connectionGateways/Read|取得連線閘道器的清單。|
|/connectionGateways/Write|建立或更新連線閘道器。|
|/connectionGateways/Delete|刪除連接閘道器。|
|/connectionGateways/Join/Action|加入連線閘道器。|
|/classicmobileservices/read|取得傳統的行動服務。|
|/skus/read|取得 SKU。|
|/certificates/Read|取得憑證清單。|
|/certificates/Write|新增憑證或更新現有憑證。|
|/certificates/Delete|刪除現有憑證。|
|/operations/read|取得作業。|
|/recommendations/Read|取得訂用帳戶的建議清單。|
|/ishostingenvironmentnameavailable/read|取得裝載環境名稱是否可供使用。|
|/apiManagementAccounts/Read|取得 ApiManagementAccounts 清單。|
|/apiManagementAccounts/Write|新增 ApiManagementAccount 或更新現有 ApiManagementAccount|
|/apiManagementAccounts/Delete|刪除現有的 ApiManagementAccount|
|/apiManagementAccounts/connectionAcls/Read|取得連線 ACL 的清單。|
|/apiManagementAccounts/apiAcls/Read|讀取 ConnectionAcls|
|/connections/Read|取得連線清單。|
|/connections/Write|建立或更新連線。|
|/connections/Delete|刪除連線。|
|/connections/Join/Action|加入連線。|
|/connections/confirmconsentcode/action|確認連線同意程式碼。|
|/connections/listconsentlinks/action|列出連線的同意連結。|
|/deploymentlocations/read|取得部署位置。|
|/sourcecontrols/read|取得原始檔控制。|
|/sourcecontrols/write|更新原始檔控制。|
|/managedhostingenvironments/read|取得受管理的裝載環境。|
|/managedhostingenvironments/sites/read|取得受管理裝載環境的 Web Apps。|
|/managedhostingenvironments/serverfarms/read|取得受管理裝載環境的 App Service 方案。|
|/locations/managedapis/read|取得位置管理的 API。|
|/locations/apioperations/read|取得位置 API 作業。|
|/locations/connectiongatewayinstallations/read|取得位置連線閘道器安裝。|
|/listSitesAssignedToHostName/Read|取得指派給主機名稱之網站的名稱。|

## <a name="next-steps"></a>後續步驟

- 了解如何[建立自訂角色](role-based-access-control-custom-roles.md)。

- 檢閱[內建的 RBAC 角色](role-based-access-built-in-roles.md)。

- 了解如何[依使用者](role-based-access-control-manage-assignments.md)或[依資源](role-based-access-control-configure.md)來管理存取權指派 
