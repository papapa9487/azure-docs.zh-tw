---
title: "設定 Azure Active Directory 已加入裝置 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 已加入裝置。"
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
ms.date: 08/29/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: f548833cca27debb67cb155be0791299470f28dd
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---
# <a name="set-up-azure-active-directory-joined-devices"></a>設定 Azure Active Directory 已加入裝置

Azure Active Directory (Azure AD) 中的裝置管理可協助您確保使用者會從符合安全性與合規性標準的裝置存取您的資源。 如需詳細資訊，請參閱 [Azure Active Directory 中的裝置管理簡介](device-management-introduction.md)。

透過 [Azure AD 已加入裝置](device-management-introduction.md#azure-ad-joined-devices)，您可以讓組織擁有的 Windows 10 裝置交由 Azure AD 控管。 這些裝置不應該已[加入內部部署 AD](device-management-introduction.md#hybrid-azure-ad-joined-devices)。

本主題提供向 Azure AD 註冊 Windows 10 裝置的指示。 

## <a name="prerequisites"></a>必要條件

在開始之前，您應該確認：

- 您擁有將裝置加入 Azure AD 的權限。

    ![連線](./media/device-management-azuread-joined-devices-setup/21.png)

- 您尚未超過每位使用者的裝置數目上限 

    ![連線](./media/device-management-azuread-joined-devices-setup/22.png)


如需詳細資訊，請參閱[設定裝置設定](device-management-azure-portal.md#configure-device-settings)。



## <a name="what-you-should-know"></a>您應該知道的事情


- Windows 會將裝置註冊在 Azure AD 中的組織目錄。

- 您可能必須通過 Multi-Factor Authentication 挑戰。 您的 IT 管理員可以設定這項挑戰。

- Azure AD 會確認裝置是否需要註冊行動裝置管理。 它會視情況註冊裝置。

- Windows 會透過自動登入程序將受管理的使用者重新導向桌面。

- 同盟使用者會重新導向 Windows 登入頁面以便輸入認證。


## <a name="joining-a-device"></a>加入裝置

本節說明將 Windows 10 裝置加入 Azure AD 的步驟。 如果您已成功將裝置加入 Azure AD，您的 [存取公司或學校資源] 對話方塊會透過 [已連線至 \<您的 Azure AD\>] 項目來指出這一點。

![連線](./media/device-management-azuread-joined-devices-setup/13.png)


**若要加入您的 Windows 10 裝置：**

1. 在 [開始] 功能表中，按一下 [設定]。

    ![設定](./media/device-management-azuread-joined-devices-setup/01.png)

2. 按一下 [帳戶]。

    ![帳戶](./media/device-management-azuread-joined-devices-setup/02.png)


3. 按一下 [存取公司或學校資源]。

    ![存取公司或學校資源](./media/device-management-azuread-joined-devices-setup/03.png)

4. 在 [存取公司或學校資源] 對話方塊中，按一下 [連線]。

    ![連線](./media/device-management-azuread-joined-devices-setup/04.png)


5. 在 [設定公司或學校帳戶] 對話方塊中，按一下 [將此裝置加入 Azure Active Directory]。

    ![連線](./media/device-management-azuread-joined-devices-setup/08.png)


6. 在 [Let's get you signed in] \(讓我們協助您登入帳戶) 對話方塊中，輸入您的帳戶名稱 (例如 someone@example.com)，然後按一下 [下一步]。

    ![讓我們協助您登入帳戶](./media/device-management-azuread-joined-devices-setup/10.png)


6. 在 [輸入密碼] 對話方塊中，輸入您的密碼，然後按一下 [登入]。

    ![輸入密碼](./media/device-management-azuread-joined-devices-setup/05.png)


7. 在 [確定這是您的組織] 對話方塊中，按一下 [加入]。

    ![確定這是您的組織](./media/device-management-azuread-joined-devices-setup/11.png)


8. 在 [You're all set] \(您已就緒) 對話方塊中，按一下 [完成]。

    ![您已就緒](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>驗證

若要確認裝置是否已加入 Azure AD，您可以在裝置上檢閱 [存取公司或學校資源] 對話方塊。

![連線](./media/device-management-azuread-joined-devices-setup/13.png)

或者，您也可以執行下列命令：`dsregcmd /status`  
在已成功加入的裝置上，**AzureAdJoined** 為 **Yes**。

![連線](./media/device-management-azuread-joined-devices-setup/14.png)

您也可以在 Azure AD 入口網站上檢閱裝置設定。

![連線](./media/device-management-azuread-joined-devices-setup/15.png)

如需詳細資訊，請參閱[尋找裝置](device-management-azure-portal.md#locate-devices)。


## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱： 

- [Azure Active Directory 中的裝置管理簡介](device-management-introduction.md)

- [使用 Azure 入口網站管理裝置](device-management-azure-portal.md)





