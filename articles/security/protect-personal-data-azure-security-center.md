---
title: "使用 Azure 資訊安全中心保護個人資料 | Microsoft Docs"
description: "使用 Azure 資訊安全中心保護個人資料"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 8738ffc13734437f4c61cf7be76590e5d73adf42
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---
# <a name="protect-personal-data-from-breaches-and-attacks-azure-security-center"></a>保護個人資料免受入侵和攻擊：Azure 資訊安全中心

本文將協助您了解如何使用 Azure 資訊安全中心，來保護個人資料免受入侵和攻擊。

## <a name="scenario"></a>案例 

總部設於美國的大型郵輪公司將擴展其營運，以提供地中海、波羅的海和不列顛群島的行程。 為了協助這些工作，它收購了以義大利、德國、丹麥和英國為據點的數個較小型郵輪公司。

此公司使用 Microsoft Azure 在雲端儲存公司資料。 其中包含名稱、地址、電話號碼和信用卡資訊等個人識別資訊。 它也包含類似如下的人力資源資訊：

- 位址
- 電話號碼
- 稅務識別編號
- 醫療資訊

此郵輪公司也會維護獎勵和忠誠度方案會員的大型資料庫。 公司員工會從公司的遠端辦公室存取網路，而位於全球的旅行社會存取某些公司資源。
個人資料會透過網路在這些位置與 Microsoft 資料中心之間傳輸。

## <a name="problem-statement"></a>問題陳述

此公司擔心其 Azure 資源遭受攻擊威脅。 他們想要防止未經授權的人員公開客戶和員工的個人資料。 他們需要預防和回應/補救的相關指引，以及持續監視其雲端資源安全性的有效方法。
他們需要堅強的防線來抵禦今日複雜且有組織的攻擊者。

## <a name="company-goal"></a>公司目標

公司的目標之一是透過保護資料免受威脅，來確保客戶和員工的個人資料隱私權。 其中一個目標是立即回應入侵徵兆，以緩和影響。 它需要一套方法來評定目前的安全性狀態、識別易受攻擊的設定並予以修復。

## <a name="solutions"></a>解決方案

Microsoft Azure 資訊安全中心 (ASC) 提供了整合式安全性監控和原則管理解決方案。 它提供威脅預防、偵測及回應功能，簡單易用又有效。

### <a name="prevention"></a>預防

ASC 可協助您防止入侵，方法是讓您設定安全性原則、提供 Just-In-Time 存取並實作安全性建議。

安全性原則可針對指定之訂用帳戶內的資源，定義一組建議的控制機制。 Just-In-Time 存取可用來鎖定進入 Azure VM 的流量，以減少攻擊面。 當 ASC 分析 Azure 資源的安全性狀態之後，會建立安全性建議。

#### <a name="how-do-i-set-security-policies-in-asc"></a>如何在 ASC 設定安全性原則？

您可以為每個訂用帳戶設定安全性原則。 若要修改安全性原則，您必須是該訂用帳戶的擁有者或參與者。 在 Azure 入口網站中，執行下列動作：

1. 在 ASC 儀表板中，選取 [原則]。

2. 選取您要啟用原則的訂用帳戶。

3. 選擇 [防護原則] 為每個訂用帳戶設定原則。 [從虛擬機器收集資料] 應該設定為 [開啟]。

4. 在 [防護原則] 選項中，選取 [開啟] 以啟用訂用帳戶的相關安全性建議。

![](media/protect-personal-data-azure-security-center/prevention-policy.png)

如需可啟用之每個原則建議的詳細指示和說明，請參閱[在 Azure 資訊安全中心設定安全性原則](https://docs.microsoft.com/azure/security-center/security-center-policies#set-security-policies)。

#### <a name="how-do-i-configure-just-in-time-access-jit"></a>如何設定 Just-In-Time (JIT) 存取？

啟用 JIT 時，資訊安全中心會建立 NSG 規則，藉此鎖定進入 Azure VM 的流量。 系統會鎖定選取的 VM 連接埠的輸入流量。 若要使用 JIT 存取，請執行下列動作：

1. 在 ASC 刀鋒視窗中，選取 [Just-in-Time VM 存取] 磚。

2. 選取 [建議] 索引標籤。

3. 在 [VM] 下方，選取您想要啟用的 VM。 這會讓 VM 旁邊顯示核取記號。 
4. 在 VM 上選取 [啟用 JIT]。
5. 選取 [ **儲存**]。

然後您就會看到已針對 JIT 啟用 ASC 建議的預設連接埠。 您也可以新增和設定要啟用 Just-In-Time 解決方案的新連接埠。 資訊安全中心中的 [Just-in-Time VM 存取] 磚會顯示針對 JIT 存取設定的 VM 數目。 它也會顯示過去一週所核准的存取要求數目。

![](media/protect-personal-data-azure-security-center/jit-vm.png)

如需操作說明及 Just-in-Time 存取的其他資訊，請參閱[使用 Just-In-Time 管理虛擬機器存取](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)。

#### <a name="how-do-i-implement-asc-security-recommendations"></a>如何實作 ASC 安全性建議？

當資訊安全中心識別潛在的安全性弱點時，它會建立建議。 這些建議會引導您完成設定所需控制項的程序。 
1. 在 ASC 儀表板中，選取 [建議] 磚。

2. 檢視以表格格式顯示的建議，其中每一行代表一個建議。

3. 若要篩選建議，請選取 [篩選] 並選取您想要查看的嚴重性和狀態值。

4. 若要關閉不適用的建議，您可以按一下滑鼠右鍵，然後選取 [關閉]。

5. 評估應該先套用的建議。

6. 依優先順序套用建議。

如需可能的建議清單及如何套用每個建議的逐步解說，請參閱[管理 Azure 資訊安全中心的安全性建議](https://docs.microsoft.com/azure/security-center/security-center-recommendations)。

### <a name="detection-and-response"></a>偵測和回應

偵測會和回應是一體的，因為您想要在偵測到威脅之後盡快回應。
ASC 威脅偵測的運作方式如下：從您的 Azure 資源、網路及已連線的合作夥伴解決方案自動收集安全性資訊。 ASC 可以在攻擊者發行新的和日益複雜的攻擊時，快速地更新其偵測演算法。 如需 ASC 威脅偵測運作方式的詳細資訊，請參閱 [Azure 資訊安全中心的偵測功能](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)。

#### <a name="how-do-i-manage-and-respond-to-security-alerts"></a>如何管理和回應安全性警示？

資訊安全中心會顯示依優先順序排列的安全性警示清單，以及快速調查問題所需的資訊。 資訊安全中心也包含如何修復攻擊的建議。 若要管理您的安全性警示，請執行下列動作：

1. 在 ASC 儀表板中，選取 [安全性警示] 磚。 這會顯示每個警示的詳細資料。

2. 若要依日期、狀態和嚴重性篩選警示，請選取 [篩選]，然後選取您要查看的值。

3. 若要回應警示，請選取該警示並檢閱資訊，然後選取遭到攻擊的資源。

4. 在 [描述] 欄位中，您會看到的詳細資料，包括建議的補救。

![](media/protect-personal-data-azure-security-center/security-alerts.png)

如需回應安全性警示的詳細指示，請參閱[管理及回應 Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)。

此公司可以使用 [Azure 記錄整合](https://aka.ms/AzLog)來整合 ASC 警示與自己的 SIEM 解決方案，以取得調查安全性警示的進一步協助。

#### <a name="how-do-i-manage-security-incidents"></a>如何管理安全性事件？

在 ASC 中，安全性事件是符合攻擊鏈模式之資源的所有警示彙總。 事件會顯示相關警示的清單，以讓您取得所引發的每個警示的詳細資訊。 事件會出現在 [安全性警示] 磚和刀鋒視窗中。

若要檢閱和管理安全性事件，請執行下列動作：

1. 選取 [安全性警示] 磚。 如果偵測到安全性事件，它便會出現在安全性警示圖表下方。 它會有不同於其他警示的圖示。

2. 選取事件以查看有關此安全性事件的更多詳細資料。 其他詳細資料包含其完整描述、其嚴重性、其目前狀態、受攻擊的資源、事件的補救步驟，以及包含在此事件中的警示。

您可以篩選僅查看**事件**、僅查看**警示**或查看**兩者**。

#### <a name="how-do-i-access-the-threat-intelligence-report"></a>如何存取威脅情報報告？

ASC 會分析來自多個來源的資訊以識別威脅。 為了協助事件回應小組調查和修復威脅，資訊安全中心包含威脅情報報告，報告中含有偵測到之威脅的相關資訊。

資訊安全中心有三種威脅報告，不同攻擊會提供不同的報告。
所提供的報告如下︰

- 群組活動報告︰提供攻擊者、其目標和策略的深入探討。

- 活動報告︰著重說明特定攻擊活動的詳細資料。

- 威脅摘要報告︰涵蓋先前兩種報告的所有項目。

這種類型的資訊在事件回應程序期間非常有用，因為該期間會持續進行調查，以了解攻擊來源、攻擊者的動機，以及日後該如何緩和此問題。

1. 若要存取威脅情報報告，請執行下列動作：

2. 在 ASC 儀表板中，選取 [安全性警示] 磚。

3. 選取您要取得更多資訊的安全性警示。

4. 在 [報告] 欄位中，按一下威脅情報報告的連結。

5. 這會開啟 PDF 檔案以供您下載。

![](media/protect-personal-data-azure-security-center/security-alerts-suspicious-process.png)

如需 ASC 威脅情報報告的其他資訊，請參閱 [Azure 資訊安全中心威脅情報報告](https://docs.microsoft.com/azure/security-center/security-center-threat-report)。

### <a name="assessment"></a>評量

為了協助測試、評量和評估安全性狀態，ASC 提供與 Qualys 雲端代理程式整合的弱點評量，這是其虛擬機器建議元件的一部分。

Qualys 代理程式會向 Qualys 管理平台回報弱點資料，然後將弱點和狀況監控資料傳回 ASC。 新增弱點評量解決方案的建議會顯示在 ASC 儀表板的 [建議] 刀鋒視窗中。

在目標 VM 上安裝弱點評估解決方案後，資訊安全中心會掃描 VM 以偵測並找出系統和應用程式弱點。 偵測到的問題會顯示在 [虛擬機器建議]  選項之下。

#### <a name="how-do-i-implement-a-vulnerability-assessment-solution"></a>如何實作弱點評量解決方案？ 

如果虛擬機器尚未部署整合式弱點評量解決方案，資訊安全中心會建議予以安裝。

1. 在 ASC 儀表板的 [建議] 刀鋒視窗中，選取 [新增弱點評量解決方案]。

2. 選取您要安裝弱點評量解決方案的 VM。

3. 按一下 [Install on [number of] VMs] \(安裝在 [數目] 部 VM 上)。

4. 在 Azure Marketplace 中選取合作夥伴解決方案，或在 [使用現有解決方案] 下選取 [Qualys]。

5. 您可以在 [合作夥伴解決方案] 刀鋒視窗中，開啟或關閉自動更新設定。

如需如何實作弱點評量解決方案的進一步指示，請參閱 [Azure 資訊安全中心的弱點評量](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)。

## <a name="next-steps"></a>後續步驟

- [Azure 資訊安全中心快速入門指南](https://docs.microsoft.com/azure/security-center/security-center-get-started)

- [Azure 資訊安全中心簡介](https://docs.microsoft.com/azure/security-center/security-center-intro)

- [整合 Azure 資訊安全中心警示與 Azure 記錄整合](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration)

- [Boost Azure Security Center with Integrated Vulnerability Assessment](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/16/boost-azure-security-center-with-integrated-vulnerability-assessment/) (利用整合式弱點評量增強 Azure 資訊安全中心)

