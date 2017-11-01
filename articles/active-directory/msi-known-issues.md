---
title: "受管理的服務識別 (MSI) 常見問題和已知問題 (Azure Active Directory)"
description: "受管理的服務識別已知問題 (Azure Active Directory)"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 10/20/2017
ms.author: bryanla
ms.openlocfilehash: 859cfbeae6701336699b4f3f7a96d6b08c599340
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2017
---
# <a name="faqs-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>受管理的服務識別 (MSI) 常見問題和已知問題 (Azure Active Directory)

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>常見問題集 (FAQ)

### <a name="does-msi-work-with-azure-cloud-services"></a>MSI 是否使用 Azure 雲端服務？

否，沒有在 Azure 雲端服務中支援 MSI 的計劃。

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>MSI 可搭配 Directory Authentication Library (ADAL) 或 Microsoft Authentication Library (MSAL) 使用嗎？

不行，MSI 未尚未與 ADAL 或 MSAL 整合。

### <a name="what-are-the-supported-linux-distributions"></a>支援的 Linux 散發套件有哪些？

下列 Linux 散發套件支援 MSI： 

- CoreOS Stable
- CentOS 7.1
- RedHat 7.2
- Ubuntu 15.04

其他 Linux 散發套件目前不受支援，且在不支援的散發套件上擴充功能可能會失敗。

擴充功能在 CentOS 6.9 上可以運作。 不過，由於在 6.9 中缺乏系統支援，因此若當機或停止，擴充功能將不會自動重新啟動。 在虛擬機器重新啟動時，它才會跟著重新啟動。 若要手動重新啟動擴充功能，請參閱[如何重新啟動 MSI 擴充功能？](#how-do-you-restart-the-msi-extension)

### <a name="how-do-you-restart-the-msi-extension"></a>如何重新啟動 MSI 擴充功能？
在特定版本的 Windows 和 Linux 上，擴充功能停止時，可手動用下列 Cmdlet 來重新啟動：

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

其中： 
- 適用於 Windows 的擴充功能名稱和類型是：ManagedIdentityExtensionForWindows
- 適用於 Linux 的擴充功能名稱和類型是：ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>已知問題

### <a name="automation-script-fails-when-attempting-schema-export-for-msi-extension"></a>嘗試匯出 MSI 擴充的結構描述時，「自動化指令碼」失敗

在虛擬機器上啟用受管理服務識別後，嘗試對虛擬機器或其資源群組使用「自動化指令碼」功能時，出現下列錯誤：

![MSI 自動化指令碼匯出錯誤](media/msi-known-issues/automation-script-export-error.png)

「受管理服務識別」虛擬機器擴充目前不支援將其結構描述匯出至資源群組範本。 因此，產生的範本不會顯示可在資源上啟用受管理服務識別的設定參數。 您可以依照[使用範本來設定虛擬機器受管理服務識別](msi-qs-configure-template-windows-vm.md)中的範例，手動新增這些區段。

當結構描述匯出功能變成可用於 MSI 虛擬機器擴充時，就會列在[匯出包含虛擬機器擴充的資源群組](../virtual-machines/windows/extensions-export-templates.md#supported-virtual-machine-extensions)中。

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Azure 入口網站中沒出現組態刀鋒視窗

如果 VM 組態刀鋒視窗沒有出現在您的 VM 上，則表示 MSI 尚未在您區域的入口網站中啟用。  請稍後再試。  您也可以使用 [PowerShell](msi-qs-configure-powershell-windows-vm.md) 或 [Azure CLI](msi-qs-configure-cli-windows-vm.md)來啟用 VM 的 MSI。

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>在 [存取控制 (IAM)] 刀鋒視窗中無法將存取權指派給虛擬機器

在 Azure 入口網站中，如果**虛擬機器**沒有在 [存取控制 (IAM)]  >  [新增權限] 中顯示為**指派存取權的對象**，則表示受管理的服務識別尚未在您區域的入口網站中啟用。 請稍後再試。  您仍然可藉由搜尋受管理的服務身分識別服務主體，選取 MSI 來指派角色。  在 [選取] 欄位中輸入 VM 名稱，服務主體就會出現在搜尋結果中。

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>VM 從資源群組或訂用帳戶移走後會無法啟動

如果 VM 在執行期間遭到移動，VM 會在移動時繼續執行。 不過，移動之後，如果 VM 停止並重新啟動，則會無法啟動。 此問題是因為 VM 不會更新 MSI 身分識別的參考值，而持續指向舊資源群組中的參考值所致。

**因應措施** 
 
觸發 VM 上的更新，如此一來 VM 就可以為 MSI 取得正確的值。 您可以執行 VM 屬性變更，更新 MSI 身分識別的參考值。 例如，您可以使用下列命令在 VM 上設定新的標記值：

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
此命令會在 VM 上設定值為 1 的新標記 "fixVM"。 
 
設定此屬性後，VM 會使用正確的 MSI 資源 URI 進行更新，然後您應該就可以啟動 VM 了。 
 
一旦啟動 VM 後，您可以使用下列命令移除標記：

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```
