---
title: "資料安全性和加密最佳作法 | Microsoft Docs"
description: "本文提供使用內建 Azure 功能的一些資料安全性和加密最佳作法。"
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2017
ms.author: yurid
ms.openlocfilehash: 81136e53756adfdba2f07c103b042499fe2967db
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Azure 資料安全性和加密最佳作法
在雲端保護資料的其中一個關鍵是考慮您的資料可能會發生的狀態，以及哪些控制項適用於該狀態。 基於 Azure 資料安全性和加密最佳作法的目的，相關建議將以下列資料狀態為主︰

* 待用︰這包括實體媒體 (磁碟或光碟) 上以靜態方式存在的所有資訊儲存物件、容器和類型。
* 傳輸中︰當資料在元件、位置或程式之間傳送，例如透過網路、透過服務匯流排 (從內部部署至雲端，反之亦然，包括諸如 ExpressRoute 的混合式連線)，或在輸入/輸出過程中，它會被視為移動中。

本文將討論 Azure 資料安全性和加密最佳作法的集合。 這些最佳作法衍生自我們的 Azure 資料安全性和加密經驗和客戶的經驗。

針對每個最佳作法，我們會說明︰

* 最佳作法是什麼
* 您為何想要啟用該最佳作法
* 如果無法啟用最佳作法，結果可能為何
* 最佳作法的可能替代方案
* 如何學習啟用最佳作法

這篇「Azure 資料安全性和加密最佳作法」是以共識意見以及 Azure 平台功能和特性集 (因為在撰寫本文時已存在) 為基礎。 意見和技術會隨著時間改變，這篇文章將會定期進行更新以反映這些變更。

本文討論的 Azure 資料安全性和加密最佳作法包括︰

* 強制執行 Multi-Factor Authentication
* 使用角色型存取控制 (RBAC)
* 加密 Azure 虛擬機器
* 使用硬體安全性模型
* 透過安全的工作站管理
* 啟用 SQL 資料加密
* 保護傳輸中的資料
* 強制執行檔案層級資料加密

## <a name="enforce-multi-factor-authentication"></a>強制執行 Multi-Factor Authentication
在 Microsoft Azure 中存取和控制資料的第一個步驟是驗證使用者。 [Azure Multi-Factor Authentication (MFA)](../multi-factor-authentication/multi-factor-authentication.md) 是除了使用使用者名稱與密碼之外，利用其他方法驗證使用者身分識別的驗證方法。 此驗證方法有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。

為您的使用者啟用 Azure MFA，您可為使用者登入和交易增加第二層安全性。 在此情況下，交易可能會存取位於檔案伺服器或 SharePoint Online 中的文件。 Azure MFA 也可協助 IT 降低遭入侵的認證能夠存取公司資料的可能性。

例如︰如果您對使用者強制執行 Azure MFA，並將它設定為以電話或簡訊做為驗證，如果使用者的認證遭到入侵，攻擊者將無法存取任何資源，因為攻擊者無法使用使用者的電話。 未新增此額外身分識別保護層的組織會更容易受到認證竊取攻擊，這可能會導致資料洩漏。

想要保留驗證控制內部部署的組織有一個替代方法，就是使用 [Azure Multi-factor Authentication Server](../multi-factor-authentication/multi-factor-authentication-get-started-server.md) (也稱為 MFA 內部部署)。 使用此方法，您仍可強制執行 Multi-Factor Authentication，同時保留 MFA 伺服器內部部署。

如需 Azure MFA 的詳細資訊，請參閱[開始在雲端中使用 Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)。

## <a name="use-role-based-access-control-rbac"></a>使用角色型存取控制 (RBAC)
根據[需要知道](https://en.wikipedia.org/wiki/Need_to_know)和[最低權限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)安全性原則限制存取權限。 對於想要強制執行資料存取安全性原則的組織，這是必須做的事。 Azure 角色型存取控制 (RBAC) 可用來指派權限給特定範圍的使用者、群組和應用程式。 角色指派的範圍可以是訂用帳戶、資源群組或單一資源。

您可以利用 Azure 中[內建的 RBAC](../active-directory/role-based-access-built-in-roles.md) 角色指派權限給使用者。 請考慮將*儲存體帳戶參與者*用於需要管理儲存體帳戶的雲端操作者，以及使用*傳統儲存體帳戶參與者*角色來管理傳統儲存體帳戶。 對於需要管理 VM 和儲存體帳戶的雲端操作者，請考慮將他們新增至*虛擬機器參與者*角色。

未利用諸如 RBAC 等功能來強制執行資料存取控制的組織，可能會對其使用者提供超過所需的權限。 一開始就讓有些使用者可以存取他們不應具備的資料，可能會導致資料洩漏。

若要深入了解 Azure RBAC，請閱讀 [Azure 角色型存取控制](../active-directory/role-based-access-control-configure.md)一文。

## <a name="encrypt-azure-virtual-machines"></a>加密 Azure 虛擬機器
對許多組織來說，[待用資料加密](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/)是達到資料隱私性、法規遵循及資料主權的必要步驟。 Azure 磁碟加密可讓 IT 管理員加密 Windows 和 Linux IaaS 虛擬機器 (VM) 磁碟。 Azure 磁碟加密利用 Windows 的業界標準 BitLocker 功能和 Linux 的 DM-Crypt 功能，為 OS 和資料磁碟提供磁碟區加密。

您可以利用 Azure 磁碟加密來協助保護資料安全，以符合您的組織安全性和法規遵循承諾。 組織也應該考慮使用加密，協助降低與未經授權存取資料相關的風險。 此外，也建議您在將敏感性資料寫入磁碟機之前，先將磁碟機加密。

確定將您的 VM 資料磁碟區和開機磁碟區加密，以保護您的 Azure 儲存體帳戶中待用的資料。 利用 [Azure 金鑰保存庫](../key-vault/key-vault-whatis.md)來保護加密金鑰和密碼。

對於您的內部部署 Windows Server，請考慮下列加密最佳作法︰

* 使用 [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) 進行資料加密
* 在 AD DS 中儲存修復資訊。
* 如果擔憂 BitLocker 金鑰被盜用，建議您將磁碟機格式化，以移除磁碟機中 BitLocker 中繼資料的所有執行個體，或將整個磁碟機解密後再次加密。

未強制執行資料加密的組織更容易遭遇資料完整性問題，例如惡意或粗暴使用者竊取資料與入侵帳戶，且未經授權存取單純格式的資料。 除了這些風險，必須遵守業界法規的公司必須證明他們是十分用心，並使用正確的安全性控制項來增強資料安全性。

若要深入了解 Azure 磁碟加密，請閱讀[適用於 Windows 和 Linux IaaS VM 的 Azure 磁碟加密](azure-security-disk-encryption.md)一文。

## <a name="use-hardware-security-modules"></a>使用硬體安全性模型
業界加密解決方案會使用秘密金鑰來加密資料。 因此，務必安全地儲存這些金鑰。 金鑰管理會變成資料保護不可或缺的部分，因為它會用來儲存加密資料所用的秘密金鑰。

Azure 磁碟加密使用 [Azure 金鑰保存庫](https://azure.microsoft.com/services/key-vault/) ，既幫助您控制和管理您的金鑰保存庫訂用帳戶中的磁碟加密金鑰和密碼，同時也確保虛擬機器磁碟中的所有資料在您 Azure 儲存體中待用時會受到加密。 您應使用 Azure 金鑰保存庫來稽核金鑰和原則使用方式。

若沒有採用適當的安全性控制項來保護用來加密資料的秘密金鑰，就會有許多相關的固有風險。 如果攻擊者可以存取秘密金鑰，他們就能夠將資料解密，並可能存取機密資訊。

若要深入了解 Azure 中憑證管理的一般建議，請閱讀 [Certificate Management in Azure: Do’s and Don’ts (Azure 中的憑證管理︰建議與禁忌)](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/)。

如需 Azure 金鑰保存庫的詳細資訊，請閱讀[開始使用 Azure 金鑰保存庫](../key-vault/key-vault-get-started.md)。

## <a name="manage-with-secure-workstations"></a>透過安全的工作站管理
因為絕大多數的攻擊是以使用者為目標，所以端點會成為主要攻擊點之一。 如果攻擊者入侵端點，他可以運用使用者的認證來存取組織的資料。 大部分的端點攻擊能夠利用使用者就是其本機工作站的系統管理員的這個事實。

您可以使用安全的管理工作站來降低這些風險。 建議您使用 [Privileged Access Workstations (PAW) (特殊權限存取工作站 (PAW))](https://technet.microsoft.com/library/mt634654.aspx) 來減少工作站的受攻擊面。 這些安全的管理工作站可協助您減輕其中一些攻擊，以確保您的資料更為安全。 請務必使用 PAW 來強化並鎖定您的工作站。 這是重要的步驟，可為機密帳戶、工作和資料保護提供高安全性保證。

缺少端點保護會使您的資料面臨風險，請務必在用來取用資料的所有裝置上強制執行安全性原則 (不論資料位置是雲端或內部部署)。

若要深入了解特殊權限存取工作站，請閱讀 [Securing Privileged Access (保護特殊權限存取)](https://technet.microsoft.com/library/mt631194.aspx) 一文。

## <a name="enable-sql-data-encryption"></a>啟用 SQL 資料加密
[Azure SQL Database 的透明資料加密](https://msdn.microsoft.com/library/dn948096.aspx) (TDE) 可在不需變更應用程式的情況下，對靜止的資料庫、相關聯的備份和交易記錄檔執行即時加密和解密，協助防止惡意活動的威脅。  TDE 會使用稱為資料庫加密金鑰的對稱金鑰來加密整個資料庫的儲存體。

即使整個儲存體都已加密，也一定要您的資料庫本身加密。 這是資料保護的深度防禦方法實作。 如果您使用 [Azure SQL Database](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx)，而且想要保護敏感性資料 (例如信用卡或身分證號碼)，可以使用 FIPS 140-2 驗證的 256 位元 AES 加密來加密資料庫，其符合許多產業標準 (例如 HIPAA、PCI) 的需求。

請務必了解使用 TDE 加密資料庫時，[緩衝集區擴充](https://msdn.microsoft.com/library/dn133176.aspx) (BPE) 相關檔案並不會加密。 您必須對 BPE 相關檔案使用檔案系統層級的加密工具，像是 BitLocker 或 [Encrypting File System (加密檔案系統)](https://technet.microsoft.com/library/cc700811.aspx) (EFS)。

因為經過授權的使用者 (如安全性系統管理員或資料庫管理員) 可以存取資料，所以即使已使用 TDE 將資料庫加密，您也應該遵循下列建議︰

* 資料庫層級的 SQL 驗證
* 使用 RBAC 角色的 Azure AD 驗證
* 使用者和應用程式應使用不同的帳戶進行驗證。 如此一來，您可以限制授與使用者和應用程式的權限，並降低惡意活動的風險。
* 使用固定的資料庫角色 (例如 db_datareader 或 db_datawriter) 實作資料庫層級安全性，或者您可以為應用程式建立自訂角色，以授與明確的權限給選取的資料庫物件

不使用資料庫層級加密的組織可能更容易受到攻擊，進而危害 SQL Database 中的資料。

若要深入了解 SQL TDE 加密，請閱讀 [Azure SQL Database 的透明資料加密](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx)一文。

## <a name="protect-data-in-transit"></a>保護傳輸中的資料
保護傳輸中的資料應該是您的資料保護策略中不可或缺的部分。 由於資料會從許多位置來回移動，一般會建議您一律使用 SSL/TLS 通訊協定來交換不同位置的資料。 在某些情況下，建議您使用虛擬私人網路 (VPN)，隔離您的內部部署與雲端基礎結構之間的整個通訊通道。

對於在內部部署基礎結構與 Azure 之間移動的資料，您應該考慮適當的防護措施，例如 HTTPS 或 VPN。

對於需要從位於內部部署的多個工作站安全存取 Azure 的組織而言，請使用 [Azure 站對站 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md)。

對於需要從位於內部部署的一個工作站安全存取 Azure 的組織而言，請使用[點對站 VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md)。

較大的資料集可以透過專用的高速 WAN 連結 (例如 [ExpressRoute](https://azure.microsoft.com/services/expressroute/)) 移動。 如果您選擇使用 ExpressRoute，您也可以使用 [SSL/TLS](https://support.microsoft.com/kb/257591) 或其他通訊協定，在應用程式層級加密資料，以提供額外的保護。

如果您透過 Azure 入口網站與 Azure 儲存體互動，則所有交易都會透過 HTTPS 發生。 透過 HTTPS 的[儲存體 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) 也可用來與 [Azure 儲存體](https://azure.microsoft.com/services/storage/)和 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 互動。

無法保護傳輸中資料的組織比較容易遭受[攔截攻擊](https://technet.microsoft.com/library/gg195821.aspx)、[竊聽](https://technet.microsoft.com/library/gg195641.aspx)及工作階段攔截。 這些攻擊可能是取得機密資料存取權的第一步。

若要深入了解 Azure VPN 選項，請閱讀[規劃與設計 VPN 閘道](../vpn-gateway/vpn-gateway-plan-design.md)一文。

## <a name="enforce-file-level-data-encryption"></a>強制執行檔案層級資料加密
不論檔案的位置為何，可提高資料安全性層級的另一層保護，就是將檔案本身加密，。

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) 會使用加密、身分識別和授權原則，協助您保護檔案和電子郵件。 Azure RMS 可跨多個裝置運作 — 手機、平板電腦和 PC。保護您的組織內部和外部 因為 Azure RMS 新增資料所屬的保護層級，所以即使資料脫離您組織的範圍，這項功能仍然可行。

當您使用 Azure RMS 來保護檔案時，您要使用業界標準的密碼編譯搭配 [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html) 的完整支援。 當您利用 Azure RMS 進行資料保護時，即使檔案被複製到不受 IT 控制的儲存體 (例如雲端儲存體服務)，也保證該檔案持續受到保護。 同樣的情況會出現在透過電子郵件共用的檔案，檔案會以電子郵件的附件形式受到保護，並提供如何開啟受保護附件的指示。

規劃 Azure RMS 採用時，建議執行下列作業︰

* 安裝 [RMS 共用應用程式](https://technet.microsoft.com/library/dn339006.aspx)。 此應用程式會藉由安裝 Office 增益集來與 Office 應用程式整合，讓使用者可以輕鬆地直接保護檔案。
* 設定應用程式和服務以支援 Azure RMS
* 建立可反映您的業務需求的[自訂範本](https://technet.microsoft.com/library/dn642472.aspx)。 例如︰最高秘密資料的範本應套用於所有最高機密相關的電子郵件。

[資料分類](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf)和檔案保護能力不佳的組織可能更容易受資料外洩。 沒有適當的檔案保護，組織將無法取得商業見解、監督濫用情形，以及防止檔案被惡意存取。

若要深入了解 Azure RMS，請閱讀[開始使用 Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx) 一文。
