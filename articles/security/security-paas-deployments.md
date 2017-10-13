---
title: "保護 PaaS 部署 | Microsoft Docs"
description: " 了解 PaaS 與其他雲端服務模型相較之下的安全性優點，以及了解保護 Azure PaaS 部署的建議做法。 "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: techlake
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2017
ms.author: terrylan
ms.openlocfilehash: f218fe7e59e46683b544fd83bfea505b7cbe2d59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="securing-paas-deployments"></a>保護 PaaS 部署

本文提供的資訊可協助您：

- 了解將應用程式裝載在雲端的安全性優點
- 評估平台即服務 (PaaS) 與其他雲端服務模型相較之下的安全性優點
- 將您的安全性焦點從以網路為中心變更成以身分識別為中心的周邊安全性方法
- 實作一般 PaaS 安全性最佳做法建議

## <a name="cloud-security-advantages"></a>雲端安全性優點
在雲端環境中有一些安全性優點。 在內部部署環境中，組織可能責任重大但可投資在安全性上的資源卻相當有限，導致創造出一種攻擊者能夠利用所有層級弱點的環境。

![雲端時代的安全性優點][1]

組織能夠藉由使用提供者的雲端型安全性功能和雲端智慧，改進其威脅偵測和回應時間。  藉由將責任轉移給雲端提供者，組織便可擴大安全性涵蓋範圍，而能夠將安全性資源和預算重新配置給其他業務優先順序項目。

## <a name="division-of-responsibility"></a>責任劃分
了解您與 Microsoft 之間的責任劃分相當重要。 在內部部署環境中，您擁有整個堆疊，但是當您移到雲端時，部分責任就會轉移給 Microsoft。 以下責任矩陣圖顯示 SaaS、PaaS 及 IaaS 部署中由您負責和由 Microsoft 負責的堆疊領域。

![責任區][2]

就所有雲端部署類型而言，您擁有您的資料和身分識別。 您需負責保護您資料和身分識別、內部部署資源及您所控制之雲端元件 (因服務類型而異) 的安全性。

不論部署類型為何，一律由您承擔責任的對象包括：

- 資料
- 端點
- 帳戶
- 存取管理

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>PaaS 雲端服務模型的安全性優點
讓我們使用相同的責任矩陣圖，來看看 Azure PaaS 部署與內部部署相較之下的安全性優點。

![PaaS 的安全性優點][3]

Microsoft 是從堆疊底部的實體基礎結構開始來減輕常見的風險和責任。 由於 Microsoft 雲端受到 Microsoft 持續不斷的監視，因此難以對它進行攻擊。 對攻擊者來說，以 Microsoft 雲端作為目標並非明智之舉。 除非攻擊者擁有許多資金和資源，否則攻擊者就可能轉移到另一個目標。  

在攻擊當中，PaaS 部署與內部部署之間並沒有差異。 在應用程式層以及帳戶和存取管理層，您都有類似的風險。 在本文的＜後續步驟＞一節中，我們將引導您進行將這些風險消除或降到最低的最佳做法。

在堆疊頂端的資料控管和權限管理，您需承擔一項可由金鑰管理降低的風險。 (金鑰管理涵蓋在最佳做法中)。雖然金鑰管理是一項額外的責任，但在 PaaS 部署中有些領域已不再需要由您管理，因此您可以將資源轉移到金鑰管理。

Azure 平台也藉由使用各種網路型技術，提供您增強式 DDoS 保護。 不過，所有類型的網路型 DDoS 保護方法在每一連結和每一資料中心上都有其限制。 若要協助避免大型 DDoS 攻擊所帶來的影響，您可以利用可讓您快速且自動相應放大規模來防禦 DDoS 攻擊的 Azure 核心雲端功能。 我們將在建議的做法文章中，更詳細地深入探討如何這麼做。

## <a name="modernizing-the-defenders-mindset"></a>讓防禦者的心態邁向現代化
伴隨 PaaS 部署而來的就是您整體安全性方法的轉變。 您會從需要全部自行控制轉變成與 Microsoft 分享責任。

PaaS 與傳統內部部署的另一個重大差異在於一個新觀點，就是定義主要安全性周邊的是什麼。 在過去，主要內部部署安全性周邊是您的網路，而大多數內部部署安全性設計皆使用網路作為其主要安全性樞紐。 就 PaaS 部署而言，將身分識別視為主要安全性周邊可為您提供較佳的服務。

## <a name="identity-as-the-primary-security-perimeter"></a>以身分識別作為主要安全性周邊
雲端運算的五個基本特性之一是廣泛的網路存取，這使得以網路為中心的思維不是那麼重要。 雲端運算的大部分目標在於讓使用者不論身在哪個位置都能存取資源。 就大多數使用者而言，他們的位置將是網際網路上的某一處。

下圖說明安全性周邊如何從網路周邊發展到身分識別周邊。 安全性變得較不著重於防禦您的網路，而是較著重於防禦您的資料，以及管理您應用程式和使用者的安全性。 主要的差異在於您想要讓安全性更貼近於您公司所看重的方面。

![以身分識別作為新的安全性周邊][4]

一開始，Azure PaaS 服務 (例如 Web 角色和 Azure SQL) 提供極少或未提供任何傳統網路周邊防禦。 在認知上，元素的用途是對網際網路公開 (Web 角色)，而驗證則提供新的周邊 (例如 BLOB 或 Azure SQL)。

新式安全性做法是假設敵人已經突破網路周邊。 因此，新式防禦做法已經轉移到身分識別。 組織必須以增強式驗證與授權防疫 (最佳做法) 建立身分識別型安全性周邊。

## <a name="recommendations-for-managing-the-identity-perimeter"></a>管理身分識別周邊的建議

網路周邊的原則和模式已經存在數十年。 對照之下，業界在使用身分識別作為主要安全性周邊方面就相對較無經驗。 儘管如此，我們也已累積足夠的經驗來提供一些一般性的建議，這些建議已經過實地驗證且適用於幾乎所有 PaaS 服務。

以下將摘要說明一個管理您身分識別周邊的一般性最佳做法。

- **不要遺失您的金鑰或認證**：保護金鑰和認證對保護 PaaS 部署來說相當重要。 遺失金鑰和認證是相當常見的問題。 其中一個絕佳的解決方案是使用集中式解決方案，將金鑰和密碼存放在硬體安全性模組 (HSM) 中。 Azure 藉由 [Azure Key Vault](../key-vault/key-vault-whatis.md) 提供您一個雲端 HSM。
- **不要將認證及其他密碼放在原始程式碼或 GitHub 中**：唯一比遺失金鑰和認證更糟的情況就是讓未經授權的一方能夠存取這些機密資料。 攻擊者能夠利用 Bot 技術來尋找存放在程式碼儲存機制 (例如 GitHub) 中的金鑰和密碼。 請勿將金鑰和密碼放在這些公用原始程式碼儲存機制中。
- **保護混合式 PaaS 和 IaaS 服務上的 VM 管理介面**：IaaS 和 PaaS 服務是在虛擬機器 (VM) 上執行。 視服務類型而定，有數個管理介面可供您從遠端直接管理這些 VM。 可使用的遠端管理通訊協定包括像是[安全殼層通訊協定 (SSH)](https://en.wikipedia.org/wiki/Secure_Shell)、[遠端桌面通訊協定 (RDP)](https://support.microsoft.com/kb/186607) 及[遠端 PowerShell](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting)。 一般而言，建議您不要啟用從網際網路直接遠端存取 VM 的功能。 您應該使用替代方法 (如果可用)，例如使用虛擬私人網路來連線到 Azure 虛擬網路。 如果沒有替代方法可用，則請務必使用複雜密碼，並且如果可以使用雙重要素驗證 (例如 [Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md))，便使用此驗證。
- **使用增強式驗證與授權平台**

  - 使用 Azure AD 中的同盟身分識別，而不要使用自訂使用者存放區。 使用同盟身分識別時，您可以利用平台型方法並將已授權之身分識別的管理委派給您的合作夥伴。 在員工已被解雇而該資訊必須透過多個身分識別與授權系統來反映的案例中，同盟身分識別方法尤其重要。
  - 使用平台提供的驗證與授權機制，而不要使用自訂程式碼。 原因在於開發自訂驗證程式碼可能容易出錯。 您的大多數開發人員都不是安全性專家，因此可能不是很清楚驗證與授權方面的微妙細節和最新發展。 商業程式碼 (例如來自 Microsoft) 通常都經過廣泛的安全性檢閱。
  - 使用多重要素驗證。 多重要素驗證是現行的驗證與授權標準，因為它可避免使用者名稱與密碼型驗證中固有的安全性弱點。 您應該將 Azure 管理 (入口網站/遠端 PowerShell) 介面和面向客戶之服務的存取方式都設計並設定成使用 [Azure Multi-Factor Authentication (MFA)](../multi-factor-authentication/multi-factor-authentication.md)。
  - 使用標準驗證通訊協定，例如 OAuth2 和 Kerberos。 這些通訊協定已經過廣泛的對等檢閱，而可能作為您驗證與授權平台程式庫的一部分來實作。

## <a name="next-steps"></a>後續步驟
在本文中，我們是將焦點放在 Azure PaaS 部署的安全性優點。 接下來，請了解適用於保護您 PaaS Web 和行動解決方案的建議做法。 我們將從 Azure App Service、Azure SQL Database 及「Azure SQL 資料倉儲」開始著手。 當有適用於其他 Azure 服務的建議做法文章推出時，就會在以下清單中提供連結：

- [Azure App Service](security-paas-applications-using-app-services.md)
- [Azure SQL Database 和 Azure SQL Data Warehouse](security-paas-applications-using-sql.md)
- Azure 儲存體
- Azure REDIS Cache
- Azure 服務匯流排
- Web 應用程式防火牆

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png
