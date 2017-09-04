---
title: "針對常見的 Azure 部署錯誤進行疑難排解 | Microsoft Docs"
description: "說明如何解決使用 Azure Resource Manager 將資源部署至 Azure 時的常見錯誤。"
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "部署錯誤, azure 部署, 部署至 azure"
ms.assetid: c002a9be-4de5-4963-bd14-b54aa3d8fa59
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 30adc10d01290f14a3e116813b19916fa36ab0bc
ms.contentlocale: zh-tw
ms.lasthandoff: 08/19/2017

---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解
本主題說明如何解決可能會遇到的一些常見 Azure 部署錯誤。

本主題會說明下列錯誤碼︰

* [AccountNameInvalid](#accountnameinvalid)
* [授權失敗](#authorization-failed)
* [BadRequest](#badrequest)
* [DeploymentFailed](#deploymentfailed)
* [DisallowedOperation](#disallowedoperation)
* [InvalidContentLink](#invalidcontentlink)
* [InvalidTemplate](#invalidtemplate)
* [MissingSubscriptionRegistration](#noregisteredproviderfound)
* [NotFound](#notfound)
* [NoRegisteredProviderFound](#noregisteredproviderfound)
* [OperationNotAllowed](#quotaexceeded)
* [ParentResourceNotFound](#parentresourcenotfound)
* [QuotaExceeded](#quotaexceeded)
* [RequestDisallowedByPolicy](#requestdisallowedbypolicy)
* [ResourceNotFound](#notfound)
* [SkuNotAvailable](#skunotavailable)
* [StorageAccountAlreadyExists](#storagenamenotunique)
* [StorageAccountAlreadyTaken](#storagenamenotunique)

## <a name="deploymentfailed"></a>DeploymentFailed

這個錯誤碼會指出一般部署錯誤，但不是您要開始進行疑難排解的錯誤碼。 實際上協助您解決問題的錯誤碼通常位於此錯誤的下一層。 例如，下圖顯示在部署錯誤底下的 **RequestDisallowedByPolicy** 錯誤碼。

![顯示錯誤碼](./media/resource-manager-common-deployment-errors/error-code.png)

## <a name="skunotavailable"></a>SkuNotAvailable

在部署資源 (通常是虛擬機器) 時，您可能會收到下列錯誤碼和錯誤訊息︰

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

當您選取的資源 SKU (例如 VM 大小) 不適用於您選取的位置時，您會收到這個錯誤。 若要解決此問題，您需要判斷區域中有哪些 SKU。 您可以使用 PowerShell、入口網站或 REST 作業來尋找可用的 SKU。

- 若是 PowerShell，請使用 [Get-AzureRmComputeResourceSku](/powershell/module/azurerm.compute/get-azurermcomputeresourcesku) 並依位置篩選。 您必須擁有最新版 PowerShell 才能執行此命令。

  ```powershell
  Get-AzureRmComputeResourceSku | where {$_.Locations.Contains("southcentralus")}
  ```

  結果包括該位置的 SKU 清單，以及該 SKU 的任何限制。

  ```powershell
  ResourceType                Name      Locations Restriction                      Capability Value
  ------------                ----      --------- -----------                      ---------- -----
  availabilitySets         Classic southcentralus             MaximumPlatformFaultDomainCount     3
  availabilitySets         Aligned southcentralus             MaximumPlatformFaultDomainCount     3
  virtualMachines      Standard_A0 southcentralus
  virtualMachines      Standard_A1 southcentralus
  virtualMachines      Standard_A2 southcentralus
  ```

- 若要使用[入口網站](https://portal.azure.com)，請登入入口網站並透過介面加入資源。 當您設定值時，您會看到該資源可用的 SKU。 您不需要完成部署。

    ![可用的 SKU](./media/resource-manager-common-deployment-errors/view-sku.png)

- 若要為虛擬機器使用 REST API，請傳送下列要求︰

  ```HTTP 
  GET
  https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/skus?api-version=2016-03-30
  ```

  它會以下列格式傳回可用的 SKU 和區域︰

  ```json
  {
    "value": [
      {
        "resourceType": "virtualMachines",
        "name": "Standard_A0",
        "tier": "Standard",
        "size": "A0",
        "locations": [
          "eastus"
        ],
        "restrictions": []
      },
      {
        "resourceType": "virtualMachines",
        "name": "Standard_A1",
        "tier": "Standard",
        "size": "A1",
        "locations": [
          "eastus"
        ],
        "restrictions": []
      },
      ...
    ]
  }    
  ```

如果您在該區域或符合您業務需求的替代區域中找不到適當的 SKU，請向 Azure 支援服務提交 [SKU 要求](https://aka.ms/skurestriction)。

## <a name="disallowedoperation"></a>DisallowedOperation

```
Code: DisallowedOperation
Message: The current subscription type is not permitted to perform operations on any provider 
namespace. Please use a different subscription.
```

如果您收到此錯誤，表示您使用的訂用帳戶不被允許存取 Azure Active Directory 以外的任何 Azure 服務。 當您需要存取傳統入口網站，但不被允許部署資源時，就表示您擁有的訂用帳戶可能是這種類型。 若要解決此問題，您必須使用有權部署資源的訂用帳戶。  

若要使用 PowerShell 檢視您可用的訂用帳戶，請使用︰

```powershell
Get-AzureRmSubscription
```

若要設定目前的訂用帳戶，請使用︰

```powershell
Set-AzureRmContext -SubscriptionName {subscription-name}
```

若要使用 Azure CLI 2.0 檢視您可用的訂用帳戶，請使用︰

```azurecli
az account list
```

若要設定目前的訂用帳戶，請使用︰

```azurecli
az account set --subscription {subscription-name}
```

## <a name="invalidtemplate"></a>InvalidTemplate
此錯誤可能起因於數個不同類型的錯誤。

- 語法錯誤

   如果您收到錯誤訊息指出無法驗證範本，則可能是範本中發生語法問題。

  ```
  Code=InvalidTemplate
  Message=Deployment template validation failed
  ```

   此錯誤很容易發生，因為範本運算式可能很複雜。 例如，儲存體帳戶的下列名稱指派包含一組方括號、三個函式、三組圓括號、一組單引號和一個屬性︰

  ```json
  "name": "[concat('storage', uniqueString(resourceGroup().id))]",
  ```

   如果您未提供相符的語法，範本會產生與您所要的值截然不同的值。

   當您收到此類錯誤時，請仔細檢閱運算式語法。 請考慮使用 [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) 或 [Visual Studio Code](resource-manager-vs-code.md) 等 JSON 編輯器，它們可以警告您有語法錯誤。

- 不正確的區段長度

   資源名稱的格式不正確時，就會發生另一種無效範本錯誤。

  ```
  Code=InvalidTemplate
  Message=Deployment template validation failed: 'The template resource {resource-name}'
  for type {resource-type} has incorrect segment lengths.
  ```

   根層級資源的名稱必須比資源類型少一個區段。 每個區段是以斜線區分。 在下列範例中，類型具有 2 個區段，而名稱則有 1 個區段，因此是**有效名稱**。

  ```json
  {
    "type": "Microsoft.Web/serverfarms",
    "name": "myHostingPlanName",
    ...
  }
  ```

   但下一個範例則 **不是有效名稱** ，因為它的區段數目和類型相同。

  ```json
  {
    "type": "Microsoft.Web/serverfarms",
    "name": "appPlan/myHostingPlanName",
    ...
  }
  ```

   對於子資源來說，類型和名稱具有相同的區段數目。 此區段數目很合理，因為子資源的完整名稱和類型包含父資源的名稱和類型。 因此，完整名稱較完整類型仍少一個區段。

  ```json
  "resources": [
      {
          "type": "Microsoft.KeyVault/vaults",
          "name": "contosokeyvault",
          ...
          "resources": [
              {
                  "type": "secrets",
                  "name": "appPassword",
                  ...
              }
          ]
      }
  ]
  ```

   對於跨資源提供者套用的 Resource Manager 類型而言，要讓區段保持正確可能很棘手。 例如，將資源鎖定套用至網站需要具有四個區段的類型。 因此，名稱會是三個區段：

  ```json
  {
      "type": "Microsoft.Web/sites/providers/locks",
      "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
      ...
  }
  ```

- 不應該複製索引

   當您將 **copy** 元素套用到不支援此元素的範本時，您會遇到此 **InvalidTemplate** 錯誤。 您只可以將複製元素套用到資源類型。 您不能將複製套用到資源類型內的屬性。 例如，您將複製套用到虛擬機器，但無法將它套用到虛擬機器的作業系統磁碟。 在某些情況下，您可以將子資源轉換成父資源，以建立複製迴圈。 如需有關使用複製的詳細資訊，請參閱 [在 Azure Resource Manager 中建立資源的多個執行個體](resource-group-create-multiple.md)。

- 參數無效

   如果範本會指定參數允許的值，而您提供的值不是其中一個值，則會收到類似下列錯誤的訊息：

  ```
  Code=InvalidTemplate;
  Message=Deployment template validation failed: 'The provided value {parameter value}
  for the template parameter {parameter name} is not valid. The parameter value is not
  part of the allowed values
  ``` 

   在範本中重複檢查允許的值，並在部署期間提供一個值。

- 偵測到循環相依性

   當資源以防止部署啟動的方式互相相依，您會收到這個錯誤。 只要有相互相依性的組合，就會讓兩個或多個資源等候其他也正在等候的資源。 例如，resource1 相依於 resource3、resource2 相依於 resource1，而 resource3 相依於 resource2。 您通常可以移除不必要的相依性來解決此問題。 

<a id="notfound" />
### <a name="notfound-and-resourcenotfound"></a>NotFound 和 ResourceNotFound
當您的範本包含的資源名稱無法解析時，您會收到類似以下的錯誤：

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

如果您嘗試在範本中部署遺漏的資源，請檢查是否需要新增相依性。 如果可能，Resource Manager 會以平行方式建立資源，將部署最佳化。 如果一個資源必須在另一個資源之後部署，您就必須在範本中使用 **dependsOn** 元素來建立與該另一個資源的相依性。 例如，在部署 Web 應用程式時，App Service 方案必須存在。 如果您沒有指定該 Web 應用程式相依於 App Service 方案，Resource Manager 會同時建立這兩個資源。 您收到錯誤訊息，指出找不到 App Service 方案的資源，因為嘗試在 Web 應用程式上設定屬性時它尚未存在。 您會設定 Web 應用程式的相依性，以避免此錯誤。

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

如需針對相依性錯誤進行疑難排解的建議，請參閱[檢查部署順序](#check-deployment-sequence)。

當資源存在於所要部署不同的資源群組中，則也會看到此錯誤。 在該情況下，請使用 [resourceId 函式](resource-group-template-functions-resource.md#resourceid)來取得資源的完整名稱。

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

如果您嘗試對無法解析的資源使用 [reference](resource-group-template-functions-resource.md#reference) 或 [listKeys](resource-group-template-functions-resource.md#listkeys) 函式，您會收到下列錯誤：

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

尋找包含 **reference** 函式的運算式。 重複檢查確定參數值正確。

## <a name="parentresourcenotfound"></a>ParentResourceNotFound

當某資源為另一個資源的父資源時，父資源必須在建立子資源之前就存在。 如果尚未存在，您會收到下列錯誤︰

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

子資源的名稱包含父系名稱。 例如，SQL Database 可能定義如下︰

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

但是，如果您尚未於父資源上指定相依性，子資源可能會在父資源之前進行部署。 若要解決這個錯誤，請包含相依性。

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

<a id="storagenamenotunique" />

## <a name="storageaccountalreadyexists-and-storageaccountalreadytaken"></a>StorageAccountAlreadyExists 和 StorageAccountAlreadyTaken
對於儲存體帳戶，您必須提供在 Azure 中是唯一的資源名稱。 如果您未提供唯一的名稱，您會收到類似下列的錯誤：

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

您可以將您的命名慣例與 [uniqueString](resource-group-template-functions-string.md#uniquestring) 函式的結果串連，以建立一個唯一名稱。

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

如果您以與您的訂用帳戶中現有的儲存體帳戶相同的名稱部署儲存體帳戶，但提供不同的位置，您會遇到錯誤，指出儲存體帳戶已存在於不同的位置。 刪除現有的儲存體帳戶，或提供與現有儲存體帳戶相同的位置。

## <a name="accountnameinvalid"></a>AccountNameInvalid
嘗試提供的儲存體帳戶名稱中包含禁止的字元時，您會看到 **AccountNameInvalid** 錯誤。 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能使用數字和小寫字母。 [uniqueString](resource-group-template-functions-string.md#uniquestring) 函式會傳回 13 個字元。 如果您要對 **uniqueString** 結果串連前置詞，請提供 11 個字元以下的前置詞。

## <a name="badrequest"></a>BadRequest

提供的屬性值無效時，您可能會碰到 BadRequest 狀態。 例如，如果您針對儲存體帳戶提供不正確的 SKU 值，則部署會失敗。 若要判斷屬性的有效值，請查看 [REST API](/rest/api) 了解您要部署的資源類型。

<a id="noregisteredproviderfound" />

## <a name="noregisteredproviderfound-and-missingsubscriptionregistration"></a>NoRegisteredProviderFound 和 MissingSubscriptionRegistration
在部署資源時，您可能會收到下列錯誤代碼和訊息︰

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

或者，您可能會收到類似訊息，指出︰

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

您會因為下列三個原因其中一個而收到此錯誤︰

1. 尚未向您的訂用帳戶註冊此資源提供者
2. 此資源類型不支援 API 版本
3. 此資源類型不支援位置

錯誤訊息應可提供給您支援的位置和 API 版本建議。 您可以將您的範本變更為其中一個建議的值。 Azure 入口網站或正在使用的命令列介面會自動註冊大部分的提供者；但並非全部。 如果您未曾使用特定的資源提供者，您可能需要註冊該提供者。 您可以透過 PowerShell 或 Azure CLI，深入探索資源提供者。

**入口網站**

您可以看到註冊狀態，並透過入口網站註冊資源提供者命名空間。

1. 對於您的訂用帳戶，選取**資源提供者**。

   ![選取資源提供者](./media/resource-manager-common-deployment-errors/select-resource-provider.png)

2. 查看資源提供者的清單，並視需要選取 [註冊] 連結以註冊您嘗試部署的資源提供者類型。

   ![列出資源提供者](./media/resource-manager-common-deployment-errors/list-resource-providers.png)

**PowerShell**

若要查看您的註冊狀態，請使用 **Get-AzureRmResourceProvider**。

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

若要註冊提供者，請使用 **Register-AzureRmResourceProvider** ，並提供您想要註冊的資源提供者名稱。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn
```

若要取得支援特定資源類型的位置，請使用︰

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

若要取得支援特定資源類型的 API 版本，請使用︰

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

**Azure CLI**

若要查看是否已註冊該提供者，請使用 `azure provider list` 命令。

```azurecli
az provider list
```

若要註冊資源提供者，請使用 `azure provider register` 命令，然後指定要註冊的*命名空間*。

```azurecli
az provider register --namespace Microsoft.Cdn
```

若要查看資源類型所支援的位置和 API 版本，請使用︰

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

<a id="quotaexceeded" />

## <a name="quotaexceeded-and-operationnotallowed"></a>QuotaExceeded 和 OperationNotAllowed
當部署超過配額時 (也許是每個資源群組、訂用帳戶、帳戶及其他範圍的配額)，可能會發生問題。 例如，您的訂用帳戶可能設定為要限制區域的核心數目。 如果您嘗試部署超過允許核心數目的虛擬機器，您會收到錯誤訊息指出已超過配額。
如需完整的配額資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](../azure-subscription-service-limits.md)。

若要檢查您訂用帳戶的核心配額，可以在 Azure CLI 中使用 `azure vm list-usage` 命令。 以下範例示範核心配額為 4 的免費試用帳戶：

```azurecli
az vm list-usage --location "South Central US"
```

它會傳回：

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

如果您部署的範本會在美國西部區域中建立四個以上的核心，您會看到類似以下的部署錯誤：

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

或者，您可以在 PowerShell 中使用 **Get-AzureRmVMUsage** Cmdlet。

```powershell
Get-AzureRmVMUsage
```

它會傳回：

```powershell
...
CurrentValue : 0
Limit        : 4
Name         : {
                 "value": "cores",
                 "localizedValue": "Total Regional Cores"
               }
Unit         : null
...
```

在這些情況下，您應該移至入口網站，並提出支援問題，以針對您想要部署的區域提高配額。

> [!NOTE]
> 請記住，對於資源群組，配額適用於每個個別區域，而不是整個訂用帳戶。 如果您需要在美國西部部署 30 個核心，就必須要求在美國西部擁有 30 個資源管理員核心。 如果您需要在任何具有存取權限的區域中部署 30 個核心，就應該要求在所有區域中擁有 30 個 Resource Manager 核心。
>
>

## <a name="invalidcontentlink"></a>InvalidContentLink
當您收到錯誤訊息：

```
Code=InvalidContentLink
Message=Unable to download deployment content from ...
```

您最有可能嘗試連結至無法使用的巢狀範本。 再次確認您為巢狀範本提供的 URI。 如果儲存體帳戶中已有範本，請確定 URI 可存取。 您可能需要傳送 SAS 權杖。 如需詳細資訊，請參閱 [透過 Azure 資源管理員使用連結的範本](resource-group-linked-templates.md)。

## <a name="requestdisallowedbypolicy"></a>RequestDisallowedByPolicy
當訂用帳戶包含會讓您無法在部署期間嘗試執行某個動作的資源原則時，您就會收到這個錯誤。 請在錯誤訊息中尋找原則識別碼。

```
Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'
```

在 **PowerShell** 中，提供該原則識別碼做為 **Id** 參數，以擷取有關封鎖了部署之原則的詳細資料。

```powershell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

在 **Azure CLI** 中，提供原則定義的名稱︰

```azurecli
az policy definition show --name regionPolicyAssignment
```

如需詳細資訊，請參閱下列文章。

- [RequestDisallowedByPolicy 錯誤](resource-manager-policy-requestdisallowedbypolicy-error.md)
- [使用原則來管理資源和控制存取](resource-manager-policy.md)。

## <a name="authorization-failed"></a>授權失敗
您可能會在部署期間收到錯誤，因為嘗試部署資源的帳戶或服務主體並有執行這些動作的存取權。 Azure Active Directory 可讓您或您的系統管理員最精確地控制哪些身分識別可以存取哪些資源。 例如，如果您的帳戶被指派「讀取者」角色，您將無法建立資源。 在此情況下，您會看到錯誤訊息，指出授權失敗。

如需角色型存取控制的詳細資訊，請參閱 [Azure 角色型存取控制](../active-directory/role-based-access-control-configure.md)。


## <a name="next-steps"></a>後續步驟
* 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](resource-group-audit.md)。
* 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](resource-manager-deployment-operations.md)。

