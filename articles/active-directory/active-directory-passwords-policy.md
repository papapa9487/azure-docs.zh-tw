---
title: "原則︰Azure AD SSPR | Microsoft Docs"
description: "Azure AD 自助式密碼重設原則選項"
services: active-directory
keywords: "Active directory 密碼管理, 密碼管理, Azure AD 自助式密碼重設"
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
ms.openlocfilehash: 9d61f46070e6956c60f1135b98a9ebe71011b922
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2017
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>密碼原則和 Azure Active Directory 中的限制

本文說明與儲存在 Azure AD 租用戶中的使用者帳戶相關聯的密碼原則和複雜性需求。

## <a name="administrator-password-policy-differences"></a>系統管理員密碼原則差異

Microsoft 會對任何系統管理員角色 (全域管理員、技術支援系統管理員、密碼管理員等) 強制執行強式預設「雙閘道」密碼重設原則

這會讓系統管理員無法使用安全性問題，並強制執行下列各項。

在下列狀況下適用需要兩種驗證資料 (電子郵件地址「和」電話號碼) 的雙關卡原則

* 所有的 Azure 系統管理員角色
  * 服務台系統管理員
  * 服務支援管理員
  * 計費管理員
  * 合作夥伴第 1 層支援
  * 合作夥伴第 2 層支援
  * Exchange 服務管理員
  * Lync 服務管理員
  * 使用者帳戶管理員
  * 目錄撰寫者
  * 全域管理員/公司系統管理員
  * SharePoint 服務管理員
  * 規範管理員
  * 應用程式系統管理員
  * 安全性系統管理員
  * 特殊權限角色管理員
  * Intune 服務管理員
  * 應用程式 Proxy 服務管理員
  * CRM 服務管理員
  * Power BI 服務管理員
  
* 試用已經過 30 天**或**
* 虛名網域存在於 (contoso.com) **或**
* Azure AD Connect 正在同步處理內部部署目錄中的身分識別

### <a name="exceptions"></a>例外狀況
在下列狀況下適用需要一種驗證資料 (電子郵件地址「或」電話號碼) 的單關卡原則

* 試用的前 30 天**或**
* 虛名網域不存在 (*.onmicrosoft.com) **且** Azure AD Connect 並未同步處理身分識別


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>適用於所有使用者帳戶的 UserPrincipalName 原則

每個需要登入 Azure AD 的使用者帳戶都必須具有與其帳戶相關聯的唯一使用者主體名稱 (UPN) 屬性值。 下表概述的原則適用於已同步至雲端的內部部署 Active Directory 使用者帳戶和僅限雲端的使用者帳戶。

| 屬性 | UserPrincipalName 需求 |
| --- | --- |
| 允許的字元 |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> 。 - \_ ! \# ^ \~</li></ul> |
| 不允許的字元 |<ul> <li>任何不是用來分隔使用者名稱和網域的 '@' 字元。</li> <li>'@' 符號前面不可直接包含句點字元 '.'</li></ul> |
| 長度限制 |<ul> <li>總長度不得超過 113 個字元</li><li>'@' 符號前為 64 個字元</li><li>'@' 符號後為 48 個字元</li></ul> |

## <a name="password-policies-that-apply-only-to-cloud-user-accounts"></a>僅適用於雲端使用者帳戶的密碼原則

下表描述可套用至在 Azure AD 中建立及管理的使用者帳戶的可用密碼原則設定。

| 屬性 | 需求 |
| --- | --- |
| 允許的字元 |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| 不允許的字元 |<ul><li>Unicode 字元</li><li>空格</li><li> **僅限使用強式密碼**：'@' 符號前面不能直接包含點字元 '.'</li></ul> |
| 密碼限制 |<ul><li>最少 8 個字元，最多 16 個字元</li><li>**僅限使用增強式密碼**︰需要下列 4 種字元中的 3 種︰<ul><li>小寫字元</li><li>大寫字元</li><li>數字 (0-9)</li><li>符號 (請參閱上面的密碼限制)</li></ul></li></ul> |
| 密碼到期時間 |<ul><li>預設值：**90** 天 </li><li>值可透過適用於 Windows PowerShell 的 Azure Active Directory 模組使用 Set-MsolPasswordPolicy Cmdlet 設定。</li></ul> |
| 密碼到期通知 |<ul><li>預設值：**14** 天 (密碼到期之前)</li><li>您可使用 Set-MsolPasswordPolicy Cmdlet 設定此值。</li></ul> |
| 密碼到期 |<ul><li>預設值︰**false** 天 (表示已啟用密碼到期) </li><li>可以針對個別使用者帳戶使用 Set-msoluser Cmdlet 設定值。 </li></ul> |
| 密碼**變更**歷程記錄 |**變更**密碼時，**無法**再次使用上次的密碼。 |
| 密碼**重設**歷程記錄 | **重設**遺忘的密碼時，**可以**再次使用上次的密碼。 |
| 帳戶鎖定 |10 次嘗試登入失敗 (錯誤密碼) 之後，使用者會被封鎖一分鐘。 後續嘗試登入的錯誤會增加使用者被封鎖的時間。 |

## <a name="set-password-expiration-policies-in-azure-active-directory"></a>在 Azure Active Directory 中設定密碼到期原則

Microsoft 雲端服務的全域管理員可以使用「適用於 Windows PowerShell 的 Microsoft Azure Active Directory 模組」，將使用者密碼設為不會到期。 您亦可使用 Windows PowerShell Cmdlet 移除永不到期組態，或是查看哪些使用者密碼設為不會到期。 本指引適用於其他提供者 (例如 Microsoft Intune 和 Office 365)，他們也仰賴 Microsoft Azure Active Directory 提供身分識別和目錄服務。 這是原則中唯一可變更的部分。

> [!NOTE]
> 您僅可將未透過目錄同步作業執行同步處理的使用者帳戶密碼，設定為不會到期。 如需有關目錄同步作業，請參閱 [Connect AD 與 Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)。
>
>

## <a name="set-or-check-password-policies-using-powershell"></a>使用 PowerShell 設定或檢查密碼原則

首先，您必須[下載並安裝 Azure AD PowerShell 模組](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0)。 一旦安裝好，即可依照下列步驟設定每一個欄位。

### <a name="how-to-check-expiration-policy-for-a-password"></a>如何檢查密碼到期原則
1. 使用您公司的管理員認證連線至 Windows PowerShell。
2. 執行下列其中一個命令：

   * 若要查看單一使用者的密碼是否設為永久有效，請透過使用者主體名稱 (UPN) (例如 aprilr@contoso.onmicrosoft.com)，或是您想要檢查之使用者的使用者識別碼，來執行下列 Cmdlet：`Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * 若要查看所有使用者的「密碼永久有效」設定，請執行下列 Cmdlet：`Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

### <a name="set-a-password-to-expire"></a>設定密碼到期

1. 使用您公司的管理員認證連線至 Windows PowerShell。
2. 執行下列其中一個命令：

   * 若要將某位使用者的密碼設為會到期，請透過使用使用者主體名稱 (UPN) 或使用者的使用者識別碼，執行下列 Cmdlet：`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * 若要將組織中所有使用者的密碼設為會到期，請使用下列 Cmdlet：`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>設定密碼為永久有效

1. 使用您公司的管理員認證連線至 Windows PowerShell。
2. 執行下列其中一個命令：

   * 若要將某位使用者的密碼設為永久有效，請透過使用使用者主體名稱 (UPN) 或使用者的使用者識別碼，來執行下列 Cmdlet： `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * 若要將組織中所有使用者的密碼設為永久有效，請執行下列 Cmdlet： `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

   > [!WARNING]
   > 如果您設定 `-PasswordNeverExpires $true`，密碼仍然會根據 `pwdLastSet` 屬性計算時效。 這意謂著如果您將密碼設定為永不過期，接著根據 `pwdLastSet` 經過 90 多天，然後您變更 `-PasswordNeverExpires $false`，則所有 `pwdLastSet` 超過 90 天的密碼在下次登入時都必須變更。 這項變更可能會影響到大量的使用者。 

## <a name="next-steps"></a>後續步驟

下列連結提供有關使用 Azure AD 重設密碼的其他資訊

* [如何完成 SSPR 成功首度發行？](active-directory-passwords-best-practices.md)
* [重設或變更您的密碼](active-directory-passwords-update-your-own-password.md)。
* [註冊自助式密碼重設](active-directory-passwords-reset-register.md)。
* [您有授權問題嗎？](active-directory-passwords-licensing.md)
* [SSPR 使用哪些資料，以及您應該為使用者填入哪些資料？](active-directory-passwords-data.md)
* [哪些驗證方法可供使用者使用？](active-directory-passwords-how-it-works.md#authentication-methods)
* [什麼是密碼回寫，且為什麼我需要了解它？](active-directory-passwords-writeback.md)
* [如何回報 SSPR 中的活動？](active-directory-passwords-reporting.md)
* [SSPR 中的所有選項有哪些，以及它們有何意義？](active-directory-passwords-how-it-works.md)
* [我認為有中斷。如何針對 SSPR 進行疑難排解？](active-directory-passwords-troubleshoot.md)
* [在其他某處並未涵蓋我的問題](active-directory-passwords-faq.md)
