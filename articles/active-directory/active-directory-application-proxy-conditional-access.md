---
title: "條件式存取內部部署應用程式 - Azure AD | Microsoft Docs"
description: "說明如何針對您使用 Azure AD 應用程式 Proxy 發佈可供遠端存取的應用程式設定條件式存取。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 2e97722b-eb4e-4078-b607-9fed210d8a0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.openlocfilehash: 463946256f9e335fa6d98fc904835e5c3dc2725e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="working-with-conditional-access-in-azure-ad-application-proxy"></a>使用 Azure AD 應用程式 Proxy 中的條件式存取

>[!NOTE]
>本文適用於 Azure 傳統入口網站 (即將淘汰)。 建議您使用 [Azure 入口網站](https://portal.azure.com)。 在 Azure 入口網站中，應用程式 Proxy 應用程式具有與任何其他 SaaS 應用程式相同的條件式式存取功能。 若要深入了解條件式存取，請參閱[開始使用 Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal-get-started.md)。

對於使用應用程式 Proxy 發佈的應用程式，您可以設定存取規則，以授與對這些應用程式的條件式存取。 這可讓您：

* 要求各應用程式的多重要素驗證
* 只在使用者不在公司時要求多重要素驗證
* 在使用者不在公司時封鎖使用者存取應用程式

這些規則可以套用至所有使用者和群組，或只套用至特定使用者和群組。 根據預設，規則會套用至所有可存取應用程式的使用者。 不過，也可以將規則限制為指定之安全性群組的使用者成員。  

當使用者存取使用 OAuth 2.0、OpenID Connect、SAML 或 WS-同盟的同盟應用程式時，就會評估存取規則。 此外，當使用重新整理權杖來取得存取權杖時，會以 OAuth 2.0 和 OpenID Connect 評估存取規則。

## <a name="conditional-access-prerequisites"></a>條件式存取的先決條件
* Azure Active Directory Premium 的訂用帳戶
* 同盟或受管理的 Azure Active Directory 租用戶
* 同盟租用戶需要 Multi-Factor Authentication (MFA)  
    ![設定存取規則 - 要求 Multi-Factor Authentication](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## <a name="configure-per-application-multi-factor-authentication"></a>設定每個應用程式的 Multi-Factor Authentication
1. 在 Azure 傳統入口網站中，以系統管理員身分登入。
2. 移至 Active Directory，並選取您要啟用應用程式 Proxy 所在的目錄。
3. 按一下 [應用程式] 並向下捲動至 [存取規則] 區段。 只有使用應用程式 Proxy 發佈並使用同盟驗證的應用程式，才會顯示 [存取規則] 區段。
4. 為 [啟用存取規則] 選取 [開啟] 來啟用規則。
5. 指定要套用規則的使用者和群組。 使用 [新增群組] 按鈕來選取一或多個要套用存取規則的群組。 此對話方塊也可用來移除選取的群組。  選取要套用至群組的規則之後，只會對屬於其中一個指定安全性群組的使用者強制執行存取規則。  

   * 若要明確地將安全性群組從規則中排除，請選取 [例外]  並指定一或多個群組。 如果使用者是 [除外] 清單中某個群組的成員，則不需要執行多重要素驗證。  
   * 如果已使用每個使用者的多重要素驗證功能來設定使用者，則此設定會優先於應用程式的多重要素驗證規則。 如果使用者已設定為使用每個使用者的多重要素驗證，則必須執行多重要素驗證，即使已從應用程式的多重要素驗證規則中免除他們也一樣。 深入了解 [多重要素驗證和每個使用者設定](../multi-factor-authentication/multi-factor-authentication.md)。
6. 選取您要設定的存取規則：

   * **需要多重要素驗證**︰套用存取規則的使用者必須先完成多重要素驗證，才能存取套用規則的應用程式。
   * **不在工作時需要多重要素驗證**︰嘗試從受信任的 IP 位址存取應用程式的使用者不需要執行多重要素驗證。 可以在 [Multi-Factor Authentication 設定] 頁面上設定受信任的 IP 位址範圍。
   * **不在工作時封鎖存取**︰嘗試從公司網路外部存取應用程式的使用者將無法存取應用程式。

## <a name="configuring-mfa-for-federation-services"></a>設定同盟服務的 MFA
對於同盟的租用戶，Multi-Factor Authentication (MFA) 可能由 Azure Active Directory 或內部部署 AD FS 伺服器執行。 根據預設，MFA 會發生在 Azure Active Directory 所裝載的任何頁面上。 若要設定內部部署 MFA，請執行 Windows PowerShell 並使用 –SupportsMFA 屬性來設定 Azure AD 模組。

下列範例示範如何在 consoso.com 租用戶上使用 [Set-MsolDomainFederationSettings Cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx) 來啟用內部部署 MFA︰`Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `

除了設定這個旗標，同盟租用戶 AD FS 執行個體必須設為執行多重要素驗證。 請遵循 [內部部署 Microsoft Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-server.md)的指示。

## <a name="see-also"></a>另請參閱
* [使用宣告感知應用程式](active-directory-application-proxy-claims-aware-apps.md)
* [使用應用程式 Proxy 發行應用程式](active-directory-application-proxy-publish.md)
* [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
* [使用您自己的網域名稱發行應用程式](active-directory-application-proxy-custom-domains.md)

如需最新消息，請查閱 [應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
