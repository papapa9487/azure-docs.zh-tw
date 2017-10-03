---
title: "在 Azure AD 中設定 LinkedIn 整合 | Microsoft Docs"
description: "說明如何在 Azure Active Directory 中為 Microsoft 應用程式啟用或停用 LinkedIn 整合。"
services: active-directory
author: jeffgilb
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: a29f1e7b39b7f35073aa5aa6c6bd964ffaa6ffd0
ms.openlocfilehash: 48b26bfcce67ce915c404a0ab2ac0f399c3b821d
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="enabling-linkedin-integration-in-azure-active-directory"></a>在 Azure Active Directory 中啟用 LinkedIn 整合
啟用 LinkedIn 整合可讓使用者存取公用的 LinkedIn 資料，而且如果他們願意的話，也可以存取其在 Microsoft 應用程式內的個人 LinkedIn 網路。 每個使用者都可以獨立地選擇將其工作帳戶連結到其 LinkedIn 帳戶。

### <a name="linkedin-integration-from-your-end-users-perspective"></a>使用者觀點的 LinkedIn 整合
當組織中的使用者將其 LinkedIn 帳戶連結至其工作帳戶時，他們就能夠更精確地識別他們在組織內外所合作的人員。 將這兩個帳戶連結可讓使用者的 LinkedIn 連線和設定檔資料用於組織的 Microsoft 應用程式中，但使用者隨時都能藉由移除讓 LinkedIn 共用該資料的權限來選擇退出。 整合也會使用公開的設定檔資訊，而且使用者可以選擇是否要透過 LinkedIn 隱私權設定來與 Microsoft 應用程式共用其公開的設定檔和網路資訊。

>[!NOTE]
> 在 Azure AD 中啟用 LinkedIn 整合可讓應用程式和服務存取使用者的某些資訊。 若要深入了解在 Azure AD 中啟用 LinkedIn 整合時的隱私權考量，請閱讀 [Microsoft 隱私權聲明](https://privacy.microsoft.com/privacystatement/)。 

## <a name="enable-linkedin-integration"></a>啟用 LinkedIn 整合
Azure AD 中預設會啟用企業版的 LinkedIn 整合。 因此，當您對租用戶提供這項功能時，組織中的所有使用者都會看到 LinkedIn 的功能和設定檔。 啟用 LinkedIn 整合可讓組織中的使用者在 Microsoft 服務內使用 LinkedIn 功能，例如在 Outlook 中接收電子郵件時檢視設定檔。 停用此功能會讓使用者無法存取 LinkedIn 功能，並停止透過 Microsoft 服務在 LinkedIn 與組織之間共用使用者帳戶連線和資料。

> [!IMPORTANT]
> go-local、sovereign 和 government 租用戶無法使用這項功能。 此外，Azure AD 服務更新 (例如 LinkedIn 整合功能) 也不會同時部署至所有 Azure 租用戶。 直到您的 Azure 租用戶推出這項功能後，您才能將 LinkedIn 整合到 Azure AD。

1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure Active Directory 管理中心](https://aad.portal.azure.com/)。
2. 選取 [使用者和群組]。
3. 在 [使用者和群組] 刀鋒視窗上，選取 [使用者設定]。
4. 在 [LinkedIn 整合] 底下，選取 [是] 來啟用 LinkedIn 整合，或選取 [否] 來予以停用。
   ![啟用 LinkedIn 整合](./media/linkedin-integration/LinkedIn-integration.PNG)

### <a name="learn-more"></a>詳細資訊 
* [Microsoft 應用程式中的 LinkedIn 資訊和功能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn 說明中心](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>後續步驟
您可以使用下列連結，從 Azure 入口網站來啟用或停用 LinkedIn 與 Azure AD 的整合作業：

[設定 LinkedIn 整合](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 
