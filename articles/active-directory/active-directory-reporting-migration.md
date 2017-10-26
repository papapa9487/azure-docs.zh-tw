---
title: "在 Azure 入口網站中尋找活動報告 | Microsoft Docs"
description: "了解如何在 Azure 入口網站中尋找 Azure Active Directory 活動報告。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: d93521f8-dc21-4feb-aaff-4bb300f04812
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/21/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 6f1485ad57eb28841ef5fe585e8cf1b8eb29e4a7
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2017
---
# <a name="find-activity-reports-in-the-azure-portal"></a>在 Azure 入口網站中尋找活動報告

如果您從 Azure 傳統入口網站移轉到 Azure 入口網站，您可看到新的 Azure Active Directory 活動記錄風貌。 在一篇近期的[部落格文章](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/)中，我們說明如何在 Azure 入口網站中您正在處理的資源內容中查看活動記錄。 在本文中，我們會說明如何在 Azure 入口網站中尋找您使用於 Azure 傳統入口網站的報告。

## <a name="whats-new"></a>新功能

Azure 傳統入口網站中的報告分為下列幾類：

1.  安全性報告
2.  活動報告
3.  整合式應用程式報告

### <a name="activity-and-integrated-app-reports"></a>活動和整合式應用程式報告

為了在 Azure 入口網站中進行以內容為基礎的報告，現有的報告會合併成單一檢視。 單一基礎 API 可提供此檢視的資料。

若要查看這個檢視，請在 [Azure Active Directory] 刀鋒視窗的 [活動] 之下，選取 [稽核記錄]。

![稽核記錄](./media/active-directory-reporting-migration/482.png "稽核記錄")

下列報告會整合在此檢視中：

-   稽核報告
-   密碼重設活動
-   密碼重設註冊活動
-   自助式群組活動
-   Office365 群組名稱變更
-   帳戶佈建活動
-   密碼變換狀態
-   帳戶佈建錯誤


[應用程式使用量] 報告已增強並包含於 [登入] 檢視中。 若要查看這個檢視，請在 [Azure Active Directory] 刀鋒視窗的 [活動] 之下，選取 [登入]。

![登入檢視](./media/active-directory-reporting-migration/483.png "登入檢視")

[登入] 檢視包含所有使用者登入。您可以利用這項資訊來取得應用程式使用量資訊。 您也可以在 [企業應用程式] 概觀的 [管理] 區段中檢視應用程式使用量資訊。

![企業應用程式](./media/active-directory-reporting-migration/484.png "企業應用程式")

## <a name="access-a-specific-report"></a>存取特定報告

雖然 Azure 入口網站提供了單一檢視，但您也可以查看特定報告。

### <a name="audit-logs"></a>稽核記錄

為了回應客戶的意見反應，您可以在 Azure 入口網站中使用進階篩選來存取想要的資料。 您可以使用的其中一個篩選是「活動類別」，它會列出 Azure AD 中的各種活動記錄。 若要縮小您要尋找的結果範圍，您可以選取一個類別。

例如，如果您只對取得自助式密碼重設相關活動感興趣，您可以選擇 [自助式密碼管理] 類別。 您可看見的類別取決於您正在處理的資源。  

![[篩選稽核記錄] 頁面上的類別選項](./media/active-directory-reporting-migration/06.png "[篩選稽核記錄] 頁面上的類別選項")

活動類別包括︰

- 核心目錄
- 自助式密碼管理
- 自助式群組管理
- 帳戶佈建

### <a name="application-usage"></a>應用程式使用情況

若要檢視所有應用程式或單一應用程式的應用程式使用量詳細資料，請在 [活動] 之下選取 [登入]。若要縮小結果範圍，您可以依據使用者名稱或應用程式名稱進行篩選。

![[篩選登入事件] 頁面](./media/active-directory-reporting-migration/07.png "[篩選登入事件] 頁面")

### <a name="security-reports"></a>安全性報告

#### <a name="azure-ad-anomalous-activity-reports"></a>Azure AD 異常活動報告

來自 Azure 傳統入口網站的 Azure AD 異常活動安全性報告已經合併，可為您提供一個集中檢視。 此檢視顯示 Azure AD 可以偵測及作為報告依據的所有安全性相關風險事件。

下表列出 Azure 入口網站中的 Azure AD 異常活動安全性報告，以及對應的風險事件類型。

| Azure AD 異常活動報告 |  Identity Protection 風險事件類型|
| :--- | :--- |
| 認證外洩的使用者 | 認證外洩 |
| 異常的登入活動 | 不可能到達非典型位置的移動 |
| 從可能受感染的裝置登入 | 從受感染的裝置登入|
| 從不明來源登入 | 從匿名 IP 位址登入 |
| 從具有可疑活動的 IP 位址登入 | 從具有可疑活動的 IP 位址登入 |
| - | 從不熟悉的位置登入 |

下列 Azure AD 異常活動安全性報告並未包含在 Azure 入口網站的風險事件中：

* 在多次失敗後登入
* 從多個地理區域登入

這些報告仍可在 Azure 傳統入口網站中取得，但是將會在未來某個時候淘汰。

如需詳細資訊，請參閱 [Azure Active Directory 風險事件](active-directory-identity-protection-risk-events.md)。  


#### <a name="detected-risk-events"></a>偵測到的風險事件

在 Azure 入口網站中，您可以在 [Azure Active Directory] 刀鋒視窗的 [安全性] 之下，存取所偵測到之風險事件的相關報告。 偵測到的風險事件會在下列報告中進行追蹤︰   

- 有風險的使用者
- 有風險的登入

![安全性報告](./media/active-directory-reporting-migration/04.png "安全性報告")

如需安全性報告的詳細資訊，請參閱︰

- [Azure Active Directory 入口網站中有風險使用者的安全性報告](active-directory-reporting-security-user-at-risk.md)
- [Azure Active Directory 入口網站中有風險的登入報告](active-directory-reporting-security-risky-sign-ins.md)


## <a name="activity-reports-in-the-azure-classic-portal-vs-the-azure-portal"></a>Azure 傳統入口網站與 Azure 入口網站中活動報告的比較

本節中的表格會列出 Azure 傳統入口網站的現有報表。 此外也說明如何在 Azure 入口網站中取得相同的資訊。

若要檢視所有稽核資料，請在 [Azure Active Directory] 刀鋒視窗的 [活動] 之下，移至 [稽核記錄]。

![稽核記錄](./media/active-directory-reporting-migration/61.png "稽核記錄")

| Azure 傳統入口網站                 | 在 Azure 入口網站中尋找                                                         |
| ---                                  | ---                                                                        |
| 稽核記錄                           | 選取 [核心目錄] 做為 [活動類別]。                       |
| 密碼重設活動              | 選取 [自助式密碼管理] 做為 [活動分類]。 |
| 密碼重設註冊活動 | 選取 [自助式密碼管理] 做為 [活動分類]。     |
| 自助式群組活動         | 選取 [自助式群組管理] 做為 [活動類別]。        |
| 帳戶佈建活動        | 選取 [帳戶使用者佈建] 做為 [活動類別]。         |
| 密碼變換狀態             | 選取 [自動應用程式密碼變換] 做為 [活動類別]。      |
| 帳戶佈建錯誤          | 選取 [帳戶使用者佈建] 做為 [活動類別]。        |
| Office365 群組名稱變更         | 選取 [自助式密碼管理] 做為 [活動分類]。 選取 [群組] 做為 [活動資源類型]。 選取 [O365 群組] 做為 [活動來源]。|

若要檢視 [應用程式使用量] 報告，請在 [Azure Active Directory] 刀鋒視窗的 [管理] 之下，依序選取 [企業應用程式] 和 [登入]。


![企業應用程式登入報告](./media/active-directory-reporting-migration/199.png "企業應用程式登入報告")

## <a name="next-steps"></a>後續步驟

如需報告概觀，請參閱 [Azure Active Directory 報告](active-directory-reporting-azure-portal.md)。
