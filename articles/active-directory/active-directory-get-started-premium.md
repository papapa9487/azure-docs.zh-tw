---
title: "註冊 Azure Active Directory Premium"
description: "說明如何註冊 Azure Active Directory Premium 版本。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
ms.assetid: 391764e5-c4eb-4ae6-b8a9-3d09f0de04a6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/09/2017
ms.author: curtand
ms.custom: it-pro;
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: fe9f4fa6f776a0c2edb572d0526c7520d1c1c7a0
ms.contentlocale: zh-tw
ms.lasthandoff: 08/10/2017

---
# <a name="quickstart-sign-up-for-azure-active-directory-premium"></a>快速入門：註冊 Azure Active Directory Premium
若要開始使用 Azure Active Directory (Azure AD) Premium，您需要購買授權並將其與您的 Azure 訂用帳戶建立關聯。 如果您建立新的 Azure 訂用帳戶，您也需要啟用您的授權方案及 Azure AD 服務存取，如下列各節所述。 

## <a name="sign-up-for-active-directory-premium"></a>註冊 Active Directory Premium
若要註冊 Active Directory Premium，您有數個選項︰ 

### <a name="azure-or-office-365"></a>Azure 或 Office 365 
身為 Azure 或 Office 365 訂閱者，您可以線上購買 Azure Active Directory Premium。 

如需詳細步驟，請參閱[如何購買 Azure Active Directory Premium - 現有客戶](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer)或[如何購買 Azure Active Directory Premium - 新客戶](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers)。  

### <a name="enterprise-mobility--security"></a>Enterprise Mobility + Security
Enterprise Mobility + Security (EMS) 是一個符合成本效益的方式，可供組織依據單一授權方案來搭配使用下列服務：Azure Active Directory Premium、Azure Information Protection 和 Microsoft Intune。 您可以在 [Enterprise Mobility + Security 網路](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)深入了解 EMS，以及在 [Enterprise Mobility + Security 價格選項](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-pricing)頁面深入了解可以購買的 EMS 授權類型。  

您可以利用下列其中一個授權選項，透過 EMS 授權開始使用 Azure AD：

- 透過免費的 [Enterprise Mobility + Security E5 試用訂用帳戶](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1)試用 EMS
- 購買 [Enterprise Mobility + Security E5 授權](https://signup.microsoft.com/Signup?OfferId=e6de2192-536a-4dc3-afdc-9e2602b6c790&ali=1)
- 購買 [Enterprise Mobility + Security E3 授權](https://signup.microsoft.com/Signup?OfferId=4BBA281F-95E8-4136-8B0F-037D6062F54C&ali=1)

### <a name="microsoft-volume-licensing"></a>Microsoft 大量授權
Azure Active Directory Premium 可透過 [Microsoft Enterprise 合約](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) (250 個以上的授權) 或 [Open 大量授權](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) (5-250 個授權) 方案來取得。

您可以在[如何透過大量授權購買](https://www.microsoft.com/licensing/how-to-buy/how-to-buy.aspx)頁面深入了解大量授權購買選項。

> [!NOTE]
> Azure Active Directory Premium 和 Basic 版本適用於使用全球 Azure Active Directory 執行個體的中國客戶。 由 21Vianet 在中國提供的 Microsoft Azure 服務目前不支援 Azure Active Directory Premium 和 Basic 版本。 如需詳細資訊，請透過 [Azure Active Directory 論壇](https://feedback.azure.com/forums/169401-azure-active-directory/)與我們連絡。
> 
> 

如果您先前已針對在前述步驟中使用的相同 Azure 訂用帳戶購買並啟用 Azure AD 授權，這些授權就會自動在相同的目錄中啟用。 您不需要繼續進行本文其餘部分所述的步驟。

## <a name="activate-your-license-plan"></a>啟用您的授權方案
這是您向 Microsoft 購買的第一個 Azure AD 授權方案嗎？ 若是如此，系統會在購買完成時產生確認電子郵件並傳送給您。 您必須透過這封電子郵件才能啟用您的第一個授權方案。

**若要啟用授權方案，請執行下列其中一個步驟︰**

1. 若要開始啟用，請按一下 [登入] 或 [註冊]。
   
    ![Sign in][1]

    - 如果您有現有的租用戶，請按一下 [登入]  以使用現有的系統管理員帳戶登入。 使用目錄的全域管理員認證進行登入，且目錄必須已啟用授權。

    - 如果您想要建立新的 Azure AD 租用戶來與您的授權方案搭配使用，請按一下 [註冊] 以開啟 [建立帳戶設定檔] 對話方塊。

        ![建立帳戶設定檔][2]

完成時將會顯示下列對話方塊，以確認您的租用戶的授權方案已啟用︰

![確認][3]

## <a name="activate-your-azure-active-directory-access"></a>啟用 Azure Active Directory 存取權
如果您要將新的 Azure AD Premium 授權新增到現有的訂用帳戶中，應已啟用您的 Azure AD 存取權。 否則，您必須在收到**歡迎電子郵件**之後啟用 Azure AD 存取權。  

當您購買的授權佈建到您的目錄時，您會收到 **歡迎電子郵件**。 此電子郵件會確認您可以開始管理 Azure Active Directory Premium 或 Enterprise Mobility + Security 的授權和功能。 

> [!TIP]
> 直到您使用授權佈建程序完成時自動傳送的歡迎電子郵件來啟用 Azure AD 目錄存取權時，您才可存取新租用戶的 Azure AD。 

**若要啟用 Azure AD 存取權，請執行下列步驟：**

1. 在**歡迎電子郵件**中按一下 [登入]。 
   
    ![歡迎電子郵件][4]
2. 登入成功時，您還需要使用行動裝置完成第二因素驗證︰
   
    ![行動裝置驗證][5]

啟用應該只需幾分鐘的時間，您管理您的 Azure AD 存取權。 

## <a name="next-steps"></a>後續步驟
在本快速入門中，您學到如何註冊 Azure AD Premium 並啟用您的 Azure Active Directory 存取權。 

如果您已經有 Azure 訂用帳戶，可以使用下列連結來啟動試用版或從 Azure 入口網站購買 Azure AD Premium 授權。

> [!div class="nextstepaction"]
> [啟用 Azure AD Premium 授權](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/TryBuyProductBlade) 

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png
