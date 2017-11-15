---
title: "使用 OMS 進行 Azure Service Fabric 事件分析 | Microsoft Docs"
description: "了解如何使用 OMS 視覺化及分析事件，來監視和診斷 Azure Service Fabric 叢集。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 09542c0e7f628ca4fea00a6562c0b9525432c213
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="event-analysis-and-visualization-with-oms"></a>使用 OMS 進行事件分析和視覺效果

Operations Management Suite (OMS) 是管理服務集合，可協助您監視及診斷裝載於雲端中的應用程式和服務。 若要取得 OMS 及其提供項目的更詳細概觀，請參閱 [OMS 是什麼？](../operations-management-suite/operations-management-suite-overview.md)

## <a name="log-analytics-and-the-oms-workspace"></a>Log Analytics 和 OMS 工作區

Log Analytics 會從 Managed 資源 (包括 Azure 儲存體資料表或代理程式) 收集資料，並在中央存放庫中維護資料。 此資料可接著用於分析、警示和視覺效果，或進一步匯出。 Log Analytics 支援事件、效能資料或任何其他自訂資料。

設定 OMS 時，您可以存取特定 *OMS 工作區*，以從中查詢資料或在儀表板中視覺化該資料。

Log Analytics 收到資料之後，OMS 會針對數個案例自訂數個*管理解決方案*，這些預先封裝的解決方案可監視傳入資料。 其中包括 *Service Fabric 分析*解決方案和*容器*解決方案，這是與使用 Service Fabric 叢集進行診斷和監視最相關的兩個解決方案。 還有幾個其他解決方案也值得您探索，此外 OMS 也可讓您建立自訂解決方案，您可以在[這裡](../operations-management-suite/operations-management-suite-solutions.md)深入了解。 您選擇用於叢集的每個解決方案都會在並存 Log Analytics 的相同 OMS 工作區中設定。 工作區可讓您自訂儀表板和資料視覺效果，並修改您要收集、處理及分析的資料。

## <a name="setting-up-an-oms-workspace-with-the-service-fabric-analytics-solution"></a>使用 Service Fabric 分析解決方案設定 OMS 工作區
建議您在 OMS 工作區中包含 Service Fabric 解決方案 - 它包含儀表板，會顯示從平台和應用程式層級傳入的各種記錄通道，而且提供查詢 Service Fabric 特定記錄的功能。 以下顯示一個相當簡單的 Service Fabric 解決方案，該解決方案在叢集上部署了單一應用程式：

![OMS SF 解決方案](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

請參閱[設定 OMS Log Analytics](service-fabric-diagnostics-oms-setup.md)，開始對於叢集進行此作業。

## <a name="using-the-oms-agent"></a>使用 OMS 代理程式

建議使用 EventFlow 和 WAD 作為彙總解決方案，因為它們允許以更模組化的方式進行診斷和監視。 例如，如果您想要變更 EventFlow 中的輸出，您不需要變更實際工具，只要簡單修改設定檔即可。 不過，如果您決定使用 OMS Log Analytics，您應該設定 [OMS 代理程式](../log-analytics/log-analytics-windows-agents.md)。 您也應該在將容器部署至叢集時使用 OMS 代理程式，如下所述。 

參閱[將 OMS 代理程式新增至叢集](service-fabric-diagnostics-oms-agent.md)了解此作業的步驟。

這樣做有下列好處：

* 效能計數器和計量端的資料更豐富
* 容易設定從叢集收集的計量，而且不需要更新叢集的設定。 變更代理程式的設定可以從 OMS 入口網站完成，而且代理程式會自動重新啟動以符合所需的設定。 若要設定 OMS 代理程式以挑選特定的效能計數器，請移至工作區 [首頁] > [設定] > [資料] > [Windows 效能計數器]，然後挑選您要收集的資料
* 比起必須儲存再由 OMS / Log Analytics 挑選，資料的顯示速度更快
* 更容易監視容器，因為它可以挑選 docker 記錄 (stdout、stderr) 和統計資料 (容器和節點層級的效能計量)

這裡的主要考量是由於它是代理程式，它將會與您所有的應用程式一起部署在您的叢集上，因此對叢集上的應用程式效能只會有一些影響。

## <a name="monitoring-containers"></a>監視容器

將容器部署到 Service Fabric 叢集時，建議已透過 OMS 代理程式設定叢集，而且已將容器解決方案新增至 OMS 工作區，以啟用監視和診斷功能。 工作區中的容器解決方案如下所示：

![基本 OMS 儀表板](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

代理程式會收集數個容器的特定記錄檔，您可以在 OMS 中查詢這些記錄檔，或用來視覺化效能指標。 所收集的記錄類型有：

* ContainerInventory︰顯示容器位置、名稱和映像的相關資訊
* ContainerImageInventory︰已部署映像相關資訊，包括 ID 或大小
* ContainerLog︰特定的錯誤記錄檔、 docker 記錄檔 (stdout 等) 及其他項目
* ContainerServiceLog：已執行的 docker 精露命令
* 效能︰包括容器 CPU、記憶體、網路流量、磁碟 I/O，以及主機電腦的自訂計量

[使用 OMS Log Analytics 監視容器](service-fabric-diagnostics-oms-containers.md)涵蓋為您的叢集設定容器監視所需的步驟。 若要深入了解 OMS 的容器解決方案，請參閱其[文件](../log-analytics/log-analytics-containers.md)。

## <a name="next-steps"></a>後續步驟

請探索下列 OMS 工具和選項，以依照您的需求自訂工作區：

* 針對內部部署叢集，OMS 提供可用來傳送資料至 OMS 的閘道 (HTTP 正向 Proxy)。 如需詳細資訊，請參閱[在無網際網路存取下使用 OMS 閘道將電腦連線到 OMS](../log-analytics/log-analytics-oms-gateway.md)
* 設定 OMS 以設定[自動化警示](../log-analytics/log-analytics-alerts.md)，來協助偵測與診斷
* 熟悉 Log Analytics 的[記錄搜尋和查詢](../log-analytics/log-analytics-log-searches.md)功能