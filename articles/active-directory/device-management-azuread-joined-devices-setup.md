---
title: "如何設定已加入 Azure Active Directory 的裝置 | Microsoft Docs"
description: "了解如何設定已加入 Azure Active Directory 的裝置。"
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
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: 303660fa53919ec22a032931aab729fd36676b82
ms.contentlocale: zh-tw
ms.lasthandoff: 08/30/2017

---
# <a name="how-to-configure-azure-active-directory-joined-devices"></a>如何設定已加入 Azure Active Directory 的裝置

使用 Azure Active Directory (Azure AD) 中的裝置管理，您可以確保使用者會從符合安全性與合規性之標準的裝置來存取您的資源。 如需詳細資訊，請參閱 [Azure Active Directory 中的裝置管理簡介](device-management-introduction.md)。

如果您想要將公司擁有的 Windows 10 裝置整合到 Azure AD 的控制下，您可以透過設定已加入 Azure AD 的裝置來完成這項作業。 本主題為您提供相關步驟。 


## <a name="prerequisites"></a>必要條件

若要加入 Windows 10 裝置，必須設定 Device Registration Service，才能註冊裝置。 除了擁有在 Azure AD 租用戶中加入裝置的權限之外，您所擁有的已註冊裝置必須少於已設定的最大值。 如需詳細資訊，請參閱[設定裝置設定](device-management-azure-portal.md#configure-device-settings)。



## <a name="what-you-should-know"></a>您應該知道的事情


- Windows 會在 Azure AD 中加入組織目錄中的裝置。

- 您可能必須經過 Multi-Factor Authentication 挑戰。 這項挑戰可由 IT 系統管理員設定。

- Azure AD 會檢查這個裝置是否需要註冊行動裝置管理，如果適用的話就加以註冊。

- 如果您是受管理的使用者，Windows 會透過自動登入將您導向桌面。

- 如果您是同盟使用者，您必須使用自己的認證登入。


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
- 




