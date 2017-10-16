---
title: "Azure Active Directory 條件式存取的技術參考 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 中使用條件式存取控制。 指定驗證使用者並控制對應用程式存取的條件。 符合指定的條件時，使用者會通過驗證，並獲授與對應用程式的存取權。"
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
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 27ace4e9bc4a1626059fba657dce0c629d52f32d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Azure Active Directory 條件式存取的技術參考

您可以使用 [Azure Active Directory (Azure AD) 條件式存取](active-directory-conditional-access-azure-portal.md)，微調授權使用者存取資源的方式。  

本主題會針對條件式存取原則的下列設定選項，提供支援資訊： 

- 雲端應用程式指派

- 裝置平台條件 

- 雲端應用程式條件

- 已核准的用戶端應用程式需求



## <a name="cloud-apps-assignments"></a>雲端應用程式指派

當您設定條件式存取原則時，必須[選取使用您原則的雲端應用程式](active-directory-conditional-access-azure-portal.md#who)。 

![選取您原則適用的雲端應用程式](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Microsoft 雲端應用程式

您可以將條件式存取原則指派給下列 Microsoft 的雲端應用程式：

- Azure RemoteApp

- Microsoft Dynamics 365

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (包括商務用 OneDrive)

- Microsoft Power BI 

- Microsoft Visual Studio Team Services

- Microsoft Teams


### <a name="other-applications"></a>其他應用程式 

除了 Microsoft 雲端應用程式之外，您還可以將條件式存取原則指派給下列類型的雲端應用程式：

- Azure AD 連線的應用程式

- 預先整合的同盟軟體即服務 (SaaS) 應用程式

- 使用密碼單一登入 (SSO) 的應用程式

- 企業營運應用程式

- 使用 Azure AD 應用程式 Proxy 的應用程式


## <a name="device-platform-condition"></a>裝置平台條件

在條件式存取原則中，您可以設定裝置平台條件，將原則繫結到用戶端上的作業系統。

![將存取原則繫結至用戶端作業系統](./media/active-directory-conditional-access-technical-reference/41.png)

Azure AD 條件式存取支援下列裝置平台：

- Android

- iOS

- Windows Phone

- Windows

- macOS (預覽)



## <a name="client-apps-condition"></a>用戶端應用程式條件 

當您設定條件式存取原則時，可以選取用戶端應用程式條件適用的[用戶端應用程式](active-directory-conditional-access-azure-portal.md#client-apps)。 當有人從下列類型的用戶端應用程式嘗試存取時，將用戶端應用程式條件設定為授與或封鎖存取：

- [瀏覽器]
- 行動裝置應用程式和桌面應用程式

![控制對用戶端應用程式的存取](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>支援的瀏覽器 

使用條件式存取原則中的 [瀏覽器] 選項，控制瀏覽器存取。 只有在支援的瀏覽器嘗試存取時，才會授與存取權。 不支援的瀏覽器嘗試存取時，嘗試會遭到封鎖。

![控制對支援之瀏覽器的存取](./media/active-directory-conditional-access-technical-reference/05.png)

在條件式存取原則中，支援下列瀏覽器： 


| 作業系統                     | 瀏覽器                    | 支援     |
| :--                    | :--                         | :-:         |
| Windows 10             | Internet Explorer、Edge     | ![勾選][1] |
| Windows 10             | Chrome                      | ![勾選][1] |
| Windows 8 / 8.1        | Internet Explorer、Chrome   | ![勾選][1] |
| Windows 7              | Internet Explorer、Chrome   | ![勾選][1] |
| iOS                    | Safari、Intune Managed Browser                      | ![勾選][1] |
| Android                | Chrome、Intune Managed Browser                      | ![勾選][1] |
| Windows Phone          | Internet Explorer、Edge     | ![勾選][1] |
| Windows Server 2016    | Internet Explorer、Edge     | ![勾選][1] |
| Windows Server 2016    | Chrome                      | 敬請期待 |
| Windows Server 2012 R2 | Internet Explorer、Chrome   | ![勾選][1] |
| Windows Server 2008 R2 | Internet Explorer、Chrome   | ![勾選][1] |
| macOS                  | Safari                      | ![勾選][1] |
| macOS                  | Chrome                      | 敬請期待 |

> [!NOTE]
> 如需 Chrome 支援，您必須使用 muse Chrome 支援 Windows 10 Creators Update (1703 版) 或更新版本。<br>
> 您可以安裝[這個擴充功能](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)。

### <a name="supported-mobile-applications-and-desktop-clients"></a>支援的行動裝置應用程式和桌面用戶端

使用條件式存取原則中的 [行動裝置應用程式和桌面用戶端] 選項，控制應用程式和用戶端存取。 只有在支援的行動裝置應用程式或桌面用戶端嘗試存取時，才會授與存取權。 不支援的應用程式或用戶端嘗試存取時，嘗試會遭到封鎖。

![控制對支援之行動裝置應用程式或桌面用戶端的存取](./media/active-directory-conditional-access-technical-reference/06.png)

下列行動裝置應用程式和桌面用戶端支援 Office 365 和其他 Azure AD 連線服務應用程式的條件式存取︰


| 用戶端應用程式| 目標服務| 平台 |
| --- | --- | --- |
| 應用程式適用的 Azure Multi-Factor Authentication 和位置原則 (不支援裝置型原則)| 任何 My Apps 應用程式服務| Android、iOS|
| Azure RemoteApp| Azure RemoteApp 服務| Windows 10、Windows 8.1、Windows 7、iOS、Android、macOS|
| Dynamics 365 應用程式| Dynamics 365| Windows 10、Windows 8.1、Windows 7、iOS、Android|
| Microsoft Office 365 Teams (控制支援 Microsoft Teams 及其所有用戶端應用程式的所有服務：Windows 桌面、iOS、Android、Windows Phone 和 Web 用戶端)| Microsoft Teams| Windows 10、Windows 8.1、Windows 7、iOS、Android|
| 郵件/行事曆/連絡人應用程式、Outlook 2016、Outlook 2013 (已啟用新式驗證)、商務用 Skype (採用新式驗證)| Office 365 Exchange Online| Windows 10|
| Outlook 2016、Outlook 2013 (已啟用新式驗證)、商務用 Skype (採用新式驗證)| Office 365 Exchange Online| Windows 8.1、Windows 7|
| Outlook 行動應用程式| Office 365 Exchange Online| iOS|
| Outlook 2016 (macOS 版 Office)| Office 365 Exchange Online| macOS|
| Office 2016 應用程式、通用 Office 應用程式、Office 2013 (具備新式驗證)、[OneDrive](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e) 同步處理用戶端、未來規劃支援 Office Groups 和 SharePoint 應用程式| Office 365 SharePoint Online| Windows 10|
| Office 2016 應用程式、Office 2013 (具備新式驗證)、[OneDrive](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e) 同步處理用戶端| Office 365 SharePoint Online| Windows 8.1、Windows 7|
| Office 行動應用程式| Office 365 SharePoint Online| iOS、Android|
| macOS 版 Office 2016 (僅支援 Word、Excel、PowerPoint、OneNote)、未來規劃支援商務用 OneDrive| Office 365 SharePoint Online| macOS|
| Office Yammer 應用程式| Office 365 Yammer| Windows 10、iOS、Android|
| Power BI 應用程式 (目前在 Android 上不受支援)| PowerBI service| Windows 10、Windows 8.1、Windows 7 及 iOS|
| Visual Studio Team Services 應用程式| Visual Studio Team Services| Windows 10、Windows 8.1、Windows 7、iOS、Android|



## <a name="approved-client-app-requirement"></a>核准的用戶端應用程式需求 

使用條件式存取原則中的 [需要經過核准的用戶端應用程式] 選項，控制用戶端連線。 只有在經過核准的用戶端應用程式嘗試連線時，才會授與存取權。

![控制對經過核准之用戶端應用程式的存取](./media/active-directory-conditional-access-technical-reference/21.png)

下列用戶端應用程式可以搭配經過核准的用戶端應用程式需求使用：

- Microsoft Excel

- Microsoft OneDrive

- Microsoft Outlook

- Microsoft OneNote

- Microsoft PowerPoint

- Microsoft SharePoint

- Microsoft 商務用 Skype

- Microsoft Teams

- Microsoft Visio

- Microsoft Word


**備註**

- 經過核准的用戶端應用程式支援 Intune 行動應用程式管理功能。

- **需要經過核准的用戶端應用程式**需求：

    - 僅支援[裝置平台條件](#device-platforms-condition)適用的 iOS 和 Android。

    - 不支援[用戶端應用程式條件](#supported-browsers)適用的 [瀏覽器] 選項。
    
    - 選取[用戶端應用程式條件](#supported-mobile-apps-and-desktop-clients)適用的 [行動裝置應用程式和桌面應用程式] 選項時，取代該選項。


## <a name="next-steps"></a>後續步驟

- 如需條件式存取的概觀，請參閱 [Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal.md)。
- 如果您已準備好設定您環境的條件式存取原則，請參閱 [Azure Active Directory 中條件式存取的建議做法](active-directory-conditional-access-best-practices.md)。



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png


