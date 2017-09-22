---
title: "受管理的服務識別 (MSI) 已知問題 (Azure Active Directory)"
description: "受管理的服務識別已知問題 (Azure Active Directory)"
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 09/14/2017
ms.author: skwan
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 6fb8317f33ec8c36af8553466665fb2088c49527
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---

# <a name="known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>受管理的服務識別 (MSI) 已知問題 (Azure Active Directory)

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

## <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Azure 入口網站中沒出現組態刀鋒視窗

如果 VM 組態刀鋒視窗沒有出現在您的 VM 上，則表示 MSI 尚未在您區域的入口網站中啟用。  請稍後再試。  您也可以使用 [PowerShell](msi-qs-configure-powershell-windows-vm.md) 或 [Azure CLI](msi-qs-configure-cli-windows-vm.md)來啟用 VM 的 MSI。

## <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>在 [存取控制 (IAM)] 刀鋒視窗中無法將存取權指派給虛擬機器

在 Azure 入口網站中，如果**虛擬機器**沒有在 [存取控制 (IAM)]  >  [新增權限] 中顯示為**指派存取權的對象**，則表示受管理的服務識別尚未在您區域的入口網站中啟用。 請稍後再試。  您仍然可藉由搜尋受管理的服務身分識別服務主體，選取 MSI 來指派角色。  在 [選取] 欄位中輸入 VM 名稱，服務主體就會出現在搜尋結果中。

## <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>VM 從資源群組或訂用帳戶移走後會無法啟動

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

## <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>MSI 可搭配 Directory Authentication Library (ADAL) 或 Microsoft Authentication Library (MSAL) 使用嗎？

不行，MSI 未尚未與 ADAL 或 MSAL 整合。

