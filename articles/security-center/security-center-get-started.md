---
title: "Azure 資訊安全中心快速入門指南 | Microsoft Docs"
description: "此文章帶領您認識安全性監視和原則管理元件，並連結至下一個步驟，協助您快速開始使用 Azure 資訊安全中心。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: c28f92af96f31d1c386cf072f83fc142b9a7f588
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-quick-start-guide"></a>Azure 資訊安全中心快速入門指南
此文章帶領您認識資訊安全中心的安全性監視和原則管理元件，協助您快速開始使用 Azure 資訊安全中心。

## <a name="prerequisites"></a>必要條件
若要開始使用資訊安全中心，您必須有 Microsoft Azure 訂用帳戶。 如果您沒有訂用帳戶，可以註冊[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。

所有 Azure 訂用帳戶都會自動啟用資訊安全中心的免費層，並提供安全性原則、持續的安全性評估，以及可操作的安全性建議，以幫助您保護 Azure 資源。

您可以從 [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)存取資訊安全中心。 若要深入了解 Azure 入口網站，請參閱[入口網站文件](https://azure.microsoft.com/documentation/services/azure-portal/)。

## <a name="permissions"></a>權限
在「資訊安全中心」中，當您獲指派為資源所屬的訂用帳戶或資源群組「擁有者」、「參與者」或「讀取者」角色時，您只會看到與資源相關的項目。 請參閱[Azure 資訊安全中心的權限](security-center-permissions.md)以深入了解角色與資訊安全中心允許的動作。

## <a name="data-collection"></a>資料收集
資訊安全中心會從您的 Azure 虛擬機器 (VM) 和非 Azure 電腦收集資料，以監視安全性漏洞與威脅。 資料是使用 Microsoft Monitoring Agent 收集而得，收集的方式是讀取機器的各種安全性相關組態和事件記錄檔，並將資料複製到工作區進行分析。 資訊安全中心會在所有現有支援的 Azure 虛擬機器和任何新建立的 VM 上佈建 Microsoft Monitoring Agent。 若要深入了解資料收集的運作方式，請參閱[啟用資料收集](security-center-enable-data-collection.md)。

強烈建議使用自動佈建，而且資訊安全中心標準層的訂閱需要啟用自動佈建。 停用自動佈建會限制對資源的安全性監視。

如需免費和標準定價層的詳細資訊，請參閱[資訊安全中心定價](security-center-pricing.md)。

下列步驟說明如何存取和使用安全中心的元件。

> [!NOTE]
> 此文章將使用範例部署來介紹服務。 此文章不是逐步指南。
>
>

## <a name="access-security-center"></a>存取資訊安全中心
在入口網站中，執行下列步驟來存取資訊安全中心：

1. 在 [Microsoft Azure] 功能表中，選取 [資訊安全中心]。

   ![Azure 功能表][1]
2. 如果您是第一次存取資訊安全中心，[歡迎] 刀鋒視窗隨即開啟。 選取 [啟動資訊安全中心] 以開啟 [資訊安全中心]。
   ![歡迎使用畫面][10]
3. 從 [歡迎使用] 刀鋒視窗啟動資訊安全中心，或從 [Microsoft Azure] 功能表中選取 [資訊安全中心] 之後，[資訊安全中心] 隨即開啟。 為方便日後快速存取 [資訊安全中心] 刀鋒視窗，請選取 [將刀鋒視窗釘選到儀表板] 選項 (右上方)。
   ![將刀鋒視窗釘選到儀表板選項][2]

## <a name="use-security-center"></a>使用資訊安全中心
您可以設定 Azure 訂用帳戶和資源群組的安全性原則。 一同設定您訂用帳戶的安全性原則：

1. 在 [資訊安全中心] 主功能表上，選取 [安全性原則]。
2. 在 [資訊安全中心 - 安全性原則] 下方，選取訂用帳戶。
3. 在 [安全性原則 - 資料收集] 下方，開啟 [自動佈建]。 資訊安全中心會在所有現有支援的 Azure 虛擬機器和任何新建立的 VM 上佈建 Microsoft Monitoring Agent。

    ![安全性原則][12]

4. 在 [安全性原則] 刀鋒視窗中，選取原則元件 [安全性原則]。

     ![安全性原則][11]

5. 在 [顯示下列項目的建議] 下方，開啟您想要作為安全性原則一部分的建議。 範例：

   * 將 [系統更新] 設定為 [開啟] 會掃描所有支援的 VM，尋找遺漏的 OS 更新。
   * 將 [OS 弱點] 設定為 [開啟] 會掃描所有支援的 VM，以識別任何可能讓 VM 更容易受到攻擊的 OS 設定。

6. 選取 [ **儲存**]。

### <a name="view-recommendations"></a>檢視建議
1. 返回 [資訊安全中心] 刀鋒視窗，然後選取 [建議] 圖格。 資訊安全中心會定期分析 Azure 資源的安全性狀態。 當資訊安全中心識別潛在的安全性弱點時，它會在 [建議] 刀鋒視窗上顯示建議。
   ![Azure 資訊安全中心的建議][5]
2. 選取 [建議] 刀鋒視窗上的建議，檢視詳細資訊及/或採取行動以解決問題。

### <a name="view-the-security-state-of-your-resources"></a>檢視資源的健康狀態
1. 返回 [資訊安全中心] 刀鋒視窗。 儀表板的 [防護] 區段包含 VM、網路、資料和應用程式的安全性狀態指標。
2. 選取 [計算] 以檢視更多資訊。 [計算] 刀鋒視窗隨即開啟，並顯示三個索引標籤：

  - **概觀** - 包含監視和 VM 的建議。
  - **VM 與電腦**：列出所有 VM 與電腦目前的安全性狀態。
  - **雲端服務** - 列出資訊安全中心監視的 Web 和背景工作角色清單。

    ![計算安全性健康情況][6]

3. 在 [概觀] 索引標籤上選取建議，以檢視詳細資訊及/或採取行動來設定必要的控制項。
4. 在 [VM 與電腦] 索引標籤上，選取資源以檢視其他詳細資料。

### <a name="view-security-alerts"></a>檢視安全性警示
1. 返回 [資訊安全中心] 刀鋒視窗，然後選取 [安全性警示] 圖格。 [安全性警示] 刀鋒視窗會開啟並顯示警示清單。 資訊安全中心透過對安全性記錄檔和網路活動的分析，產生警示。 包含來自整合式合作夥伴解決方案的警示。
   ![Azure 資訊安全中心的安全性警示][7]

2. 選取警示以檢視其他資訊。 在此範例中，選取 [Modified system binary discovered in dump filter] \(在傾印篩選中探索到修改過的系統二進位檔\)。 這會開啟刀鋒視窗，提供有關警示的其他詳細資料。
   ![Azure 資訊安全中心的安全性警示詳細資料][8]

### <a name="view-the-health-of-your-partner-solutions"></a>檢視合作夥伴解決方案的健全狀況
1. 返回 [資訊安全中心]  刀鋒視窗。 [安全性解決方案] 圖格可讓您監視與您的 Azure 訂用帳戶整合之合作夥伴解決方案的健康狀態，一目了然。
2. 選取 [安全性解決方案] 圖格。 隨即開啟一個刀鋒視窗，並顯示已連接到資訊安全中心的合作夥伴解決方案清單。
   ![][9]
3. 選取合作夥伴解決方案。 隨即開啟一個刀鋒視窗，並顯示合作夥伴解決方案的狀態和解決方案相關聯的資源。 選取 [解決方案主控台]  以開啟此解決方案的合作夥伴管理體驗。

   ![合作夥伴解決方案][13]

## <a name="next-steps"></a>後續步驟
此文章介紹了資訊安全中心的安全性監視和原則管理元件。 現在，您已熟悉資訊安全中心，請嘗試下列步驟︰

* 設定您 Azure 訂用帳戶的安全性原則，請參閱[在 Azure 資訊安全中心設定安全性原則](security-center-policies.md)。
* 使用資訊安全中心的建議來協助保護您的 Azure 資源，請參閱[管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)。
* 檢閱與管理您目前的安全性警示，請參閱[管理及回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)。
- 深入了解與合作夥伴整合以加強整體安全性的資訊，請參閱[合作夥伴和解決方案整合](security-center-partner-integration.md)。
- 了解資訊安全中心如何管理及保護其中的資料，請參閱 [Azure 資訊安全中心資料安全性](security-center-data-security.md)。
* 深入了解 資訊安全中心[標準層](security-center-pricing.md)隨附的[進階威脅偵測功能](security-center-detection-capabilities.md)。 標準層的前 60 天免費。
* 如果您對使用資訊安全中心有問題，請參閱 [Azure 資訊安全中心常見問題集](security-center-faq.md)。

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png
[11]: ./media/security-center-get-started/show-recommendations-for.png
[12]: ./media/security-center-get-started/automatic-provisioning.png
[13]: ./media/security-center-get-started/partner-solutions-detail.png
