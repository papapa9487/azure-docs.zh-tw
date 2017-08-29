---
title: "Azure AD SSPR 資料需求 | Microsoft Docs"
description: "Azure AD 自助式密碼重設的資料需求和如何滿足這些需求"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: f7ecb22ee46d83867453e035f8a639bc7f7f2d81
ms.contentlocale: zh-tw
ms.lasthandoff: 05/08/2017

---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>部署密碼重設而不需要使用者註冊

需要有驗證資料才能部署自助式密碼重設 (SSPR)。 某些組織會要求使用者自行輸入其驗證資料，但許多組織會選擇與 Active Directory 中現有的資料同步處理。 如果您已正確格式化內部部署目錄中的資料，並[使用快速設定完成 Azure AD Connect 設定](./connect/active-directory-aadconnect-get-started-express.md)，則 Azure AD 和 SSPR 可取用該資料，而不需要使用者互動。

任何電話號碼必須是這種格式才有效：+國碼 電話號碼。範例：+1 4255551234。

> [!NOTE]
> 密碼重設不支援電話分機。 即使是 +1 4255551234X12345 格式，撥號之前都會移除分機號碼。

## <a name="fields-populated"></a>填入的欄位

如果您使用 Azure AD Connect 的預設設定，則會建立下列對應。

| 內部部署 AD | Azure AD | Azure AD 驗證的連絡資訊 |
| --- | --- | --- |
| telephoneNumber | 辦公室電話 | 備用手機 |
| mobile | 行動電話 | 電話 |


## <a name="security-questions-and-answers"></a>安全性問題和答案

安全性問題和答案會安全地儲存在 Azure AD 租用戶中，使用者只能透過 [SSPR 註冊入口網站](https://aka.ms/ssprsetup)存取。 系統管理員無法看到或修改其他使用者的問題和答案內容。

### <a name="what-happens-when-a-user-registers"></a>使用者註冊時的情況

當使用者註冊時，註冊頁面會設定下列欄位：

* 驗證電話
* 驗證電子郵件
* 安全性問題和答案

如果您已提供 [行動電話] 或 [備用電子郵件] 的值，使用者即使尚未註冊此服務，也可以立即使用這些值來重設其密碼。 此外，使用者會在第一次註冊時看到這些值，並可隨意加以修改。 成功註冊之後，這些值就會分別保存在 [驗證電話] 和 [驗證電子郵件] 欄位中。

## <a name="set-and-read-authentication-data-using-powershell"></a>使用 PowerShell 設定和讀取驗證資料

您可以使用 PowerShell 設定下列欄位

* 替代電子郵件
* 行動電話
* 辦公室電話 - 只有在未與內部部署目錄同步處理時才能設定

### <a name="using-powershell-v1"></a>使用 PowerShell V1

首先，您必須[下載並安裝 Azure AD PowerShell 模組](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)。 安裝之後，即可依照下列步驟設定每一個欄位。

#### <a name="set-authentication-data-with-powershell-v1"></a>使用 PowerShell V1 設定驗證資料

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-authentication-data-with-powershellpowershell-v1"></a>使用 PowerShellPowerShell V1 讀取驗證資料

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="authentication-phone-and-authentication-email-can-only-be-read-using-powershell-v1-using-the-commands-that-follow"></a>必須在 Powershell V1 中使用下列命令，才能讀取 [驗證電話] 和 [驗證電子郵件]

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="using-powershell-v2"></a>使用 PowerShell V2

首先，您必須[下載並安裝 Azure AD V2 PowerShell 模組](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md)。 安裝之後，即可依照下列步驟設定每一個欄位。

若要從支援 Install-Module 的最新 PowerShell 版本快速安裝，請執行下列命令 (第一行只是檢查是否已安裝)︰

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-authentication-data-with-powershell-v2"></a>使用 PowerShell V2 設定驗證資料

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

### <a name="read-authentication-data-with-powershell-v2"></a>使用 PowerShell V2 讀取驗證資料

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>後續步驟

下列連結提供有關使用 Azure AD 重設密碼的其他資訊

* [**快速入門**](active-directory-passwords-getting-started.md) - 開始執行 Azure AD 自助式密碼管理 
* [**授權**](active-directory-passwords-licensing.md) - 設定 Azure AD 授權
* [**推出**](active-directory-passwords-best-practices.md) - 使用此處提供的指引來規劃 SSPR 並部署給使用者
* [**自訂**](active-directory-passwords-customize.md) - 為您的公司自訂 SSPR 體驗的外觀與風格。
* [**原則**](active-directory-passwords-policy.md) - 了解並設定 Azure AD 密碼原則
* [**報告**](active-directory-passwords-reporting.md) - 探索您的使用者是否、何時、何地存取 SSPR 功能
* [**技術性深入探討**](active-directory-passwords-how-it-works.md) - 深入探索以了解其運作方式
* [**常見問題集**](active-directory-passwords-faq.md) - 如何？ 原因為何？ 何事？ 何地？ 何人？ 何時？ - 您一直想要詢問之問題的答案
* [**疑難排解**](active-directory-passwords-troubleshoot.md) - 了解如何解決我們看到的 SSPR 常見問題

