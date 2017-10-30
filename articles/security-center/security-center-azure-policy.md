---
title: "Azure 資訊安全中心安全性原則與 Azure 原則的整合 | Microsoft Docs"
description: "本文件可協助您設定 Azure 資訊安全中心安全性原則與 Azure 原則的整合。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 5e07cd6891a5ab04012f819b5f6b9379312e530d
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2017
---
# <a name="set-security-policies-in-security-center-powered-by-azure-policy"></a>在資訊安全中心設定安全性原則 (採用 Azure 原則)
這份文件透過帶領您完成執行這項工作的必要步驟，協助您設定資訊安全中心的安全性原則 (採用 Azure 原則)。 


## <a name="how-security-policies-work"></a>安全性原則的運作方式為何？
資訊安全中心會為每個 Azure 訂用帳戶自動建立預設安全性原則。 您可以在資訊安全中心編輯原則，或使用 [Azure 原則](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)來建立新的原則定義、在整個管理群組 (可以代表整個組織、其中的營業單位等等) 指派原則，並且監視原則合規性。

> [!NOTE]
> Azure 原則是以有限預覽形式提供。 按一下[這裡](https://aka.ms/getpolicy)即可加入。 如需 Azure 原則的詳細資訊，請閱讀[建立和管理原則來強制執行合規性](http://docs.microsoft.com/en-us/azure/azure-policy/create-manage-policy)。

## <a name="edit-security-policies"></a>編輯安全性原則
您可以在資訊安全中心內，編輯每個 Azure 訂用帳戶的預設安全性原則。 若要修改安全性原則，您必須是該訂用帳戶或所在管理群組的擁有者、參與者或安全性系統管理員。 登入 Azure 入口網站，並依照後續步驟在資訊安全中心內檢視安全性原則：

1. 在 [資訊安全中心] 儀表板的 [一般] 底下，按一下 [安全性原則]。
2. 選取您要啟用安全性原則的訂用帳戶。

    ![原則管理](./media/security-center-policies/security-center-policies-fig10.png)

3. 在 [原則元件] 區段中，按一下 [安全性原則]。

    ![原則元件](./media/security-center-policies/security-center-policies-fig12.png)

4. 這是透過 Azure 原則指派給資訊安全中心的預設原則。 您可以刪除 [原則和參數] 底下的項目，也可以新增 [可用的選項] 底下的其他原則定義。 若要這樣做，只須按一下定義名稱旁的加號。

    ![原則定義](./media/security-center-policies/security-center-policies-fig11.png)

5. 如果您想要更詳細的原則說明，請按一下該原則，隨即會開啟另一個含有詳細資料的頁面，以及具有[原則定義](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy/#policy-definition-structure)結構的 JSON 程式碼：

    ![Json](./media/security-center-policies/security-center-policies-fig14.png)

6. 編輯完成時，請按一下 [儲存]。


## <a name="available-security-policy-definitions"></a>可用的安全性原則定義

使用下表作為參考，以了解預設安全性原則中可用的原則定義： 

| 原則 | 當狀態為開啟時 |
| --- | --- |
| 系統更新 |從 Windows Update 或 Windows Server Update Services (WSUS) 擷取每天可用的安全性和重大更新清單。 擷取的清單取決於為該虛擬機器設定的服務，並建議要套用的遺漏更新。 對於 Linux 系統，此原則會使用散發套件提供的套件管理系統，來判斷哪些套件有可用的更新。 它也會檢查來自 [Azure 雲端服務](../cloud-services/cloud-services-how-to-configure.md) 虛擬機器的安全性和重大更新。 |
| 作業系統弱點 |每天分析作業系統組態，以判斷讓虛擬機器容易遭受攻擊的問題。 此原則還會建議可解決這些弱點的組態變更。 如需受監視之特定組態的詳細資訊，請參閱 [建議的基準清單](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) 。 (目前，未完全支援 Windows Server 2016)。 |
| 端點保護 |建議為所有 Windows 虛擬機器佈建端點保護，以協助識別和移除病毒、間諜軟體及其他惡意軟體。 |
| 磁碟加密 |建立在所有虛擬機器中啟用磁碟加密以增強待用資料保護。 |
| 網路安全性群組 |建議設定 [網路安全性群組](../virtual-network/virtual-networks-nsg.md) 來控制具有公用端點之 VM 的輸入與輸出流量。 除非另有指定，否則所有虛擬機器網路介面都會繼承為子網路設定的網路安全性群組。 除了檢查是否已設定網路安全性群組之外，此原則還會評估輸入安全性規則來識別可允許連入流量的規則。 |
| Web 應用程式防火牆 |建議您，當下列其中一項為真時，在虛擬機器上佈建 web 應用程式防火牆︰ </br></br>會使用[執行個體層級公開 IP](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP)，且會設定相關聯網路安全性群組的輸入安全性規則，以允許存取連接埠 80/443。</br></br>會使用負載平衡 IP，且會設定相關聯的負載平衡和輸入網路位址轉譯 (NAT) 規則，以允許存取連接埠 80/443。 如需詳細資料，請參閱 [Azure Resource Manager 的負載平衡器支援](../load-balancer/load-balancer-arm.md)。 |
| 新一代防火牆 |提供超越 Azure 內建網路安全性群組的網路保護。 資訊安全中心會探索建議使用新一代防火牆的部署，並可讓您佈建虛擬應用裝置。 |
| SQL 稽核與威脅偵測 |建議針對法規遵循、進階威脅偵測及調查用途，啟用 Azure 資料庫的存取稽核。 |
| SQL 加密 |建議為您的 Azure SQL Database、關聯的備份及交易記錄檔啟用待用期加密。 您的資料即使遭到入侵，也無法被讀取。 |
| 弱點評估 |建議在 VM 上安裝弱點評估解決方案。 |
| 儲存體加密 |這項功能目前可用於 Azure Blob 和檔案。 啟用儲存體服務加密之後，只會加密新的資料，而此儲存體帳戶中任何現有的檔案都會保持未加密狀態。 |
| JIT 網路存取 |啟用 Just-In-Time 時，資訊安全中心會建立 NSG 規則，藉此鎖定進入 Azure VM 的流量。 系統應會鎖定選取的 VM 連接埠的輸入流量。 如需詳細資訊，請參閱[使用 Just-In-Time 管理虛擬機器存取](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)。 |


## <a name="next-step"></a>後續步驟
在本文件中，您了解到如何在資訊安全中心設定安全性原則。 如要深入了解資訊安全中心，請參閱下列主題：

* [Azure 資訊安全中心規劃和操作指南](security-center-planning-and-operations-guide.md)。 了解如何規劃及了解採用 Azure 資訊安全中心的設計考量。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md)。 了解如何監視 Azure 資源的健全狀況。
* [管理及回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)。 了解如何管理和回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md)。 了解如何監視合作夥伴解決方案的健全狀態。
* [Azure 資訊安全中心常見問題集](security-center-faq.md)。 尋找有關使用服務的常見問題。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)。 尋找有關 Azure 安全性與相容性的部落格文章。
