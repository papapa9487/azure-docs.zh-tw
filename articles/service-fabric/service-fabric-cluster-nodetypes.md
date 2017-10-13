---
title: "Azure Service Fabric 節點類型與虛擬機器擴展集 | Microsoft Docs"
description: "了解 Azure Service Fabric 節點類型與 VM 擴展集的關係，以及如何遠端連線到擴展集執行個體或叢集節點。"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: chackdan
ms.openlocfilehash: 2bd3053d645d9acd4850fddf7f27237ff954e8c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Azure Service Fabric 節點類型與虛擬機器擴展集
虛擬機器擴展集是 Azure 計算資源。 您可以使用擴展集來將虛擬機器集合以一組的方式加以部署和管理。 為 Azure Service Fabric 叢集中定義的每個節點類型設定不同的擴展集。 您可以分開相應增加或減少每個節點類型，可以開啟不同組的連接埠，並使用不同的容量計量。

下圖顯示具有 FrontEnd 和 BackEnd 兩個節點類型的叢集。 每個節點類型各有五個節點。

![有兩個節點類型的叢集][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>將虛擬機器擴展集執行個體對應至節點
如上圖所示，擴展集執行個體是由執行個體 0 開始編號，然後再逐個增加 1。 編號會反映在節點名稱中。 例如，BackEnd_0 節點是 BackEnd 擴展集的執行個體 0。 這個特定的擴展集有五個執行個體，名稱分別是 BackEnd_0、BackEnd_1、BackEnd_2、BackEnd_3、BackEnd_4。

當您相應增加擴展集，系統就會建立一個新的執行個體。 新擴展集執行個體的名稱通常是擴展集名稱 + 下一個執行個體編號。 在我們的範例中是 BackEnd_5。

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>將擴展集負載平衡器對應至節點類型和擴展集
如果您已 Azure 入口網站中部署叢集，或已使用範例 Azure Resource Manager 範本部署叢集，系統會列出資源群組下的所有資源都。 可以看到每個擴展集或節點類型的負載平衡器。 負載平衡器的名稱使用下列格式：**LB-&lt;節點類型名稱&gt;**。 例如下圖中顯示的 LB-sfcluster4doc-0：

![資源][Resources]
## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>遠端連線到虛擬機器擴展集執行個體或叢集節點
為叢集中定義的每個節點類型設定不同的擴展集。 您可以分開相應增加或減少節點類型。 您也可以使用不同的 VM SKU。 不同於單一執行個體的 VM，VM 擴展集的執行個體不會有自己的虛擬 IP 位址。 當您要尋找可用來遠端連線至特定執行個體的 IP 位址和連接埠時，可能有點困難。

若要找出可用來遠端連線至特定執行個體的 IP 位址和連接埠，請完成下列步驟。

**步驟 1**：取得遠端桌面通訊協定 (RDP) 的輸入 NAT 規則，找出該節點類型的虛擬 IP 位址。

首先，取得 `Microsoft.Network/loadBalancers` 的資源定義中定義的輸入 NAT 規則值。

在 Azure 入口網站的負載平衡器頁面上，選取 [設定]  >  [輸入 NAT 規則]。 這會給您可用來遠端連線至第一個 擴展集執行個體的 IP 位址和連接埠。 

![負載平衡器][LBBlade]

在下圖中，IP 位址和連接埠是 **104.42.106.156** 和 **3389**。

![NAT 規則][NATRules]

**步驟 2**：找到可用來遠端連線至特定擴展集執行個體或節點的連接埠。

擴展集執行個體對應至節點。 使用擴展集資訊判斷要使用哪一個通訊埠。

連接埠是以和擴展集執行個體相符的遞增順序配置。 以之前的 FrontEnd 節點類型為例，下表列出這五個節點執行個體的連接埠。 對您的擴展集執行個體套用相同的對應。

| **虛擬機器擴展集執行個體** | **連接埠** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

**步驟 3**：遠端連線到特定擴展集執行個體。

下圖示範使用「遠端桌面連線」連線到 FrontEnd_1 擴展集執行個體：

![遠端桌面連線][RDP]

## <a name="change-the-rdp-port-range-values"></a>變更 RDP 連接埠範圍值

### <a name="before-cluster-deployment"></a>叢集部署之前
當您使用 Resource Manager 範本設定叢集時，在 `inboundNatPools` 指定範圍。

移至 `Microsoft.Network/loadBalancers` 的資源定義。 找到 `inboundNatPools` 的說明。  取代 `frontendPortRangeStart` 和 `frontendPortRangeEnd` 的值。

![inboundNatPools 值][InboundNatPools]

### <a name="after-cluster-deployment"></a>叢集部署之後
在叢集部署後變更 RDP 連接埠範圍值比較複雜。 為了確保您不會循環 VM，使用 Azure PowerShell 來設定新的值。 

> [!NOTE]
> 確定您的電腦已安裝 Azure PowerShell 1.0 或更新版本。 如果您沒有 Azure Powershell 1.0 或更新版本，建議您依照[如何安裝和設定 Azure PowerShell](/powershell/azure/overview) 中敍述的步驟進行。

1. 登入您的 Azure 帳戶。 如果下列 PowerShell 命令失敗，請確認您已正確安裝 PowerShell。

    ```
    Login-AzureRmAccount
    ```

2. 若要取得負載平衡器的詳細資料，及查看 `inboundNatPools` 的描述值，執行下列程式碼：

    ```
    Get-AzureRmResource -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
    ```

3. 將 `frontendPortRangeEnd` 和 `frontendPortRangeStart` 設為您想要的值。

    ```
    $PropertiesObject = @{
        #Property = value;
    }
    Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name> -ApiVersion <use the API version that is returned> -Force
    ```

## <a name="change-the-rdp-user-name-and-password-for-nodes"></a>變更節點的 RDP 使用者名稱和密碼

若要變更特定節點類型所有節點的密碼，請完成下列步驟。 這些變更將套用至擴展集中所有目前和未來的節點。

1. 以系統管理員身分啟動 PowerShell。 
2. 若要登入並選取工作階段的訂用帳戶，執行下列命令。 將 `SUBSCRIPTIONID` 參數變更為您的訂用帳戶識別碼。 

    ```powershell
    Login-AzureRmAccount
    Get-AzureRmSubscription -SubscriptionId 'SUBSCRIPTIONID' | Select-AzureRmSubscription
    ```

3. 執行下列指令碼。 使用相關的 `NODETYPENAME`、`RESOURCEGROUP`、`USERNAME`、`PASSWORD` 值。 `USERNAME` 和 `PASSWORD` 值是您在未來的 RDP 工作階段將使用的新認證。 

    ```powershell
    $nodeTypeName = 'NODETYPENAME'
    $resourceGroup = 'RESOURCEGROUP'
    $publicConfig = @{'UserName' = 'USERNAME'}
    $privateConfig = @{'Password' = 'PASSWORD'}
    $extName = 'VMAccessAgent'
    $publisher = 'Microsoft.Compute'
    $node = Get-AzureRmVmss -ResourceGroupName $resourceGroup -VMScaleSetName $nodeTypeName
    $node = Add-AzureRmVmssExtension -VirtualMachineScaleSet $node -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion '2.0' -AutoUpgradeMinorVersion $true

    Update-AzureRmVmss -ResourceGroupName $resourceGroup -Name $nodeTypeName -VirtualMachineScaleSet $node
    ```

## <a name="next-steps"></a>後續步驟
* 請參閱[「到處部署」功能和與 Azure 受管理叢集比較的概觀](service-fabric-deploy-anywhere.md)。
* 了解[叢集安全性](service-fabric-cluster-security.md)。
* 了解 [Service Fabric SDK 和開始使用](service-fabric-get-started.md)。

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png
