---
title: "Azure 資訊安全中心中的夥伴與解決方案整合 | Microsoft Docs"
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
ms.date: 09/12/2017
ms.author: yurid
ms.openlocfilehash: a6998c997840f1a9f349b85a4274908b611cd315
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="partner-and-solutions-integration-in-azure-security-center"></a>Azure 資訊安全中心中的夥伴與解決方案整合

在本文中，我們將說明 Azure 資訊安全中心如何與夥伴整合，以協助您提高整體安全性。 資訊安全中心在 Azure 中提供整合體驗，並利用 Azure Marketplace 進行夥伴認證和計費。

## <a name="deploy-partner-solutions-from-security-center"></a>從資訊安全中心部署夥伴解決方案

使用資訊安全中心的夥伴整合有四個主要原因：

- **部署方式簡單**。 依照資訊安全中心的建議來部署夥伴解決方案更加容易。 使用預設的安裝和網路拓撲，可以全面自動化部署程序。 或者，客戶可以選擇半自動的選項以取得更多彈性和自訂。
- **整合偵測**。 來自夥伴解決方案的安全性事件會自動收集、彙總以及顯示為資訊安全中心警示和事件的一部分。 這些事件也會與來自其他來源的偵測整合，以提供進階的威脅偵測功能。
- **統一的健康情況監視與管理**。 客戶可使用整合式的健全狀況事件，一眼監視所有夥伴解決方案。 提供基本管理功能，而且可以讓您輕鬆使用夥伴解決方案存取進階設定。
- **匯出至 SIEM**。 客戶現在可以使用 Azure log integration (預覽)，以 Common Event Format (CEF) 格式將所有資訊安全中心與夥伴的警示匯出至內部部署 Security Information and Event Management (SIEM) 系統中。


## <a name="partners-that-integrate-with-security-center"></a>與資訊安全中心整合的夥伴

目前可在 Azure Marketplace 中與資訊安全中心搭配使用的夥伴解決方案之原生整合如下：

- **端點保護**。 [Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html)、Symantec 以及 [適用於 Azure 雲端服務和虛擬機器的 Microsoft 反惡意程式碼軟體](https://docs.microsoft.com/azure/security/azure-security-antimalware)。
- **Web 應用程式防火牆**。 [Barracuda](https://www.barracuda.com/products/webapplicationfirewall)、[F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html)、[Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF)、[Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets)，以及 [Azure 應用程式閘道](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)。 
- **新一代防火牆**。 [Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/)、[Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/)、[Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) 和 [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)。 
- **弱點評估**。 [Qualys](https://www.qualys.com/public-clouds/microsoft-azure/)。 

資訊安全中心會隨時間擴展這些類別內夥伴的數目，並新增新的類別。 

## <a name="deploy-a-partner-solution"></a>部署夥伴解決方案

根據您的 Azure 環境設定和您定義的安全性原則，資訊安全中心可能會建議您部署夥伴解決方案。 此資訊安全中心建議將引導您完成選取和安裝夥伴解決方案的程序。 整體部署體驗可能會根據您使用的解決方案類型和夥伴而有所不同。 如需詳細資訊，請參閱下列文章。

- [安裝端點保護](security-center-install-endpoint-protection.md)
- [新增 Web 應用程式防火牆](security-center-add-web-application-firewall.md)
- [新增新一代防火牆](security-center-add-next-generation-firewall.md)
- [未安裝弱點評估](security-center-vulnerability-assessment-recommendations.md)

## <a name="manage-partner-solutions"></a>管理夥伴解決方案

在部署後，若要檢視解決方案的健康情況相關資訊並執行基本管理工作，請在 [資訊安全中心] 儀表板中，選取 [夥伴解決方案]。

![夥伴解決方案整合](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

您在開啟安全性解決方案時所看到的內容會根據您的基礎結構而有所不同。 此頁面使用先前的映像作為範例，具有三個區段：

- **連線的解決方案**。 顯示連線到資訊安全中心的解決方案。
- **探索到的解決方案**。 顯示未連線到資訊安全中心的解決方案。 您可以連線這些解決方案，它們就會顯示在**連線的解決方案**之下。 如果資訊安全中心未偵測到任何未連線的解決方案，本區段就會加以隱藏。
- **新增資料來源**。 顯示您可以新增到資訊安全中心的 Azure 和非 Azure 資料來源。

### <a name="connected-solutions"></a>連線的解決方案

**連線的解決方案**區段會顯示目前與資訊安全中心連線的所有安全性解決方案。 

![連線的解決方案](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

您看到的資訊會根據解決方案而有所不同。 每個圖格上可用的某些資訊包括：

- **夥伴的公司圖示**。 如果資訊安全中心沒有公司圖示，就會顯示夥伴名稱的第一個字元。
- **解決方案類型**。 顯示解決方案的類型。
- **電腦名稱**。 顯示電腦名稱。
- **健康情況狀態**。 如果未傳送健康情況指標，資訊安全中心就會顯示最後收到之事件的日期和時間，以指出應用裝置是否進行報告。 如果資訊安全中心未收到特定解決方案的健康情況指示器，解決方案的圖格就不會出現在本區段中。

> [!NOTE]
> 資訊安全中心會顯示最後收到之事件的日期和時間，以指出應用裝置是否進行報告。 若在過去 14 天內傳送了警示或事件，未傳送健康情況指標的解決方案會顯示為已連線。
>  

這些解決方案其中有一些可能會完全整合在 Azure 中，其他則為內部部署。 因為資訊安全中心支援 [CEF](https://docs.microsoft.com/azure/operations-management-suite/oms-security-connect-products#what-is-cef)，它可以與使用 CEF (例如支援 CEF 的防火牆) 的解決方案進行連線。 將此解決方案新增至資訊安全中心之後，防火牆會將記錄以 CEF 格式傳送至資訊安全中心，從而加以處理為 [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)。 防火牆是非 Azure 資源，且會傳送事件而非健康情況指標。 資訊安全中心唯一擁有的健康情況相關資訊是此應用裝置上次傳送事件的時間。 針對所有的非 Azure 資源，資訊安全中心會在圖格的健康情況區域中顯示收到最後一個事件的日期和時間。 這項資訊指出仍在回報非 Azure 資源。

### <a name="discovered-solutions"></a>探索到的解決方案

[探索到的解決方案] 區段會顯示已透過 Azure 新增的所有解決方案。 它也會顯示資訊安全中心建議應該連線的所有解決方案。

![探索到的解決方案](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

資訊安全中心可與內建的 Azure 解決方案進行整合，例如 [Azure Active Directory (Azure AD) Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)。 如果您有 Azure AD Identity Protection 的授權，但尚未連線到資訊安全中心，Azure AD Identity Protection 會列在**探索到的解決方案**下。 若要將此解決方案與資訊安全中心進行整合，選取 [Azure AD Identity Protection] 圖格上的 [連線]，下列分頁隨即出現：

![Azure AD Identity Protection](./media/security-center-partner-integration/security-center-partner-integration-fig6.png)

若要完成連線 Azure AD Identity Protection，請選取儲存資料的工作區。 Azure AD Identity Protection 中的所有資料都會從此步驟中已選取的工作區區域流出。 使用工作區選取器以選取工作區，且資料會開始流向該處。

您必須是全域管理員或安全性管理員才能連線到資訊安全中心。如果您沒有權限，則 [連線] 按鈕會停用。 訊息會出現，說明按鈕為什麼停用。

Azure AD Identity Protection 警示會經過資訊安全中心偵測管道。 如此一來，您會收到來自資訊安全中心和 Azure AD Identity Protection 的警示。 資訊安全中心會將看似相關的所有警示加以合併，以建立[安全性事件](https://docs.microsoft.com/azure/security-center/security-center-incident)。 安全性事件描述會讓您更深入了解有關可疑活動的情報。

### <a name="add-data-sources"></a>新增資料來源

您可以新增 Azure 和非 Azure 電腦，來與資訊安全中心進行整合。 新增非 Azure 電腦即表示您可以新增內部部署電腦或支援 CEF 的應用裝置。 

![資料來源](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="see-also"></a>另請參閱

在本文中，您已了解如何在資訊安全中心中整合夥伴解決方案。 如要深入了解資訊安全中心，請參閱下列文章：

* [資訊安全中心規劃和操作指南](security-center-planning-and-operations-guide.md)
* [在資訊安全中心管理和回應安全性警示](security-center-managing-and-responding-alerts.md)
* [資訊安全中心不同類型的安全性警示](security-center-alerts-type.md)
* [資訊安全中心的安全性健康情況監視](security-center-monitoring.md)。 了解如何監視 Azure 資源的健全狀況。
* [使用資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md)。 了解如何監視合作夥伴解決方案的健全狀態。
* [Azure 資訊安全中心常見問題](security-center-faq.md)。 取得有關使用資訊安全中心常見問題的答案。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)。 尋找有關 Azure 安全性與相容性的部落格文章。
