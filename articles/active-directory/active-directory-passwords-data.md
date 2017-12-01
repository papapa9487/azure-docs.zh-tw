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
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: ed82200bf81702bbe35a371e7d86676c2c27d8f4
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2017
---
# <a name="deploy-password-reset-without-requiring-end-user-registration"></a>部署密碼重設而不需要使用者註冊

若要部署 Azure Active Directory (Azure AD) 自助式密碼重設 (SSPR)，必須要有驗證資料。 有些組織會讓其使用者自行輸入自己的驗證資料。 但許多組織會偏好與 Active Directory 中既有的資料進行同步處理。 如果您執行下列操作，同步處理的資料將可供 Azure AD 和 SSPR 使用而無須使用者輸入：
   * 將您內部部署目錄中的資料正確格式化。
   * [使用快速設定來設定 Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md)。

為了正確運作，電話號碼的格式必須是：+國碼 電話號碼，例如 +1 4255551234。

> [!NOTE]
> 密碼重設不支援電話分機。 即使是 +1 4255551234X12345 格式，撥號之前都會移除分機號碼。

## <a name="fields-populated"></a>填入的欄位

如果您使用 Azure AD Connect 的預設設定，將會建立下列對應：

| 內部部署 Active Directory | Azure AD | Azure AD 驗證連絡資訊 |
| --- | --- | --- |
| telephoneNumber | 辦公室電話 | 備用手機 |
| mobile | 行動電話 | 電話 |


## <a name="security-questions-and-answers"></a>安全性問題和答案

安全性問題和答案會安全地儲存在 Azure AD 租用戶中，且僅供使用者透過 [SSPR 註冊入口網站](https://aka.ms/ssprsetup)存取。 系統管理員無法看到或修改其他使用者的問題和答案內容。

### <a name="what-happens-when-a-user-registers"></a>使用者註冊時的情況

當使用者註冊時，註冊頁面會設定下列欄位：

* **驗證電話**
* **驗證電子郵件**
* **安全性問題和答案**

如果您已提供 [行動電話] 或 [備用電子郵件] 的值，使用者即使尚未註冊此服務，也可以立即使用這些值來重設其密碼。 此外，使用者會在第一次註冊時看到這些值，並可視需要加以修改。 在使用者成功註冊之後，這些值就會分別保存在 [驗證電話] 和 [驗證電子郵件] 欄位中。

## <a name="set-and-read-the-authentication-data-through-powershell"></a>透過 PowerShell 設定和讀取驗證資料

以下是可透過 PowerShell 設定的欄位：

* **備用電子郵件**
* **行動電話**
* **辦公室電話**：只有在未與內部部署目錄同步處理時才能設定

### <a name="use-powershell-version-1"></a>使用 PowerShell 第 1 版

首先，您必須[下載並安裝 Azure AD PowerShell 模組](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)。 安裝該模組之後，即可使用下列步驟來設定每個欄位。

#### <a name="set-the-authentication-data-with-powershell-version-1"></a>使用 PowerShell 第 1 版來設定驗證資料

```
Connect-MsolService

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"

Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com") -MobilePhone "+1 1234567890" -PhoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-1"></a>使用 PowerShell 第 1 版來讀取驗證資料

```
Connect-MsolService

Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber

Get-MsolUser | select DisplayName,UserPrincipalName,AlternateEmailAddresses,MobilePhone,PhoneNumber | Format-Table
```

#### <a name="read-the-authentication-phone-and-authentication-email-options"></a>讀取 [驗證電話] 和 [驗證電子郵件] 選項

使用 PowerShell 第 1 版時，若要讀取 [驗證電話] 和 [驗證電子郵件]，請使用下列命令：

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

### <a name="use-powershell-version-2"></a>使用 PowerShell 第 2 版

若要開始進行，您必須[下載並安裝 Azure AD 第 2 版 PowerShell 模組](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md) \(英文\)。 安裝該模組之後，即可使用下列步驟來設定每個欄位。

若要從支援 Install-Module 的最新 PowerShell 版本快速安裝，請執行下列命令。 (第一行會檢查是否已經安裝模組)。

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="set-the-authentication-data-with-powershell-version-2"></a>使用 PowerShell 第 2 版來設定驗證資料

```
Connect-AzureAD

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 2345678901"
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"

Set-AzureADUser -ObjectId user@domain.com -OtherMails @("emails@domain.com") -Mobile "+1 1234567890" -TelephoneNumber "+1 1234567890"
```

#### <a name="read-the-authentication-data-with-powershell-version-2"></a>使用 PowerShell 第 2 版來讀取驗證資料

```
Connect-AzureAD

Get-AzureADUser -ObjectID user@domain.com | select otherMails
Get-AzureADUser -ObjectID user@domain.com | select Mobile
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber

Get-AzureADUser | select DisplayName,UserPrincipalName,otherMails,Mobile,TelephoneNumber | Format-Table
```

## <a name="next-steps"></a>後續步驟

* [如何完成 SSPR 成功首度發行？](active-directory-passwords-best-practices.md)
* [重設或變更您的密碼](active-directory-passwords-update-your-own-password.md)
* [註冊自助式密碼重設](active-directory-passwords-reset-register.md)
* [您有授權問題嗎？](active-directory-passwords-licensing.md)
* [哪些驗證方法可供使用者使用？](active-directory-passwords-how-it-works.md#authentication-methods)
* [使用 SSPR 的原則選項有哪些？](active-directory-passwords-policy.md)
* [什麼是密碼回寫，且為什麼我需要了解它？](active-directory-passwords-writeback.md)
* [如何回報 SSPR 中的活動？](active-directory-passwords-reporting.md)
* [SSPR 中的所有選項有哪些，以及它們有何意義？](active-directory-passwords-how-it-works.md)
* [我認為有中斷。如何針對 SSPR 進行疑難排解？](active-directory-passwords-troubleshoot.md)
* [在其他某處並未涵蓋我的問題](active-directory-passwords-faq.md)
