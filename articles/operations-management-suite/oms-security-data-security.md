---
title: "Operations Management Suite 安全性和稽核解決方案資料安全性 | Microsoft Docs"
description: "本文件說明如何在 Operations Management Suite 安全性和稽核解決方案中管理和保護資料。"
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 9cdf7deb-2a30-4672-b89f-71179ee8326a
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
ms.openlocfilehash: 3b6327b1f5150f32afd71639f32c55d823f1d1f0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="operations-management-suite-security-and-audit-solution-data-security"></a>Operations Management Suite 安全性和稽核解決方案資料安全性
為了協助客戶預防、偵測及回應威脅，[Operations Management Suite (OMS) 安全性和稽核解決方案](operations-management-suite-overview.md)會收集和處理資源相關資料，其中包括︰

* 安全性事件記錄檔
* Windows 事件追蹤 (ETW) 事件
* AppLocker 稽核事件
* Windows 防火牆記錄檔
* Advanced Threat Analytics 事件
* 基準評估結果
* 反惡意程式碼評估結果
* 更新/修補評估結果
* 在代理程式上明確啟用的 Syslog 串流

我們會強烈承諾，以保護此資料的隱私權和安全性。 Microsoft 從撰寫程式碼到運作服務均遵守嚴格的規範與安全性指導方針。
本文說明如何在 OMS 安全性和稽核解決方案中管理和保護資料。

## <a name="data-sources"></a>資料來源
OMS 安全性和稽核解決方案會分析來自虛擬機器和 OM 代理程式安裝所在實體電腦的資料。 OMS 安全性和稽核解決方案可以收集有關安全性事件的組態資訊，例如 Windows 事件、稽核記錄檔、IIS 記錄檔和 syslog 訊息。 這類資料的範例包括︰作業系統類型和版本、執行中程序、電腦名稱、IP 位址、已登入的使用者和租用戶識別碼。  

## <a name="data-protection"></a>資料保護
**資料隔離**：資料會以邏輯方式分開保存在服務的每個元件上。 每個組織加上標記的所有資料。 這項標記作業在整個資料生命週期持續發生，它會強制執行服務的每個層級。 

**資料存取**︰為了提供安全性建議及調查潛在的安全性威脅，Microsoft 人員可以存取服務所收集或分析的資訊。 我們會遵守 [Microsoft Online Services Terms](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) 和[隱私權聲明](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)，其中陳述 Microsoft 不會使用客戶資料或從中衍生資訊作為任何廣告或類似的商業用途。 為了提供安全性建議及調查潛在的安全性威脅，Microsoft 人員可以存取服務所收集或分析的資訊。 我們會視需要只使用客戶資料為您提供 Azure 服務，包括與提供這些服務相容的用途。 您可保有自有資料的所有權限。

**資料使用**：Microsoft 使用可見於多個租用戶的模式和威脅智慧來加強我們的防護和偵測功能；我們會根據[隱私權聲明](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)中所述的隱私權承諾進行。

> [!NOTE]
> 資料位置是在工作區建立期間設定於 OMS 工作區層級，這是初始 OMS 安全性和稽核設定程序的一部分。
> 
> 

## <a name="see-also"></a>另請參閱
在本文件中，您已了解如何在 OMS 中管理和保護資料。 若要深入了解 OMS 安全性和稽核解決方案，請參閱：

* [Operations Management Suite (OMS) 概觀](operations-management-suite-overview.md)
* [在 Operations Management Suite 安全性和稽核內監視及回應安全性警示](oms-security-responding-alerts.md)
* [在 Operations Management Suite 安全性和稽核解決方案內監視資源](oms-security-monitoring-resources.md)

