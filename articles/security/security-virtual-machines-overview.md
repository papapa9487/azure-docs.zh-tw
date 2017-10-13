---
title: "用於 Azure 虛擬機器的 Azure 安全性功能 | Microsoft Docs"
description: " 可用於 Azure 虛擬機器的核心 Azure 安全性功能概觀。 Azure VM 讓您能夠有彈性地進行虛擬化，而不需購買並維護執行 VM 的實體硬體。 "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: terrylan
ms.openlocfilehash: f1fb7f876c7dc010c03f01a4f6698ddc18da1100
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-virtual-machines-security-overview"></a>Azure 虛擬機器安全性概觀
您可利用 Azure 虛擬機器以敏捷的方式部署範圍廣泛的許多運算解決方案。 利用 Microsoft Windows、Linux、Microsoft SQL Server、Oracle、IBM、SAP 與 Azure BizTalk 服務的支援，就可以在近乎所有的作業系統上，使用任何語言部署所有工作負載。

Azure 虛擬機器讓您能夠有彈性地進行虛擬化，而不需購買並維護執行虛擬機器的實體硬體。  您可以建置並部署您的應用程式，並保證您的資料會在我們的高度安全資料中心中受到安全的保護。

您可以使用 Azure 建置安全性已加強且相容的解決方案：

* 保護虛擬機器抵禦病毒和惡意程式碼
* 將敏感性資料加密
* 保護網路流量
* 識別和偵測威脅
* 符合規範需求

本文的目的是對可用於虛擬機器的 Azure 安全性功能提供核心的概觀。 我們也會提供文章的連結，更詳細說明每個功能，以讓您深入了解。  

本文涵蓋核心 Azure 虛擬機器安全性功能︰

* 反惡意程式碼
* 硬體安全性模型
* 虛擬機器磁碟加密
* 虛擬機器備份
* Azure Site Recovery
* 虛擬網路
* 安全性原則管理和報告
* 法規遵循

## <a name="antimalware"></a>反惡意程式碼
運用 Azure，您可以使用來自安全性廠商 (例如 Microsoft、Symantec、Trend Micro 和 Kaspersky) 的反惡意程式碼軟體，以保護您的虛擬機器抵禦惡意檔案、廣告軟體和其他威脅。 請參閱以下的「深入了解」一節，尋找合作夥伴解決方案上的文章。

適用於 Azure 雲端服務和虛擬機器的 Microsoft Antimalware 是即時保護功能，有助於識別和移除病毒、間諜軟體和其他惡意軟體。  Microsoft Antimalware 會提供可設定的警示，在已知的惡意或垃圾軟體嘗試自行安裝或在您的 Azure 系統上執行時發出警示。

Microsoft Antimalware 是一個針對應用程式和租用戶環境所提供的單一代理程式解決方案，其設計可於無人為介入的情況下在背景中執行。 您可依據應用程式工作負載需求，選擇預設的基本安全性或進階的自訂組態 (包括反惡意程式碼監視) 來部署保護。

當您部署並啟用 Microsoft Antimalware 時，有下列幾項核心功能可供使用：

* 即時保護 - 監視雲端服務和虛擬機器上的活動，以偵測和封鎖惡意程式碼執行。
* 排程掃描 - 定期執行目標掃描以偵測惡意程式碼，包括主動執行程式。
* 惡意程式碼補救 - 自動針對偵測到的惡意程式碼採取行動，例如刪除或隔離惡意檔案及清除惡意的登錄項目。
* 簽章更新 - 自動安裝最新的保護簽章 (病毒定義) 以確保依預定頻率維持最新的保護狀態。
* Antimalware 引擎更新 – 自動更新 Microsoft Antimalware 引擎。
* Antimalware 平台更新 – 自動更新 Microsoft Antimalware 平台。
* 主動保護 - 對 Azure 遙測中繼資料報告有關偵測到的威脅和可疑的資源以確保能快速回應，並可透過 Microsoft Active Protection System (MAPS) 啟用即時的同步簽章傳遞。
* 範例報告 - 將範例提供並報告至 Microsoftt Antimalware 服務，以協助改善其服務並啟用疑難排解。
* 排除項目 – 可讓應用程式和服務管理員設定特定的檔案、處理序及磁碟機，以因應效能及/或其他原因將其從保護和掃描中排除。
* 事件收集 -記錄作業系統事件記錄檔中反惡意程式碼服務的健康狀況、可疑的活動及其所採取的補救動作，並將這些資料收集至客戶的 Azure 儲存體帳戶。

深入了解︰若要深入了解反惡意程式碼軟體以保護虛擬機器，請參閱︰

* [適用於 Azure 雲端服務和虛擬機器的 Microsoft Antimalware](azure-security-antimalware.md)
* [在 Azure 虛擬機器上部署反惡意程式碼解決方案](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [如何在 Windows VM 上安裝和設定 Trend Micro Deep Security as a Service](../virtual-machines/windows/classic/install-trend.md)
* [如何在 Windows VM 上安裝和設定 Symantec Endpoint Protection](../virtual-machines/windows/classic/install-symantec.md)
* [Azure Marketplace 中的安全性解決方案](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>硬體安全性模型
您可以藉由改善金鑰安全性來增強加密和驗證保護。 您可以藉由將關鍵密碼和金鑰存放在 Azure 金鑰保存庫中來簡化其管理與安全性。 金鑰保存庫讓您能選擇將金鑰存放在通過 FIPS 140-2 Level 2 標準認證的硬體安全性模組 (HSM) 中。 備份或 [透明資料加密](https://msdn.microsoft.com/library/bb934049.aspx) 的 SQL Server 加密金鑰都能與應用程式的任何金鑰或密碼一起存放在金鑰保存庫中。 這些受保護項目的權限和存取權是透過 [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)來管理。

深入了解：

* [什麼是 Azure 金鑰保存庫？](../key-vault/key-vault-whatis.md)
* [開始使用 Azure 金鑰保存庫](../key-vault/key-vault-get-started.md)
* [Azure 金鑰保存庫部落格](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>虛擬機器磁碟加密
Azure 磁碟加密是可讓您加密您的 Windows 和 Linux Azure 虛擬機器磁碟的新功能。 Azure 磁碟加密使用 Windows 的業界標準 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 功能和 Linux 的 [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) 功能，為 OS 和資料磁碟提供磁碟區加密。

此解決方案與 Azure 金鑰保存庫整合，可幫助您控制和管理您的金鑰保存庫訂用帳戶中的磁碟加密金鑰和密碼，同時確保虛擬機器磁碟中的所有資料會在您的 Azure 儲存體中輕鬆加密。

深入了解：

* [Windows 和 Linux IaaS VM 適用的 Azure 磁碟加密](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)
* [適用於 Linux 和 Windows 虛擬機器的 Azure 磁碟加密](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)
* [加密虛擬機器](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>虛擬機器備份
Azure 備份是可調式解決方案，可以不需成本地保護您的應用程式資料，以及將操作成本降到最低。 應用程式錯誤可能導致資料損毀，而人為錯誤可能會將 Bug 導入應用程式中。 使用 Azure 備份，您執行 Windows 與 Linux 的虛擬機器會受到保護。

深入了解：

* [何謂 Azure 備份？](../backup/backup-introduction-to-azure-backup.md)
* [Azure 備份學習路徑](https://azure.microsoft.com/documentation/learning-paths/backup/)
* [Azure 備份服務 - 常見問題集](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
組織的 BCDR 策略的其中一個重要部分是，找出在計劃中和非計劃中的中斷發生時讓企業工作負載和應用程式保持啟動並執行的方法。 Azure Site Recovery 有助於協調工作負載和應用程式的複寫、容錯移轉及復原，因此能夠在主要位置發生故障時透過次要位置來提供工作負載和應用程式。

網站復原：

* **簡化 BCDR 策略** - Site Recovery 可讓您從單一位置輕鬆處理多個商務工作負載和應用程式的複寫、容錯移轉及復原。 Site Recovery 會協調複寫和容錯移轉，但不會攔截應用程式資料或擁有任何相關資訊。
* **提供彈性的複寫** - 使用 Site Recovery，您就可以複寫 Hyper-V 虛擬機器、VMware 虛擬機器和 Windows/Linux 實體伺服器上執行的工作負載。
* **支援容錯移轉和復原** - Site Recovery 可提供測試用容錯移轉，既能支援災害復原演練，又不會影響生產環境。 您也可以執行計劃性容錯移轉，因為是預期中的中斷，所以不會遺失任何資料；或是執行非計劃性容錯移轉，以在發生非未預期的災害時將資料損失減到最少 (取決於複寫頻率)。 在容錯移轉之後，您可以容錯回復到主要站台。 Site Recovery 提供了包含指令碼和 Azure 自動化作業手冊的復原計畫，以供您自訂多層式應用程式的容錯移轉和復原。
* **消除次要資料中心** - 您可以複寫至次要內部部署站台或 Azure。 使用 Azure 做為災害復原目的地，可排除次要站台的維護成本和複雜度。 複寫的資料會儲存在 Azure 儲存體。
* **與現有 BCDR 技術整合** - Site Recovery 能夠與其他應用程式的 BCDR 功能搭配使用。 例如，您可以使用 Site Recovery 來保護公司工作負載的 SQL Server 後端。 這包括原生支援 SQL Server AlwaysOn 以管理可用性群組的容錯移轉。

深入了解：

* [什麼是 Azure Site Recovery？](../site-recovery/site-recovery-overview.md)
* [Azure Site Recovery 如何運作？](../site-recovery/site-recovery-components.md)
* [Azure Site Recovery 保護哪些工作負載？](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>虛擬網路
虛擬機器需要遠端連線。 為了支援該需求，Azure 需要虛擬機器連接到 Azure 虛擬網路。 Azure 虛擬網路是以實體 Azure 網路網狀架構為基礎所建置的邏輯建構。 每個邏輯 Azure 虛擬網路都會與其他所有 Azure 虛擬網路隔離。 此隔離可協助確保其他 Microsoft Azure 客戶無法存取您部署中的網路流量。

深入了解：

* [Azure 網路安全性概觀](security-network-overview.md)
* [虛擬網路概觀](../virtual-network/virtual-networks-overview.md)
* [網路功能與企業合作夥伴關係的案例](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>安全性原則管理和報告
Azure 資訊安全中心可協助您預防、偵測和回應威脅，並加強提供對 Azure 資源安全性的能見度及控制權。 它提供您 Azure 訂用帳戶之間的整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

Azure 資訊安全中心藉由下列方式來協助您最佳化和監視虛擬機器︰

* 提供虛擬機器 [安全性建議](../security-center/security-center-recommendations.md) ，例如套用系統更新、設定 ACL 端點、啟用反惡意程式碼、啟用網路安全性群組，以及套用磁碟加密。
* 監視您的虛擬機器的狀態

深入了解：

* [Azure 資訊安全中心簡介](../security-center/security-center-intro.md)
* [Azure 資訊安全中心常見問題集](../security-center/security-center-faq.md)
* [Azure 資訊安全中心規劃和操作](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>法規遵循
Azure 虛擬機器經過 FISMA、FedRAMP、HIPAA、PCI DSS Level 1 及其他重要規範計劃認證。 此認證可讓您自己的 Azure 應用程式更容易符合規範要求，並讓您的企業解決廣泛的國內與國際法規要求。

深入了解：

* [Microsoft 信任中心：法規遵循](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)
* [受信任的雲端：Microsoft Azure 安全性、隱私權及法規遵循](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)
