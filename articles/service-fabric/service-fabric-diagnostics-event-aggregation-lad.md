---
title: "使用 Linux Azure 診斷的 Azure Service Fabric 事件彙總 | Microsoft Docs"
description: "了解如何使用 LAD 彙總及收集事件，來監視和診斷 Azure Service Fabric 叢集。"
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
ms.date: 07/17/2017
ms.author: dekapur
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: bcc3a229369a065cfcfbd32eadbf3f6ae6fe0036
ms.contentlocale: zh-tw
ms.lasthandoff: 07/20/2017

---

# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>使用 Linux Azure 診斷的事件彙總和收集
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

當您執行 Azure Service Fabric 叢集時，最好從中央位置的所有節點收集記錄檔。 將記錄檔集中在中央位置，可協助您分析並針對叢集或該叢集中執行之應用程式與服務的問題進行疑難排解。

上傳和收集記錄的其中一種方式就是使用 Linux Azure 診斷 (LAD) 延伸模組，此延伸模組可將記錄上傳到 Azure 儲存體，也可以選擇將記錄傳送至 Azure Application Insights 或事件中樞。 您也可以使用外部程序來讀取儲存體中的事件，然後將它們放在 [OMS Log Analytics](../log-analytics/log-analytics-service-fabric.md) 這類的分析平台產品或其他記錄剖析解決方案中。

## <a name="log-and-event-sources"></a>記錄和事件來源

### <a name="service-fabric-platform-events"></a>Service Fabric 平台事件
Service Fabric 會透過 [LTTng](http://lttng.org) 發出少數的現成記錄，包括運作事件或執行階段事件。 這些記錄檔會儲存在叢集的 Resource Manager 範本所指定的位置。 若要取得或設定儲存體帳戶的詳細資訊，請搜尋 **AzureTableWinFabETWQueryable** 標籤並尋找 **StoreConnectionString**。

### <a name="application-events"></a>應用程式事件
 檢測軟體時，會如您在應用程式和服務的程式碼中所指定的，發出事件。 您可以使用任何撰寫文字型記錄檔的記錄解決方案，例如 LTTng。 如需詳細資訊，請參閱 LTTng 文件中關於追蹤您應用程式的內容。

[監視和診斷本機開發設定中的服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。

## <a name="deploy-the-diagnostics-extension"></a>部署診斷擴充功能
收集記錄檔的第一個步驟是將診斷擴充功能部署在 Service Fabric 叢集的每個 WM 上。 診斷擴充功能會收集每個 VM 上的記錄檔，並將它們上傳至您指定的儲存體帳戶。 步驟視您使用 Azure 入口網站或 Azure Resource Manager 而有所不同。

若要在建立叢集過程中將診斷擴充功能部署至叢集中的 VM，請將 [診斷] 設為 [開啟]。 建立叢集之後，您就無法使用入口網站變更這項設定。

接著，設定 Linux Azure 診斷 (LAD) 來收集檔案，並將它們放入您的儲存體帳戶中。 [使用 LAD 監視 Linux VM 的效能和診斷資料](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)一文中的案例 3 (＜上傳您自己的記錄檔＞) 將會說明這個程序。 遵循這個程序就可以存取追蹤。 您可以將追蹤上傳到所選擇的視覺化檢視。

您也可以使用 Azure Resource Manager 來部署診斷擴充功能。 此程序在 Windows 及 Linux 上都很類似，而[如何利用 Azure 診斷收集記錄檔](service-fabric-diagnostics-how-to-setup-wad.md)中將針對 Windows 叢集提供相關說明。

您也可以使用 Operations Management Suite，如 [Operations Management Suite Log Analytics with Linux (搭配 Linux 使用 Operations Management Suite Log Analytics)](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/) 中所述。

完成此設定之後，LAD 代理程式就會監視指定的記錄檔。 每當新的一行附加至檔案時，系統就會建立 syslog 項目並傳送至您所指定的儲存體。

## <a name="next-steps"></a>後續步驟

若要更仔細了解您在進行問題的疑難排解時應該調查哪些事件，請參閱 [LTTng 文件](http://lttng.org/docs)和[使用 LAD](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)。
