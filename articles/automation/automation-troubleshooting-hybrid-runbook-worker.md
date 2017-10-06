---
title: "針對 Azure 自動化混合式 Runbook 背景工作角色進行疑難排解 | Microsoft Docs"
description: "說明 Azure 自動化中最常見混合式 Runbook 背景工作角色問題的徵狀、原因和解決方法。"
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 02c6606e-8924-4328-a196-45630c2255e9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: 9d1ceda5a072f494651a751a25a8ccf66e4c72ef
ms.contentlocale: zh-tw
ms.lasthandoff: 07/28/2017

---

# <a name="troubleshooting-tips-for-hybrid-runbook-worker"></a>混合式 Runbook 背景工作角色的疑難排解提示

本文可協助您針對使用自動化混合式 Runbook 背景工作角色時可能遇到的錯誤進行疑難排解，並建議可能的解決方法。

## <a name="a-runbook-job-terminates-with-a-status-of-suspended"></a>Runbook 作業在暫止狀態下終止

Runbook 在嘗試執行三次後會馬上暫止。 在某些情況下，Runbook 可能會因為受到干擾而無法順利完成，而相關的錯誤訊息卻不含任何指出原因的詳細資訊。 本文提供 Hybrid Runbook Worker Runbook 執行失敗之相關問題的疑難排解步驟。

若本文中未提及您的 Azure 問題，請造訪 [MSDN 及 Stack Overflow 上的 Azure 論壇](https://azure.microsoft.com/support/forums/)。 您可以在這些論壇上張貼您的問題，或將問題貼到 Twitter [@AzureSupport上的](https://twitter.com/AzureSupport) 。 此外，您也可以選取 [Azure 支援](https://azure.microsoft.com/support/options/)網站上的 [取得支援]，來提出 Azure 支援要求。

### <a name="symptom"></a>徵狀
Runbook 執行失敗且傳回的錯誤是「作業動作 'Activate' 無法執行，因為處理序意外停止。 作業動作已嘗試 3 次」。

這個錯誤的可能原因有幾個，包括︰ 

1. Hybrid Worker 位於 Proxy 或防火牆後方
2. 混合式背景工作角色執行所在的電腦未滿足最低硬體[需求](automation-offering-get-started.md#hybrid-runbook-worker)  
3. Runbook 無法使用本機資源驗證

#### <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>原因 1︰Hybrid Runbook Worker 位在 Proxy 或防火牆後方
Hybrid Runbook Worker 執行所在的電腦位於防火牆或 Proxy 伺服器後方，因此輸出網路存取未獲得允許或設定錯誤。

#### <a name="solution"></a>方案
確認電腦可透過連接埠 443 輸出存取 *.azure-automation.net。 

#### <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>原因 2︰電腦未滿足最低硬體需求
執行 Hybrid Runbook Worker 的電腦應滿足最低硬體需求，您才能指派它來裝載這項功能。 否則，根據其他背景處理序的資源使用率和 Runbook 在執行期間造成的競爭，電腦可能會變得過度使用，導致 Runbook 作業延遲或逾時。 

#### <a name="solution"></a>方案
先確認指派來執行 Hybrid Runbook Worker 功能的電腦滿足最低硬體需求。  如果滿足最低硬體需求，請監視 CPU 和記憶體使用率，判斷 Hybrid Runbook Worker 處理序之效能和 Windows 之間是否有任何相互關聯。  如果有記憶體或 CPU 壓力，這表示電腦可能需要升級或增加額外處理器，或增加記憶體來解決資源瓶頸及解決錯誤。 您也可以選擇其他可支援最低需求，也能在工作負載需求指出需要增加資源時擴充的計算資源。         

#### <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>原因 3：Runbook 無法使用本機資源驗證

#### <a name="solution"></a>方案
查閱 **Microsoft-SMA** 事件記錄檔，找出描述為「Win32 處理序結束，代碼為 [4294967295]」的對應事件。  此錯誤的原因是您尚未在 Runbook 中設定驗證，或尚未指定混合式背景工作角色群組的執行身分認證。  請檢閱 [Runbook 權限](automation-hrw-run-runbooks.md#runbook-permissions) ，確認 Runbook 的驗證設定正確無誤。  

## <a name="next-steps"></a>後續步驟

如需針對自動化中的其他問題進行疑難排解的協助，請參閱[針對 Azure 自動化進行疑難排解](automation-troubleshooting-automation-errors.md) 
