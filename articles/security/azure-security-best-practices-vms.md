---
title: "Azure 虛擬機器安全性最佳作法 | Microsoft Docs"
description: "本文提供各種可在位於 Azure 的虛擬機器中使用的安全性最佳作法。"
services: security
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 5e757abe-16f6-41d5-b1be-e647100036d8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2017
ms.author: yurid
ms.openlocfilehash: c8a920a0523cb4737e6bbca7e49d0b9e2c942565
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="best-practices-for-azure-vm-security"></a>Azure VM 安全性的最佳作法

在大部分的基礎結構即服務 (IaaS) 案例中，[Azure 虛擬機器 (VM)](https://docs.microsoft.com/en-us/azure/virtual-machines/) 對於使用雲端運算的組織來說是主要工作負載。 在組織想要慢慢地將工作負載移轉至雲端的[混合式案例](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx)中，這特別顯得重要。 在這種情況下，請遵循 [IaaS 的一般安全性考量](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)，並將安全性最佳作法套用到您所有的 VM。

這篇文章討論各種 VM 安全性最佳作法，每個都衍生自我們的客戶和我們自己使用 VM 的直接體驗。

最佳作法是根據共識的意見，並使用目前的 Azure 平台功能及功能集。 由於意見和技術會隨著時間改變，我們計劃定期更新本文章以反映這些變更。

針對每個最佳做法，本文說明︰

* 最佳作法是什麼。
* 為什麼啟用它是個不錯的主意。
* 如何學習啟用它。
* 如果您無法啟用它，可能會發生什麼事。
* 最佳作法的可能替代方案。

本文會檢查以下的 VM 安全性最佳作法︰

* VM 驗證和存取控制
* VM 可用性和網路存取
* 強制執行加密以保護 VM 中待用資料的安全
* 管理您的 VM 更新
* 管理您的 VM 安全性狀態
* 監視 VM 效能

## <a name="vm-authentication-and-access-control"></a>VM 驗證和存取控制

保護 VM 的第一個步驟是確保只有已獲授權的使用者能夠設定新的 VM。 您可以使用 [Azure Resource Manager 原則](../azure-resource-manager/resource-manager-policy.md)來建立組織中資源的慣例、建立自訂原則，並將這些原則套用到資源，例如[資源群組](../azure-resource-manager/resource-group-overview.md)。

屬於資源群組的 VM 自然會繼承其原則。 雖然建議這種方法來管理 VM，您也可以使用[角色型存取控制 (RBAC)](../active-directory/role-based-access-control-configure.md)來控制存取個別 VM 原則。

當您啟用 Resource Manager 原則和 RBAC 來控制 VM 存取時，有助於改善 VM 的整體安全性。 我們建議將具有相同生命週期的 VM 合併到相同的資源群組。 藉由使用資源群組，您可以部署、監視和彙總資源的計費成本。 若要讓使用者能夠存取和設定 VM，請使用[最低權限的方法](https://technet.microsoft.com/en-us/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models)。 而且當您指派權限給使用者時，計劃使用下列內建的 Azure 角色︰

- [虛擬機器參與者](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)：可以管理 VM，但無法管理他們連接的虛擬網路或儲存體帳戶。
- [傳統虛擬機器參與者](../active-directory/role-based-access-built-in-roles.md#classic-virtual-machine-contributor)：可以管理使用傳統的部署模型建立的 VM，但無法管理 VM 連接的虛擬網路或儲存體帳戶。
- [安全性管理員](../active-directory/role-based-access-built-in-roles.md#security-manager)：可以管理安全性元件、安全性原則及 VM。
- [DevTest Labs 使用者](../active-directory/role-based-access-built-in-roles.md#devtest-labs-user)：可以檢視所有項目，並連接、啟動、重新啟動和關閉 VM。

請勿讓系統管理員共用帳戶和密碼，且請勿在多個使用者帳戶或服務上重複使用密碼，特別是用於社交媒體或其他非系統管理活動的密碼。 在理想情況下，您應該使用 [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) 範本，安全地設定您的 VM。 您可以藉由使用這種方法，來強化部署選項，並強制執行整個部署的安全性設定。

未利用諸如 RBAC 等功能來強制執行資料存取控制的組織，可能會對其使用者授與超過所需的權限。 不適當的使用者存取某些資料可能會直接危害該資料。

## <a name="vm-availability-and-network-access"></a>VM 可用性和網路存取

如果您的 VM 會執行需要具備高可用性的重要應用程式，則強烈建議您使用多個 VM。 如需更佳的可用性，請在[可用性設定組](../virtual-machines/windows/tutorial-availability-sets.md)中至少建立兩個 VM。

[Azure Load Balancer](../load-balancer/load-balancer-overview.md) 也會要求已負載平衡的 VM 屬於同一個可用性設定組。 如果需要從網際網路存取這些 VM，您必須設定[網際網路面向的負載平衡器](../load-balancer/load-balancer-internet-overview.md)。

將 VM 公開至網際網路時，務必[使用網路安全性群組來控制網路流量 (NSG)](../virtual-network/virtual-networks-nsg.md)。 由於 NSG 可以套用至子網路，依子網路群組您的資源並將 NSG 套用至子網路，即可減少 NSG 的數量。 目的是要建立網路隔離層，您可以藉由在 Azure 中適當設定[網路安全性](../best-practices-network-security.md)功能來完成此動作。

您也可以從 Azure 資訊安全中心使用 just-in-time (JIT) VM 存取功能，來控制人員以及某人可遠端存取特定 VM 多久的時間。

未對網際網路面向的 VM 強制執行網路存取限制的組織會暴露於安全性風險中，例如遠端桌面通訊協定 (RDP) 暴力密碼破解攻擊。

## <a name="protect-data-at-rest-in-your-vms-by-enforcing-encryption"></a>強制執行加密以保護 VM 中待用資料的安全

[待用資料加密 (英文)](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) 是達到資料隱私性、合規性及資料主權的必要步驟。 [Azure 磁碟加密](../security/azure-security-disk-encryption.md)可讓 IT 系統管理員加密 Windows 和 Linux IaaS VM 磁碟。 磁碟加密結合業界標準的 Windows BitLocker 功能和 Linux dm-crypt 功能，為 OS 和資料磁碟提供磁碟區加密。

您可以套用磁碟加密來協助保護資料安全，以符合您的組織安全性及合規性需求。 組織應該考慮使用加密，協助降低與未經授權存取資料相關的風險。 我們也建議您在將機密資料寫入它們之前先加密您的磁碟機。

確定會將 VM 資料磁碟區加密，以保護您 Azure 儲存體帳戶中待用資料磁碟區的安全。 使用 [Azure Key Vault](https://azure.microsoft.com/en-us/documentation/articles/key-vault-whatis/) 來保護加密金鑰和密碼。

未強制執行資料加密的組織會更容易遭受資料完整性問題的攻擊。 例如，未經授權或惡意使用者可能會竊取遭入侵帳戶中的資料，或未經授權存取以 ClearFormat 編碼的資料。 除了承擔這些風險外，公司必須證明他們是十分用心遵守業界法規，並使用正確的安全性控制項來增強其資料安全性。

如需深入了解磁碟加密，請參閱 [Windows 和 Linux IaaS VM Preview 適用的 Azure 磁碟加密](azure-security-disk-encryption.md)。


## <a name="manage-your-vm-updates"></a>管理您的 VM 更新

因為 Azure VM 像是所有內部部署 VM，主要做為使用者管理，因此 Azure 不會將 Windows 更新推送給它們。 不過，建議您讓自動安裝 Windows Update 設定保持啟用狀態。 另一個選項是在其他 VM 或內部部署上部署 [Windows Server Update Services (WSUS)](https://technet.microsoft.com/windowsserver/bb332157.aspx) 或其他適當的更新管理產品。 WSUS 和 Windows Update 都會讓 VM 保持最新狀態。 我們也建議您使用掃描產品，以確認所有 IaaS VM 都是最新狀態。

Azure 提供的原廠映像會定期更新，以包含最新一輪的 Windows Update。 不過，不保證映像在部署期間是最新的。 遵循公用版本可能會稍微落後 (最多幾個禮拜)。 檢查並安裝所有 Windows Update，應該是每個部署的第一個步驟。 在部署來自您或您自己的程式庫之映像時，套用此量值特別重要。 預設會自動更新 Azure Marketplace 中提供的映像。

未強制執行軟體更新原則的組織會更容易遭受利用已知的先前已修正弱點威脅的攻擊。 除了承擔這些威脅外，公司必須證明他們是十分用心遵守業界法規，並使用正確的安全性控制項來協助確保他們位於雲端上的工作負載安全性。

請務必強調傳統資料中心的軟體更新最佳作法和 Azure IaaS 有許多相似之處。 因此，建議您評估目前的軟體更新原則來包含 VM。

## <a name="manage-your-vm-security-posture"></a>管理您的 VM 安全性狀態

網路威脅日新月異，因此保護您的 VM 需要豐富的監視功能，以便能夠快速偵測到威脅、防止未經授權存取您的資源、觸發警示，以及減少誤判。 這類工作負載的安全性狀態包含 VM 的所有安全性層面，從更新管理到保護網路存取。

若要監視 [Windows](../security-center/security-center-virtual-machine.md) 和 [Linux VM](../security-center/security-center-linux-virtual-machine.md) 的安全狀態，請使用 [Azure 資訊安全中心](../security-center/security-center-intro.md)。 在 Azure 資訊安全中心，請利用下列功能以保護您的 VM：

* 套用具有建議設定規則的 OS 安全性設定
* 找出並下載系統安全性和可能遺失的重大更新
* 部署端點反惡意程式碼保護建議
* 驗證磁碟加密
* 評估和修復弱點
* 偵測威脅

資訊安全中心可以主動監視威脅，並將可能的威脅公開於 [安全性警示] 之下。 相互關聯的威脅將彙總於稱為「安全性事件」的單一檢視中。

若要了解資訊安全中心如何在您位於 Azure 的 VM 中協助您識別潛在的威脅，請觀賞下列影片：

<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

未針對其 VM 強制執行強式安全性的組織仍不會知道未經授權的使用者可能嘗試規避已建立的安全性控制。

## <a name="monitor-vm-performance"></a>監視 VM 效能

當 VM 程序比所應消耗更多的資源時，可能會產生資源不當使用的問題。 VM 的效能問題可能會導致服務中斷，違反可用性的安全性原則。 因此，請務必了解，不只需要在發生問題時以反應性方式監視 VM 存取，還必須主動在正常作業期間如測量完成基準效能。

藉由分析 [Azure 診斷記錄檔](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/)，您可以監視 VM 資源，並找出可能影響效能和可用性的潛在問題。 Azure 診斷擴充功能會在以 Windows 為基礎的 VM 上提供監視和診斷功能。 您可以將此擴充功能納入為 Azure Resource Manager [範本](../virtual-machines/windows/extensions-diagnostics-template.md)的一部分，藉以啟用這些功能。

您也可以使用 [Azure 監視器](../monitoring-and-diagnostics/monitoring-overview-metrics.md)來查看您的資源健康狀態。

未監視 VM 效能的組織無法決定效能模式中的特定變更是正常或不正常。 若 VM 比一般消耗更多資源，這類異常狀況可能表示來自外部資源的潛在攻擊，或是在此虛擬機器中執行的入侵程序。
