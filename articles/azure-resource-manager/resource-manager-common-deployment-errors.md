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
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 2ebb469289afc36b08c90ae9839f5bdba41cd90b
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2017
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解

本文說明可能會遇到的一些常見 Azure 部署錯誤，並且提供解決錯誤的資訊。 如果您找不到部署錯誤的錯誤碼，請參閱[尋找錯誤碼](#find-error-code)。

## <a name="error-codes"></a>錯誤碼

| 錯誤碼 | 緩和 | 詳細資訊 |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | 遵循儲存體帳戶的命名限制。 | [解析儲存體帳戶名稱](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | 檢查可用儲存體帳戶屬性。 | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AnotherOperationInProgress | 等候並行作業完成。 | |
| AuthorizationFailed | 您的帳戶或服務主體沒有完成部署的足夠存取權。 請檢查您的帳戶所屬的角色以及它針對部署範圍的存取權。 | [Azure 角色型存取控制](../active-directory/role-based-access-control-configure.md) |
| BadRequest | 您傳送的部署值不符合資源管理員的預期。 請查看內部狀態訊息，以取得疑難排解的說明。 | [範本參考](/azure/templates/)和[支援位置](resource-manager-template-location.md) |
| 衝突 | 您要求的作業在資源的目前狀態下不允許。 例如，只有在建立 VM 時或解除配置 VM 之後，才可調整磁碟大小。 | |
| DeploymentActive | 等候此資源群組的並行部署完成。 | |
| DnsRecordInUse | DNS 記錄名稱必須是唯一的。 請提供不同的名稱，或是修改現有的記錄。 | |
| ImageNotFound | 檢查 VM 映像設定。 | [針對 Linux 映像進行疑難排解](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)和[針對 Windows 映像進行疑難排解](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) |
| InUseSubnetCannotBeDeleted | 當您嘗試更新資源時，可能會遇到這個錯誤，但是藉由刪除和建立資源即可處理要求。 請務必指定所有不變的值。 | [更新資源](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | 取得適當租用戶的存取權杖。 您只能從您的帳戶所屬的租用戶取得權杖。 | |
| InvalidContentLink | 您最有可能嘗試連結至無法使用的巢狀範本。 再次確認您為巢狀範本提供的 URI。 如果儲存體帳戶中已有範本，請確定 URI 可存取。 您可能需要傳送 SAS 權杖。 | [連結的範本](resource-group-linked-templates.md) |
| InvalidParameter | 您為資源提供的其中一個值與預期值不相符。 此錯誤的原因可能是許多不同情況。 例如，密碼強度不足，或 blob 名稱不正確。 請檢查錯誤訊息，以判斷哪個值需要修正。 | |
| InvalidRequestContent | 您的部署值包含未預期的值，或遺漏必要值。 請確認您的資源類型值。 | [範本參考](/azure/templates/) |
| InvalidRequestFormat | 執行部署時啟用偵錯記錄，並且確認要求的內容。 | [偵錯記錄](resource-manager-troubleshoot-tips.md#enable-debug-logging) |
| InvalidResourceNamespace | 請檢查您在 **type** 屬性中指定的資源命名空間。 | [範本參考](/azure/templates/) |
| InvalidResourceReference | 資源不存在或未正確地參考。 檢查是否需要新增相依性。 確認您使用 **reference** 函式包括案例的必要參數。 | [解析相依性](resource-manager-not-found-errors.md) |
| InvalidResourceType | 請檢查您在 **type** 屬性中指定的資源類型。 | [範本參考](/azure/templates/) |
| InvalidTemplate | 請檢查錯誤的範本語法。 | [解析無效的範本](resource-manager-invalid-template-errors.md) |
| LinkedAuthorizationFailed | 檢查您的帳戶是否屬於與部署所在之資源群組相同的租用戶。 | |
| LinkedInvalidPropertyId | 資源的資源識別碼未正確地解析。 請檢查您為資源識別碼提供所有必要值，包含訂用帳戶識別碼、資源群組名稱、資源類型、父代資源名稱 (如有需要) 和資源名稱。 | |
| LocationRequired | 提供資源的位置。 | [設定位置](resource-manager-template-location.md) |
| MissingRegistrationForLocation | 檢查資源提供者註冊狀態和支援的位置。 | [解析註冊](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | 向資源提供者註冊訂用帳戶。 | [解析註冊](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | 檢查資源提供者註冊狀態。 | [解析註冊](resource-manager-register-provider-errors.md) |
| NotFound | 您可以嘗試與父代資源並行部署相依資源。 檢查是否需要新增相依性。 | [解析相依性](resource-manager-not-found-errors.md) |
| OperationNotAllowed | 部署嘗試進行超過訂用帳戶、資源群組或區域配額的作業。 可能的話，請修改您的部署，以維持在配額內。 否則，請考慮要求變更您的配額。 | [解析配額](resource-manager-quota-errors.md) |
| ParentResourceNotFound | 請確定父代資源在建立子系資源之前即已存在。 | [解析父代資源](resource-manager-parent-resource-errors.md) |
| PrivateIPAddressInReservedRange | 指定的 IP 位址包含 Azure 所需的位址範圍。 變更 IP 位址以避免保留的範圍。 | [IP 位址](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | 指定的 IP 位址在子網路範圍之外。 變更 IP 位址，使其落在子網路範圍內。 | [IP 位址](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | 有些屬性無法在已部署的資源上變更。 更新資源時，將您的變更限制為允許的屬性。 | [更新資源](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | 您的訂用帳戶包含會讓您無法在部署期間嘗試執行某個動作的資源原則。 尋找封鎖動作的原則。 可能的話，請修改您的部署以符合原則的限制。 | [解析原則](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | 提供不包含保留名稱的資源名稱。 | [唯一的資源名稱](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | 等候刪除完成。 | |
| ResourceGroupNotFound | 檢查部署的目標資源群組名稱。 它必須已經存在於您的訂用帳戶中。 檢查訂用帳戶內容。 | [Azure CLI](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/azurerm.profile/set-azurermcontext) |
| ResourceNotFound | 您的部署會參考無法解析的資源。 確認您使用 **reference** 函式包括案例的必要參數。 | [解析參考](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | 部署嘗試建立資源，這些資源超過訂用帳戶、資源群組或區域的配額。 可能的話，請修改您的基礎結構，以維持在配額內。 否則，請考慮要求變更您的配額。 | [解析配額](resource-manager-quota-errors.md) |
| SkuNotAvailable | 選取可供您選取之位置使用的 SKU (例如 VM 大小)。 | [解析 SKU](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | 提供儲存體帳戶的唯一名稱。 | [解析儲存體帳戶名稱](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | 提供儲存體帳戶的唯一名稱。 | [解析儲存體帳戶名稱](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | 檢查您嘗試使用的訂用帳戶、資源群組和儲存體帳戶名稱。 | |
| SubnetsNotInSameVnet | 虛擬機器只能有一個虛擬網路。 在部署多個 NIC 時，請確定它們屬於相同的虛擬網路。 | [多個 NIC](../virtual-machines/windows/multiple-nics.md) |

## <a name="find-error-code"></a>尋找錯誤碼

當您在部署期間遇到錯誤時，資源管理員會傳回錯誤碼。 您可以透過入口網站、PowerShell 或 Azure CLI 查看錯誤訊息。 外部錯誤訊息可能過於普遍而無法進行疑難排解。 尋找包含有關錯誤之詳細資訊的內部訊息。 如需詳細資訊，請參閱[判斷錯誤碼](resource-manager-troubleshoot-tips.md#determine-error-code)。

## <a name="next-steps"></a>後續步驟
* 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](resource-group-audit.md)。
* 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](resource-manager-deployment-operations.md)。
