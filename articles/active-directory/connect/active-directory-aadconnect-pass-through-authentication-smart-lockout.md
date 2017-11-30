---
title: "Azure AD Connect：傳遞驗證 - 智慧鎖定 | Microsoft Docs"
description: "本文說明 Azure Active Directory (Azure AD) 傳遞驗證如何保護您的內部部署帳戶不受雲端的暴力密碼破解攻擊所威脅"
services: active-directory
keywords: "Azure AD Connect 傳遞驗證, 安裝 Active Directory, Azure AD 的必要元件, SSO, 單一登入"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: 2e1468c6a576ab71b85e3f69e5914df6ee9e4d5a
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2017
---
# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>Azure Active Directory 傳遞驗證：智慧鎖定

## <a name="overview"></a>概觀

Azure Active Directory (Azure AD) 可防範暴力密碼破解攻擊，並防止正版使用者遭到鎖定而無法使用其 Office 365 和 SaaS 應用程式。 當您使用傳遞驗證來作為登入方法時，系統便支援這項稱為*智慧鎖定*的功能。 依預設會為所有租用戶啟用智慧鎖定，且此功能會持續保護您的使用者帳戶。

智慧鎖定會追蹤失敗的登入嘗試。 在超過特定的*鎖定閾值*後，它會啟動*鎖定持續時間*。 在鎖定持續時間內，智慧鎖定會拒絕來自攻擊者的任何登入嘗試。 如果攻擊者持續攻擊，則在鎖定持續時間結束後，後續的失敗登入嘗試將會導致更久的鎖定持續期間。

>[!NOTE]
>預設的鎖定閾值是 10 次失敗的嘗試。 而預設的鎖定持續期間則是 60 秒。

智慧鎖定也會區分出正版使用者的登入和來自攻擊者的登入。 在大部分的情況下，它只會鎖定攻擊者。 這項功能可防止攻擊者惡意地讓使用者遭到鎖定。 智慧鎖定會使用過去的登入行為、使用者的裝置和瀏覽器以及其他訊號，來區分正版使用者和攻擊者。 演算法會持續改善。

傳遞驗證會將密碼驗證要求轉送到您的內部部署 Active Directory，因此您必須防止攻擊者鎖定使用者的 Active Directory 帳戶。 Active Directory 具有其本身的帳戶鎖定原則，具體來說，即[帳戶鎖定閾值](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx)和[下列時間過後重設帳戶鎖定計數器](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx)原則。 請適當設定 Azure AD 鎖定閾值和鎖定持續時間值，以在雲端中的攻擊者存取內部部署 Active Directory 之前將其篩選掉。

>[!NOTE]
>智慧鎖定功能是免費的，且所有客戶均預設「開啟」此功能。 不過，若要使用圖形 API 來修改 Azure AD 的鎖定閾值和鎖定持續期間值，您的租用戶必須至少有一個 Azure AD Premium P2 授權。 不需要_每位使用者_都有 Azure AD Premium P2 授權，也能透過傳遞驗證取得智慧鎖定功能。

若要確保使用者的內部部署 Active Directory 帳戶受到完善保護，您必須確定：

   * Azure AD 的鎖定閾值「小於」Active Directory 帳戶的鎖定閾值。 請適當設定這些值，使 Active Directory 帳戶的鎖定閾值比 Azure AD 鎖定閾值至少長兩到三倍。
   * Azure AD 的鎖定持續期間 (以秒來表示) 比 Active Directory 帳戶的「下列時間過後重設帳戶鎖定計數器」持續期間 (以分鐘來表示)「還長」。

>[!IMPORTANT]
>目前，如果使用者的雲端帳戶已被智慧鎖定功能鎖定，則系統管理員無法將其解除鎖定。 系統管理員必須等待鎖定持續期間結束。

## <a name="verify-your-active-directory-account-lockout-policies"></a>驗證 Active Directory 帳戶鎖定原則

請使用下列指示來驗證您的 Active Directory 帳戶鎖定原則：

1.  開啟群組原則管理工具。
2.  編輯套用到所有使用者的群組原則，例如**預設網域原則**。
3.  瀏覽到 **[電腦設定]** > **[原則]** > **[Windows 設定]** > **[安全性設定]** > **[帳戶原則]** > **[帳戶鎖定原則]**。
4.  確認 [帳戶鎖定閾值] 和 [下列時間過後重設帳戶鎖定計數器] 的值。

![Active Directory 帳戶鎖定原則](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values-requires-a-premium-license"></a>使用圖形 API 管理租用戶的智慧鎖定值 (需要 Premium 授權)

>[!IMPORTANT]
>使用圖形 API 來修改 Azure AD 鎖定閾值和鎖定持續時間值，是 Azure AD Premium P2 的功能。 另外，您也必須是租用戶的全域管理員。

您可以使用 [Graph 總管](https://developer.microsoft.com/graph/graph-explorer)來讀取、設定及更新 Azure AD 智慧鎖定值。 您也可以透過程式設計方式進行這些作業。

### <a name="view-smart-lockout-values"></a>檢視智慧鎖定值

請遵循下列步驟來檢視租用戶的智慧鎖定值：

1. 以租用戶全域管理員的身分登入 Graph 總管。 如果出現提示，請針對要求的權限授與存取權。
2. 選取 [修改權限]，然後選取 [Directory.ReadWrite.All] 權限。
3. 依照下列方式設定圖形 API 要求：將版本設為 [BETA]，要求類型設為 [GET]，並將 URL 設為 `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`。
4. 選取 [執行查詢] 以檢視租用戶的智慧鎖定值。 如果您之前從未設定過租用戶的值，您會看到空白的設定。

### <a name="set-smart-lockout-values"></a>設定智慧鎖定值

請依照下列步驟設定租用戶的智慧鎖定值 (只有首次設定時需要)：

1. 以租用戶全域管理員的身分登入 Graph 總管。 如果出現提示，請針對要求的權限授與存取權。
2. 選取 [修改權限]，然後選取 [Directory.ReadWrite.All] 權限。
3. 依照下列方式設定圖形 API 要求：將版本設為 [BETA]，要求類型設為 [POST]，並將 URL 設為 `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`。
4. 複製下列 JSON 要求並貼到 [要求本文] 欄位。
5. 選取 [執行查詢] 以設定租用戶的智慧鎖定值。

```
{
  "templateId": "5cf42378-d67d-4f36-ba46-e8b86229381d",
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "300"
    },
    {
      "name": "LockoutThreshold",
      "value": "5"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

>[!NOTE]
>如果您不使用 **BannedPasswordList** 和 **EnableBannedPasswordCheck** 值，您可以讓這兩個值分別保留為空白 (**""**) 和 **false**。

使用[檢視智慧鎖定值](#view-smart-lockout-values)中的步驟，確認您已正確設定租用戶的智慧鎖定值。

### <a name="update-smart-lockout-values"></a>更新智慧鎖定值

請依照下列步驟更新租用戶的智慧鎖定值 (如果您已設定這些值)：

1. 以租用戶全域管理員的身分登入 Graph 總管。 如果出現提示，請針對要求的權限授與存取權。
2. 選取 [修改權限]，然後選取 [Directory.ReadWrite.All] 權限。
3. [請遵循下列步驟來檢視租用戶的智慧鎖定值](#view-smart-lockout-values)。 將 **displayName** 為 **PasswordRuleSettings** 之項目的 `id` 值 (GUID) 複製下來。
4. 依照下列方式設定圖形 API 要求：將版本設為 [BETA]，要求類型設為 [PATCH]，並將 URL 設為 `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>`。 將步驟 3 中的 GUID 用於 `<id>`。
5. 複製下列 JSON 要求並貼到 [要求本文] 欄位。 適當變更智慧鎖定值。
6. 選取 [執行查詢] 以更新租用戶的智慧鎖定值。

```
{
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "30"
    },
    {
      "name": "LockoutThreshold",
      "value": "4"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

使用使用[檢視智慧鎖定值](#view-smart-lockout-values)中的步驟，確認您已正確更新租用戶的智慧鎖定值。

## <a name="next-steps"></a>後續步驟
[UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)：使用 Azure Active Directory 論壇提出新功能要求。
