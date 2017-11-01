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
ms.openlocfilehash: f0cefab15a115719ea9c378546a7e6004bd06187
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2017
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

佈建及設定 OMS 工作區的方式有兩種：透過 Resource Manager 範本或直接從 Azure Marketplace。 如果您想要部署叢集，請使用前者；如果您已部署叢集並啟用診斷功能，請使用後者。

### <a name="deploying-oms-using-a-resource-management-template"></a>使用資源管理範本部署 OMS

使用 Resource Manager 範本部署叢集時，該範本也可以建立新的 OMS 工作區、在其中新增 Service Fabric 解決方案，並將它設定為讀取適當儲存體資料表中的資料。

>[!NOTE]
>若要正常運作，必須啟用診斷功能，OMS / Log Analytics 才會有可從中讀取資訊的 Azure 儲存體資料表。

[這裡](https://azure.microsoft.com/resources/templates/service-fabric-oms/)提供一個範例範本，您可以使用並視需求修改，以執行上述動作。 如果您想要有更多選擇，根據您可能在設定 OMS 工作區的哪一個環節，還有一些其他範本提供您不同選擇。您可以在 [Service Fabric 和 OMS 範本](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS)中找到這些範本。

### <a name="deploying-oms-using-through-azure-marketplace"></a>使用 Azure Marketplace 部署 OMS

如果您想要在部署叢集之後新增 OMS 工作區，請前往 Azure Marketplace 並尋找「Service Fabric 分析」。

![Marketplace 中的 OMS SF 分析](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

* 按一下 [建立]
* 在 Service Fabric 分析建立視窗中，針對 [OMS 工作區] 欄位按一下 [選取工作區]，然後按一下 [建立新的工作區]。 填寫必要項目；這裡唯一的需求是 Service Fabric 的訂用帳戶必須與 OMS 工作區相同。 一旦驗證所有項目，您的 OMS 工作區就會開始部署。 此程序應該只需要幾分鐘的時間。
* 完成時，再次按一下 Service Fabric 分析建立視窗底部的 [建立]。 請確認新的工作區顯示在 [OMS 工作區] 底下。 這會將解決方案新增至您剛才建立的工作區。


雖然這會將解決方案新增至工作區，工作區仍然需要連線到來自叢集的診斷資料。 瀏覽至您建立 Service Fabric 分析解決方案所在的資源群組。 您應該會看到 *ServiceFabric(\<nameOfOMSWorkspace\>)*。

* 按一下解決方案以瀏覽至其概觀分頁，您可以在其中變更解決方案設定、工作區設定以及瀏覽至 OMS 入口網站。
* 在左側導覽功能表中，按一下 [工作區資料來源] 底下的 [儲存體帳戶記錄]。

    ![入口網站中的 Service Fabric 分析解決方案](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-portal.png)

* 在 [儲存體帳戶記錄] 分頁上，按一下頂端的 [新增] 以將您的叢集記錄新增至工作區。
* 按一下 [儲存體帳戶] 以新增叢集中建立的適當帳戶。 如果您使用預設名稱，儲存體帳戶名稱為 sfdg\<resourceGroupName\>。 您也可以藉由檢查用來部署叢集的 Azure Resource Manager 範本、檢查針對 `applicationDiagnosticsStorageAccountName` 所使用的值，加以確認。 如果帳戶名稱未顯示，您也可能必須向下捲動並按一下 [載入更多]。 當正確的儲存體帳戶名稱顯示時，按一下它加以選取。
* 接下來，您必須指定 [資料類型]，它應該是 **Service Fabric 事件**。
* 來源應該自動設定為 *WADServiceFabric\*EventTable*。
* 按一下 [確定] 以將您的工作區連線至您的叢集記錄。

    ![將儲存體帳戶記錄新增至 OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

* 帳戶現在應該會在工作區資料來源中顯示成為「儲存體帳戶記錄」的一部分。

使用它，您現在就可以在 OMS Log Analytics 工作區中新增 Service Fabric 分析解決方案，該工作區現在正確連線至叢集平台和應用程式記錄資料表。 您可以相同方式將其他來源新增至工作區。

## <a name="using-the-oms-agent"></a>使用 OMS 代理程式

建議使用 EventFlow 和 WAD 作為彙總解決方案，因為它們允許以更模組化的方式進行診斷和監視。 例如，如果您想要變更 EventFlow 中的輸出，您不需要變更實際工具，只要簡單修改設定檔即可。 不過，如果您決定要投資使用 OMS，並願意繼續使用它進行事件分析 (不一定要是您使用的唯一平台，但至少是其中一個平台)，建議您向上探索設定到 [OMS 代理程式](../log-analytics/log-analytics-windows-agents.md)。 您也應該在將容器部署至叢集時使用 OMS 代理程式，如下所述。

執行這項作業的程序相當簡單，因為您只需要新增代理程式作為 Resource Manager 範本的虛擬機器擴展集延伸模組，就能確保安裝到您的每個節點。 針對執行 [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) 或 [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux) 的叢集，您可以找到 Resource Manager 範本的範例，該範本會使用 Service Fabric 解決方案來部署 OMS 工作區 (如上所示)，並將代理程式新增至您的節點。

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

本文章涵蓋為您的叢集設定容器監視所需的步驟。 若要深入了解 OMS 的容器解決方案，請參閱其[文件](../log-analytics/log-analytics-containers.md)。

若要在您的工作區中設定容器解決方案，請務必遵循上述步驟，將 OMS 代理程式部署在您的叢集節點上。 一旦叢集就緒，請將容器部署到該叢集。 請記住，第一次將容器映像部署至叢集時，須花費數分鐘時間下載影像，視其大小而定。

在 Azure Marketplace 中，搜尋 [容器監視解決方案]，然後建立 [容器監視解決方案]  結果，該結果應在 [監視 + 管理] 類別下顯示。

![新增容器解決方案](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

在建立步驟中，它會要求 OMS 工作區。 選取以上述部署建立的工作區。 此步驟會在您的 OMS 工作區中建立容器解決方案，且由範本所建立的 OMS 代理程式會自動偵測。 代理程式會開始收集叢集中容器處理序的資料，並在約 15 分鐘內開始，您應看見解決方案因資料而增色，如上方儀表板圖所示。


## <a name="next-steps"></a>後續步驟

請探索下列 OMS 工具和選項，以依照您的需求自訂工作區：

* 針對內部部署叢集，OMS 提供可用來傳送資料至 OMS 的閘道 (HTTP 正向 Proxy)。 如需詳細資訊，請參閱[在無網際網路存取下使用 OMS 閘道將電腦連線到 OMS](../log-analytics/log-analytics-oms-gateway.md)
* 設定 OMS 以設定[自動化警示](../log-analytics/log-analytics-alerts.md)，來協助偵測與診斷
* 熟悉 Log Analytics 的[記錄搜尋和查詢](../log-analytics/log-analytics-log-searches.md)功能