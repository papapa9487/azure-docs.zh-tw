---
title: "如何使用 Azure 虛擬機器受控服務識別來登入"
description: "使用 Azure 虛擬機器 MSI 服務主體來進行指令碼用戶端登入和資源存取的逐步指示與範例。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: bryanla
ms.openlocfilehash: 5f0228dbe8df8cf0892569fd89162dfaf39cc8cb
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2017
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in"></a>如何使用 Azure 虛擬機器受控服務識別 (MSI) 來登入 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)] 本文提供使用 MSI 服務主體進行登入的 PowerShell 和 CLI 指令碼範例，以及錯誤處理等重要主題的指引。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

如果您打算使用本文中的 Azure PowerShell 或 Azure CLI 範例，請務必安裝最新版的 [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) 或 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

> [!IMPORTANT]
> - 本文中的所有範例指令碼都假設命令列用戶端在已啟用 MSI 的虛擬機器上執行。 在 Azure 入口網站中使用虛擬機器「連線」功能，從遠端連線到您的虛擬機器。 如需有關在虛擬機器上啟用 MSI 的詳細資訊，請參閱[使用 Azure 入口網站設定虛擬機器受管理服務識別 (MSI)](msi-qs-configure-portal-windows-vm.md)，或其中一篇變化文章 (使用 PowerShell、CLI、範本或 Azure SDK)。 
> - 若要避免資源存取期間發生錯誤，虛擬機器的 MSI 必須至少取得適當範圍 (該虛擬機器或更高層級) 的「讀者」存取權，以允許在虛擬機器上進行 Azure Resource Manager 作業。 如需詳細資訊，請參閱[使用 Azure 入口網站將受管理服務識別 (MSI) 存取權指派給資源](msi-howto-assign-access-portal.md)。

## <a name="overview"></a>概觀

MSI 會提供[服務主體](develop/active-directory-dev-glossary.md#service-principal-object)，這是在虛擬機器上[啟用 MSI 時所建立](msi-overview.md#how-does-it-work)。 服務主體可以獲得 Azure 資源的存取權，並可讓指令碼/命令列用戶端用來作為登入及存取資源時的身分識別。 傳統上，若要以受保護資源本身的身分識別來存取該資源，指令碼用戶端需要：  

   - 向 Azure AD 註冊並獲得同意成為機密/Web 用戶端應用程式
   - 在其服務主體下登入，並使用應用程式的認證 (這可能會內嵌於指令碼中)

透過 MSI，您的指令碼用戶端不再需要進行這其中的任何一項，因為它可以在 MSI 服務主體下登入。 

## <a name="azure-cli"></a>Azure CLI

下列指令碼示範如何：

1. 在虛擬機器的 MSI 服務主體下登入 Azure AD  
2. 呼叫 Azure Resource Manager 並取得虛擬機器的服務主體識別碼。 CLI 會自動為您管理權杖的擷取/使用。 務必以您的虛擬機器名稱取代 `<VM-NAME>`。  

   ```azurecli
   az login --msi
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The MSI service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

下列指令碼示範如何：

1. 取得虛擬機器的 MSI 存取權杖。  
2. 使用存取權杖在對應的 MSI 服務主體下登入 Azure AD。   
3. 呼叫 Azure Resource Manager Cmdlet 以取得虛擬機器的相關資訊。 PowerShell 會自動為您管理權杖的使用。  

   ```azurepowershell
   # Get an access token for the MSI
   $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                                 -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
   $content =$response.Content | ConvertFrom-Json
   $access_token = $content.access_token
   echo "The MSI access token is $access_token"

   # Use the access token to sign in under the MSI service principal. -AccountID can be any string to identify the session.
   Login-AzureRmAccount -AccessToken $access_token -AccountId "MSI@50342"

   # Call Azure Resource Manager to get the service principal ID for the VM's MSI. 
   $vmInfoPs = Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The MSI service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Azure 服務的資源識別碼

關於支援 Azure AD 且經過 MSI 測試的資源及其各自資源識別碼的清單，請參閱[支援 Azure AD 驗證的 Azure 服務](msi-overview.md#azure-services-that-support-azure-ad-authentication)。

## <a name="error-handling-guidance"></a>錯誤處理指引 

以下所示的回應可能代表虛擬機器的 MSI 未正確設定：

- PowerShell：Invoke-WebRequest：無法連線到遠端伺服器
- CLI：MSI：無法從 'http://localhost:50342/oauth2/token' 擷取權杖，包含 HTTPConnectionPool(主機='localhost'，連接埠=50342) 錯誤 

如果您收到這些錯誤的其中一個，請返回 [Azure 入口網站](https://portal.azure.com)中的 Azure 虛擬機器，並且：

- 移至 [設定] 頁面，並確定「受控服務識別」已設定為「是」。
- 移至 [延伸模組] 頁面，並確定已成功部署 MSI 延伸模組。

如果其中一者不正確，您可能需要再次在資源上重新部署 MSI，或針對部署失敗進行疑難排解。 如果您需要設定虛擬機器的協助，請參閱[使用 Azure 入口網站設定虛擬機器受控服務識別 (MSI)](msi-qs-configure-portal-windows-vm.md)。

## <a name="related-content"></a>相關內容

- 若要在 Azure VM 上啟用 MSI，請參閱[使用 PowerShell 設定虛擬機器受控服務識別 (MSI)](msi-qs-configure-powershell-windows-vm.md) 或 [使用 Azure CLI 設定虛擬機器受控服務識別 (MSI)](msi-qs-configure-cli-windows-vm.md)

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。








