---
title: "Azure Service Fabric - 使用 OMS 代理程式設定監視 | Microsoft Docs"
description: "了解如何設定 OMS 代理程式，以監視 Azure Service Fabric 叢集的容器和效能計數器。"
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
ms.date: 10/31/2017
ms.author: dekapur
ms.openlocfilehash: e1a45f9924291382bb1bbdc969e97ee54a7b6132
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="add-the-oms-agent-to-a-cluster"></a>將 OMS 代理程式新增至叢集

本文會逐步說明如何將 OMS 代理程式作為虛擬機器擴展集延伸模組，新增至您的叢集，並將它連接到現有 OMS Log Analytics 工作區。 如此即可收集容器、應用程式和效能監控的相關診斷資料。 將之新增為延伸模組，Azure Resource Manager 便一定能將其安裝在每個節點上，即使在調整過叢集規模時也無妨。

> [!NOTE]
> 本文假設您已設定好 OMS Log Analytics 工作區。 如果尚未完成，請前往[設定 OMS Log Analytics](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>透過 Azure CLI 新增代理程式延伸模組

要將 OMS 代理程式加入叢集，最佳方法是透過 Azure CLI 提供的虛擬機擴展集 API。 如果您尚未設定 Azure CLI，請前往 Azure 入口網站，並開啟 [Cloud Shell](../cloud-shell/overview.md) 執行個體，或[安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。

1. 一旦要求了 Cloud Shell，請確定用於作業的訂用帳戶與資源的訂用帳戶相同。 請使用 `az account show` 進行檢查，並確保「名稱」值符合叢集訂用帳戶的名稱。

2. 在入口網站中，瀏覽至您的 OMS 工作區所在的資源群組。 按一下 Log Analytics 資源 (資源類型為 Log Analytics)，在右側導覽區中，向下捲動並按一下 [屬性]。

    ![OMS 屬性頁面](media/service-fabric-diagnostics-oms-agent/oms-properties.png)

    記下您的 `workspaceId`。 

3. 您也需要 `workspaceKey` 以部署代理程式。 若要取得您的金鑰，請在左側導覽區的 [設定] 區段下方，按一下 [進階設定]。 如果您要建立 Windows 叢集，請按一下 [Windows 伺服器]，如果您要建立 Linux 叢集，請按一下 [Linux 伺服器]。 需要使用所顯示的*主索引鍵*來部署代理程式，如 `workspaceKey` 所示。

4. 執行命令，以使用 Cloud Shell 中的 `vmss extension set` API，將 OMS 代理程式安裝到您的叢集：

    若為 Windows 叢集：
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    若為 Linux 叢集：

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId'}":'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    以下是將 OMS 代理程式新增至 Windows 叢集的範例。

    ![OMS 代理程式 cli 命令](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
    應該在 15 分鐘內即可成功地將代理程式新增至您的節點。 您可以使用 `az vmss extension list` API 驗證是否已新增代理程式：

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>透過 Resource Manager 範本新增代理程式

部署 OMS Log Analytics 工作區，並將代理程式新增至每個節點的範例 Resource Manager 範本可用於 [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) 或 [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux)。

您可以下載並修改此範本，以部署最適合您需求的叢集。

## <a name="next-steps"></a>後續步驟

* 收集相關[效能計數器](service-fabric-diagnostics-event-generation-perf.md)。 若要設定 OMS 代理程式以挑選特定的效能計數器，請前往 OMS 入口網站 (OMS Log Analytics 資源的頂端連結)。 然後按一下 [首頁] > [設定] > [資料] > [Windows 效能計數器]，或 [Linux 效能計數器]，然後選擇您想要收集的計數器。
* 設定 OMS 以設定[自動化警示](../log-analytics/log-analytics-alerts.md)，來協助偵測與診斷
