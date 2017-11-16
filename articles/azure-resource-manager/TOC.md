# 概觀
## [什麼是 Resource Manager？](resource-group-overview.md)
## [資源提供者和類型](resource-manager-supported-services.md)
## [Resource Manager 與傳統部署](resource-manager-deployment-model.md)
## [訂用帳戶治理](resource-manager-subscription-governance.md)

# 開始使用
## [建立和部署範本](resource-manager-create-first-template.md)
## [適用於範本的 VS Code 延伸模組](resource-manager-vscode-extension.md)
## [搭配 Resource Manager 使用 Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# 範例
## [程式碼範例](https://azure.microsoft.com/en-us/resources/samples/?service=azure-resource-manager)
## Azure PowerShell
### [部署範本](resource-manager-samples-powershell-deploy.md)

## Azure CLI
### [部署範本](resource-manager-samples-cli-deploy.md)

# 作法
## 建立範本
### [範本區段](resource-group-authoring-templates.md)
### [範本的最佳做法](resource-manager-template-best-practices.md)
### [連結至其他範本](resource-group-linked-templates.md)
### [定義資源間的相依性](resource-group-define-dependencies.md)
### [建立多個執行個體](resource-group-create-multiple.md)
### [設定位置](resource-manager-template-location.md)
### [指派標記](resource-manager-template-tags.md)
### [設定子資源名稱與類型](resource-manager-template-child-resource.md)
### [更新資源](resource-manager-update.md)
### [使用參數的物件](resource-manager-objects-as-parameters.md)
### [在連結的範本間共用狀態](best-practices-resource-manager-state.md)
### [設計範本的模式](best-practices-resource-manager-design-templates.md)


## 部署
### Azure PowerShell
#### [部署範本](resource-group-template-deploy.md)
#### [使用 SAS 權杖部署私人範本](resource-manager-powershell-sas-token.md)
#### [匯出範本並重新部署](resource-manager-export-template-powershell.md)
### Azure CLI
#### [部署範本](resource-group-template-deploy-cli.md)
#### [使用 SAS 權杖部署私人範本](resource-manager-cli-sas-token.md)
#### [匯出範本並重新部署](resource-manager-export-template-cli.md)
### Azure 入口網站
#### [部署資源](resource-group-template-deploy-portal.md)
#### [匯出範本](resource-manager-export-template.md)
### [REST API](resource-group-template-deploy-rest.md)
### [跨資源群組部署](resource-manager-cross-resource-group-deployment.md)
### [持續與 Visual Studio Team Services 整合](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [在部署期間傳遞安全值](resource-manager-keyvault-parameter.md)

## 管理
### [Azure PowerShell](powershell-azure-resource-manager.md)
### [Azure CLI](xplat-cli-azure-resource-manager.md)
### [Azure 入口網站](resource-group-portal.md)
### [REST API](resource-manager-rest-api.md)
### [使用標籤來整理資源](resource-group-using-tags.md)
### [將資源移至新群組或訂用帳戶](resource-group-move-resources.md)
### [利用管理群組組織訂用帳戶](../billing/billing-enterprise-mgmt-group-overview.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [治理範例](resource-manager-subscription-examples.md)
### [受管理的應用程式](../managed-applications/overview.md)

## 控制存取權
### 建立服務主體
#### [Azure PowerShell](resource-group-authenticate-service-principal.md)
#### [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Azure 入口網站](resource-group-create-service-principal-portal.md)
### [驗證 API 以存取訂閱帳戶](resource-manager-api-authentication.md)
### [鎖定資源](resource-group-lock-resources.md)

## 稽核
### [檢視活動記錄](resource-group-audit.md)
### [檢視部署作業](resource-manager-deployment-operations.md)

## 疑難排解
### [一般部署錯誤](resource-manager-common-deployment-errors.md)
### [了解部署錯誤](resource-manager-troubleshoot-tips.md)
### 解決錯誤
#### [AccountNameInvalid](resource-manager-storage-account-name-errors.md)
#### [InvalidTemplate](resource-manager-invalid-template-errors.md)
#### [NoRegisteredProviderFound](resource-manager-register-provider-errors.md)
#### [NotFound](resource-manager-not-found-errors.md)
#### [ParentResourceNotFound](resource-manager-parent-resource-errors.md)
#### [RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md)
#### [ReservedResourceName](resource-manager-reserved-resource-name.md)
#### [ResourceQuotaExceeded](resource-manager-quota-errors.md)
#### [SkuNotAvailable](resource-manager-sku-not-available-errors.md)
### 虛擬機器部署錯誤
#### Linux
##### [部署問題](../virtual-machines/linux/troubleshoot-deploy-vm.md)
##### [佈建和配置問題](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)
##### [常見的錯誤訊息](../virtual-machines/linux/error-messages.md)
#### Windows
##### [部署問題](../virtual-machines/windows/troubleshoot-deploy-vm.md)
##### [佈建和配置問題](../virtual-machines/windows/troubleshoot-deployment-new-vm.md)
##### [常見的錯誤訊息](../virtual-machines/windows/error-messages.md)

# 參考
## [範本格式](/azure/templates/)
## [範本函式](resource-group-template-functions.md)
### [陣列和物件函式](resource-group-template-functions-array.md)
### [比較函式](resource-group-template-functions-comparison.md)
### [部署函式](resource-group-template-functions-deployment.md)
### [邏輯函式](resource-group-template-functions-logical.md)
### [數值函式](resource-group-template-functions-numeric.md)
### [資源函式](resource-group-template-functions-resource.md)
### [字串函式](resource-group-template-functions-string.md)
## [PowerShell](/powershell/module/azurerm.resources)
## [Azure CLI](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST](/rest/api/resources/)

# 資源
## [Azure 藍圖](https://azure.microsoft.com/roadmap/?category=monitoring-management)
## [定價計算機](https://azure.microsoft.com/pricing/calculator/)
## [服務更新](https://azure.microsoft.com/updates/?product=azure-resource-manager)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [節流要求](resource-manager-request-limits.md)
## [追蹤非同步作業](resource-manager-async-operations.md)
## [影片](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
