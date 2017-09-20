---
title: "Azure 資訊安全中心簡介 | Microsoft Docs"
description: "了解 Azure 資訊安全中心、其主要功能及運作方式。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: terrylan
ms.translationtype: Human Translation
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: 8951167213da6ab5341c1ca420353ec625ef5424
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---
# <a name="introduction-to-azure-security-center"></a>Azure 資訊安全中心簡介
了解 Azure 資訊安全中心、其主要功能及運作方式。

> [!NOTE]
> 從 2017 年 6 月初開始，資訊安全中心會使用 Microsoft Monitoring Agent 來收集和儲存資料。 若要深入了解，請參閱 [Azure 資訊安全中心平台移轉](security-center-platform-migration.md)。 本文中的資訊說明轉換至 Microsoft Monitoring Agent 後的資訊安全中心功能。
>
>

## <a name="what-is-azure-security-center"></a>什麼是 Azure 資訊安全中心？
 資訊安全中心利用加強對您 Azure 資源的能見度及安全性控制權，來協助您預防、偵測及回應威脅。 它提供您 Azure 訂用帳戶之間的整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

## <a name="key-capabilities"></a>主要功能
 資訊安全中心，提供 Azure 內建的威脅預防、偵測及回應功能，簡單易用又有效。 主要功能：

| 階段 | 功能 |
| --- | --- |
| 防止 |監視 Azure 資源的安全性狀態 |
| 防止 | 根據您的公司安全性需求、您使用的應用程式類型和資料的機密性，定義您的 Azure 訂用帳戶原則 |
| 防止 | 使用原則導向的安全性建議，引導服務擁有者完成需要控制項的實作程序 |
| 防止 | 快速部署 Microsoft 和第三方的安全性服務和應用裝置 |
| 偵測 |自動收集和分析下列來源的安全性資料：Azure 資源、網路，以及反惡意程式碼程式和防火牆等夥伴解決方案 |
| 偵測 | 使用來自 Microsoft 產品與服務、Microsoft 數位犯罪防治中心 (DCU)、Microsoft 安全性回應中心 (MSRC) 以及外部摘要的全球威脅情報 |
| 偵測 | 套用進階分析，包括機器學習服務和行為分析 |
| 回應 |依優先順序提供安全性事件/警示 |
| 回應 | 提供對攻擊和受影響資源來源的深入見解 |
| 回應 | 建議停止目前攻擊及協助避免未來攻擊的方法 |

## <a name="introductory-walkthrough"></a>逐步解說介紹

> [!NOTE]
> 本文件將使用範例部署來介紹服務。 本文件不是一份逐步解說指南。
>
>

 您可以從 [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)存取資訊安全中心。 [登入入口網站](https://portal.azure.com)。 在主要入口網站功能表下，捲動至 [資訊安全中心] 選項，或選取之前釘選到入口網站儀表板的 [資訊安全中心] 圖格。

![Azure 入口網站中的安全性磚][1]

從資訊安全中心，您可以設定安全性原則、監視安全性組態，並檢視安全性警示。

### <a name="security-policies"></a>安全性原則
您可以根據公司的安全性需求來定義 Azure 訂用帳戶的原則。 您也可以根據您使用的應用程式類型或每個訂用帳戶中的資料機密性來修改這些原則。 例如，用於開發或測試的資源與用於實際執行應用程式的資源，兩者的安全性需求可能不同。 同樣地，具有規範資料 (例如 PII) 的應用程式可能需要較高層級的安全性。

> [!NOTE]
> 若要修改安全性原則，您必須是安全性系統管理員或是訂用帳戶的擁有者或參與者。 如需角色與資訊安全中心所允許動作的詳細資訊，請參閱 [Azure 資訊安全中心的權限](security-center-permissions.md)。
>
>

在 [資訊安全中心] 刀鋒視窗上，選取 [原則] 圖格來取得訂用帳戶和資源群組清單。   

![[資訊安全中心] 刀鋒視窗][2]

在 [安全性原則] 刀鋒視窗上，選取訂用帳戶來檢視原則詳細資料。

**資料收集**啟用安全性原則的資料收集。 若加以啟用可以：

* 每日掃描所有支援的虛擬機器 (VM)，取得安全性監視和建議。
* 收集安全性事件，供分析和偵測威脅所用。

> [!NOTE]
> 資料收集是在訂用帳戶等級設定。
>
>

選取 [防護原則] 以開啟 [防護原則] 刀鋒視窗。 [顯示建議] 可讓您選擇想要監視的安全性控制項，以及您想要根據訂用帳戶內的資源安全性需求而看見的建議。

### <a name="security-recommendations"></a>安全性建議
 資訊安全中心會分析 Azure 資源的安全性狀態，以識別潛在的安全性弱點。 建議清單會引導您完成設定所需控制項的程序。 範例包括：

* 佈建反惡意程式碼，以協助識別及移除惡意軟體
* 設定網路安全性群組和規則，以控制對 VM 的流量
* 佈建 Web 應用程式防火牆，以協助抵禦以 Web 應用程式為目標的攻擊
* 部署遺漏的系統更新
* 處理不符合建議基準的作業系統組態

按一下 [建議] 圖格以取得建議清單。 按一下每個建議，檢視其他資訊或採取行動以解決問題。

![Azure 資訊安全中心的安全性建議][5]

### <a name="security-state-of-azure-resources"></a>Azure 資源的安全性狀態
儀表板的 [保護] 區段會依資源類型顯示環境的整體安全性狀態，包括 VM、Web 應用程式和其他資源。   

選取 [保護] 之下的資源類型來檢視詳細資訊，包括一份任何已識別潛在安全性弱點的清單。 (下面的範例已選取 [計算])。

![資源健康狀態磚][6]

### <a name="security-alerts"></a>安全性警示
 資訊安全中心會自動收集、分析和整合下列來源的記錄檔資料：Azure 資源、網路，以及反惡意程式碼程式和防火牆等夥伴解決方案。 偵測到威脅時，會建立安全性警示。 偵測範例包括：

* 已受到危害的 VM 與已知的惡意 IP 位址進行通訊
* 使用 Windows 錯誤報告偵測到進階的惡意程式碼
* 針對 VM 的暴力密碼破解攻擊
* 來自整合式反惡意程式碼程式和防火牆的安全性警示

按一下 [安全性警示]  圖格會顯示已排定優先順序的警示清單。

![安全性警示][7]

選取警示可顯示攻擊的詳細資訊以及矯正方法建議。

![安全性警示詳細資料][8]

### <a name="partner-solutions"></a>合作夥伴解決方案
[合作夥伴解決方案] 圖格可監視與 Azure 訂用帳戶整合之合作夥伴解決方案的安全性狀態，讓您一目了然。 資訊安全中心會顯示來自解決方案的警示。

選取 [合作夥伴解決方案] 圖格。 隨即開啟一個刀鋒視窗，其中顯示所有已連接的合作夥伴解決方案清單。

![合作夥伴解決方案][9]

## <a name="get-started"></a>開始使用
若要開始使用資訊安全中心，您需要 Microsoft Azure 訂用帳戶。 資訊安全中心已經由 Azure 訂用帳戶啟用。 如果您沒有訂用帳戶，可以註冊[免費試用](https://azure.microsoft.com/pricing/free-trial/)。

 您可以從 [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)存取資訊安全中心。 如需詳細資訊，請參閱[入口網站文件](https://azure.microsoft.com/documentation/services/azure-portal/)。

[開始使用 Azure 資訊安全中心](security-center-get-started.md)會快速引導您認識資訊安全中心的安全性監視和原則管理元件。

## <a name="next-steps"></a>後續步驟
本文介紹了資訊安全中心、其重要功能和如何開始進行。 若要深入了解，請參閱下列資源：

* [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md) — 了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
* [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md) — 了解建議如何協助保護您的 Azure 資源。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) — 了解如何監視 Azure 資源的健全狀況。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) — 了解如何管理與回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) — 了解如何監視合作夥伴解決方案的健全狀況。
- [Azure 資訊安全中心資料安全性](security-center-data-security.md) - 了解資訊安全中心如何管理及保護其中的資料。
* [Azure 資訊安全中心常見問題集](security-center-faq.md) — 尋找有關使用服務的常見問題。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) — 取得最新的 Azure 安全性新聞和資訊。

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.png
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png
[9]: ./media/security-center-intro/partner-solutions.png

