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
ms.openlocfilehash: 977f7c075005efb826e862ee582864b0f6efab38
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="integrate-security-center-security-policies-with-azure-policy"></a>整合資訊安全中心安全性原則與 Azure 原則
本文件可協助您設定 Azure 資訊安全中心安全性原則 (採用 Azure 原則技術)。 

## <a name="how-security-policies-work"></a>安全性原則的運作方式
資訊安全中心會為每個 Azure 訂用帳戶自動建立預設安全性原則。 您可以編輯資訊安全中心的原則，或使用 [Azure 原則](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)執行下列動作：
* 建立新原則定義。
* 將原則指派給代表整個組織或組織內業務單位的各個管理群組。
* 監視原則相容性。

> [!NOTE]
> Azure 原則是以有限預覽形式提供。 若要加入，請移至[註冊 Azure 原則](https://aka.ms/getpolicy)。 如需 Azure 原則的詳細資訊，請參閱[建立和管理原則來強制執行合規性](http://docs.microsoft.com/en-us/azure/azure-policy/create-manage-policy)。

## <a name="edit-security-policies"></a>編輯安全性原則
您可以在資訊安全中心內，編輯每個 Azure 訂用帳戶的預設安全性原則。 若要修改安全性原則，您必須是該訂用帳戶或所在管理群組的擁有者、參與者或安全性系統管理員。 若要檢視資訊安全中心的安全性原則，請執行下列作業：

1. 登入 Azure 入口網站。

2. 在 [資訊安全中心] 儀表板的 [一般] 底下，選取 [安全性原則]。

    ![原則管理窗格](./media/security-center-policies/security-center-policies-fig10.png)

3. 選取您要啟用安全性原則的訂用帳戶。  

4. 在 [原則元件] 區段中，選取 [安全性原則]。  
    [基本資料] 視窗隨即開啟。

    ![原則元件](./media/security-center-policies/security-center-policies-fig12.png)

5. 若要刪除原則定義，請在 [原則和參數] 下您要刪除的定義旁，選取 [刪除]。

6. 按一下 [儲存] 。  
    [可用定義] 視窗隨即開啟，並顯示透過 Azure 原則指派給資訊安全中心的預設原則。 

7. (選擇性) 在 [可用定義] 視窗中，執行下列其中一個動作：

    * 若要新增原則定義，請選取定義旁邊的加號 (+)。

    ![可用原則定義](./media/security-center-policies/security-center-policies-fig11.png)

    * 選取以取得原則的詳細說明。  
    定義的 [預覽] 視窗隨即開啟。 其中會顯示定義說明，以及提供[原則定義](../azure-policy/policy-definition.md)結構的 JSON 程式碼連結。

    ![定義的預覽視窗](./media/security-center-policies/security-center-policies-fig14.png)

7. 編輯完成時，請選取 [儲存]。

## <a name="available-security-policy-definitions"></a>可用的安全性原則定義

若要了解預設安全性原則中可用的原則定義，請參考下表： 

| 原則 | 原則啟用後的功能 |
| --- | --- |
| 系統更新 |從 Windows Update 或 Windows Server Update Services (WSUS) 擷取每天可用的安全性和重大更新清單。 擷取的清單取決於為您虛擬機器設定的服務，並建議要套用的遺漏更新。 對於 Linux 系統，此原則會使用散發套件提供的套件管理系統，來判斷哪些套件有可用的更新。 它也會檢查來自 [Azure 雲端服務](../cloud-services/cloud-services-how-to-configure.md) 虛擬機器的安全性和重大更新。 |
| 作業系統弱點 |每天分析作業系統組態，以判斷讓虛擬機器容易遭受攻擊的問題。 此原則還會建議可解決這些弱點的組態變更。 如需受監視之特定組態的詳細資訊，請參閱[建議的基準清單](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)。 (目前，未完全支援 Windows Server 2016)。 |
| 端點保護 |建議為所有 Windows 虛擬機器 (VM) 設定端點保護，以協助識別和移除病毒、間諜軟體及其他惡意軟體。 |
| 磁碟加密 |建立在所有虛擬機器中啟用磁碟加密以增強待用資料保護。 |
| 網路安全性群組 |建議設定 [網路安全性群組](../virtual-network/virtual-networks-nsg.md) 來控制具有公用端點之 VM 的輸入與輸出流量。 除非另有指定，否則所有虛擬機器網路介面都會繼承為子網路設定的網路安全性群組。 除了檢查是否已設定網路安全性群組之外，此原則還會評估輸入安全性規則來識別可允許連入流量的規則。 |
| Web 應用程式防火牆 |建議您，當下列其中一項為真時，在虛擬機器上設定 Web 應用程式防火牆︰ <ul><li>會使用[執行個體層級公開 IP](../virtual-network/virtual-networks-instance-level-public-ip.md)，且會設定相關聯網路安全性群組的輸入安全性規則，以允許存取連接埠 80/443。</li><li>會使用負載平衡 IP，且會設定相關聯的負載平衡和輸入網路位址轉譯 (NAT) 規則，以允許存取連接埠 80/443。 如需詳細資料，請參閱 [Azure Resource Manager 的負載平衡器支援](../load-balancer/load-balancer-arm.md)。</li> |
| 新一代防火牆 |提供超越 Azure 內建網路安全性群組的網路保護。 資訊安全中心會探索建議使用新一代防火牆的部署，然後您就可以設定虛擬設備。 |
| SQL 稽核與威脅偵測 |建議針對法規遵循、進階威脅偵測及調查用途，啟用 Azure 資料庫的存取稽核。 |
| SQL 加密 |建議為您的 Azure SQL Database、關聯的備份及交易記錄檔啟用待用期加密。 您的資料即使遭到入侵，也無法被讀取。 |
| 弱點評估 |建議在 VM 上安裝弱點評估解決方案。 |
| 儲存體加密 |這項功能目前可用於 Azure Blob 儲存體和 Azure 檔案。 啟用儲存體服務加密之後，只會加密新的資料，而此儲存體帳戶中任何現有的檔案都會保持未加密狀態。 |
| JIT 網路存取 |啟用 Just-In-Time 網路存取時，資訊安全中心會建立網路安全性群組規則，藉此鎖定進入 Azure VM 的流量。 系統應會鎖定選取的 VM 連接埠的輸入流量。 如需詳細資訊，請參閱[使用 Just-In-Time 管理虛擬機器存取](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)。 |


## <a name="next-steps"></a>後續步驟
在本文中，您了解到如何在資訊安全中心設定安全性原則。 如要深入了解資訊安全中心，請參閱下列文章：

* [Azure 資訊安全中心規劃和操作指南](security-center-planning-and-operations-guide.md)：了解如何規劃及了解 Azure 資訊安全中心的設計考量。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md)：了解如何監視 Azure 資源的健全狀況。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)：了解如何管理與回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md)：了解如何監視合作夥伴解決方案的健全狀況。
* [Azure 資訊安全中心常見問題集](security-center-faq.md)：取得使用服務常見問題的解答。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)：尋找有關 Azure 安全性與相容性的部落格文章。
