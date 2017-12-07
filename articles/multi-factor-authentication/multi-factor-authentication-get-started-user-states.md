---
title: "Microsoft Azure Multi-Factor Authentication 使用者狀態"
description: "了解 Azure MFA 中的使用者狀態"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 0b9fde23-2d36-45b3-950d-f88624a68fbd
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: b73b5e47e3e14742e5094b8b0e979de7835cb9c7
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2017
---
# <a name="how-to-require-two-step-verification-for-a-user-or-group"></a>如何要求使用者或群組使用雙步驟驗證

可供您要求使用雙步驟驗證的方法有兩種。 第一個選項是為每個使用者啟用 Azure Multi-Factor Authentication (MFA)。 當您個別地為使用者啟用時，這些使用者永遠會執行雙步驟驗證 (但有一些例外，例如當他們從受信任的 IP 位址登入時，或如果他們開啟已記住的裝置功能)。 第二個選項是設定條件式存取原則，以在某些情況下要求使用雙步驟驗證。

>[!TIP] 
>請選擇其中一種方法來要求使用雙步驟驗證，但不要兩種方法都使用。 為使用者啟用 Azure MFA 的效力會凌駕任何條件式存取原則。

## <a name="which-option-is-right-for-you"></a>您合適的選項

**藉由變更使用者狀態來啟用 Azure MFA** 是用來要求使用雙步驟驗證的傳統方法。 這種方法適用於雲端 Azure MFA 和 Azure MFA Server。 您所啟用的所有使用者都會有相同的體驗，也就是每次登入時都要執行雙步驟驗證。 啟用使用者的效力會凌駕任何可能影響該使用者的條件式存取原則。 

**使用條件式存取原則來啟用 Azure MFA** 是用來要求使用雙步驟驗證的更彈性方法。 但此方法只適用於雲端 Azure MFA，而且條件式存取是 [Azure Active Directory 的付費功能](https://www.microsoft.com/cloud-platform/azure-active-directory-features)。 您可以建立適用於群組以及個別使用者的條件式存取原則。 您可以對高風險群組施加比低風險群組更多的限制，或者，也可以只針對高風險雲端應用程式要求雙步驟驗證，至於低風險的雲端應用程式則略過雙步驟驗證。 

當您開啟此要求後，這兩個選項都會提示使用者在第一次登入時註冊 Azure Multi-Factor Authentication。 這兩個選項也能與可設定的 [Azure Multi-Factor Authentication 設定](multi-factor-authentication-whats-next.md)搭配運作

## <a name="enable-azure-mfa-by-changing-user-status"></a>藉由變更使用者狀態來啟用 Azure MFA

Azure Multi-Factor Authentication 中的使用者帳戶具有下列三種不同狀態：

| 狀態 | 說明 | 受影響的非瀏覽器應用程式 | 受影響的瀏覽器應用程式和現代化驗證 |
|:---:|:---:|:---:|:--:|
| 已停用 |未註冊 Azure Multi-Factor Authentication (MFA) 之新使用者的預設狀態。 |否 |否 |
| 已啟用 |已在 Azure MFA 中註冊使用者，但使用者尚未註冊。 系統將在他們下一次登入時提示他們註冊。 |不會。  它們會繼續運作，直到註冊程序完成為止。 | 是。 一旦工作階段的重新整理權杖到期，就必須進行 MFA 註冊。|
| 已強制 |已註冊使用者，而且使用者已完成 Azure MFA 的註冊程序。 |是。  應用程式需要應用程式密碼。 |是。 登入時需要 MFA。 |

使用者的狀態會反映系統管理員是否已在 Azure MFA 中註冊他們，以及他們是否已完成註冊程序。

所有使用者一開始都是「已停用」狀態。 當您在 Azure MFA 中註冊使用者時，他們的狀態會變更為「已啟用」。 當已啟用的使用者登入並完成註冊程序之後，他們的狀態就會變更為「已強制」。  

### <a name="view-the-status-for-a-user"></a>檢視使用者的狀態

使用下列步驟來存取您可在其中檢視並管理使用者狀態的頁面：

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 移至 [Azure Active Directory] > [使用者和群組] > [所有使用者]。
3. 選取 [多重要素驗證]。
   ![選取 Multi-Factor Authentication](./media/multi-factor-authentication-get-started-user-states/selectmfa.png)
4. 隨即會開啟新的頁面，以顯示使用者狀態。
   ![Multi-Factor Authentication 使用者狀態 - 螢幕擷取畫面](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

### <a name="change-the-status-for-a-user"></a>變更使用者的狀態

1. 使用上述步驟來取得 Multi-Factor Authentication使用者頁面。
2. 尋找您想要針對 Azure MFA 啟用的使用者。 您可能需要在頂端變更檢視。 
   ![尋找使用者 - 螢幕擷取畫面](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. 勾選其名稱旁的方塊。
4. 在右邊的快速步驟底下，選擇 [啟用] 或 [停用]。
   ![啟用所選的使用者 - 螢幕擷取畫面](./media/multi-factor-authentication-get-started-cloud/user1.png)

   >[!TIP]
   >「已啟用」的使用者會在註冊 Azure MFA 時自動切換為「已強制」。 請勿以手動方式將使用者狀態變更為「已強制」。 

5. 在開啟的快顯視窗中確認您的選取項目。 

當您啟用使用者之後，應透過電子郵件通知他們。 告訴他們系統會在他們下一次登入時要求其進行註冊。 此外，如果您的組織使用不支援新式驗證的非瀏覽器應用程式，他們就需要建立應用程式密碼。 您也可以包含我們所提供 [Azure MFA 使用者指南](./end-user/multi-factor-authentication-end-user.md)的連結，以協助他們開始使用。

### <a name="use-powershell"></a>使用 PowerShell
若要使用 [Azure AD PowerShell](/powershell/azure/overview) 來變更使用者狀態，請變更 `$st.State`。 有三個可能的狀態︰

* 已啟用
* 已強制
* 已停用  

請勿直接將使用者移至「已強制」狀態。 非瀏覽器型應用程式會停止運作，因為使用者未通過 MFA 註冊並取得[應用程式密碼](multi-factor-authentication-whats-next.md#app-passwords)。 

當您需要大量啟用使用者時，使用 PowerShell 是一個不錯的選項。 建立會在使用者清單中循環移動並啟用這些使用者的 PowerShell 指令碼：

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

下列是一個範例：

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }

## <a name="enable-azure-mfa-with-a-conditional-access-policy"></a>使用條件式存取原則來啟用 Azure MFA

條件式存取是 Azure Active Directory 的付費功能，其具有許多可能的設定選項。 下列步驟會逐步解說一種用來建立原則的方式。 如需詳細資訊，請參閱 [Azure Active Directory 中的條件式存取](../active-directory/active-directory-conditional-access-azure-portal.md)。

1. 以系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 移至 [Azure Active Directory] > [條件式存取]。
3. 選取 [新增原則]。
4. 在 [指派] 底下選取 [使用者和群組]。 使用 [包含] 和 [排除] 索引標籤來指定要由原則管理的使用者和群組。
5. 在 [指派] 底下選取 [雲端應用程式]。 選擇要包含 [所有雲端應用程式]。
6. 在 [存取控制] 底下選取 [授與]。 選擇 [需要多重要素驗證]。
7. 將 [啟用原則] 轉為 [開啟]，然後選取 [儲存]。

條件式存取原則中的其他選項可讓您明確指定何時應該要求雙步驟驗證。 例如，您可以建立規定如下的原則：當承包商嘗試從未加入網域的裝置上不受信任的網路存取我們的採購應用程式時，要求進行雙步驟驗證。 

## <a name="next-steps"></a>後續步驟

- 取得[條件式存取的最佳做法](../active-directory/active-directory-conditional-access-best-practices.md)秘訣

- 管理[您的使用者和其裝置](multi-factor-authentication-manage-users-and-devices.md)的 Multi-Factor Authentication 設定
