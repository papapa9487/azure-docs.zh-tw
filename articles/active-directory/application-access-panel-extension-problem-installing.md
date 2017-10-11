---
title: "安裝應用程式存取面板的瀏覽器延伸模組時出現的錯誤 | Microsoft Docs"
description: "如何修正在安裝存取面板的瀏覽器延伸模組時所遇到的常見錯誤"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.reviewer: japere
ms.openlocfilehash: 8b7327508633e33917d1fa9c1f35ed1bde5a26e1
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="problem-installing-the-application-access-panel-browser-extension"></a>安裝應用程式存取面板的瀏覽器延伸模組時出現的錯誤

存取面板是網頁型入口網站，可讓在 Azure Active Directory (Azure AD) 中具有公司或學校帳戶的使用者，檢視和啟動 Azure AD 系統管理員已授權他們存取的雲端式應用程式。 具有 Azure AD 版本的使用者也可以透過存取面板使用自助群組和應用程式管理功能。 存取面板與 Azure 入口網站分開，使用者不需要具備 Azure 訂用帳戶也能使用。

若要在存取面板中使用密碼單一登入 (SSO)，必須在使用者的瀏覽器中安裝存取面板延伸模組。 當使用者選取已設定密碼 SSO 的應用程式時，就會自動下載此存取面板延伸模組。

## <a name="meeting-browser-requirements-for-the-access-panel"></a>符合存取面板的瀏覽器需求

存取面板需要支援 JavaScript 且已啟用 CSS 的瀏覽器。 若要在存取面板中使用密碼單一登入 (SSO)，必須在使用者的瀏覽器中安裝存取面板延伸模組。 當使用者選取已設定密碼 SSO 的應用程式時，就會自動下載此存取面板延伸模組。

若是密碼 SSO，則使用者的瀏覽器可以是：

-   Internet Explorer 8、9、10、11 -- 在 Windows 7 或更新版本上

-   Windows 10 Anniversary Edition 或更新版本上的 Edge 

-   Chrome - 在 Windows 7 或更新版本，和在 MacOS X 或更新版本上

-   Firefox 26.0 或更新版本 - 在 Windows XP SP2 或更新版本，和在 Mac OS X 10.6 或更新版本上

## <a name="how-to-install-the-access-panel-browser-extension"></a>如何安裝存取面板的瀏覽器延伸模組

若要安裝存取面板的瀏覽器延伸模組，請遵循下列步驟：

1.  在其中一種支援的瀏覽器中開啟[存取面板](https://myapps.microsoft.com)，然後在您的 Azure AD 中以**使用者**身分登入。

2.  按一下存取面板中的 [密碼-SSO 應用程式]。

3.  在要求安裝軟體的提示中，選取 [立即安裝]。

4.  系統會根據您的瀏覽器將您導向至下載連結。 將延伸模組**新增**到瀏覽器中。

5.  如果您的瀏覽器要求，請選取 [啟用] 或 [允許] 該延伸模組。

6.  安裝之後，**重新啟動**瀏覽器工作階段。

7.  登入存取面板，並查看您是否可以**啟動**密碼-SSO 應用程式

您可能也會從下列直接連結中下載適用於 Chrome 和 Edge 的擴充功能：

-   [Chrome 存取面板延伸模組](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Edge 存取面板延伸模組](https://www.microsoft.com/store/apps/9pc9sckkzk84) 

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>設定適用於 Internet Explorer 的群組原則

您可以設定一個群組原則，以允許您在使用者電腦上遠端安裝適用於 Internet Explorer 的存取面板延伸模組。

必要條件包括：

-   您已設定了 [Active Directory 網域服務](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)，並且已將使用者的電腦加入網域。

-   您必須擁有「編輯設定」權限，才能編輯群組原則物件 (GPO)。 根據預設，下列安全性群組的成員擁有此權限：網域系統管理員、企業系統管理員及群組原則建立者擁有者。 [深入了解](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)。

如需如何設定群組原則並將它部署到使用者的逐步指示，請遵循[如何使用群組原則部署 Internet Explorer 的存取面板延伸模組](active-directory-saas-ie-group-policy.md)教學課程。

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>進行 Internet Explorer 中存取面板的疑難排解

如需存取診斷工具和設定 IE 延伸模組的逐步指示，請遵循[疑難排解 Internet Explorer 的存取面板延伸模組](active-directory-saas-ie-troubleshooting.md)指南。

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>如果這些疑難排解步驟無法解決問題

使用下列資訊 (若有的話) 開啟支援票證︰

-   相互關聯錯誤 ID

-   UPN (使用者電子郵件地址)

-   TenantID

-   瀏覽器類型

-   錯誤發生期間的時區和時間/時間範圍

-   Fiddler 追蹤

## <a name="next-steps"></a>後續步驟
[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)
