---
title: "Azure 資訊安全中心平台移轉 | Microsoft Docs"
description: "本文件說明 Azure 資訊安全中心資料收集方式的一些變更。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 80246b00-bdb8-4bbc-af54-06b7d12acf58
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: yurid
ms.openlocfilehash: 5ddf71dcd9c5a2b03e3b1441d8c9b4d91b6bad12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-platform-migration"></a>Azure 資訊安全中心平台移轉

從 2017 年 6 月初開始，Azure 資訊安全中心對安全性資料的收集和儲存方式推出重要變更。  這些變更揭開一些新功能，例如能夠輕鬆搜尋安全性資料，且更密切與其他 Azure 管理和監視服務配合。

> [!NOTE]
> 平台移轉應該不會影響您的生產資源，您這端不需要採取任何動作。


## <a name="whats-happening-during-this-platform-migration"></a>在此平台移轉期間發生什麼事？

資訊安全中心先前使用 Azure Monitoring Agent 從您的 VM 收集安全性資料。 這包括用來識別弱點之安全性設定，以及用來偵測威脅之安全性事件的相關資訊。 此資料會儲存於 Azure 中您的儲存體帳戶。

此後，資訊安全中心會使用 Microsoft Monitoring Agent (這是 Operations Management Suite 和 Log Analytics 服務所用的相同代理程式)。 從這個代理程式收集的資料會儲存在與您的 Azure 訂用帳戶相關聯的現有 Log Analytics [工作區](../log-analytics/log-analytics-manage-access.md)或新的工作區中 (將 VM 的地理位置納入考量)。

## <a name="agent"></a>代理程式

在轉換過程中，Microsoft Monitoring Agent (適用於 [Windows](../log-analytics/log-analytics-windows-agents.md) 或 [Linux](../log-analytics/log-analytics-linux-agents.md)) 會安裝在目前從中收集資料的所有 Azure VM 上。  如果 VM 已經安裝 Microsoft Monitoring Agent，則資訊安全中心會利用目前安裝的代理程式。

一段時間 (通常數天) 之後，這兩個代理程式會並排執行，以確保順利轉換且不會遺失任何資料。 這可讓 Microsoft 在停止使用目前的管線之前，驗證新的資料管線可以運作。 一旦驗證，將從您的 VM 移除 Azure Monitoring Agent。 您不需要執行任何工作。 所有客戶都已移轉後，系統會以電子郵件通知您。
 
不建議在移轉期間手動解除安裝 Azure Monitoring Agent，因為可能會造成安全性資料的落差。 如果您需要進一步協助，請洽詢 [Microsoft 客戶服務與支援中心](https://support.microsoft.com/contactus/)。 

適用於 Windows 的 Microsoft Monitoring Agent 需要使用 TCP 通訊埠 443，如需詳細資訊，請閱讀 [Azure 安全性中心疑難排解指南](security-center-troubleshooting-guide.md)。


> [!NOTE] 
> 因為其他 Azure 管理和監視服務可能會使用 Microsoft Monitoring Agent，所以當您在資訊安全中心關閉資料收集時，並不會自動解除安裝代理程式。 不過，您可以視需要手動解除安裝代理程式。

## <a name="workspace"></a>工作區

如先前所述，從 Microsoft Monitoring Agent (代表資訊安全中心) 收集的資料會儲存在與您的 Azure 訂用帳戶相關聯的現有 Log Analytics 工作區或新的工作區中 (將 VM 的地理位置納入考量)。

在 Azure 入口網站中，您可以瀏覽以查看 Log Analytics 工作區清單，包括資訊安全中心所建立的任何工作區。 將會針對新的工作區建立相關的資源群組。 兩者都會遵循此命名慣例：

- 工作區：*DefaultWorkspace-[subscription-ID]-[geo]*
- 資源群組：*DefaultResouceGroup-[geo]* 
 
若為資訊安全中心所建立的工作區，資料會保留 30 天。 若為現有工作區，保留是以工作區定價層為基礎。

> [!NOTE]
> 資訊安全中心先前收集的資料會保留在儲存體帳戶中。 完成移轉之後，您可以刪除這些儲存體帳戶。

### <a name="oms-security-solution"></a>OMS 安全性解決方案 

對於未安裝 OMS 安全性解決方案的現有客戶，Microsoft 會安裝在其工作區上，但是僅以 Azure VM 為目標。 請勿解除安裝此解決方案，因為如果從 OMS 管理主控台進行此動作，就沒有任何自動補救方式。


## <a name="other-updates"></a>其他更新

為了搭配平台移轉，我們推出一些額外的次要更新：

- 將支援其他作業系統版本。 請參閱[這裡](security-center-faq.md#virtual-machines)的清單。
- 作業系統弱點清單將會展開。 請參閱[這裡](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335)的清單。
- [價格](https://azure.microsoft.com/pricing/details/security-center/)將會按小時 (先前按日) 計算，這可讓某些客戶節省成本。
- 採用標準定價層的客戶需要並自動啟用資料收集。
- Azure 資訊安全中心將開始探索未透過 Azure 擴充功能部署的反惡意程式碼解決方案。 將會首先探索 Symantec Endpoint Protection 和 Defender for Windows 2016。
- 防護原則和通知只能在「訂用帳戶」層級設定，但價格仍然可以在「資源群組」層級設定

