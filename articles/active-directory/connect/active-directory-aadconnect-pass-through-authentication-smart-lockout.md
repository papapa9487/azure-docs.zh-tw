---
title: "Azure AD Connect：傳遞驗證 - 智慧鎖定 | Microsoft Docs"
description: "本文說明 Azure Active Directory (Azure AD) 傳遞驗證如何保護您的內部部署帳戶不受雲端的暴力密碼破解攻擊所威脅。"
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
ms.date: 08/02/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: c84b2406e6373701c83c509342129bd6d7d4034b
ms.contentlocale: zh-tw
ms.lasthandoff: 08/04/2017

---

# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>Azure Active Directory 傳遞驗證：智慧鎖定

## <a name="overview"></a>概觀

Azure AD 可防範暴力密碼破解攻擊，並防止正版使用者遭到其 Office 365 和 SaaS 應用程式鎖定而無法使用應用程式。 當您使用傳遞驗證來作為登入方法時，系統便支援這項稱為**智慧鎖定**的功能。 所有租用戶預設都會啟用智慧鎖定，而且此功能會隨時保護您的使用者帳戶；您不必自行將其開啟。

智慧鎖定在運作時，會追蹤失敗的登入嘗試，並在經過一定的**鎖定閾值**後，開始**鎖定期間**。 在鎖定期間，系統會拒絕來自攻擊者的任何登入嘗試。 如果攻擊者持續攻擊，則在鎖定期間結束後，後續的失敗登入嘗試將會導致更久的鎖定期間。

>[!NOTE]
>預設的鎖定閾值是 10 次失敗嘗試，而預設的鎖定期間則是 60 秒。

智慧鎖定也會區分出正版使用者的登入和來自攻擊者的登入，大部分情況下，智慧鎖定只會鎖定攻擊者。 這項功能可防止攻擊者惡意地讓使用者遭到鎖定。 我們使用過去的登入行為、使用者的裝置和瀏覽器以及其他訊號，來區分正版使用者和攻擊者。 我們會持續改善我們的演算法。

傳遞驗證會將密碼驗證要求轉送到您的內部部署 Active Directory (AD)，因此您必須防止攻擊者鎖定使用者的 AD 帳戶。 由於您有自己的 AD 帳戶鎖定原則 (具體而言就是[**帳戶鎖定閾值**](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx)和[**下列時間過後重設帳戶鎖定計數器原則**](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx))，因此您必須適當地設定 Azure AD 的鎖定閾值和鎖定期間值，以將雲端攻擊篩選掉，不讓其到達您的內部部署 AD。

>[!NOTE]
>智慧鎖定功能是免費的，且所有客戶均預設「開啟」此功能。 不過，若要使用圖形 API 來修改 Azure AD 的 [鎖定閾值] 和 [鎖定期間] 值，則您的租用戶必須至少有一個 Azure AD Premium P2 授權。 不需要_每位使用者_都有 Azure AD Premium P2 授權，也能透過傳遞驗證取得智慧鎖定功能。

若要確保使用者的內部部署 AD 帳戶受到完善保護，您必須確定：

1.  Azure AD 的鎖定閾值「小於」AD 的帳戶鎖定閾值。 我們對於這些值的設定建議是，您可以將 AD 的帳戶鎖定閾值設定為 Azure AD 鎖定閾值的至少兩到三倍。
2.  Azure AD 的鎖定期間 (以秒來表示) 比 AD 的下列時間過後重設帳戶鎖定計數器 (以分鐘來表示)「還久」。

## <a name="verify-your-ad-account-lockout-policies"></a>驗證 AD 帳戶鎖定原則

請使用下列指示來驗證您的 AD 帳戶鎖定原則：

1.  開啟群組原則管理工具。
2.  編輯要套用到所有使用者的群組原則 (例如，預設網域原則)。
3.  瀏覽至 [電腦設定]\[原則]\[Windows 設定]\[安全性設定]\[帳戶原則]\[帳戶鎖定原則]。
4.  確認 [帳戶鎖定閾值] 和 [下列時間過後重設帳戶鎖定計數器] 的值。

![AD 帳戶鎖定原則](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values"></a>使用圖形 API 來管理租用戶的智慧鎖定值

>[!IMPORTANT]
>使用圖形 API 來修改 Azure AD 的鎖定閾值和鎖定期間值是 Azure AD Premium P2 的功能。 另外，您也必須是租用戶的全域管理員。

您可以使用 [Graph 總管](https://developer.microsoft.com/graph/graph-explorer)來讀取、設定及更新 Azure AD 的智慧鎖定值。 但您也可以透過程式設計的方式進行這些作業。

### <a name="read-smart-lockout-values"></a>讀取智慧鎖定值

請遵循下列步驟來讀取租用戶的智慧鎖定值：

1. 以租用戶全域管理員的身分登入 Graph 總管。 如果出現提示，請授與所要求權限的存取權。
2. 按一下 [修改權限]，然後選取 [Directory.ReadWrite.All] 權限。
3. 如下所示來設定圖形 API 要求：將版本設為 [BETA]，要求類型設為 [GET]，並將 URL 設為 `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`。
4. 按一下 [執行查詢] 以查看租用戶的智慧鎖定值。 如果您之前從未設定過租用戶的值，您會看到空白的設定。

### <a name="set-smart-lockout-values"></a>設定智慧鎖定值

請遵循下列步驟來設定租用戶的智慧鎖定值 (僅適用於首次設定)：

1. 以租用戶全域管理員的身分登入 Graph 總管。 如果出現提示，請授與所要求權限的存取權。
2. 按一下 [修改權限]，然後選取 [Directory.ReadWrite.All] 權限。
3. 如下所示來設定圖形 API 要求：將版本設為 [BETA]，要求類型設為 [POST]，並將 URL 設為 `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`。
4. 複製下列 JSON 要求並貼到 [要求本文] 欄位。 適當變更智慧鎖定值，並為 `templateId` 使用隨機 GUID。
5. 按一下 [執行查詢] 以設定租用戶的智慧鎖定值。

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
>如果您沒有要使用 **BannedPasswordList** 和 **EnableBannedPasswordCheck** 值，您可以讓這兩個值分別保留空白 ("") 和 "false" 設定。

確認您是否已使用[這些步驟](#read-smart-lockout-values)正確地設定租用戶的智慧鎖定值。

### <a name="update-smart-lockout-values"></a>更新智慧鎖定值

請遵循下列步驟來更新租用戶的智慧鎖定值 (如果您之前已設定過這些值)：

1. 以租用戶全域管理員的身分登入 Graph 總管。 如果出現提示，請授與所要求權限的存取權。
2. 按一下 [修改權限]，然後選取 [Directory.ReadWrite.All] 權限。
3. [請遵循下列步驟來讀取租用戶的智慧鎖定值](#read-smart-lockout-values)。 將 "displayName" 為 "PasswordRuleSettings" 之項目的 `id` 值 (GUID) 複製下來。
4. 如下所示來設定圖形 API 要求：將版本設為 [BETA]，要求類型設為 [PATCH]，並將 URL 設為 `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>` - 對 `<id>` 使用從步驟 3 複製下來的 GUID。
5. 複製下列 JSON 要求並貼到 [要求本文] 欄位。 適當變更智慧鎖定值。
6. 按一下 [執行查詢] 以更新租用戶的智慧鎖定值。

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

確認您是否已使用[這些步驟](#read-smart-lockout-values)正確地更新租用戶的智慧鎖定值。

## <a name="next-steps"></a>後續步驟
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用於提出新的功能要求。

