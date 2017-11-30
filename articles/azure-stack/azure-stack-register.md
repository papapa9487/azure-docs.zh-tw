---
title: "註冊 Azure Stack | Microsoft Docs"
description: "使用您的 Azure 訂用帳戶註冊 Azure Stack。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: erikje
ms.openlocfilehash: 977630741b8424c4c6bd5f5d492e33b9981b9cb5
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2017
---
# <a name="register-azure-stack-with-your-azure-subscription"></a>使用您的 Azure 訂用帳戶註冊 Azure Stack

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

您可以向 Azure 註冊 [Azure Stack](azure-stack-poc.md)，以便從 Azure 下載市集項目，以及設定向 Microsoft 回報商務資料的功能。 

> [!NOTE]
>我們建議您註冊，因為它可讓您測試重要的 Azure Stack 功能，例如市集摘要整合和使用方式報告。 註冊 Azure Stack 之後，使用方式會回報給 Azure 商務。 您可以在註冊時所使用的訂用帳戶下看到這項資訊。 Azure Stack 開發套件使用者將不需針對回報的任何使用方式支付費用。
>


## <a name="get-azure-subscription"></a>取得 Azure 訂用帳戶

使用 Azure 註冊 Azure Stack 之前，您必須：

- Azure 訂用帳戶的訂用帳戶 ID。 若要取得 ID，請登入 Azure 並按一下 [更多服務]  >  [訂用帳戶]，然後按一下您要使用的訂用帳戶，便可以在 [基本資訊] 下找到 [訂用帳戶 ID]。 目前不支援中國、德國，和美國政府雲端訂用帳戶。
- 訂用帳戶擁有者的帳戶使用者名稱和密碼 (支援 MSA/2FA 帳戶)。
- Azure 訂用帳戶的 Azure Active Directory。 您可以將游標暫留在 Azure 入口網站右上角的顯示圖片，即可在 Azure 中找到此目錄。 

如果您沒有符合這些需求的 Azure 訂用帳戶，則可以[在這裡建立免費的 Azure 帳戶](https://azure.microsoft.com/en-us/free/?b=17.06)。 註冊 Azure Stack 不會對您的 Azure 訂用帳戶收取任何費用。



## <a name="register-azure-stack-resource-provider-in-azure"></a>在 Azure 中註冊 Azure Stack 資源提供者
> [!NOTE] 
> 此步驟僅應在 Azure Stack 環境中完成一次。
>

1. 以系統管理員的身分開啟 PowerShell 工作階段。
2. 登入作為 Azure 訂用帳戶擁有者的 Azure 帳戶 (您可以使用 Login-AzureRmAccount Cmdlet 登入；在您登入時，請務必將 -EnvironmentName 參數設為「AzureCloud」)。
3. 註冊 Azure 資源提供者「Microsoft.AzureStack」。

**範例：** 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
```

## <a name="register-azure-stack-with-azure"></a>向 Azure 註冊 Azure Stack

> [!NOTE]
> 這些步驟全都必須從可存取具有特殊權限之端點的機器執行。 就 Azure Stack 開發套件而言，此機器將是主機電腦。 如果您要使用整合式系統，請連絡您的 Azure Stack 操作員。
>

1. 以系統管理員身分開啟 PowerShell 主控台，然後[安裝適用於 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。  

2. 新增您將用來註冊 Azure Stack 的 Azure 帳戶。 若要這麼做，請執行未使用任何參數的 `Add-AzureRmAccount` Cmdlet。 當系統提示您輸入 Azure 帳戶認證時，您可能需要根據帳戶的組態使用雙因素驗證。  

3. 如果您有多個訂用帳戶，請執行下列命令以選取您要使用的訂用帳戶：  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. 刪除任何對應於註冊的現有 Powershell 模組版本，然後[從 GitHub 下載其最新版本](azure-stack-powershell-download.md)。  

5. 從先前的步驟中建立的 "AzureStack-Tools-master" 目錄巡覽至「註冊」資料夾，然後匯入 ".\RegisterWithAzure.psm1" 模組：  

   ```powershell 
   Import-Module .\RegisterWithAzure.psm1 
   ```

6. 在同一個 PowerShell 工作階段中，執行下列指令碼。 在出現認證的提示時，將 `azurestack\cloudadmin` 指定為使用者，而密碼則與您在部署期間用於本機管理員的密碼相同。  

   ```powershell
   $AzureContext = Get-AzureRmContext
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
   Add-AzsRegistration `
       -CloudAdminCredential $CloudAdminCred `
       -AzureSubscriptionId $AzureContext.Subscription.Id `
       -AzureDirectoryTenantName $AzureContext.Tenant.TenantId `
       -PrivilegedEndpoint AzS-ERCS01 `
       -BillingModel Development 
   ```

   | 參數 | 說明 |
   | -------- | ------------- |
   | CloudAdminCredential | 用來[存取具有特殊權限的端點](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)的雲端網域認證。 使用者名稱的格式為 "\<Azure Stack 網域\>\cloudadmin"。 針對開發套件，使用者名稱會設為"azurestack\cloudadmin"。 如果您要使用整合式系統，請向您的 Azure Stack 操作員索取此值。|
   | AzureSubscriptionId | 您用來註冊 Azure Stack 的 Azure 訂用帳戶。|
   | AzureDirectoryTenantName | 與您的 Azure 訂用帳戶相關聯的 Azure 租用戶目錄名稱。 註冊資源會建立在此目錄租用戶中。 |
   | PrivilegedEndpoint | 一個預先設定的遠端 PowerShell 主控台，可為您提供記錄收集和其他後續部署工作之類的功能。 針對開發套件，具有特殊權限的端點會裝載在 "AzS-ERCS01" 虛擬機器上。 如果您要使用整合式系統，請向您的 Azure Stack 操作員索取此值。 若要深入了解，請參閱[使用具有特殊權限的端點](azure-stack-privileged-endpoint.md)主題。|
   | BillingModel | 您的訂用帳戶所使用的計費模型。 此參數允許的值為 - "Capacity"、"PayAsYouUse" 和 "Development"。 針對開發套件，此值會設為 "Development"。 如果您要使用整合式系統，請向您的 Azure Stack 操作員索取此值。 |

7. 指令碼完成時，您會看到「正在啟動 Azure Stack (此步驟可能需要 10 分鐘才能完成)」的訊息。 

## <a name="verify-the-registration"></a>確認註冊

1. 登入系統管理員入口網站 (https://adminportal.local.azurestack.external)。
2. 按一下 [更多服務]  >  [市集管理]  >  [從 Azure 新增]。
3. 如果您看到 Azure 提供的項目清單 (例如 WordPress)，則表示啟用已成功。

> [!NOTE]
> 註冊完成後，就不會再出現未註冊的作用中警告。

## <a name="next-steps"></a>後續步驟

[連接至 Azure Stack](azure-stack-connect-azure-stack.md)

