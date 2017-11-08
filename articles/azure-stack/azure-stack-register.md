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
ms.date: 10/27/2017
ms.author: erikje
ms.openlocfilehash: 24cde66a132ae2e1ba0eb9b1564915746e5ca448
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2017
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
- [已註冊 Azure Stack 資源提供者](#register-azure-stack-resource-provider-in-azure)。

如果您沒有符合這些需求的 Azure 訂用帳戶，則可以[在這裡建立免費的 Azure 帳戶](https://azure.microsoft.com/en-us/free/?b=17.06)。 註冊 Azure Stack 不會對您的 Azure 訂用帳戶收取任何費用。



## <a name="register-azure-stack-resource-provider-in-azure"></a>在 Azure 中註冊 Azure Stack 資源提供者
> [!NOTE] 
> 此步驟僅需在 Azure Stack 環境中完成一次。
>

1. 以系統管理員身分啟動 PowerShell ISE。
2. 透過將 -EnvironmentName 參數設定為「AzureCloud」以登入至 Azure 訂用帳戶擁有者的 Azure 帳戶。
3. 註冊 Azure 資源提供者「Microsoft.AzureStack」。

範例： 
```Powershell
Login-AzureRmAccount -EnvironmentName "AzureCloud"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
```


## <a name="register-azure-stack-with-azure"></a>向 Azure 註冊 Azure Stack

> [!NOTE]
>這些所有步驟必須在主機電腦上完成。
>

1. [安裝適用於 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。 
2. 將 [RegisterWithAzure.psm1 指令碼](https://go.microsoft.com/fwlink/?linkid=842959)複製至資料夾 (例如 C:\Temp)。
3. 以系統管理員身分啟動 PowerShell ISE，並匯入 RegisterWithAzure 模組。    
4. 從 RegisterWithAzure.psm1 指令碼，執行 Add-AzsRegistration 模組。 取代下列預留位置： 
    - *YourCloudAdminCredential* 是 PowerShell 物件，其中包含 domain\cloudadmin 的本機網域認證 (針對開發套件，這是 azurestack\cloudadmin)。
    - *YourAzureSubscriptionID* 是您要用來註冊 Azure Stack 的 Azure 訂用帳戶。
    - YourAzureDirectoryTenantName 是與您的 Azure 訂用帳戶相關聯之 Azure 租用戶目錄的名稱。 註冊資源會建立在此目錄租用戶中。 
    - *YourPrivilegedEndpoint* 是[特殊權限端點](azure-stack-privileged-endpoint.md)的名稱。

    ```powershell
    Add-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -AzureDirectoryTenantName $YourAzureDirectoryTenantName  -AzureSubscriptionId $YourAzureSubscriptionId -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Development 
    ```
5. 在彈出的登入視窗中，輸入您的 Azure 訂用帳戶認證。

## <a name="verify-the-registration"></a>確認註冊

1. 登入系統管理員入口網站 (https://adminportal.local.azurestack.external)。
2. 按一下 [更多服務]  >  [市集管理]  >  [從 Azure 新增]。
3. 如果您看到 Azure 提供的項目清單 (例如 WordPress)，則表示啟用已成功。

> [!NOTE]
> 註冊完成後，就不會再出現未註冊的作用中警告。

## <a name="next-steps"></a>後續步驟

[連接至 Azure Stack](azure-stack-connect-azure-stack.md)

