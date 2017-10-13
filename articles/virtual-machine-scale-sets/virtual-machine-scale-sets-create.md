---
title: "建立 Azure 虛擬機器擴展集 | Microsoft Docs"
description: "使用 Azure CLI、PowerShell、範本或 Visual Studio 建立和部署 Linux 或 Windows Azure 虛擬機器擴展集。"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 07/21/2017
ms.author: adegeo
ms.openlocfilehash: 32af01aa545c541688128a7ae6bbb82a0e046f2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-deploy-a-virtual-machine-scale-set"></a>建立和部署虛擬機器擴展集
虛擬機器擴展集可讓您將完全相同的虛擬機器以集合的方式進行部署和管理。 調整集可為超大規模的應用程式提供高度擴充和可自訂的計算層，並且可支援 Windows 平台映像、Linux 平台映像、自訂映像和擴充。 如需調整集的詳細資訊，請參閱[虛擬機器擴展集](virtual-machine-scale-sets-overview.md)。

本教學課程示範如何**不**使用 Azure 入口網站而建立虛擬機器擴展集。 如需有關如何使用 Azure 入口網站的詳細資訊，請參閱[如何使用 Azure 入口網站建立虛擬機器擴展集](virtual-machine-scale-sets-portal-create.md)。

>[!NOTE]
>如需 Azure Resource Manager 資源的詳細資訊，請參閱 [Azure Resource Manager 與傳統部署](../azure-resource-manager/resource-manager-deployment-model.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

如果您使用 Azure CLI 2.0 或 Azure PowerShell 建立擴展集，您必須先登入訂用帳戶。

如需如何使用 Azure CLI 或 PowerShell 安裝、設定和登入 Azure 的詳細資訊，請參閱[開始使用 Azure CLI 2.0](/cli/azure/get-started-with-azure-cli.md) 或[開始使用 Azure PowerShell Cmdlet](/powershell/azure/overview)。

```azurecli
az login
```

```powershell
Login-AzureRmAccount
```

## <a name="create-a-resource-group"></a>建立資源群組

您首先必須建立虛擬機器擴展集相關聯的資源群組。

```azurecli
az group create --location westus2 --name MyResourceGroup1
```

```powershell
New-AzureRmResourceGroup -Location westus2 -Name MyResourceGroup1
```

## <a name="create-from-azure-cli"></a>從 Azure CLI 建立

使用 Azure CLI 建立虛擬機器擴展集最輕鬆。 如果您省略預設值，系統會為您提供預設值。 例如，如果您未指定任何虛擬網路資訊，則會為您建立虛擬網路。 如果您省略下列部分，系統會為您建立它們︰ 
- 負載平衡器
- 虛擬網路
- 公用 IP 位址

選擇您想要在虛擬機器擴展集上使用的虛擬機器映像時，您有幾個選擇︰

- URN  
資源的識別碼：  
**Win2012R2Datacenter**

- URN 別名  
URN 的易記名稱：  
**MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest**

- 自訂資源識別碼  
Azure 資源的路徑︰  
**/subscriptions/subscription-guid/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/images/MyImage**

- Web 資源  
HTTP URI 的路徑︰  
**http://contoso.blob.core.windows.net/vhds/osdiskimage.vhd**

>[!TIP]
>您可以使用 `az vm image list`取得可用的映像清單。

若要建立虛擬機器擴展集，您必須指定下列項目︰

- 資源群組 
- 名稱
- 作業系統映像
- 驗證資訊 
 
下列範例建立基本的虛擬機器擴展集 (此步驟可能需要幾分鐘的時間)。

```azurecli
az vmss create --resource-group MyResourceGroup1 --name MyScaleSet --image UbuntuLTS --authentication-type password --admin-username azureuser --admin-password P@ssw0rd!
```

一旦命令完成，就會建立您的虛擬機器擴展集。 您可能需要取得虛擬機器的 IP 位址，以便與它連線。 您可以使用下列命令來取得虛擬機器的許多不同資訊 (包括 IP 位址)。 

```azurecli
az vmss list-instance-connection-info --resource-group MyResourceGroup1 --name MyScaleSet
```

## <a name="create-from-powershell"></a>從 PowerShell 建立

PowerShell 比使用 Azure CLI 更複雜。 Azure CLI 會提供網路相關資源 (例如，負載平衡器、IP 位址和虛擬網路) 的預設值，PowerShell 不會提供。 使用 PowerShell 參考映像也有點複雜。 您可以使用下列 Cmdlet 取得映像︰

1. Get-AzureRMVMImagePublisher
2. Get-AzureRMVMImageOffer
3. Get-AzureRmVMImageSku

Cmdlet 工作可以經由管線依序執行。 以下示範如何取得**美國西部 2** 區域的所有映像，此區域的發行者名稱中包含 **microsoft**。

```powershell
Get-AzureRMVMImagePublisher -Location WestUS2 | Where-Object PublisherName -Like *microsoft* | Get-AzureRMVMImageOffer | Get-AzureRmVMImageSku | Select-Object PublisherName, Offer, Skus
```

```
PublisherName              Offer                    Skus
-------------              -----                    ----
microsoft-ads              linux-data-science-vm    linuxdsvm
microsoft-ads              standard-data-science-vm standard-data-science-vm
MicrosoftAzureSiteRecovery Process-Server           Windows-2012-R2-Datacenter
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Enterprise
MicrosoftBizTalkServer     BizTalk-Server           2013-R2-Standard
MicrosoftBizTalkServer     BizTalk-Server           2016-Developer
MicrosoftBizTalkServer     BizTalk-Server           2016-Enterprise
...
```

建立虛擬機器擴展集的工作流程如下︰

1. 建立含有擴展集相關資訊的組態物件。
2. 參考基本作業系統映像。
3. 進行作業系統設定︰驗證、VM 名稱前置詞和使用者/密碼。
4. 設定網路功能。
5. 建立擴展集。

這個範例為已安裝 Windows Server 2016 的電腦建立基本雙執行個體擴展集。

```powershell
# Resource group name from above
$rg = "MyResourceGroup1"
$location = "WestUS2"

# Create a config object
$vmssConfig = New-AzureRmVmssConfig -Location $location -SkuCapacity 2 -SkuName Standard_A0  -UpgradePolicyMode Automatic

# Reference a virtual machine image from the gallery
Set-AzureRmVmssStorageProfile $vmssConfig -ImageReferencePublisher MicrosoftWindowsServer -ImageReferenceOffer WindowsServer -ImageReferenceSku 2016-Datacenter -ImageReferenceVersion latest

# Set up information for authenticating with the virtual machine
Set-AzureRmVmssOsProfile $vmssConfig -AdminUsername azureuser -AdminPassword P@ssw0rd! -ComputerNamePrefix myvmssvm

# Create the virtual network resources

## Basics
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "my-subnet" -AddressPrefix 10.0.0.0/24
$vnet = New-AzureRmVirtualNetwork -Name "my-network" -ResourceGroupName $rg -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $subnet

## Load balancer
$publicIP = New-AzureRmPublicIpAddress -Name "PublicIP" -ResourceGroupName $rg -Location $location -AllocationMethod Static -DomainNameLabel "myuniquedomain"
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontend" -PublicIpAddress $publicIP
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"
$probe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2
$inboundNATRule1= New-AzureRmLoadBalancerRuleConfig -Name "webserver" -FrontendIpConfiguration $frontendIP -Protocol Tcp -FrontendPort 80 -BackendPort 80 -IdleTimeoutInMinutes 15 -Probe $probe -BackendAddressPool $backendPool
$inboundNATPool1 = New-AzureRmLoadBalancerInboundNatPoolConfig -Name "RDP" -FrontendIpConfigurationId $frontendIP.Id -Protocol TCP -FrontendPortRangeStart 53380 -FrontendPortRangeEnd 53390 -BackendPort 3389

New-AzureRmLoadBalancer -ResourceGroupName $rg -Name "LB1" -Location $location -FrontendIpConfiguration $frontendIP -LoadBalancingRule $inboundNATRule1 -InboundNatPool $inboundNATPool1 -BackendAddressPool $backendPool -Probe $probe

## IP address config
$ipConfig = New-AzureRmVmssIpConfig -Name "my-ipaddress" -LoadBalancerBackendAddressPoolsId $backendPool.Id -SubnetId $vnet.Subnets[0].Id -LoadBalancerInboundNatPoolsId $inboundNATPool1.Id

# Attach the virtual network to the IP object
Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmssConfig -Name "network-config" -Primary $true -IPConfiguration $ipConfig

# Create the scale set with the config object (this step might take a few minutes)
New-AzureRmVmss -ResourceGroupName $rg -Name "MyScaleSet1" -VirtualMachineScaleSet $vmssConfig
```

### <a name="using-a-custom-virtual-machine-image"></a>使用自訂虛擬機器映像
如果您是從自己的自訂映像建立擴展集，而不是參考資源庫中的虛擬機器映像，_Set-AzureRmVmssStorageProfile_ 命令看起來會像這樣：
```PowerShell
Set-AzureRmVmssStorageProfile -OsDiskCreateOption FromImage -ManagedDisk PremiumLRS -OsDiskCaching "None" -OsDiskOsType Linux -ImageReferenceId (Get-AzureRmImage -ImageName $VMImage -ResourceGroupName $rg).id
```

## <a name="create-from-a-template"></a>從範本建立

您可以使用 Azure Resource Manager 範本部署虛擬機器擴展集。 您可以建立自己的範本，或使用[範本存放庫](https://azure.microsoft.com/resources/templates/?term=vmss)中的範本。 這些範本可以直接部署至 Azure 訂用帳戶。

>[!NOTE]
>若要建立您自己的範本，您需要建立 JSON 文字檔案。 如需有關如何建立和自訂範本的一般資訊，請參閱 [Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)。

您可以[在 GitHub 上](https://github.com/gatneil/mvss/tree/minimum-viable-scale-set)取得範本範例。 如需如何建立和使用該範例的詳細資訊，請參閱[最小可行的擴展集](.\virtual-machine-scale-sets-mvss-start.md)。

## <a name="create-from-visual-studio"></a>從 Visual Studio 建立

您可以使用 Visual Studio 建立 Azure 資源群組專案，然後在專案中新增虛擬機器擴展集範本。 您可以選擇要從 GitHub 或 Azure Web 應用程式庫匯入。 系統也會為您產生部署 PowerShell 指令碼。 如需詳細資訊，請參閱[如何使用 Visual Studio 建立虛擬機器擴展集](virtual-machine-scale-sets-vs-create.md)。

## <a name="create-from-the-azure-portal"></a>從 Azure 入口網站建立

Azure 入口網站很方便快速建立擴展集。 如需詳細資訊，請參閱[如何使用 Azure 入口網站建立虛擬機器擴展集](virtual-machine-scale-sets-portal-create.md)。

## <a name="next-steps"></a>後續步驟

深入了解[資料磁碟](virtual-machine-scale-sets-attached-disks.md)。

深入了解如何[管理您的應用程式](virtual-machine-scale-sets-deploy-app.md)。
