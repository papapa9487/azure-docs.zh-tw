---
title: "Azure Active Directory 條件式存取的技術參考 | Microsoft Docs"
description: "透過條件式存取控制，Azure Active Directory 會在驗證使用者時以及允許存取應用程式之前，檢查您挑選的特定條件。 一旦符合這些條件，就會驗證使用者並允許存取應用程式。"
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: f96189735512090f993f61c0d64a249f650ea2a2
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Azure Active Directory 條件式存取的技術參考

透過 [Azure Active Directory (Azure AD) 條件式存取](active-directory-conditional-access-azure-portal.md)，您可以微調授權使用者如何存取您的應用程式。  
本主題為您提供下列條件式存取原則項目的支援資訊： 

- 雲端應用程式指派

- 用戶端應用程式條件



## <a name="cloud-apps-assignments"></a>雲端應用程式指派

當您設定條件式存取原則時，必須[選取原則所適用的雲端應用程式](active-directory-conditional-access-azure-portal.md#who)。 

![控制](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-apps"></a>Microsoft 雲端應用程式

您可以將條件式存取原則指派給下列 Microsoft 的雲端應用程式：

- Azure 遠端應用程式

- Dynamics CRM

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (包括商務用 OneDrive)

- Microsoft Power BI 

- Visual Studio Team Services

- Microsoft Teams


### <a name="other-apps"></a>其他應用程式 

除了 Microsoft 雲端應用程式之外，您還可以將條件式存取原則指派給下列類型的雲端應用程式：

- 已連線 Azure Active Directory (Azure AD) 的應用程式

- 預先整合的同盟軟體即服務 (SaaS) 應用程式

- 使用密碼單一登入 (SSO) 的應用程式

- 企業營運應用程式

- 使用 Azure AD 應用程式 Proxy 的應用程式。 


## <a name="client-apps-conditions"></a>用戶端應用程式條件 

當您設定條件式存取原則時，可以設定[用戶端應用程式條件](active-directory-conditional-access-azure-portal.md#client-apps)。 當有人從這些類型的用戶端應用程式嘗試存取時，用戶端應用程式條件可讓您授與或封鎖存取：

- [瀏覽器]
- 行動裝置應用程式和桌面應用程式

![控制](./media/active-directory-conditional-access-technical-reference/03.png)


### <a name="supported-browsers"></a>支援的瀏覽器 

如果您在條件式存取原則中選取 [瀏覽器] 來授與資源的存取權，只在嘗試使用支援的瀏覽器存取時才會授與存取。 使用不支援的瀏覽器存取嘗試時，嘗試就會遭到封鎖。

![支援的瀏覽器](./media/active-directory-conditional-access-technical-reference/05.png)

在條件式存取原則中，支援下列瀏覽器： 


| 作業系統                     | 瀏覽器                 | 支援     |
| :--                    | :--                      | :-:         |
| Win 10                 | IE、Edge                 | ![勾選][1] |
| Win 10                 | Chrome                   | 預覽     |
| Win 8 / 8.1            | IE、Chrome               | ![勾選][1] |
| Win 7                  | IE、Chrome               | ![勾選][1] |
| iOS                    | Safari                   | ![勾選][1] |
| Android                | Chrome                   | ![勾選][1] |
| Windows Phone          | IE、Edge                 | ![勾選][1] |
| Windows Server 2016    | IE、Edge                 | ![勾選][1] |
| Windows Server 2016    | Chrome                   | 敬請期待 |
| Windows Server 2012 R2 | IE、Chrome               | ![勾選][1] |
| Windows Server 2008 R2 | IE、Chrome               | ![勾選][1] |
| Mac OS                 | Safari                   | ![勾選][1] |
| Mac OS                 | Chrome                   | 敬請期待 |

> [!NOTE]
> 針對 Chrome 支援，您必須使用 Windows 10 建立者更新，而且安裝可以在[這裡](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)找到的擴充功能。


### <a name="supported-mobile-apps-and-desktop-clients"></a>支援的行動裝置應用程式和桌面用戶端

如果您在條件式存取原則中選取 [行動裝置應用程式和桌面用戶端] 來授與資源的存取權，只在嘗試使用支援的行動裝置應用程式或桌面用戶端存取時才會授與存取。 使用不支援的行動裝置應用程式或桌面用戶端存取嘗試時，嘗試就會遭到封鎖。

![控制](./media/active-directory-conditional-access-technical-reference/06.png)

下列行動裝置應用程式和桌面用戶端支援 Office 365 和其他 Azure AD 連線服務應用程式的條件式存取︰


| 用戶端應用程式| 目標服務| 平台 |
| :-- | --- | --- |
| 應用程式的 MFA 和位置原則。 不支援裝置型原則。| 任何 My Apps 應用程式服務| Android 和 iOS|
| Azure 遠端應用程式| Azure 遠端應用程式服務| Windows 10、Windows 8.1、Windows 7、iOS、Android 和 Mac OS X|
| Dynamics CRM 應用程式| Dynamics CRM| Windows 10、Windows 8.1、Windows 7、iOS 和 Android|
| Microsoft Teams Services - 這會控制支援 Microsoft Teams 及其所有用戶端應用程式的所有服務 - Windows 桌面、MAC OS X、iOS、Android、WP 和 Web 用戶端| Microsoft Teams| Windows 10、Windows 8.1、Windows 7、iOS/Android 和 MAC OSX|
| 郵件/行事曆/連絡人應用程式、Outlook 2016、Outlook 2013 (已啟用新式驗證)、商務用 Skype (採用新式驗證)| Office 365 Exchange Online| Windows 10|
| Outlook 2016、Outlook 2013 (已啟用新式驗證)、商務用 Skype (採用新式驗證)| Office 365 Exchange Online| Windows 8.1、Windows 7|
| Outlook 行動應用程式| Office 365 Exchange Online| iOS|
| Outlook 2016 (macOS 版 Office)| Office 365 Exchange Online| Mac OS X|
| Office 2016 應用程式、通用 Office 應用程式、Office 2013 (具備新式驗證)、OneDrive 同步處理用戶端 (請參閱[附註](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))、預計未來提供的 Office Groups 支援、預計未來提供的 SharePoint 應用程式支援| Office 365 SharePoint Online| Windows 10|
| Office 2016 應用程式、Office 2013 (具備新式驗證)、OneDrive 同步處理用戶端 (請參閱[附註](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))| Office 365 SharePoint Online| Windows 8.1、Windows 7|
| Office 行動應用程式| Office 365 SharePoint Online| iOS、Android|
| macOS 版 Office 2016 (僅限 Word、Excel、PowerPoint、OneNote)。 未來規劃支援商務用 OneDrive| Office 365 SharePoint Online| Mac OS X|
| Office Yammer 應用程式| Office 365 Yammer| Windows 10、iOS、Android|
| PowerBI 應用程式。 適用於 Android 的 Power BI 應用程式目前不支援裝置型條件式存取。| PowerBI service| Windows 10、Windows 8.1、Windows 7 及 iOS|
| Visual Studio Team Services 應用程式| Visual Studio Team Services| Windows 10、Windows 8.1、Windows 7、iOS 和 Android|














## <a name="next-steps"></a>後續步驟

- 如需條件式存取的概觀，請參閱 [Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal.md)
- 如果您已準備好設定您環境的條件式存取原則，請參閱 [Azure Active Directory 中條件式存取的最佳做法](active-directory-conditional-access-best-practices.md)



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png



