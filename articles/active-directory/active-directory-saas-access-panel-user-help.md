---
title: "是否需要 Azure Active Directory 中 My Apps 入口網站的協助 | Microsoft Docs"
description: "在使用存取面板時，取得常見工作的執行指示。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: c67cd675-b567-41e1-8bc2-e06fe0b38d3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2017
ms.author: markvi
ms.reviewer: japere
ms.openlocfilehash: 613c68d5c13793a3b696b6afbfc0e1a31595e201
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="do-you-need-help-with-the-my-apps-portal"></a>是否需要 My Apps 入口網站的協助？

因為您在使用 My Apps 入口網站時不幸發生問題，所以可能已到達此頁面。 有一些情況下會要求您連絡服務台或系統管理員來解決問題，而以下是可能有所助益的一些疑難排解主題。

## <a name="i-am-having-trouble-signing-into-the-my-apps-portal"></a>我無法登入 My Apps 入口網站

要檢查的一般問題：

- 查看使用者是否登入正確 URL：[https://myapps.microsoft.com](https://myapps.microsoft.com)

- 嘗試將 URL 新增至瀏覽器的信任網站。

- 確定密碼未過期或忘記。 如需如何更新密碼的詳細資訊，請參閱[這裡](active-directory-passwords-update-your-own-password.md)。

- 查看您的驗證連絡資訊是否為最新的，並且不會封鎖您的存取。 如需設定驗證資訊的詳細資訊，請參閱[這裡](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/end-user/multi-factor-authentication-end-user)。

- 嘗試清除瀏覽器的 Cookie，然後嘗試再次登入。

如果您嘗試登入仍然遇到問題，請連絡系統管理員以取得進一步的協助。


## <a name="how-do-i-update-my-password"></a>如何更新我的密碼？

如果您忘記密碼、未曾收到 IT 人員給予的密碼、系統鎖定了您帳戶的密碼或您想要變更密碼，請參閱[忘記 Azure AD 密碼的說明](active-directory-passwords-update-your-own-password.md)以取得詳細資訊。

## <a name="how-do-i-register-for-password-reset"></a>如何註冊密碼重設？

身為一般使用者，您可以重設密碼或解除鎖定您的帳戶，而不需使用自助式密碼重設 (SSPR) 與人員對話。 在您可以使用這項功能之前，您必須註冊驗證方法，或確認您系統管理員已填入的預先定義驗證方法。 如需詳細資訊，請參閱[註冊自助式密碼重設](active-directory-passwords-reset-register.md)。


## <a name="i-am-having-trouble-installing-the-my-apps-portal-browser-extension"></a>我無法安裝 My Apps 入口網站瀏覽器延伸模組

查看是否符合瀏覽器需求：

- 入口網站需要支援 JavaScript 且已啟用 CSS 的瀏覽器。 如果您使用密碼單一登入應用程式，就必須一併安裝隨附的延伸模組。 當您啟動已設定密碼單一登入的應用程式時，就會自動下載此延伸模組。

- 延伸模組的的瀏覽器需求為：
    - 在 Windows 7 或更新版本上的 Internet Explorer 8、9、10、11
    - Windows 10 Anniversary Edition 或更新版本上的 Edge
    - 在 Windows 7 或更新版本，和在 MacOS X 或更新版本上的 Chrome
    - 在 Windows XP SP2 或更新版本，和在 Mac OS X 10.6 或更新版本上的 Firefox 26.0 或更新版本

您也可以從下列直接連結中下載適用於 Chrome 和 Edge 的延伸模組：

- [Chrome 延伸模組](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

- [Edge 延伸模組](https://www.microsoft.com/store/apps/9pc9sckkzk84)

如果您在嘗試下列步驟進行安裝之後遇到問題：

- 檢查您的瀏覽器延伸模組設定是否已啟用延伸模組。

- 重新啟動您的瀏覽器，然後登入 My Apps 入口網站。

- 清除瀏覽器的 Cookie，然後登入 My Apps 入口網站。

## <a name="how-do-i-add-a-new-app"></a>如何新增應用程式？

1.  在 [應用程式] 分頁上，按一下 [新增應用程式]。

2.  搜尋您要新增的應用程式，然後按一下 [新增]。

**備註：**

- 如果系統管理員已為您的帳戶啟用此選項，您只能存取此選項。

- 如果應用程式需要權限，您可能需要等待系統管理員核准。


## <a name="how-do-i-manage-my-group-memberships"></a>如何管理我的群組成員資格？

1. 按一下 [群組] 應用程式圖格。 
2. 若要建立群組，請在 [我擁有的群組] 底下，按一下 [建立群組]，然後依照指示進行。
3. 若要加入群組，請在 [我所在的群組] 底下，按一下 [加入群組]，然後依照指示進行。

**備註：**

- 如果系統管理員已為您的帳戶啟用此選項，您只能存取此選項。

- 您所隸屬的群組可讓您檢視詳細資料及脫離群組。

- 您所擁有的群組則可讓您檢視詳細資料、新增或移除成員，以及脫離群組。


## <a name="next-steps"></a>後續步驟

如需與疑難排解相關的資訊，請參閱[使用應用程式的存取面板網站或行動應用程式時發生問題](active-directory-application-access-panel-content-map.md)

