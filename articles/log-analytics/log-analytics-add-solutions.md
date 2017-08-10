---
title: "新增 Azure Log Analytics 管理解決方案 | Microsoft Docs"
description: "Operations Management Suite (OMS) / Log Analytics 管理解決方案是邏輯、視覺效果和資料擷取規則的集合，可提供針對特定問題領域進行計量的樞紐分析。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: ee0191c63f12ae32708095362b8028a61ca731cc
ms.contentlocale: zh-tw
ms.lasthandoff: 08/04/2017

---
# <a name="add-azure-log-analytics-management-solutions-to-your-workspace"></a>將 Azure Log Analytics 管理解決方案新增至您的工作區

Log Analytics 管理解決方案是**邏輯**、**視覺效果**和**資料擷取規則**的集合，可提供針對特定問題領域進行計量的樞紐分析。 本文列出 Log Analytics 所支援的管理解決方案，並說明如何使用 Azure 入口網站新增和移除工作區。 您也可以在 OMS 入口網站中使用方案庫新增解決方案。

管理解決方案可允許更深入的探討，以：

* 協助調查並更快解決操作問題、
* 收集並相互關聯各種類型的機器資料、
* 協助您主動處理解決方案所公開的活動。

> [!NOTE]
> Log Analytics 包含記錄檔搜尋功能，因此您不需要安裝管理解決方案即可啟用。 不過，您可以對工作區新增管理解決方案，以取得資料視覺效果、建議的搜尋和深入資訊。

透過本文，您可以使用 Azure 入口網站 Marketplace 對工作區新增管理解決方案。 加入方案後，系統會從基礎結構中的伺服器收集資料，然後再傳送到 OMS 服務。 OMS 服務的處理時間通常需要幾分鐘到一個小時。 待服務處理完資料後，您可以在 OMS 中予以檢視。

當您不再需要管理解決方案時，您可以輕易地將它移除。 當您移除管理解決方案時，就不會將其資料傳送至 OMS。 如果您處於免費定價層，則移除解決方案可減少資料的使用量，協助您保持在每日的資料配額之下。

## <a name="view-available-management-solutions"></a>檢視可用的管理解決方案

Azure Marketplace 包含 [Log Analytics 的管理解決方案](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions)清單。

按一下每個解決方案底部的 [立刻取得] 連結，即可從 Azure marketplace 安裝管理解決方案。

## <a name="add-a-management-solution"></a>新增管理解決方案
1. 如果您尚未這麼做，請使用 Azure 訂用帳戶登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [新增] 刀鋒視窗的 [Marketplace] 底下，選取 [監視 + 管理]。
3. 在 [監視 + 管理] 刀鋒視窗中，按一下 [檢視全部]。  
    ![監視 + 管理刀鋒視窗](./media/log-analytics-add-solutions/monitoring-management-blade.png)  
4. 在 [管理解決方案] 的右邊，按一下 [更多資訊]。
5. 在 [管理解決方案] 刀鋒視窗中，選取要新增至工作區的管理解決方案。  
    ![監視 + 管理刀鋒視窗](./media/log-analytics-add-solutions/management-solutions.png)  
6. 在 [管理解決方案] 刀鋒視窗中，檢閱管理解決方案的相關資訊，然後按一下 [建立]。
7. 在 [管理解決方案名稱] 刀鋒視窗中，選取要與管理解決方案相關聯的工作區。
8. (選擇性) 變更 Azure 訂用帳戶、資源群組和位置的工作區設定。 您也可以選擇 [自動化選項]。 按一下 [建立] 。  
    ![解決方案工作區](./media/log-analytics-add-solutions/solution-workspace.png)  
9. 若要開始使用您已新增至工作區的管理解決方案，請瀏覽至 [Log Analytics] > [訂用帳戶] > [工作區名稱] > [概觀]。 隨即會顯示管理解決方案的新圖格。 按一下圖格來加以開啟，並在解決方案的資料收集好後開始使用解決方案。

## <a name="remove-a-management-solution"></a>移除管理解決方案

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽至 [Log Analytics] > [訂用帳戶] > [工作區名稱]，然後在 [工作區名稱] 刀鋒視窗中，按一下 [解決方案]。
2. 在管理解決方案的清單中，選取您要移除的解決方案。
3. 在工作區的 [解決方案] 刀鋒視窗中，按一下 [刪除]。  
    ![刪除解決方案](./media/log-analytics-add-solutions/solution-delete.png)  
4. 在確認對話方塊中，按一下 [是]。

## <a name="offers-and-pricing-tiers"></a>優惠和定價層

下表列出適用於每個 Operations Management Suite 供應項目的管理解決方案。
此表格也會列出適用於每個管理解決方案的定價層。
下表中的所有解決方案都可從 Azure 入口網站以及 Log Analytics 入口網站的解決方案庫中取得。

| 管理解決方案                                                                       | 提供項目                                                                     | 定價層<sup>1</sup>                                                 | 注意事項 |
| ---                                                                                       | ---                                                                       | ---                                                                                                       | ---   |
| [活動 Log Analytics](log-analytics-activity.md)                                                                   | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | 可免費使用資料 90 天<br>資料不受免費層限制 |
| [AD 評估](log-analytics-ad-assessment.md)                                           | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | |
| [AD 複寫狀態](log-analytics-ad-replication-status.md)                           | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | 不適用於從 Azure 入口網站/Marketplace 新增。 |
| 代理程式健康狀態                                                                              | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | 資料不受免費層限制<br> 不適用於從 Azure 入口網站/Marketplace 新增。 |
| [警示管理](log-analytics-solution-alert-management.md)                            | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | 不適用於從 Azure 入口網站/Marketplace 新增。 |
| Application Insights 連接器 <br>(預覽)                                              | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | |
| 自動化混合式背景工作角色                                                                  | <ul><li>自動化和控制</li></ul>                                  | 免費<br> 每個&nbsp;節點&nbsp;(OMS)                                                                         | 要求您將 Log Analytics 工作區連結到自動化帳戶 |
| [Azure 應用程式閘道分析](log-analytics-azure-networking-analytics.md)    | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | |
| [Azure 網路安全性群組分析](log-analytics-azure-networking-analytics.md)     | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | |
| [Azure SQL Analytics (預覽)](log-analytics-azure-sql.md)                                                       | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br>每個&nbsp;節點&nbsp;(OMS)                                                                          | 要求您將 Log Analytics 工作區連結到自動化帳戶|
| 備份                                                                                    | <ul><li>深入解析與分析</li></ul>                                   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)                                                                       | 需要傳統的備份保存庫。<br> 不適用於從 Azure 入口網站/Marketplace 新增。 |
| 容量和效能 <br>(預覽)                                                    | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | |
| [變更追蹤](log-analytics-change-tracking.md)                                       | <ul><li>自動化和控制</li></ul>                                  | 免費<br> 每個&nbsp;節點&nbsp;(OMS)                                                                         | 要求您將 Log Analytics 工作區連結到自動化帳戶 |
| [容器](log-analytics-containers.md)                                                 | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | |
| [IT 服務管理連接器](log-analytics-itsmc-overview.md)<br>(預覽)                                               | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 每個&nbsp;節點&nbsp;(OMS)     | |
| HDInsight HBase 監視 <br>(預覽)                                                  | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | |
| [金鑰保存庫分析](log-analytics-azure-key-vault.md)                   | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | |
| Logic Apps B2B                    | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | 不適用於從 Azure 入口網站/Marketplace 新增。 |
| [惡意程式碼評估](log-analytics-malware.md)                                            | <ul><li>安全性與法規遵循</li></ul>                                 | 免費<br> 獨立<br>每個&nbsp;節點&nbsp;(OMS)                                                                           | 如果您在 2017 年 6 月 19 日以後新增安全性與合規性解決方案，則[每個節點都會計費](https://azure.microsoft.com/pricing/details/security-compliance/)，無論工作區定價層為何。 前 60 天免費。  |
| [網路效能監視器](log-analytics-network-performance-monitor.md) <br>  | <ul><li>深入解析與分析</li></ul>                                   | 免費<br> 每個&nbsp;節點&nbsp;(OMS)                                                                         | |
| Office 365 分析 <br>(預覽)                                                        | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | |
| [安全性和稽核](../operations-management-suite/oms-security-getting-started.md)      | <ul><li>安全性&nbsp;和&nbsp;合規性</li></ul>                       | 免費<br> 獨立<br>每個&nbsp;節點&nbsp;(OMS)                                                                           | 收集安全性事件記錄檔需要此解決方案<br>如果您在 2017 年 6 月 19 日以後新增安全性與合規性解決方案，則[每個節點都會計費](https://azure.microsoft.com/pricing/details/security-compliance/)，無論工作區定價層為何。 前 60 天免費。 |
| [Service Fabric 分析](log-analytics-service-fabric.md) <br>(預覽)                 | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | |
| [服務對應](../operations-management-suite/operations-management-suite-service-map.md) <br>(預覽)| <ul><li>深入解析與分析</li></ul>                       | 免費<br> 每個&nbsp;節點&nbsp;(OMS)                                                                         | 適用於美國東部、西歐和美國中西部    |
| Site Recovery                                                                             | <ul><li>深入解析與分析</li></ul>                                   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)                                                                       | 需要傳統的 Site Recovery 保存庫。<br> 不適用於從 Azure 入口網站/Marketplace 新增。 |
| [SQL 評估](log-analytics-sql-assessment.md)                                         | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | |
| 於下班時間開始/停止 VM<br>(預覽)                                              | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 每個&nbsp;節點&nbsp;(OMS)                                                                         | 要求您將 Log Analytics 工作區連結到自動化帳戶 |
| [SurfaceHub](log-analytics-surface-hubs.md)                                               | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | 不適用於從 Azure 入口網站/Marketplace 新增。 |
| [System Center Operations Manager Assessment](log-analytics-scom-assessment.md) <br>(預覽) | <ul><li>深入解析與分析</li><li>Log Analytics</li></ul>         | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | |
| 更新管理                                                                         | <ul><li>自動化和控制</li></ul>                                  | 免費<br> 每個&nbsp;節點&nbsp;(OMS)                                                                         | 要求您將 Log Analytics 工作區連結到自動化帳戶 |
| [更新合規性](https://technet.microsoft.com/en-us/itpro/windows/manage/update-compliance-get-started) <br>(預覽)                                                             | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | 免費的資料或節點<br>資料不受免費層限制。<br> 不適用於從 Azure 入口網站/Marketplace 新增。 |
| [升級整備程度](https://technet.microsoft.com/itpro/windows/deploy/manage-windows-upgrades-with-upgrade-readiness)                                                            | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | 免費的資料或節點<br>資料不受免費層限制。<br> 不適用於從 Azure 入口網站/Marketplace 新增。 |
| [VMware 監視](log-analytics-vmware.md) <br>(預覽)                                | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 標準<br> 進階&nbsp;(OMS)<br> 每個&nbsp;GB&nbsp;(獨立)<br> 每個&nbsp;節點&nbsp;(OMS)   | |
| Wire Data 2.0<br>(預覽)                                                                | <ul><li>深入解析與分析</li></ul>                                   | 免費<br> 每個&nbsp;節點&nbsp;(OMS)                                                                         | 適用於美國東部、西歐和美國中西部 |

<sup>1</sup> *標準*和*進階 (OMS)* 定價層只適用於已在 2016 年 9 月 21 日之前建立 Log Analytics 工作區的客戶。

### <a name="community-provided-management-solutions"></a>社群提供的管理解決方案

社群提供的解決方案都是來自 [Azure 範本庫](https://azure.microsoft.com/resources/templates/?term=Per&nbsp;Node&nbsp;(OMS))或直接由作者提供。

| 管理解決方案               | 提供項目                                                                     | 定價層                         | 注意事項 |
| ---                               | ---                                                                       | ---                                   | ---   |
| 所有社群提供的解決方案  | <ul><li>深入解析&nbsp;與&nbsp;分析</li><li>Log Analytics</li></ul>   | 免費<br> 每個&nbsp;節點&nbsp;(OMS)     |   要求您將 Log Analytics 工作區連結到自動化帳戶 |




## <a name="data-collection-details"></a>資料收集詳細資料
下列表格顯示資料收集方法，以及有關 Log Analytics 管理解決方案和資料來源的資料是如何收集的其他詳細資料。 這些表格會依解決方案優惠 (等同於[訂用帳戶定價層](https://go.microsoft.com/fwlink/?linkid=827926)) 來分類。 活動 Log Analytics 解決方案可免費供所有定價層使用。

Log Analytics Windows 代理程式和 System Center Operations Manager 代理程式基本上是一樣的。 Windows 代理程式還包含其他功能，可讓它連接到 OMS 工作區，並透過 Proxy 路由傳送。 如果您使用 Operations Manager 代理程式，則必須做為與 OMS 通訊的目標 OMS 代理程式。 此表格中的 Operations Manager 代理程式是連接到 Operations Manager 的 OMS 代理程式。 如需將現有 Operations Manager 環境連接到 OMS 的相關資訊，請參閱[將 Operations Manager 連接到 Log Analytics](log-analytics-om-agents.md)。

> [!NOTE]
> 您使用的代理程式類型決定資料如何傳送至 OMS，情況如下︰
> - 您可以使用 Windows 代理程式，或使用已連接 Operations Manager 的 OMS 代理程式。
> - 需要 Operations Manager 時，一律會使用 Operations Manager 管理群組將解決方案的 Operations Manager 代理程式資料傳送至 OMS。 此外，需要 Operations Manager 時，解決方案只會使用 Operations Manager 代理程式。
> - 不需要 Operations Manager 且表格顯示使用管理群組將 Operations Manager 代理程式資料傳送至 OMS 時，一律會使用管理群組將 Operations Manager 代理程式資料傳送至 OMS。 Windows 代理程式會略過管理群組，直接將其資料傳送至 OMS。
> - 不使用管理群組來傳送 Operations Manager 代理程式資料時，則會直接將資料傳送至 OMS - 略過管理群組。

### <a name="insight--analytics--log-analytics"></a>深入解析與分析 / Log Analytics

| 管理解決方案 | 平台 | Microsoft Monitoring Agent | Operations Manager 代理程式 | Azure 儲存體 | 是否需要 Operations Manager？ | 透過管理群組傳送的 Operations Manager 代理程式資料 | 收集頻率 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 活動 Log Analytics | Azure | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 與通知同時 |
| AD 評估 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |7 天 |
| AD 複寫狀態 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |5 天 |
| 代理程式健康狀態 | Windows 和 Linux | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | 1 分鐘 |
| 警示管理 (Nagios) |Linux |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |與抵達同時 |
| 警示管理 (Zabbix) |Linux |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |1 分鐘 |
| 警示管理 (Operations Manager) |Windows |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |3 分鐘 |
| Application Insights 連接器 (預覽) | Azure | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 與通知同時 |
| Azure 應用程式閘道分析 | Azure | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 與通知同時 |
| Azure 網路安全性群組分析 | Azure | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 與通知同時 |
| Azure SQL Analytics (預覽) |Windows |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 10 分鐘 |
| 產能管理 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |與抵達同時 |
| 容器 | Windows 和 Linux | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 3 分鐘 |
| 金鑰保存庫分析 |Windows |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |與通知同時 |
| 網路效能監視器 | Windows | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | TCP 會每 5 秒交握一次，而資料會每 3 分鐘傳送一次 |
| Office 365 分析 (預覽) |Windows |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |與通知同時 |
| Service Fabric 分析 |Windows |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |5 分鐘 |
| 服務對應 | Windows 和 Linux | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 15 秒 |
| SQL 評估 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |7 天 |
| Surface Hub |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |與抵達同時 |
| System Center Operations Manager 評定 (預覽) | Windows | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | 7 天 |
| 升級分析 (預覽) | Windows | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 2 天 |
| VMware 監視 (預覽) | Linux | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 3 分鐘 |
| 連線資料 |Windows (2012 R2/8.1 或更新版本) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | 1 分鐘 |


### <a name="automation--control"></a>自動化與控制

| 管理解決方案 | 平台 | Microsoft Monitoring Agent | Operations Manager 代理程式 | Azure 儲存體 | 是否需要 Operations Manager？ | 透過管理群組傳送的 Operations Manager 代理程式資料 | 收集頻率 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 自動化混合式背景工作角色 | Windows | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![是](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | n/a |
| 變更追蹤 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |每小時 |
| 變更追蹤 |Linux |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |每小時 |
| 更新管理 | Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |安裝更新之後 15 分鐘，至少一天 2 次 |

### <a name="security--compliance"></a>安全性與法規遵循

| 管理解決方案 | 平台 | Microsoft Monitoring Agent | Operations Manager 代理程式 | Azure 儲存體 | 是否需要 Operations Manager？ | 透過管理群組傳送的 Operations Manager 代理程式資料 | 收集頻率 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 反惡意程式碼評估 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |每小時 |
| 安全性和稽核<sup>1</sup> | Windows 和 Linux | ![部分](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | ![部分](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | ![部分](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![部分](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | 各種 |

<sup>1</sup> 安全性和稽核解決方案可以從 Windows、Operations Manager 和 Linux 代理程式收集記錄。 請參閱[資料來源](#data-sources)，以取得關於下列各項的資料收集資訊：

- syslog
- Windows 安全性事件記錄檔
- Windows 防火牆記錄檔
- Windows 事件記錄檔



### <a name="protection--recovery"></a>保護和復原

| 管理解決方案 | 平台 | Microsoft Monitoring Agent | Operations Manager 代理程式 | Azure 儲存體 | 是否需要 Operations Manager？ | 透過管理群組傳送的 Operations Manager 代理程式資料 | 收集頻率 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 備份 | Azure | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | n/a |
| Azure Site Recovery | Azure | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![否](./media/log-analytics-add-solutions/oms-bullet-red.png) | n/a |


### <a name="data-sources"></a>資料來源


| 資料來源 | 平台 | Microsoft Monitoring Agent | Operations Manager 代理程式 | Azure 儲存體 | 是否需要 Operations Manager？ | 透過管理群組傳送的 Operations Manager 代理程式資料 | 收集頻率 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Azure 活動記錄檔 |Windows |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |與通知同時 |
| Azure 診斷記錄 |Windows |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |與通知同時 |
| Azure 診斷計量 |Windows |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |與通知同時 |
| ETW |Windows |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |5 分鐘 |
| IIS 記錄檔 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |5 分鐘 |
| 效能計數器 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |依排程，最少 10 秒 |
| 效能計數器 |Linux |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |依排程，最少 10 秒 |
| syslog |Linux |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |從 Azure 儲存體 ：10 分鐘；從代理程式：與抵達同時 |
| Windows 安全性事件記錄檔 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |若為 Azure 儲存體：10 分鐘；代理程式：與抵達同時 |
| Windows 防火牆記錄檔 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |與抵達同時 |
| Windows 事件記錄檔 |Windows |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |![否](./media/log-analytics-add-solutions/oms-bullet-red.png) |![是](./media/log-analytics-add-solutions/oms-bullet-green.png) |若為 Azure 儲存體：10 分鐘；代理程式：與抵達同時 |



## <a name="preview-management-solutions-and-features"></a>預覽管理解決方案和功能
我們執行服務並遵循 DevOps 做法，能夠與客戶合作開發功能和方案。

在私人預覽期間，我們讓一小群客戶存取功能或解決方案的初期實作，以收集意見反應來進行改善。 此初期實作只有最少的功能和運作能力。

我們的目標是快速試驗，以了解何者可行或不可行。 我們反覆進行此程序，直到私人預覽客戶的意見反應可讓我們決定推出公開預覽為止。

在公開預覽期間，我們將功能或方案開放給所有使用者，以收集更多意見反應來驗證我們的調整彈性和效率。 在這個階段中：

* 預覽功能會出現在 [設定] 索引標籤中，可由任何使用者啟用。
* 使用者可以透過資源庫或使用指令碼來新增預覽解決方案。

### <a name="what-should-i-know-about-preview-features-and-solutions"></a>我對預覽功能和方案應該有何了解？
新的功能和管理解決方案讓我們感到很興奮，期待與您一起開發。

預覽功能和解決方案並不適合每個人。 在請求加入私人預覽或啟用公開預覽之前，請確定您使用開發中的功能是沒有問題的。

透過入口網站啟用預覽功能時，您將會看到警告，提醒您此功能還在預覽階段。

#### <a name="for-both-private-and-public-preview"></a>私人和公開預覽
公開和私人預覽都有下列資訊︰

* 不見得永遠正常運作。
  * 從造成小麻煩到完全無法運作都有可能。
* 預覽版可能對您的系統/環境造成負面影響。
  * 我們努力不讓負面狀況發生在您使用 OMS 的系統上，但有時難免發生非預期的情形。
* 可能發生資料遺失/損毀。
* 我們可能要求您收集診斷記錄或其他資料，以協助進行問題疑難排解。
* 可能會移除功能或方案 (暫時或永久)。
  * 我們根據在預覽期間所得的經驗，可能決定不推出功能或方案。
* 預覽可能無法運作，或尚未經過所有組態的測試，我們可能會限制︰
  * 可用的作業系統 (例如，預覽階段的某項功能可能只適用於 Linux)。
  * 可用的代理程式 (MMA、Operations Manager) 類型 (例如，在預覽期間，某項功能可能無法與 Operations Manager 一同運作)。  
* 服務等級協定未涵蓋預覽方案和功能。
* 使用預覽功能需要支付使用費用。
* 讓功能或方案發揮效果的功能可能遺失或不完整。
* 並非所有區域都可使用功能/方案。
* 功能/方案可能未完成當地語系化。
* 可使用功能/方案的客戶數或裝置數可能有所限制。
* 您可能需要使用指令碼來執行組態和啟用方案/功能。
* 使用者介面 (UI) 並不完整，每天都可能變更。
* 公開預覽可能不是適用於生產/重要系統。

#### <a name="for-private-preview"></a>私人  預覽
除了上述幾項，私人預覽還有下列資訊︰

* 希望您從體驗中提供意見反應，協助我們改善功能/方案。
* 我們可能會利用意見調查、電話或電子郵件，請您提供意見反應。
* 情況不見得一帆風順。
* 我們可能在您加入之前要求簽署保密合約 (NDA)，也可能含有機密內容。
  * 在發表於部落格、推特或與協力廠商接觸之前，請洽詢負責預覽版的方案經理，以了解任何保密規定。
* 請勿在生產/重要系統上執行。

### <a name="how-do-i-get-access-to-private-preview-features-and-solutions"></a>如何取得私人預覽功能和方案？
根據預覽而定，我們會透過幾種不同的方式邀請客戶體驗私人預覽。

* 請回答每月的客戶問卷調查，並授權我們與您保持連繫，就會更有機會受邀體驗私人預覽。
* 您的 Microsoft 帳戶小組可以提名您。
* 您可以根據 twitter [msopsmgmt](https://twitter.com/msopsmgmt)。
* 您可以根據社群活動分享的詳細資料來註冊 – 請在會面場合、會議和線上社群與我們接觸。

## <a name="next-steps"></a>後續步驟
* [搜尋記錄](log-analytics-log-searches.md) 以檢視管理解決方案所蒐集的詳細資訊。

