---
title: "Azure 資訊安全中心常見問題集 (FAQ) | Microsoft Docs"
description: "這個常見問題集回答「Azure 資訊安全中心」的相關問題。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: terrylan
ms.openlocfilehash: e71d407050f210c770bcac30259b9c2f2fb27aa3
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Azure 資訊安全中心常見問題集 (FAQ)
這個常見問題集回答「Azure 資訊安全中心」的相關問題，此資訊安全中心是一項針對 Microsoft Azure 資源的安全性提供更深入的洞悉和更佳控制的服務，可協助您預防、偵測及回應威脅。

> [!NOTE]
> 從 2017 年 6 月初開始，資訊安全中心會使用 Microsoft Monitoring Agent 來收集和儲存資料。 如需詳細資訊，請參閱 [Azure 資訊安全中心平台移轉](security-center-platform-migration.md)。 本文章中的資訊說明轉換至 Microsoft Monitoring Agent 後的資訊安全中心功能。
>
>

## <a name="general-questions"></a>一般問題
### <a name="what-is-azure-security-center"></a>什麼是 Azure 資訊安全中心？
Azure 資訊安全中心利用加強對 Azure 資源的能見度及安全性控制權，以預防、偵測並回應威脅。 它提供您訂用帳戶之間的整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

### <a name="how-do-i-get-azure-security-center"></a>我要如何取得 Azure 資訊安全中心？
「Azure 資訊安全中心」是藉由您的 Microsoft Azure 訂用帳戶啟用，並可從 [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)存取。 ([登入入口網站](https://portal.azure.com)，選取 [瀏覽]，然後捲動至 [資訊安全中心])。  

## <a name="billing"></a>計費
### <a name="how-does-billing-work-for-azure-security-center"></a>Azure 資訊安全中心如何計費？
資訊安全中心提供兩個層級：

「免費層」提供 Azure 資源安全性狀態的可見度、基本安全性原則、安全性建議以及與合作夥伴的安全性產品和服務的整合。

「標準層」加上了進階威脅偵測功能，包括威脅情報、行為分析、異常偵測、安全性事件，以及威脅歸因報告。 標準層的前 60 天免費。 如果您在超過 60 天後選擇繼續使用服務，服務會自動開始計費。  若要升級，請選取安全性原則中的[定價層](https://docs.microsoft.com/azure/security-center/security-center-pricing)。

## <a name="permissions"></a>權限
Azure 資訊安全中心會使用[角色型存取控制 (RBAC)](../active-directory/role-based-access-control-configure.md)，以提供可在 Azure 中指派給使用者、群組與服務的[內建角色](../active-directory/role-based-access-built-in-roles.md)。

資訊安全中心會評估資源的組態，以識別安全性問題與弱點。 在「資訊安全中心」中，當您獲指派為資源所屬的訂用帳戶或資源群組「擁有者」、「參與者」或「讀取者」角色時，您只會看到與資源相關的項目。

請參閱[Azure 資訊安全中心的權限](security-center-permissions.md)以深入了解角色與資訊安全中心允許的動作。

## <a name="data-collection"></a>資料收集
資訊安全中心會收集虛擬機器的資料，以便評估其安全性狀態、提供安全性建議，並對您發出威脅警示。 當您第一次存取資訊安全中心時，訂用帳戶中的所有虛擬機器都會啟用資料收集。 您也可以在資訊安全中心原則中啟用資料收集。

### <a name="how-do-i-disable-data-collection"></a>我要如何停用資料收集？
如果您是使用免費層的 Azure 資訊安全中心，可以隨時從虛擬機器停用資料收集。 標準層上的訂用帳戶需要資料收集。 您可以在安全性原則中停用訂用帳戶的資料收集。 ([登入 Azure 入口網站](https://portal.azure.com)，選取 [瀏覽]，選取 [資訊安全中心]，然後選取 [原則]。)選取訂用帳戶時，會開啟新的刀鋒視窗，並提供您關閉 [資料收集] 的選項。

### <a name="how-do-i-enable-data-collection"></a>我要如何啟用資料收集？
您可以在安全性原則中為您的 Azure 訂用帳戶啟用資料收集。 啟用資料收集。 [登入 Azure 入口網站](https://portal.azure.com)，選取 [瀏覽]，選取 [資訊安全中心]，然後選取 [原則]。 將 [資料收集] 設定為 [開啟]。

### <a name="what-happens-when-data-collection-is-enabled"></a>啟用資料收集時會發生什麼情況？
在啟用資料收集之後，Microsoft Monitoring Agent 會自動佈建在部署於訂用帳戶中所有現有和新的虛擬機器上。

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Monitoring Agent 是否會影響伺服器的效能？
代理程式只耗用少量的系統資源，對效能的影響應該很小。 如需有關效能影響和代理程式與擴充的詳細資訊，請參閱[規劃和操作指南](security-center-planning-and-operations-guide.md#data-collection-and-storage)。

### <a name="where-is-my-data-stored"></a>我的資料會儲存在何處？
從這個代理程式收集的資料會儲存在與您的訂用帳戶相關聯的現有 Log Analytics 工作區或新的工作區中。 如需詳細資訊，請參閱[資料安全性](security-center-data-security.md)。

## <a name="using-azure-security-center"></a>使用 Azure 資訊安全中心
### <a name="what-is-a-security-policy"></a>什麼是安全性原則？
安全性原則可針對指定之訂用帳戶內的資源，定義一組建議的控制機制。 在「Azure 資訊安全中心」中，您可以根據公司的安全性需求，以及每個訂用帳戶中應用程式的類型或資料的機密性，為您的 Azure 訂用帳戶定義原則。

「Azure 資訊安全中心」中啟用的安全性原則將會決定安全性建議與監視。 若要深入了解安全性原則，請參閱 [Azure 資訊安全中心的安全性健康情況監視](security-center-monitoring.md)。

### <a name="who-can-modify-a-security-policy"></a>誰可以修改安全性原則？
若要修改安全性原則，您必須是安全性系統管理員或是訂用帳戶的擁有者或參與者。

若要了解如何設定安全性原則，請參閱[在 Azure 資訊安全中心設定安全性原則](security-center-policies.md)。

### <a name="what-is-a-security-recommendation"></a>什麼是安全性建議？
「Azure 資訊安全中心」會分析 Azure 資源的安全性狀態。 當發現潛在的安全性弱點時，就會產生相關建議。 這些建議會引導您完成設定所需控制項的程序。 範例包括：

* 佈建反惡意程式碼，以協助識別及移除惡意軟體
* 設定 [網路安全性群組](../virtual-network/virtual-networks-nsg.md)與規則，以控制對虛擬機器的流量
* 佈建 Web 應用程式防火牆，以協助抵禦以 Web 應用程式為目標的攻擊
* 部署遺漏的系統更新
* 處理不符合建議基準的作業系統組態

這裡只會顯示 [安全性原則] 中已啟用的建議。

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>我要如何查看 Azure 資源目前的安全性狀態？
[資訊安全中心概觀] 刀鋒視窗會根據電腦、網路、儲存體與資料，以及應用程式，來分類顯示環境的安全性狀態。 每個資源類型都有一個指標，顯示是否已識別出任何潛在的安全性弱點。 按一下每個圖格都會顯示資訊安全中心所識別的安全性問題，以及訂用帳戶內資源的詳細目錄。

### <a name="what-triggers-a-security-alert"></a>什麼會觸發安全性警示？
「Azure 資訊安全中心」會自動收集、分析及整合下列來源的記錄檔資料：Azure 資源、網路，以及反惡意程式碼和防火牆等合作夥伴解決方案。 偵測到威脅時，會建立安全性警示。 偵測範例包括：

* 已受到危害的虛擬機器與已知的惡意 IP 位址進行通訊
* 使用 Windows 錯誤報告偵測到進階的惡意程式碼
* 針對虛擬機器的暴力密碼破解攻擊
* 來自已整合的合作夥伴安全性解決方案 (例如「反惡意程式碼」或「Web 應用程式防火牆」) 的安全性警示

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>在 Microsoft Security Response Center 與 Azure 資訊安全中心，偵測到威脅和收到警示有何差異？
Microsoft Security Response Center (MSRC) 執行 Azure 網路和基礎結構的選取安全性監視，並接收來自協力廠商的威脅情報和濫用客訴。 當 MSRC 察覺到有非法或未經授權的合作對象存取客戶資料，或有客戶不遵守可接受用途的條款使用 Azure，安全性事件管理員就會通知客戶。 通知方式通常是傳送電子郵件給 Azure 資訊安全中心中指定的安全性連絡人，如未指定安全性連絡人，則通知 Azure 訂用帳戶擁有者。

資訊安全中心是一項 Azure 服務，它會持續監視客戶的 Azure 環境，以及套用分析自動偵測廣泛的潛在惡意活動。 這些偵測會顯示為資訊安全中心儀表板中的安全性警示。

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Azure 資訊安全中心會監視哪些 Azure 資源？
Azure 資訊安全中心會監視下列 Azure 資源：

* 虛擬機器 (VM) (包括 [雲端服務](../cloud-services/cloud-services-choose-me.md))
* Azure 虛擬網路
* Azure SQL 服務
* Azure 儲存體帳戶
* Azure Web Apps (在 [App Service 環境](../app-service/environment/intro.md)中)
* 與您的 Azure 訂用帳戶整合的合作夥伴解決方案，例如 VM 和 App Service 環境上的 Web 應用程式防火牆

## <a name="virtual-machines"></a>虛擬機器
### <a name="what-types-of-virtual-machines-are-supported"></a>支援哪些類型的虛擬機器？
對於使用[傳統與 Resource Manager 部署模型](../azure-classic-rm.md)建立的虛擬機器 (VM)，提供監視和建議。

如需支援之平台的清單，請參閱 [Azure 資訊安全中心支援的平台](security-center-os-coverage.md)。

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>為什麼 Azure 資訊安全中心無法辨識我的 Azure VM 上所執行的反惡意程式碼軟體解決方案？
Azure 資訊安全中心能辨識透過 Azure 擴充功能安裝的反惡意程式碼軟體。 例如，資訊安全中心無法偵測您在提供的映像中預先安裝的反惡意程式碼軟體，或是您使用自己的程序 (例如組態管理系統) 在虛擬機器上安裝的反惡意程式碼軟體。

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>為什麼我會從 VM 收到「遺失掃描資料」訊息？
當沒有 VM 的掃瞄資料時會顯示此訊息。 在 Azure 資訊安全中心中啟用資料收集之後，可能需要花費一些時間 (少於一小時) 才能填入掃描資料。 在初始填入掃描資料之後，您可能會收到此訊息，因為完全沒有掃描資料或是沒有最近的掃描資料。 掃描不會填入處於停止狀態之 VM 的資料。 如果最近沒有填入掃描資料 (根據 Windows 代理程式的保留原則，預設值為 30 天)，可能也會顯示此訊息。

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>資訊安全中心多久掃描作業系統弱點、系統更新和端點保護問題一次？
資訊安全中心掃描弱點、更新和問題的延遲為：

- 作業系統弱點 (Microsoft 造成)：在 48 小時內更新資料
- 系統更新：在 24 小時內更新資料
- 端點保護問題：在 8 小時內更新資料

資訊安全中心通常會每小時掃描新的資料一次。 上述延遲值是沒有最新掃描或掃描失敗的最糟情況。

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>為什麼我會收到「VM 代理程式已遺失」訊息？
VM 代理程式必須安裝在 VM 上，才能啟用資料收集。 預設會為從 Azure Marketplace 部署的 VM 安裝「VM 代理程式」。 如需如何在其他 VM 上安裝 VM 代理程式的資訊，請參閱部落格文章 [VM Agent and Extensions (VM 代理程式和擴充功能)](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)。
