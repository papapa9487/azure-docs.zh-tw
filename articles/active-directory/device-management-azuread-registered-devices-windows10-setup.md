---
title: "如何設定 Azure Active Directory 已註冊的裝置 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 已註冊的裝置。"
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
ms.date: 08/27/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: d73062a258f05a630692670363cabf0080aba1ab
ms.contentlocale: zh-tw
ms.lasthandoff: 08/30/2017

---
# <a name="how-to-configure-azure-active-directory-registered-windows-10-devices"></a>如何設定 Azure Active Directory 已註冊的 Windows 10 裝置

使用 Azure Active Directory (Azure AD) 中的裝置管理，您可以確保使用者會從符合安全性與合規性之標準的裝置來存取您的資源。 如需詳細資訊，請參閱 [Azure Active Directory 中的裝置管理簡介](device-management-introduction.md)。

如果您需要啟用**自備裝置 (BYOD)** 情節，可以設定 Azure AD 註冊的裝置來完成這項作業。 在 Azure AD 中，您可以為 Windows 10、iOS、Android 和 macOS 設定 Azure AD 註冊裝置。 本主題為您提供 Windows 10 裝置的相關步驟。 


## <a name="before-you-begin"></a>開始之前

若要註冊 Windows 10 裝置，必須設定裝置註冊服務，才能註冊裝置。 除了擁有在 Azure AD 租用戶中註冊裝置的權限之外，您所擁有的已註冊裝置必須少於已設定的最大值。 如需詳細資訊，請參閱[設定裝置設定](device-management-azure-portal.md#configure-device-settings)。

## <a name="what-you-should-know"></a>您應該知道的事情

註冊裝置時，請記住下列要點：

- Windows 會在 Azure AD 中註冊組織目錄中的裝置

- 您可能必須經過多重要素驗證挑戰。 這項挑戰可由 IT 管理員設定。

- Azure AD 會檢查這個裝置是否需要註冊行動裝置管理，如果適用的話就加以註冊。

- 如果您是受管理的使用者，Windows 會透過自動登入將您導向桌面。

- 如果您是同盟使用者，便會將您導向 Windows 登入畫面，以輸入您的認證。


## <a name="registering-a-device"></a>註冊裝置

本節說明將 Windows 10 裝置註冊到 Azure AD 的步驟。 如果您已成功將裝置註冊到 Azure AD，您的 [存取工作或學校] 對話方塊會透過 [工作或學校帳戶] 項目來指出這一點。

![註冊](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


**若要註冊您的 Windows 10 裝置：**

1. 在 [開始] 功能表中，按一下 [設定]。

    ![設定](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. 按一下 [帳戶]。

    ![帳戶](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. 按一下 [存取工作或學校]。

    ![存取公司或學校](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. 在 [存取工作或學校] 對話方塊中，按一下 [連線]。

    ![連線](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. 在 [設定工作或學校帳戶] 對話方塊中，輸入您的帳戶名稱 (例如：someone@example.com)，然後按 [下一步]。

    ![連線](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. 在 [輸入密碼] 對話方塊中，輸入您的密碼，然後按 [下一步]。

    ![連線](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. 在 [一切準備就緒] 對話方塊中，按一下 [完成]。

    ![連線](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>驗證

若要確認裝置是否已加入 Azure AD，您可以在裝置上檢閱 [存取工作或學校] 對話方塊。

![註冊](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

或者，您也可以在 Azure AD 入口網站上檢閱裝置設定。

![註冊](./media/device-management-azuread-registered-devices-windows10-setup/09.png)





## <a name="next-steps"></a>後續步驟

- 如需詳細資訊，請參閱 [Azure Active Directory 中的裝置管理簡介](device-management-introduction.md)

- 如需有關在 Azure AD 入口網站中管理裝置的詳細資訊，請參閱[使用 Azure 入口網站來管理裝置](device-management-azure-portal.md)。





