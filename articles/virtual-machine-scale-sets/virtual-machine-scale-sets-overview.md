---
title: "Azure 虛擬機器擴展集概觀 | Microsoft Docs"
description: "深入了解 Azure 虛擬機器擴展集"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2017
ms.author: guybo
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5fa08049fd0b13945de307e9d28224ea0d5a1307
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-virtual-machine-scale-sets-in-azure"></a>什麼是 Azure 中的虛擬機器擴展集？
虛擬機器擴展集是一個您可以用來部署和管理一組相同 VM 的 Azure 計算資源。 所有的 VM 設定相同，擴展集是設計來支援 true 的自動調整，而不需要預先佈建 VM。 您可以更輕鬆地針對大量計算、巨量資料和容器化工作負載，建置大規模服務。

對於需要相應放大計算資源的應用程式，調整作業會隱含地平衡分散到容錯網域和更新網域。 如需擴展集的進一步介紹，請參閱 [Azure 部落格通知](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/)。

如需擴展集的詳細資訊，請觀看下列影片：

* [Mark Russinovich 講述 Azure 擴展集](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  
* [Guy Bowerman 與虛擬機器擴展集](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-scale-sets"></a>建立和管理擴展集
您可以在 [Azure 入口網站](https://portal.azure.com)中選取 [新增]，並在搜尋列中輸入 **scale** 來建立擴展集。 **虛擬機器擴展集**會列在結果中。 您可以在這裡填寫必要的欄位，以自訂和部署您的擴展集。 您在入口網站中也有可根據 CPU 使用量設定基本自動調整規則的選項。 

擴展集可以部署到[可用性區域](../availability-zones/az-overview.md)。

> [!NOTE]
> 目前虛擬機器擴展集僅支援部署到單一可用性區域。 未來將支援多區域部署。

您可以使用 JSON 範本和 [REST API](https://msdn.microsoft.com/library/mt589023.aspx) 來定義和部署擴展集，如同個別的 Azure Resource Manager VM 一樣。 因此，您可以使用任何標準 Azure Resource Manager 部署方法。 如需範本的詳細資訊，請參閱 [編寫 Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)。

您可以在 [Azure 快速入門範本 GitHub 存放庫](https://github.com/Azure/azure-quickstart-templates)中找到虛擬機器擴展集的範例範本集。 (尋找標題中包含 **vmss** 的範本)

以快速入門範本為例，在每個範本的讀我檔案上，[部署至 Azure] 按鈕可連結至入口網站部署功能。 若要部署擴展集，請按一下該按鈕，然後在入口網站中填入所需的任何參數。 

## <a name="scaling-a-scale-set-out-and-in"></a>相應放大和相應縮小擴展集
您可以在 Azure 入口網站中按一下 [設定] 底下的 [調整] 區段，來變更擴展集的容量。 

若要在命令列上變更擴展集容量，請在 [Azure CLI](https://github.com/Azure/azure-cli) 中使用 **scale** 命令。 例如，使用此命令將擴展集設定為 10 部 VM 的容量：

```bash
az vmss scale -g resourcegroupname -n scalesetname --new-capacity 10 
```

若要使用 PowerShell 設定擴展集內的 VM 數量，請使用 **Update-AzureRmVmss** 命令︰

```PowerShell
$vmss = Get-AzureRmVmss -ResourceGroupName resourcegroupname -VMScaleSetName scalesetname  
$vmss.Sku.Capacity = 10
Update-AzureRmVmss -ResourceGroupName resourcegroupname -Name scalesetname -VirtualMachineScaleSet $vmss
```

若要使用 Azure Resource Manager 範本增加或減少擴展集內的虛擬機器數目，請變更 capacity 屬性並重新部署範本。 此簡化程序可讓您在需要定義 Azure 自動調整不支援的自訂調整事件時，輕易地整合擴展集與 Azure 自動調整，或撰寫自己的自訂調整層。 

如果您想要重新部署 Azure Resource Manager 範本以變更容量，可以定義較小的範本，使其僅包含 **SKU** 屬性套件和更新的容量。 [以下是範例](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)。

## <a name="autoscale"></a>Autoscale

擴展集若建立於 Azure 入口網站中，則可選擇性地設定自動調整設定。 以便根據平均 CPU 使用量來增加或減少 VM 數目。 

[Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates)中有許多擴展集範本都定義了自動調整設定。 您也可以在現有擴展集新增自動調整設定。 例如，此 Azure PowerShell 指令碼可在擴展集內新增以 CPU 為基礎的自動調整︰

```PowerShell

$subid = "yoursubscriptionid"
$rgname = "yourresourcegroup"
$vmssname = "yourscalesetname"
$location = "yourlocation" # e.g. southcentralus

$rule1 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:05:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionValue 1
$rule2 = New-AzureRmAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -Operator LessThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:05:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Decrease -ScaleActionValue 1
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "autoprofile1"
Add-AzureRmAutoscaleSetting -Location $location -Name "autosetting1" -ResourceGroup $rgname -TargetResourceId /subscriptions/$subid/resourceGroups/$rgname/providers/Microsoft.Compute/virtualMachineScaleSets/$vmssname -AutoscaleProfiles $profile1
```

您可以在下列位置找到可調整的有效度量清單︰"Microsoft.Compute/virtualMachineScaleSets" 標題下的[支援 Azure 監視器的度量](../monitoring-and-diagnostics/monitoring-supported-metrics.md)。 另外還有更多可用的進階自動調整選項，包括以排程為基礎的自動調整，以及使用 Webhook 來與警示系統整合。

## <a name="monitoring-your-scale-set"></a>監視擴展集
[Azure 入口網站](https://portal.azure.com)會列出擴展集並顯示其屬性。 入口網站也支援管理作業。 您可以同時針對擴展集和擴展集內的個別 VM 執行管理作業。 入口網站也提供可自訂的資源使用量圖表。 

如果您需要檢視或編輯 Azure 資源的基礎 JSON 定義，您也可以使用 [Azure 資源總管](https://resources.azure.com)。 擴展集是 Microsoft.Compute Azure 資源提供者之下的一項資源。 從這個網站，您可以展開下列連結加以檢視：

訂用帳戶 > 您的訂用帳戶 > resourceGroups > 提供者 > Microsoft.Compute > virtualMachineScaleSets > 您的擴展集 > 等等

## <a name="scale-set-scenarios"></a>擴展集案例
本節列出一些典型的擴展集案例。 某些較高階的 Azure 服務 (例如 Batch、Service Fabric 和 Container Service) 會使用這些案例。

* **使用 RDP 或 SSH 連線至擴展集執行個體**：擴展集會建立於虛擬網路內，且預設不會為擴展集中的個別 VM 配置公用 IP 位址。 此原則可避免因為要對計算方格中的所有節點配置個別公用 IP 位址而產生的支出與管理負擔。 如果您需要與擴展集 VM 建立直接外部連線，可以將擴展集設定為自動指派公用 IP 位址給新的 VM。 或是，您可以從虛擬網路中可配置公用 IP 位址的其他資源連線至 VM，例如負載平衡器和獨立虛擬機器。 
* **使用 NAT 規則連線至 VM**：您可以建立一個公用 IP 位址、將它指派給負載平衡器，然後定義輸入 NAT 集區。 這些動作會將 IP 位址的連接埠對應至擴展集中某部 VM 的連接埠。 例如：
  
  | 來源 | 來源連接埠 | 目的地 | 目的地連接埠 |
  | --- | --- | --- | --- |
  |  公用 IP |連接埠 50000 |vmss\_0 |連接埠 22 |
  |  公用 IP |連接埠 50001 |vmss\_1 |連接埠 22 |
  |  公用 IP |連接埠 50002 |vmss\_2 |連接埠 22 |
  
   [此範例](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)會定義 NAT 規則，以便能使用單一公用 IP 位址，對擴展集內的每部 VM 進行 SSH 連線。
  
   [此範例](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)同樣適用於 RDP 和 Windows。
* **使用 "jumpbox" 連線至 VM**：如果您在相同的虛擬網路中建立擴展集和獨立 VM，則獨立 VM 與擴展集 VM 可使用虛擬網路或子網路為它們定義的內部 IP 位址彼此連線。 如果您建立公用 IP 位址，並將它指派給獨立 VM，您可以使用 RDP 或 SSH 連線至獨立 VM。 您可以接著從該部機器連線到擴展集執行個體。 此時您可能會發現，簡易擴展集在本質上比在預設組態中設定了公用 IP 位址的獨立 VM 來得安全。
  
   例如，[此範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox)會部署具有一部獨立 VM 的簡單擴展集。 
* **對擴展集執行個體進行負載平衡**：如果您想要使用「循環配置資源」方法來將工作傳送到 VM 的計算叢集，您可以使用第 4 層負載平衡規則據以設定 Azure Load Balancer。 您可以定義探查，藉由使用指定的通訊協定、間隔和要求路徑對連接埠執行 ping，以驗證您的應用程式正在執行中。 [Azure 應用程式閘道](https://azure.microsoft.com/services/application-gateway/)也支援擴展集，以及第 7 層和更複雜的負載平衡案例。
  
   [此範例](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl)會建立執行 Apache Web 伺服器的擴展集，並使用負載平衡器來平衡每部 VM 收到的負載。 (請查看 Microsoft.Network/loadBalancers 資源類型，以及 virtualMachineScaleSet 中的 networkProfile 和 extensionProfile。)

   [此 Linux 範例](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway)和[此 Windows 範例](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway)均使用應用程式閘道。  

* **在 PaaS 叢集管理員中，將擴展集部署為計算叢集**：擴展集有時會被稱為新一代的背景工作角色。 雖然是有效的說明，但這可能會導致擴展集功能與 Azure 雲端服務功能發生混淆。 就某方面來說，擴展集提供了真正的背景工作角色或背景工作資源。 因為它們是不會隨平台/執行階段而改變、可自訂且整合至 Azure Resource Manager IaaS 的一般化計算資源。
  
   雲端服務背景工作角色的平台/執行階段支援有限 (僅限 Windows 平台映像)。 但它也包含一些服務，例如 VIP 交換、可設定的升級設定，以及執行階段/應用程式部署特定設定。 這些服務「尚未」在擴展集中提供，或由其他較高層級的 PaaS 服務 (例如 Azure Service Fabric) 傳遞。 您可以將擴展集視為可支援 PaaS 的基礎結構。 [Service Fabric](https://azure.microsoft.com/services/service-fabric/) 之類的 PaaS 解決方案會建置在這個基礎結構上。
  
   在這個方法的[此範例](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)中，[Azure Container Service](https://azure.microsoft.com/services/container-service/) 會根據具有容器協調器的擴展集來部署叢集。

## <a name="scale-set-performance-and-scale-guidance"></a>擴展集的效能和調整指南
* 一個擴展集可支援多達 1,000 部 VM。 如果您建立並上傳自己的自訂 VM 映像，則限制為 300 個。 如需大型擴展集的使用考量，請參閱[使用大型的虛擬機器擴展集](virtual-machine-scale-sets-placement-groups.md)。
* 您不必預先建立 Azure 儲存體帳戶就能使用擴展集。 擴展集支援 Azure 受控磁碟，能打消關於每個儲存體帳戶之磁碟數目的效能疑慮。 如需詳細資訊，請參閱 [Azure 虛擬機器擴展集和受控磁碟](virtual-machine-scale-sets-managed-disks.md)。
* 請考慮使用 Azure 進階儲存體而非 Azure 儲存體，來提升 VM 佈建時間的速度和可預測性，並改善 IO 效能。
* 您正在部署之區域的核心配額會限制您可以建立的 VM 數目。 您可能需要連絡客戶支援人員，以提高您的計算配額限制，即使您目前用於 Azure 雲端服務的核心已具有較高的上限亦然。 若要查詢您的配額，請執行此 Azure CLI 命令︰`azure vm list-usage`。 或者，執行此 PowerShell 命令︰`Get-AzureRmVMUsage`。

## <a name="frequently-asked-questions-for-scale-sets"></a>擴展集的常見問題集
**問：** 擴展集內可以有多少部 VM？

**答：** 擴展集可以有 0 到 1,000 部以平台映像為基礎的 VM，或 0-300 部以自訂映像為基礎的 VM。 

**問：** 在擴展集內是否支援資料磁碟？

**答：** 是。 擴展集可以定義套用至集合中所有 VM 的連結資料磁碟組態。 如需詳細資訊，請參閱 [Azure 擴展集和連結的資料磁碟](virtual-machine-scale-sets-attached-disks.md)。 其他用於儲存資料的選項包括：

* Azure 檔案 (SMB 共用磁碟機)
* OS 磁碟機
* 暫存磁碟機 (本機，未受 Azure 儲存體支援)
* Azure 資料服務 (例如 Azure 資料表、Azure Blob)
* 外部資料服務 (例如遠端資料庫)

**問：** 哪些 Azure 區域支援擴展集？

**答：** 所有區域都支援擴展集。

**問：** 如何使用自訂映像建立擴展集？

**答：** 根據自訂映像 VHD 建立受控磁碟，並在擴展集範本中參考它。 [以下是範例](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os)。

**問：** 如果我將擴展集容量從 20 減少為 15，哪些 VM 會被移除？

**答：** 虛擬機器會跨更新網域和容錯網域從擴展集平均地移除，以達到最大的可用性。 系統會先移除具有最高識別碼的 VM。

**問：** 如果我後續又將容量從 15 增加到 18 呢？

**答：** 如果您將容量增加到 18，則會建立 3 個新的 VM。 每次 VM 執行個體識別碼都會從上一個最高值遞增 (例如 20、21、22)。 VM 會平衡分散到容錯網域和更新網域。

**問：** 在擴展集內使用多個延伸模組時，是否可以強制執行「執行順序」？

**答：** 不是直接，但針對 customScript 擴充，您的指令碼可以等候另一個擴充完成。 您可以在部落格文章︰ [Extension Sequencing in Azure VM Scale Sets (Azure VM 擴展集中的擴充排序)](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/) 中取得有關擴充排序的其他指導方針。

**問：** 擴展集是否可與 Azure 可用性設定組組搭配使用？

**答：** 是。 擴展集是隱含的可用性設定組，具有 5 個容錯網域和 5 個更新網域。 超過 100 個 VM 的擴展集橫跨多個「放置群組」，這相當於多個可用性設定組。 如需放置群組的詳細資訊，請參閱[使用大型的虛擬機器擴展集](virtual-machine-scale-sets-placement-groups.md)。 VM 的可用性設定組可以存在於與 VM 擴展集相同的虛擬網路中。 常見組態是在可用性設定組中放入控制節點 VM (其通常需要唯一組態)，以及在擴展集中放入資料節點。

您可以在 [Azure 虛擬機器擴展集常見問題集](virtual-machine-scale-sets-faq.md)中找到更多擴展集相關問題的答案。
