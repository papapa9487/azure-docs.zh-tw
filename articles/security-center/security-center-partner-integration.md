---
title: "Azure 資訊安全中心中的夥伴整合 | Microsoft Docs"
description: "了解 Azure 資訊安全中心如何與夥伴整合，以提高您 Azure 資源的整體安全性。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 44beafeff5cbe58ac8ca37632879f6ffc2b67e53
ms.contentlocale: zh-tw
ms.lasthandoff: 08/19/2017

---
# <a name="partner-integration-in-azure-security-center"></a>Azure 資訊安全中心中的夥伴整合

在本文中，我們將說明 Azure 資訊安全中心如何與夥伴整合，以協助您提高整體安全性。 資訊安全中心在 Azure 中提供整合體驗，並利用 Azure Marketplace 進行夥伴認證和計費。

> [!NOTE] 
> 從 2017 年 6 月開始，資訊安全中心會使用 Microsoft Monitoring Agent 來收集和儲存資料。 如需詳細資訊，請參閱 [Azure 資訊安全中心平台移轉](security-center-platform-migration.md)。 本文中的資訊說明轉換至 Microsoft Monitoring Agent 後的資訊安全中心功能。
>

## <a name="why-deploy-partner-solutions-from-security-center"></a>為什麼要從資訊安全中心部署夥伴解決方案

運用資訊安全中心中的夥伴整合有四個主要原因：

- **部署方式簡單**。 依照資訊安全中心的建議來部署夥伴解決方案更加容易。 使用預設的安裝和網路拓撲，可以全面自動化部署程序。 或者，客戶可以選擇半自動的選項以取得更多彈性和自訂。
- **整合偵測**。 來自夥伴解決方案的安全性事件會自動收集、彙總以及顯示為資訊安全中心警示和事件的一部分。 這些事件也會與來自其他來源的偵測整合，以提供進階的威脅偵測功能。
- **統一的健康情況監視與管理**。 客戶可使用整合式的健全狀況事件，一眼監視所有夥伴解決方案。 提供基本管理功能，而且可以讓您輕鬆使用夥伴解決方案存取進階設定。
- **匯出至 SIEM**。 客戶現在可以使用 Azure log integration (預覽)，以 Common Event Format (CEF) 格式將所有資訊安全中心與夥伴的警示匯出至內部部署 Security Information and Event Management (SIEM) 系統中。


## <a name="partners-that-integrate-with-security-center"></a>與資訊安全中心整合的夥伴

資訊安全中心目前與下列這些解決方案整合︰

- Endpoint protection ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html)、Symantec 以及 [適用於 Azure 雲端服務和虛擬機器的 Microsoft 反惡意程式碼軟體](https://docs.microsoft.com/azure/security/azure-security-antimalware)) 
- Web 應用程式防火牆 ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall)、[F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html)、[Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF)、[Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets)，以及 [Azure 應用程式閘道](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)) 
- 新一代防火牆 ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/)、[Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/)、[Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) 和 [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)) 
- 弱點評估 ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

資訊安全中心會隨時間擴展這些類別內夥伴的數目，並加入新的類別。 

## <a name="deploy-a-partner-solution"></a>部署夥伴解決方案

根據您的 Azure 環境設定和您定義的安全性原則，資訊安全中心可能會建議您部署夥伴解決方案。 此資訊安全中心建議將引導您完成選取和安裝夥伴解決方案的程序。 整體部署體驗可能會根據您使用的解決方案類型和夥伴而有所不同。 如需詳細資訊，請參閱下列文章。

- [安裝端點保護](security-center-install-endpoint-protection.md)
- [新增 Web 應用程式防火牆](security-center-add-web-application-firewall.md)
- [新增新一代防火牆](security-center-add-next-generation-firewall.md)
- [未安裝弱點評估](security-center-vulnerability-assessment-recommendations.md)

## <a name="manage-partner-solutions"></a>管理夥伴解決方案

在部署後，若要檢視解決方案的健康情況相關資訊並執行基本管理工作，請在 [資訊安全中心] 刀鋒視窗中，選取 [夥伴解決方案] 選項。 如需管理資訊安全中心中合作夥伴解決方案的相關資訊，請參閱[透過 Azure 資訊安全中心監視夥伴解決方案](security-center-partner-solutions.md)。

![夥伴整合](./media/security-center-partner-integration/security-center-partner-integration-fig1-new2.png)

> [!NOTE]
> Symantec Endpoint Protection 支援僅限於探索。 無法使用健康情況警示。
>

## <a name="see-also"></a>另請參閱

在本文中，您已了解如何在 Azure 資訊安全中心中整合夥伴解決方案。 如要深入了解資訊安全中心，請參閱下列文章：

* [資訊安全中心規劃和操作指南](security-center-planning-and-operations-guide.md)
* [在資訊安全中心管理和回應安全性警示](security-center-managing-and-responding-alerts.md)
* [資訊安全中心不同類型的安全性警示](security-center-alerts-type.md)
* [資訊安全中心的安全性健康情況監視](security-center-monitoring.md)。 了解如何監視 Azure 資源的健全狀況。
* [使用資訊安全中心監視夥伴解決方案](security-center-partner-solutions.md)。 了解如何監視合作夥伴解決方案的健全狀態。
* [Azure 資訊安全中心常見問題](security-center-faq.md)。 取得有關使用服務常見問題的答案。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)。 尋找有關 Azure 安全性與相容性的部落格文章。

