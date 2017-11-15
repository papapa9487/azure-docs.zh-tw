---
title: "在 Azure Active Directory 中進行自助式或病毒式註冊 | Microsoft Docs"
description: "在 Azure Active Directory (Azure AD) 租用戶中使用自助式註冊"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: b9f01876-29d1-4ab8-8b74-04d43d532f4b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 762627a1d743922d680d90d1065ae80fbd3f4420
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2017
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>什麼是 Azure Active Directory 的自助式註冊？
本文說明自助式註冊，以及如何在 Azure Active Directory (Azure AD) 中支援自助式註冊。 如果您想要從未受管理的 Azure AD 租用戶接管網域名稱，請參閱[以系統管理員身分接管未受管理的目錄](domains-admin-takeover.md)。

## <a name="why-use-self-service-signup"></a>為何使用自助式註冊？
* 讓客戶更快取得他們想要的服務
* 建立服務的電子郵件型供應項目
* 建立以電子郵件為基礎的註冊流程，讓使用者使用其易記的工作電子郵件別名來快速建立身分識別
* 自助建立的 Azure AD 目錄可以轉換成可用於其他服務的受管理目錄

## <a name="terms-and-definitions"></a>詞彙和定義
* **自助式註冊**：這是使用者用以註冊雲端服務的方法，系統會根據其電子郵件網域在 Azure AD 中自動為其建立身分識別。
* **未受管理的 Azure AD 目錄**：這是建立身分識別的目錄。 未受管理的目錄是沒有全域管理員的目錄。
* **電子郵件驗證的使用者**：這是 Azure AD 中的使用者帳戶類型。 在註冊自助式供應項目後自動建立身分識別的使用者，就是所謂的電子郵件驗證的使用者。 電子郵件驗證的使用者是加上 creationmethod=EmailVerified 標記之目錄的一般成員。

## <a name="how-do-i-control-self-service-settings"></a>如何控制自助式設定？
系統管理員目前有兩個自助式控制項。 它們可以控制：

* 使用者是否可以透過電子郵件加入目錄。
* 使用者是否可以授權自己使用應用程式和服務。

### <a name="how-can-i-control-these-capabilities"></a>如何控制這些功能？
管理員可以使用下列 Azure AD Cmdlet Set-MsolCompanySettings 參數來設定這些功能：

* **AllowEmailVerifiedUsers** 控制使用者是否可以建立或加入未受管理的目錄。 如果您將該參數設定為 $false，則經過電子郵件驗證的使用者都無法加入目錄。
* **AllowAdHocSubscriptions** 可控制使用者是否能夠執行自助式註冊。 如果您將該參數為 $false，則沒有任何使用者可以執行自助式註冊。

### <a name="how-do-the-controls-work-together"></a>這些控制項如何一起運作？
這兩個參數可合併使用，以定義更精確的自助式註冊控制。 例如，下列命令可讓使用者執行自助式註冊，但僅限於在 Azure AD 中已經有帳戶的使用者 (換句話說，需要先建立電子郵件驗證帳戶的使用者則無法執行自助式註冊)：

````
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
````
下列流程圖說明這些參數的各種不同組合，以及針對目錄和自助式註冊造成的情況。

![][1]

如需如何使用這些參數的詳細資訊和相關範，請參閱 [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)。

## <a name="next-steps"></a>後續步驟
* [將自訂網域名稱新增到 Azure AD](add-custom-domain.md)
* [如何安裝和設定 Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Cmdlet 參考](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
