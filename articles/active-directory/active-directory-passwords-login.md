---
title: "Windows 10 登入畫面中的 Azure AD SSPR | Microsoft Docs"
description: "設定 Windows 10 登入畫面 Azure AD 密碼重設和我忘記 PIN 碼"
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
ms.topic: get-started-article
ms.date: 11/08/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 9f7fdb97fd121eecf9e7b2f4edc1b568c8114869
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="azure-ad-password-reset-from-the-login-screen"></a>從登入畫面重設 Azure AD 密碼

您已經部署 Azure AD 自助式密碼重設 (SSPR)，但您的使用者仍會在忘記其密碼時打電話給技術服務人員。 他們因為無法取得網頁瀏覽器來存取 SSPR，所以才會打電話給技術服務人員。

透過新的 Windows 10 Fall Creators Update，具有已加入 Azure AD 之裝置的使用者可以在其登入畫面上看到 [重設密碼] 連結。 當使用者按一下此連結時，他們就可以享受他們所熟悉的相同自助式密碼重設 (SSPR) 體驗 

若要讓使用者能夠從 Windows 10 登入畫面重設其 Azure AD 密碼，必須符合下列需求：

* Windows 10 版本 1709，或已加入 Azure AD 網域的更新版用戶端。
* 必須啟用 Azure AD 自助式密碼重設。
* 設定及部署設定，以透過下列其中一種方法來啟用 [重設密碼] 連結：
   * [Intune 裝置組態設定檔](active-directory-passwords-login.md#configure-reset-password-link-using-intune)
   * [登錄機碼](active-directory-passwords-login.md#configure-reset-password-link-using-the-registry)

## <a name="configure-reset-password-link-using-intune"></a>使用 Intune 來設定重設密碼連結

### <a name="create-a-device-configuration-policy-in-intune"></a>在 Intune 中建立裝置設定原則

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後按一下 [Intune]。
2. 移至 [裝置組態] > [設定檔] > [建立設定檔]，以建立新的裝置組態設定檔
   * 為設定檔提供有意義的名稱
   * 選擇性地提供有意義的設定檔描述
   * 平台：**Windows 10 和更新版本**
   * 設定檔類型：**自訂**

   ![CreateProfile][CreateProfile]

3. 配置 [設定]
   * **新增**下列 OMA-URI 設定來啟用 [重設密碼] 連結
      * 提供有意義的名稱，以說明正在進行的設定
      * 選擇性地提供有意義的設定描述
      * **OMA URI** 設為 `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      * **資料類型** 設為 **整數**
      * **值** 設為 **1**
      * 按一下 [確定]
   * 按一下 [確認]
4. 按一下 [建立] 

### <a name="assign-a-device-configuration-policy-in-intune"></a>在 Intune 中指派裝置設定原則

#### <a name="create-a-group-to-apply-device-configuration-policy-to"></a>建立要套用裝置設定原則的群組

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後按一下 [Azure Active Directory]。
2. 瀏覽至 [使用者和群組] > [所有群組] > [新增群組]
3. 提供群組的名稱，然後在 [成員資格類型] 之下選擇 [已指派] 
   * 在 [成員] 之下，選擇您要套用原則之已加入 Azure AD 的 Windows 10 裝置。
   * 按一下 [選取]
4. 按一下 [建立] 

如需建立群組的詳細資訊，請參閱[使用 Azure Active Directory 群組管理資源的存取權](active-directory-manage-groups.md)。

#### <a name="assign-device-configuration-policy-to-device-group"></a>將裝置設定原則指派給裝置群組

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後按一下 [Intune]。
2. 移至 [裝置組態] > [設定檔] 來尋找先前建立的裝置組態設定檔 > 按一下稍早建立的設定檔
3. 將設定檔指派給裝置群組 
   * 按一下 [指派] > 在 [Include] 之下 > [選取要包含的群組]
   * 選取先前建立的群組，然後按一下 [選取]
   * 按一下 [儲存] 

   ![指派][Assignment]

您現在已建立並指派裝置設定原則，以使用 Intune 在登入畫面上啟用 [重設密碼] 連結。

## <a name="configure-reset-password-link-using-the-registry"></a>使用登錄來設定重設密碼連結

我們建議僅使用此方法來測試設定變更。

1. 使用系統管理認證來登入已加入 Azure AD 網域的裝置
2. 以系統管理員身分執行 **regedit**
3. 設定下列登錄機碼
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>使用者看到的內容

現已設定和指派原則，使用者有哪些變更？ 他們如何知道可以在登入畫面重設其密碼？

![LoginScreen][LoginScreen]

當使用者嘗試登入時，即可看到 [重設密碼] 連結，該連結可開啟登入畫面上的自助式密碼重設體驗。 這項功能可讓使用者重設其密碼，而不需使用另一個裝置來存取網頁瀏覽器。

您的使用者將在[重設您的工作或學校密碼](active-directory-passwords-update-your-own-password.md#reset-password-at-login)中發現使用這項功能的指引

## <a name="common-issues"></a>常見問題

使用 Hyper-V 測試這項功能時，不會出現 [重設密碼] 連結。

* 移至您用來測試的 VM，按一下 [檢視]，然後取消核取 [增強的工作階段]。

使用遠端桌面測試這項功能時，不會出現 [重設密碼] 連結

* 目前不支援從遠端桌面進行密碼重設。

## <a name="next-steps"></a>後續步驟
下列連結提供有關使用 Azure AD 重設密碼的其他資訊

* [如何部署 SSPR？](active-directory-passwords-best-practices.md)
* [如何啟用從登入畫面重設 PIN 碼](https://docs.microsoft.com/intune/device-windows-pin-reset)
* [MDM 驗證原則的詳細資訊](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-authentication)

[CreateProfile]: ./media/active-directory-passwords-login/create-profile.png "建立 Intune 裝置組態設定檔，以便在 Windows 10 登入畫面上啟用 [重設密碼] 連結"
[Assignment]: ./media/active-directory-passwords-login/profile-assignment.png "將 Intune 裝置設定原則指派給 Windows 10 裝置群組"
[LoginScreen]: ./media/active-directory-passwords-login/logon-reset-password.png "Windows 10 登入畫面上的重設密碼連結"
