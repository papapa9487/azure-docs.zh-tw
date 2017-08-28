---
title: "使用 Azure 入口網站以 Log Analytics 評估 Service Fabric 應用程式 | Microsoft Docs"
description: "您可以在 Log Analytics 中使用 Azure 入口網站，使用 Service Fabric 解決方案評估 Service Fabric 應用程式、微服務、節點和叢集的風險和健全狀況。"
services: log-analytics
documentationcenter: 
author: niniikhena
manager: jochan
editor: 
ms.assetid: 9c91aacb-c48e-466c-b792-261f25940c0c
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: nini
ms.translationtype: HT
ms.sourcegitcommit: 80fd9ee9b9de5c7547b9f840ac78a60d52153a5a
ms.openlocfilehash: 8c564c0dcbb2f9be286917b2f4d8a40da5406fae
ms.contentlocale: zh-tw
ms.lasthandoff: 08/14/2017

---
# <a name="assess-service-fabric-applications-and-micro-services-with-the-azure-portal"></a>使用 Azure 入口網站評估 Service Fabric 應用程式和微服務

> [!div class="op_single_selector"]
> * [Resource Manager](log-analytics-service-fabric-azure-resource-manager.md)
> * [PowerShell](log-analytics-service-fabric.md)
>
>

![Service Fabric 符號](./media/log-analytics-service-fabric/service-fabric-assessment-symbol.png)

這篇文章說明如何在 Log Analytics 中使用 Service Fabric 解決方案，協助識別整個 Service Fabric 叢集的問題並且進行疑難排解。

Service Fabric 解決方案會從 Service Fabric VM 使用 Azure 診斷資料，方法是從 Azure WAD 資料表收集此資料。 然後 Log Analytics 會讀取 Service Fabric 架構事件，包括**可靠的服務事件**、**動作項目事件**、**操作事件**和**自訂 ETW 事件**。 使用解決方案儀表板，就能夠檢視 Service Fabric 環境中值得注意的問題和相關事件。

若要開始使用解決方案，您必須將 Service Fabric 叢集連接到 Log Analytics 工作區。 以下是三個要考量的案例：

1. 如果您尚未部署 Service Fabric 叢集，使用***部署連接至 Log Analytics 工作區的 Service Fabric 叢集***中的步驟，以部署新的叢集，並將它設定為 Log Analytics 的報告。
2. 如果需要從主機收集效能計數器，以在 Service Fabric 叢集上使用其他 OMS 解決方案 (例如安全性)，請遵循***針對已安裝 VM 擴充的 Log Analytics 工作區，部署連接至工作區的 Service Fabric 叢集***中的步驟。
3. 如果您已部署您的 Service Fabric 叢集，並且想要將它連接到 Log Analytics，請依照***將現有的儲存體帳戶新增至 Log Analytics*** 中的步驟。

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace"></a>部署連接至 Log Analytics 工作區的 Service Fabric 叢集。
此範本會執行以下動作：

1. 部署已連接至 Log Analytics 工作區的 Azure Service Fabric 叢集。 您可以選擇在部署範本時建立新的工作區，或輸入現有 Log Analytics 工作區的名稱。
2. 將診斷儲存體帳戶新增至 Log Analytics 工作區。
3. 在 Log Analytics 工作區中啟用 Service Fabric 解決方案。

[![部署至 Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-oms%2F%2Fazuredeploy.json)

一旦選取上方的部署按鈕，Azure 入口網站便會開啟，並顯示可供編輯的參數。 如果您輸入新的 Log Analytics 工作區名稱，請務必建立新的資源群組︰

![Service Fabric](./media/log-analytics-service-fabric/2.png)

![Service Fabric](./media/log-analytics-service-fabric/3.png)

接受法律條款，然後按一下 [建立] 以開始部署。 完成部署後，您應該會看到新的工作區和叢集建立，WADServiceFabric*Event、WADWindowsEventLogs 和 WADETWEvent 資料表新增︰

![Service Fabric](./media/log-analytics-service-fabric/4.png)

## <a name="deploy-a-service-fabric-cluster-connected-to-a-log-analytics-workspace-with-vm-extension-installed"></a>針對已安裝 VM 擴充的 Log Analytics 工作區，部署連接至工作區的 Service Fabric 叢集。

此範本會執行以下動作：

1. 部署已連接至 Log Analytics 工作區的 Azure Service Fabric 叢集。 您可以建立新的工作區，或是使用現有的工作區。
2. 將診斷儲存體帳戶新增至 Log Analytics 工作區。
3. 在 Log Analytics 工作區中啟用 Service Fabric 解決方案。
4. 在 Service Fabric 叢集中，於每個虛擬機器擴展集中安裝 MMA 代理程式擴充。 安裝 MMA 代理程式之後，您就可以檢視節點的效能度量。

[![部署至 Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-vmss-oms%2F%2Fazuredeploy.json)

依照上述相同步驟，輸入必要的參數，並開始進行部署。 同樣地，您會看到新的工作區、叢集和 WAD 資料表均已建立︰

![Service Fabric](./media/log-analytics-service-fabric/5.png)

### <a name="viewing-performance-data"></a>檢視效能資料

若要從您的節點檢視效能資料︰


[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

- 啟動 Azure 入口網站中的 Log Analytics 工作區。
  ![Service Fabric](./media/log-analytics-service-fabric/6.png)
- 移至左窗格中的 [設定]，然後選取資料 >> Windows 效能計數器 >> [新增選取的效能計數器]：![Service Fabric](./media/log-analytics-service-fabric/7.png)
- 在 [記錄搜尋] 中，使用下列查詢深入節點的關鍵度量︰

    a. 比較最近一個小時您所有節點的平均 CPU 使用率，以查看哪個節點有問題，以及在哪個時間間隔節點有突然增加︰

    ```
    Type=Perf ObjectName=Processor CounterName="% Processor Time"|measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/10.png)

    b. 使用此查詢檢視每個節點上可用記憶體的類似折線圖︰

    ```
    Type=Perf ObjectName=Memory CounterName="Available MBytes Memory" | measure avg(CounterValue) by Computer Interval 1HOUR.
    ```

    若要檢視所有節點的清單，顯示每個節點的可用 Mb 的確切平均值，請使用此查詢︰

    ```
    Type=Perf (ObjectName=Memory) (CounterName="Available MBytes") | measure avg(CounterValue) by Computer
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/11.png)

    c. 如果您想要藉由檢查每小時平均、最小、最大和 75 個百分位數的 CPU 使用率，以向下鑽研至特定節點，您可以使用此查詢 (取代電腦欄位) 來執行此操作︰

    ```
    Type=Perf CounterName="% Processor Time" InstanceName=_Total Computer="BaconDC01.BaconLand.com"| measure min(CounterValue), avg(CounterValue), percentile75(CounterValue), max(CounterValue) by Computer Interval 1HOUR
    ```

    ![Service Fabric](./media/log-analytics-service-fabric/12.png)

如需 Log Analytics 效能計量的詳細資訊，請參閱[ Operations Management Suite 部落格](https://blogs.technet.microsoft.com/msoms/tag/metrics/) (英文)。


## <a name="adding-an-existing-storage-account-to-log-analytics"></a>將現有的儲存體帳戶新增至 Log Analytics

此範本僅會將現有的儲存體帳戶新增至新的或現有的 Log Analytics 工作區。

[![部署至 Azure](./media/log-analytics-service-fabric/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Foms-existing-storage-account%2Fazuredeploy.json)

> [!NOTE]
> 在選取資源群組時，如果您正在使用現有的 Log Analytics 工作區，請選取 [使用現有]，並搜尋包含 Log Analytics 工作區的資源群組。 否則建立一個新的。
> ![Service Fabric](./media/log-analytics-service-fabric/8.png)
>
>

部署此範本之後，您可以看見儲存體帳戶連接到您的 Log Analytics 工作區。 在此例中，我多新增一個儲存體帳戶至上述所建立的 Exchange 工作區。
![Service Fabric](./media/log-analytics-service-fabric/9.png)

## <a name="view-service-fabric-events"></a>檢視 Service Fabric 事件

一旦完成部署，並在您的工作區中啟用 Service Fabric 解決方案，請在 Log Analytics 入口網站選取 **Service Fabric** 圖格，以啟動 Service Fabric 儀表板。 此儀表板包含下表中的資料行。 每個資料行依計數列出前 10 個事件，這幾個事件符合該資料行中指定時間範圍的準則。 您可以按一下每個資料行右下角的 [查看全部] ，或按一下資料行標頭，以執行記錄搜尋來提供完整清單。

| **Service Fabric 事件** | **description** |
| --- | --- |
| 值得注意的問題 |顯示問題，例如 RunAsyncFailures RunAsynCancellations 和節點關閉。 |
| 運作事件 |值得注意的運作事件，例如應用程式升級和部署。 |
| 可靠的服務事件 |值得注意的可靠服務事件，例如 Runasyncinvocations。 |
| 動作項目事件 |您的微服務產生的值得注意動作項目事件，例如動作項目方法、動作項目啟用和停用等等所擲回的例外狀況。 |
| 應用程式事件 |您的應用程式產生的所有自訂 ETW 事件。 |

![Service Fabric 儀表板](./media/log-analytics-service-fabric/sf3.png)

![Service Fabric 儀表板](./media/log-analytics-service-fabric/sf4.png)

下表顯示 Service Fabric 的資料收集方法及如何收集資料的其他詳細資料。

| 平台 | 直接代理程式 | Operations Manager 代理程式 | Azure 儲存體 | 是否需要 Operations Manager？ | 透過管理群組傳送的 Operations Manager 代理程式資料 | 收集頻率 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |  |  | &#8226; |  |  |10 分鐘 |

> [!NOTE]
> 您可以按一下儀表板頂端的 [根據最近 7 天的資料]，變更 Service Fabric 解決方案中這些事件的範圍。 您也可以顯示過去七天、一天或六個小時內產生的事件。 或者，也可以選取 [自訂]，以指定自訂日期範圍。
>
>

## <a name="next-steps"></a>後續步驟

* 使用 [Log Analytics 中的記錄檔搜尋](log-analytics-log-searches.md)，檢視詳細的 Service Fabric 事件資料。

