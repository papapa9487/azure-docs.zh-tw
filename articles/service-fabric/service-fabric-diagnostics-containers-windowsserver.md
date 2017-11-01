---
title: "Azure Service Fabric 容器監視與診斷 | Microsoft Docs"
description: "了解如何使用 OMS 的容器解決方案監視與診斷在 Microsoft Azure Service Fabric 上協調的容器。"
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
ms.openlocfilehash: 319ee2c0f7492389bc1767aa2669dd273f8cfa1b
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2017
---
# <a name="monitoring-windows-server-containers-with-oms"></a>使用 OMS 監視 Windows Server 容器

## <a name="oms-containers-solution"></a>OMS 容器解決方案

Operations Management Suite (OMS) Log Analytics 具有可以用於監視容器的容器解決方案。 此解決方案加上其 Service Fabric 解決方案是很棒的工具，可監視在 Service Fabric 上協調的容器部署。 以下是解決方案中儀表板外觀的簡單範例︰

![基本 OMS 儀表板](./media/service-fabric-diagnostics-containers-windowsserver/oms-containers-dashboard.png)

它也會收集各種可使用 OMS Log Analytics 工具查詢的記錄，並可用於視覺化任何正在產生的計量或事件。 所收集的記錄類型有：

1. ContainerInventory︰顯示容器位置、名稱和映像的相關資訊
2. ContainerImageInventory︰已部署映像相關資訊，包括 ID 或大小
3. ContainerLog︰特定的錯誤記錄檔、 docker 記錄檔 (stdout 等) 及其他項目
4. ContainerServiceLog：已執行的 docker 精露命令
5. 效能︰包括容器 CPU、記憶體、網路流量、磁碟 I/O，以及主機電腦的自訂計量

本文章涵蓋為您的叢集設定容器監視所需的步驟。 若要深入了解 OMS 的容器解決方案，請參閱其[文件](../log-analytics/log-analytics-containers.md)。

## <a name="1-set-up-a-service-fabric-cluster"></a>1.設定 Service Fabric 叢集

使用[此處](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows)的 Azure Resource Manager 範本建立叢集。 務必新增唯一的 OMS 工作區名稱。 此範本也預設為將叢集部署在 Service Fabric (v255.255) 的預覽組建中，這表示它無法用於生產中，且無法升級為不同的 Service Fabric 版本。 如果您決定要長期使用此範本或用於生產，請將版本變更為穩定的版本號碼。

叢集設定完畢後，確認您已安裝適當的憑證，並確定您可以連線到叢集。

前往 [Azure 入口網站](https://portal.azure.com/)並尋找部署，以確認您的資源群組設定正確。 資源群組應該包含所有 Service Fabric 資源，亦擁有 Log Analytics 解決方案及 Service Fabric 解決方案。

如需修改現有的 Service Fabric 叢集：
* 確認已啟用診斷功能 (如果沒有，請透過[更新虛擬機器擴展集](/rest/api/virtualmachinescalesets/create-or-update-a-set)啟用)
* 透過 Azure Marketplace 建立「Service Fabric 分析」解決方案來新增 OMS 工作區
* 編輯 Service Fabric 解決方案的資料來源，藉此在叢集所在的資源群組中，從適當的 Azure 儲存體資料表 (由 WAD 設定) 中挑選資料
* 透過 PowerShell 或藉由更新虛擬機器擴展集，以新增代理程式做為[虛擬機器擴展集的延伸模組](/powershell/module/azurerm.compute/add-azurermvmssextension) (與上述相同的連結，可修改 Resource Manager 範本)

## <a name="2-deploy-a-container"></a>2.部署容器

一旦叢集已就緒且您已確認可以存取叢集，請將容器部署至叢集。 如果您選擇使用該範本所設定的預覽版本，您也可以探索 Service Fabric 的新 Docker Compose 功能。 請記住，第一次將容器映像部署至叢集時，須花費數分鐘時間下載影像，視其大小而定。

## <a name="3-add-the-containers-solution"></a>3.新增容器解決方案

在 Azure 入口網站中，透過 Azure Marketplace 建立容器資源 (在 [監視 + 管理] 類別下)。 

![新增容器解決方案](./media/service-fabric-diagnostics-containers-windowsserver/containers-solution.png)

在建立步驟中，它會要求 OMS 工作區。 選取以上述部署建立的工作區。 此步驟會在您的 OMS 工作區中建立容器解決方案，且由範本所建立的 OMS 代理程式會自動偵測。 代理程式會開始收集叢集中容器處理序的資料，並在約 10-15 分鐘內開始，您應看見解決方案因資料而增色，如上方儀表板圖所示。

## <a name="4-next-steps"></a>4.後續步驟

OMS 在工作區中提供各種工具，讓工作區對您更有幫助。 瀏覽以下選項，以依照您的需求自訂解決方案：
- 熟悉 Log Analytics 的[記錄搜尋和查詢](../log-analytics/log-analytics-log-searches.md)功能
- 設定 OMS 代理程式以挑選特定的效能計數器 (移至工作區 [首頁] > [設定] > [資料] > [Windows 效能計數器])
- 設定 OMS 以設定[自動化警示](../log-analytics/log-analytics-alerts.md)規則，來協助偵測與診斷