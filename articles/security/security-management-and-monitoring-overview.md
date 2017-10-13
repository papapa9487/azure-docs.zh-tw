---
title: "Azure 安全性管理和監視概觀 | Microsoft Docs"
description: " Azure 提供安全性機制，來協助管理與監視 Azure 雲端服務和虛擬機器。  本文概述這些核心安全性功能和服務。 "
services: security
documentationcenter: na
author: TerryLanfear
manager: StevenPo
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: terrylan
ms.openlocfilehash: 6787877deabafd0b7308e190cb45b4036049b05b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-management-and-monitoring-overview"></a>Azure 安全性管理和監視概觀
Azure 提供安全性機制，來協助管理與監視 Azure 雲端服務和虛擬機器。 本文概述這些核心安全性功能和服務。 所提供的文章連結將提供每個項目的詳細資料，以讓您深入了解。

Microsoft 雲端服務的安全性是您與 Microsoft 之間的合作關係和共同責任。 共同責任表示 Microsoft 負責 Microsoft Azure 和其資料中心的實體安全性 (藉由使用鎖定門禁卡、圍牆和防護這類安全性保護)。 此外，Azure 還提供軟體層的強式雲端安全性層級，其符合其需求客戶的安全性、隱私權和法務遵循需求。

您擁有您的資料和身分識別、保護它們的責任、您內部部署資源的安全性，以及您可控制之雲端元件的安全性。 Microsoft 提供安全性控制和功能，協助您保護資料和應用程式。 您安全性責任的高低取決於雲端服務的類型。

下表摘要說明 Microsoft 與客戶責任的平衡。

![共同責任][1]

若要深入了解安全性管理，請參閱 [Azure 的安全性管理](azure-security-management.md)。

以下是本文所涵蓋的核心功能：

* 角色型存取控制
* 反惡意程式碼
* Multi-Factor Authentication
* ExpressRoute
* 虛擬網路閘道
* Privileged Identity Management
* 身分識別保護
* 資訊安全中心

## <a name="role-based-access-control"></a>角色型存取控制
角色型存取控制 (RBAC) 提供 Azure 資源的更細緻存取權管理。 使用 RBAC，您可以僅授與使用者執行其作業所需的存取權。  RBAC 也可以協助您確保當使用者離開組織時，他們就無法存取雲端中的資源。

深入了解：

* [有關 RBAC 的 Active Directory 小組部落格](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
* [Azure 角色型存取控制](../active-directory/role-based-access-control-configure.md)

## <a name="antimalware"></a>反惡意程式碼
運用 Azure，您可以使用來自各大安全性廠商 (例如 Microsoft、Symantec、Trend Micro、McAfee 和 Kaspersky) 的反惡意程式碼軟體，以協助保護您的虛擬機器抵禦惡意檔案、廣告軟體和其他威脅。

Microsoft Antimalware 可讓您安裝 PaaS 角色和虛擬機器的反惡意程式碼代理程式。 根據 System Center Endpoint Protection，這項功能會將經證實的內部部署安全性技術帶入雲端。

我們也提供 Azure 平台中 Trend 之 [Deep Security](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/)™ 和 [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/)™ 產品的深入整合。 DeepSecurity 是一種防毒解決方案，SecureCloud 則是一種加密解決方案。 DeepSecurity 會使用擴充功能模型部署在 VM 內。 使用入口網站 UI 和 PowerShell，您可以選擇在所啟動的 VM 內或已部署的現有 VM 內使用 DeepSecurity。

Azure 也支援 Symantec End Point Protection (SEP)。 透過入口網站整合，客戶可以指定他們想要在 VM 內使用 SEP。 SEP 可以透過 Azure 入口網站安裝在全新的 VM 上，也可以使用 PowerShell 安裝在現有 VM 上。

深入了解：

* [在 Azure 虛擬機器上部署反惡意程式碼解決方案](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [適用於 Azure 雲端服務和虛擬機器的 Microsoft Antimalware](azure-security-antimalware.md)
* [如何在 Windows VM 上安裝和設定 Trend Micro Deep Security as a Service](../virtual-machines/windows/classic/install-trend.md)
* [如何在 Windows VM 上安裝和設定 Symantec Endpoint Protection](../virtual-machines/windows/classic/install-symantec.md)
* [保護 Azure 虛擬機器的新反惡意程式碼選項 - McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Azure Multi-Factor Authentication (MFA) 是需要使用多種驗證方法，並在使用者登入和交易中新增重要的第二層安全性的驗證方法。 MFA 有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。 它可以透過一些驗證選項 (例如電話、文字訊息，或行動應用程式通知或驗證代碼，以及第三方 OATH 權杖) 來提供強大的驗證功能。

深入了解：

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [什麼是 Azure Multi-Factor Authentication？](../multi-factor-authentication/multi-factor-authentication.md)
* [Azure Multi-Factor Authentication 的作用](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="expressroute"></a>ExpressRoute
Microsoft Azure ExpressRoute 可讓您透過連線提供者所提供的專用私人連線，將內部部署網路擴充至 Microsoft 雲端。 透過 ExpressRoute，您可以建立 Microsoft 雲端服務的連線，例如 Microsoft Azure、Office 365 和 CRM Online。 從任意點對任意點 (IP VPN) 網路、點對點乙太網路，或在共置設施上透過連線提供者的虛擬交叉連接，都可以進行連線。 ExpressRoute 連線不會經過公用網際網路。 相較於一般網際網路連線，這可讓 ExpressRoute 連線提供更可靠、更快速、延遲更短和更安全的連線。

深入了解：

* [ExpressRoute 技術概觀](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>虛擬網路閘道
VPN 閘道 (也稱為 Azure 虛擬網路閘道) 可用來傳送虛擬網路與內部部署位置之間的網路流量。 它們也用來傳送 Azure 內多個虛擬網路之間的流量 (VNet 對 VNet)。  VPN 閘道提供 Azure 與基礎結構之間的跨單位安全連線。

深入了解：

* [關於 VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Azure 網路安全性概觀](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
使用者有時候需要在 Azure 資源或其他 SaaS 應用程式中執行特殊權限的作業。 這通常表示組織必須授與他們永久的 Azure Active Directory (Azure AD) 特殊存取權限。 這會提高雲端資源的安全性風險，因為組織無法滴水不漏地監視這些使用者利用其特殊存取權限的所作所為。
此外，如果擁有特殊存取權限的使用者帳戶遭到入侵，這個缺口可能會影響您的整體雲端安全性。 Azure AD Privileged Identity Management 有助於解決這項風險，方法是降低權限的曝光時間，並增加使用情形的可見性。  

Privileged Identity Management 引入暫時管理員的概念來進行角色或「及時」系統管理員存取，這是需要完成指派角色啟用程序的使用者。 啟用程序會在指定的時段內，將 Azure AD 中角色的使用者指派從非作用中變更為作用中，如 8 小時。

深入了解：

* [Azure AD 特殊權限身分識別管理](../active-directory/active-directory-privileged-identity-management-configure.md)
* [開始使用 Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>身分識別保護
Azure Active Directory (AD) Identity Protection 提供可疑登入活動和潛在弱點的整合檢視，協助保護您的業務。 Identity Protection 會依據跡象偵測使用者和特權 (系統管理員) 身分識別的可疑活動，例如暴力密碼破解攻擊、認證外洩，以及從不明位置或受感染裝置的登入。

藉由提供通知和建議的補救，Identity Protection 有助於即時降低風險。 它會計算使用者風險嚴重性，而且您可以設定風險原則，以自動協助保護應用程式存取免於未來威脅。

深入了解：

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [第 9 頻道：Azure AD 和身分識別展示：Identity Protection 預覽](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>資訊安全中心
Azure 資訊安全中心可協助您預防、偵測和回應威脅，並加強提供對 Azure 資源安全性的能見度及控制權。 它提供您 Azure 訂用帳戶之間的整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

資訊安全中心藉由下列方式來協助您最佳化和監視 Azure 資源安全性︰

* 可讓您根據公司安全性需求，以及每個訂用帳戶中的應用程式類型或資料敏感性，為您的 Azure 訂用帳戶資源定義原則。
* 監視 Azure 虛擬機器、網路和應用程式的狀態。
* 提供包括來自整合式合作夥伴解決方案的優先安全性警示清單，以及需要您快速調查的資訊，和如何修復攻擊的建議。

深入了解：

* [Azure 資訊安全中心簡介](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
