---
title: "設定註冊 Azure Active Directory 的 Windows 10 裝置 | Microsoft Docs"
description: "了解如何設定註冊 Azure Active Directory 的 Windows 10 裝置。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: e2554450bdb426cff50f302132158f39f5630ee3
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---

# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>設定註冊 Azure Active Directory 的 Windows 10 裝置

Azure Active Directory (Azure AD) 中的裝置管理可協助您確保使用者會從符合安全性與合規性標準的裝置存取您的資源。 如需詳細資訊，請參閱 [Azure Active Directory 中的裝置管理簡介](device-management-introduction.md)。

透過[註冊 Azure AD 的裝置](device-management-introduction.md#azure-ad-registered-devices)，您可以實現**自備裝置 (BYOD)** 案例，讓使用者使用個人擁有的裝置存取組織資源。  

在 Azure AD 中，您可以設定註冊 Azure AD 的裝置：

- Windows 10
- iOS
- Android
- macOS。  

本主題提供向 Azure AD 註冊 Windows 10 裝置的指示。 


## <a name="prerequisites"></a>必要條件

在開始之前，您應該確認：

- 註冊裝置的權限 

    ![註冊](./media/device-management-azuread-registered-devices-windows10-setup/21.png)

- 尚未超過每位使用者的裝置數目上限 

    ![註冊](./media/device-management-azuread-registered-devices-windows10-setup/22.png)

如需詳細資訊，請參閱[設定裝置設定](device-management-azure-portal.md#configure-device-settings)。

## <a name="what-you-should-know"></a>您應該知道的事情

當您註冊裝置時，請注意下列事項：

- Windows 會將裝置註冊在 Azure AD 中的組織目錄。

- 您可能必須通過 Multi-Factor Authentication 挑戰。 您的 IT 管理員可以設定這項挑戰。

- Azure AD 會確認裝置是否需要註冊行動裝置管理。 它會視情況註冊裝置。

- Windows 會透過自動登入程序將受管理的使用者重新導向桌面。

- 同盟使用者會重新導向 Windows 登入頁面以便輸入認證。


## <a name="register-a-device"></a>註冊裝置

若要向 Azure AD 註冊 Windows 10 裝置，請完成下列步驟。 如果您已成功向 Azure AD 註冊裝置，您的 [存取工作或學校資源] 頁面會透過 [工作或學校帳戶] 項目來指出這一點。

![註冊](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


若要註冊您的 Windows 10 裝置：

1. 在 [開始] 功能表中，按一下 [設定]。

    ![選取設定](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. 按一下 [帳戶]。

    ![選取帳戶](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. 按一下 [存取公司或學校資源]。

    ![選取存取公司或學校資源](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. 在 [存取公司或學校資源] 頁面中，按一下 [連線]。

    ![存取公司或學校資源頁面](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. 在 [設定工作或學校帳戶] 頁面中，輸入您的帳戶名稱 (例如：someone@example.com)，然後按 [下一步]。

    ![設定工作或學校帳戶頁面](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. 在 [輸入密碼] 頁面中輸入您的密碼，然後按 [下一步]。

    ![輸入密碼](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. 在 [您已就緒] 頁面中，按一下 [完成]。

    ![您已就緒頁面](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>驗證

若要確認裝置是否已加入 Azure AD，請檢查裝置上的 [存取公司或學校資源] 頁面。

![工作或學校帳戶狀態](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

您也可以在 Azure AD 入口網站中檢閱裝置設定。

![Azure AD 註冊裝置](./media/device-management-azuread-registered-devices-windows10-setup/09.png)


## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱： 

- [Azure Active Directory 中的裝置管理簡介](device-management-introduction.md)

- [使用 Azure 入口網站管理裝置](device-management-azure-portal.md)





