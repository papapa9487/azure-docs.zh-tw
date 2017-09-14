---
title: "Service Fabric 節點類型與 VM 調整集 |Microsoft Docs"
description: "說明 Service Fabric 節點類型如何與 VM 調整集產生關聯，以及如何遠端連線到 VM 調整集執行個體或叢集節點。"
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
ms.translationtype: HT
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: 8c9e91d122591a19d34d944e2d9aaeb327cdafe4
ms.contentlocale: zh-tw
ms.lasthandoff: 09/05/2017

---
# <a name="the-relationship-between-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Service Fabric 節點類型與虛擬機器調整集之間的關聯性
虛擬機器擴展集是 Azure 計算資源。 它們可用來將虛擬機器集合以一組的方式加以部署和管理。 在 Service Fabric 叢集中定義的每個節點類型都會安裝為不同的虛擬機器擴展集。 然後每個節點類型可以獨立相應增加或相應減少，可以開啟不同組的連接埠，並可以有不同的容量度量。

下列螢幕擷取畫面顯示具有 FrontEnd 和 BackEnd 兩個節點類型的叢集。  每個節點類型各有五個節點。

![有兩個節點類型的叢集螢幕擷取畫面][NodeTypes]

## <a name="mapping-virtual-machine-scale-set-instances-to-nodes"></a>將虛擬機器擴展集執行個體對應至節點
如您在上面所看到的，虛擬機器擴展集執行個體是從執行個體 0 開始，然後逐漸增加。 編號反映在名稱中。 例如，BackEnd_0 是 BackEnd 虛擬機器擴展集的執行個體 0。 這個特定的虛擬機器擴展集有五個執行個體，名稱分別是 BackEnd_0、BackEnd_1、BackEnd_2、BackEnd_3、BackEnd_4。

當您相應增加虛擬機器擴展集時，就會建立新的執行個體。 新的虛擬機器擴展集執行個體名稱通常就是虛擬機器擴展集名稱 + 下一個執行個體編號。 在我們的範例中是 BackEnd_5。

## <a name="mapping-virtual-machine-scale-set-load-balancers-to-each-node-typevm-scale-set"></a>將虛擬機器擴展集負載平衡器對應至每個節點類型/VM 擴展集
如果您已從入口網站部署叢集，或已使用範例 Resource Manager 範本，就會取得資源群組下的所有資源清單。 您會看到每個虛擬機器擴展集或節點類型的負載平衡器。

名稱類似 **LB-&lt;NodeType name&gt;**。 例如，以下螢幕擷取畫面中的 LB-sfcluster4doc-0：

![資源][Resources]

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>遠端連線到虛擬機器擴展集執行個體或叢集節點
在叢集中定義的每個節點類型都會安裝作為不同的虛擬機器擴展集。  這表示節點類型可以獨立相應增加或相應減少。 此外，它們可由不同的 VM SKU 組成。 不同於單一執行個體 VM，虛擬機器擴展集執行個體不會取得本身的虛擬 IP 位址。 所以當您要尋找可用來遠端連線至特定執行個體的 IP 位址和連接埠時，可能有點困難。

您可以依照以下步驟來找出它們。

### <a name="step-1-find-out-the-virtual-ip-address-for-the-node-type-and-then-inbound-nat-rules-for-rdp"></a>步驟 1：找出該節點類型的虛擬 IP 位址，然後找出 RDP 的輸入 NAT 規則
若要獲得這項資訊，您必須取得 **Microsoft.Network/loadBalancers**的資源定義中定義的輸入 NAT 規則值。

在入口網站中，瀏覽至 [負載平衡器] 刀鋒視窗的 [設定] 。

![LBBlade][LBBlade]

在 [設定] 中，按一下 [輸入 NAT 規則]。 這會給您可用來遠端連線至第一個虛擬機器擴展集執行個體的 IP 位址和連接埠。 在以下的螢幕擷取畫面中是 **104.42.106.156** 和 **3389**

![NATRules][NATRules]

### <a name="step-2-find-out-the-port-that-you-can-use-to-remote-connect-to-the-specific-virtual-machine-scale-set-instancenode"></a>步驟 2：找到可用來遠端連線至特定虛擬機器擴展集執行個體/節點的連接埠
本文稍早討論了如何將虛擬機器擴展集執行個體對應至節點。 我們將使用它來找出確切的連接埠。

連接埠是以虛擬機器擴展集執行個體的遞增順序進行配置。 因此，在 FrontEnd 節點類型的範例中，五個執行個體的每個連接埠分別如下。 您現在需要對虛擬機器擴展集執行個體進行相同的對應。

| **虛擬機器擴展集執行個體** | **連接埠** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

### <a name="step-3-remote-connect-to-the-specific-virtual-machine-scale-set-instance"></a>步驟 3：遠端連線到特定的虛擬機器擴展集執行個體
在以下螢幕擷取畫面中，使用「遠端桌面連接」來連線到 FrontEnd_1：

![RDP][RDP]

## <a name="how-to-change-the-rdp-port-range-values"></a>如何變更 RDP 連接埠範圍值
### <a name="before-cluster-deployment"></a>叢集部署之前
當您使用 Resource Manager 範本設定叢集時，可以在 **inboundNatPools** 指定範圍。

移至 **Microsoft.Network/loadBalancers**的資源定義。 您會在下面找到 **inboundNatPools**的描述。  取代 *frontendPortRangeStart* 和 *frontendPortRangeEnd* 值。

![inboundNatPools][InboundNatPools]

### <a name="after-cluster-deployment"></a>叢集部署之後
叢集部署之後會稍微複雜一點，而且可能會導致 VM 設定被回收。 使用 Azure PowerShell 設定新的值。 請確定您的電腦已安裝 Azure PowerShell 1.0+ 或更新版本。 如果您沒有 Azure Powershell 1.0 或更新版本，強烈建議您依照[如何安裝和設定 Azure PowerShell](/powershell/azure/overview) 中概述的步驟進行。

登入您的 Azure 帳戶。 如果這個 PowerShell 命令因為某些原因無法運作，您應該檢查 Azure PowerShell 是否已正確安裝。

```
Login-AzureRmAccount
```

執行下列命令取得負載平衡器的詳細資料，您會看到 **inboundNatPools**的值以及描述：

```
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
```

現在將 *frontendPortRangeStart* 和 *frontendPortRangeEnd* 設為您要的值。

```
$PropertiesObject = @{
    #Property = value;
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <RG name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load Balancer name> -ApiVersion <use the API version that get returned> -Force
```

## <a name="how-to-change-the-rdp-username--password-for-nodes"></a>如何變更節點的 RDP 使用者名稱和密碼

下列步驟概述如何變更指定節點類型之所有節點的密碼。 這些變更將套用至虛擬機器擴展集中所有目前和未來的節點。

### <a name="step-1-open-powershell-with-elevated-privileges-administrator-mode"></a>步驟 1：使用較高的權限開啟 PowerShell (系統管理員模式)。 
### <a name="step-2-run-the-following-commands-to-log-in-and-select-your-subscription-for-the-session-change-the-subscriptionid-parameter-to-your-subscription-id"></a>步驟 2：執行下列命令以登入，並選取工作階段的訂用帳戶。 將 `SUBSCRIPTIONID` 參數變更為您的訂用帳戶識別碼。 

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription -SubscriptionId 'SUBSCRIPTIONID' | Select-AzureRmSubscription
```

### <a name="step-3-run-the-following-script-with-the-appropriate-nodetypename-resourcegroup-username-and-password-values-the-username-and-password-values-will-be-the-new-credentials-that-should-be-used-in-future-rdp-sessions"></a>步驟 3：使用適當的 `NODETYPENAME`、`RESOURCEGROUP`、`USERNAME` 和 `PASSWORD` 值來執行下列程式碼。 `USERNAME` 和 `PASSWORD` 值才是應在未來的 RDP 工作階段使用的新認證。 

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
* [「到處部署」功能和與 Azure 受管理叢集比較的概觀](service-fabric-deploy-anywhere.md)
* [叢集安全性](service-fabric-cluster-security.md)
* [ Service Fabric SDK 和開始使用](service-fabric-get-started.md)

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png

