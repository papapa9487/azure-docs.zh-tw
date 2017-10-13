---
title: "在 Azure 中建立和管理使用多個 NIC 的 Windows VM | Microsoft Docs"
description: "了解如何使用 Azure PowerShell 或 Resource Manager 範本，建立和管理連結多個 NIC 的 Windows VM。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou
ms.openlocfilehash: 9e84e18c5d6c205d320c6c9c565ce6723d8fa6e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-a-windows-virtual-machine-that-has-multiple-nics"></a>建立及管理具有多個 NIC 的 Windows 虛擬機器
Azure 中的虛擬機器 (VM) 可以連結多個虛擬網路介面卡 (NIC)。 常見案例是有不同的子網路可用於前端和後端連線，或者專門用來監視或備份解決方案的網路。 本文詳述如何建立已連結多個 NIC 的 VM。 您也了解如何新增或移除現有 VM 中的 NIC。 不同的 [VM 大小](sizes.md) 支援不同數量的 NIC，因此可據以調整您的 VM。

如需詳細資訊 (包括如何在自己的 PowerShell 指令碼內建立多個 NIC)，請參閱[部署多個 NIC 的 VM](../../virtual-network/virtual-network-deploy-multinic-arm-ps.md)。

## <a name="prerequisites"></a>必要條件
確定您已[安裝和設定最新的 Azure PowerShell 版本](/powershell/azure/overview)。

在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含 *myResourceGroup*、*myVnet* 和 *myVM*。


## <a name="create-a-vm-with-multiple-nics"></a>建立具有多個 NIC 的 VM
首先，建立資源群組。 下列範例會在 EastUs 位置建立名為 myResourceGroup 的資源群組：

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### <a name="create-virtual-network-and-subnets"></a>建立虛擬網路和子網路
常見的案例是有兩個或多個子網路的虛擬網路。 一個子網路可能用於前端流量，另一個則用於後端流量。 若要連結至這兩個子網路，您可在 VM 上使用多個 NIC。

1. 使用 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) 定義兩個虛擬網路子網路。 下列範例會定義 mySubnetFrontEnd 和 mySubnetBackEnd 的子網路：

    ```powershell
    $mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. 使用 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) 建立虛擬網路和子網路。 下列範例會建立名為 myVnet 的虛擬網路：

    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### <a name="create-multiple-nics"></a>建立多個 NIC
使用 [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) 建立兩個 NIC。 將一個 NIC 連結到前端子網路，將另一個 NIC 連結到後端子網路。 下列範例會建立名為 myNic1 和 myNic2 的兩個 NIC：

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

通常您也可以建立[網路安全性群組](../../virtual-network/virtual-networks-nsg.md)或[負載平衡器](../../load-balancer/load-balancer-overview.md)來協助管理，以及將流量分散到您的 VM。 [多個 NIC 的 VM](../../virtual-network/virtual-network-deploy-multinic-arm-ps.md)一文可逐步引導您建立網路安全性群組並指派 NIC。

### <a name="create-the-virtual-machine"></a>建立虛擬機器
現在開始建置您的 VM 組態。 在每個 VM 大小中，您可以新增至 VM 的 NIC 總數是有限制的。 如需詳細資訊，請參閱 [Windows VM 大小](sizes.md)。

1. 將您的 VM 認證設定為 `$cred` 變數，如下所示︰

    ```powershell
    $cred = Get-Credential
    ```

2. 使用 [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) 來定義您的 VM。 下列範例會定義名為 myVM 的 VM，並使用支援兩個以上 NIC 的 VM 大小 (Standard_DS3_v2)：

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. 使用 [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) 和 [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) 建立其餘的 VM 組態。 下列範例會建立 Windows Server 2016 VM：

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
   ```

4. 使用 [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) 連結您先前建立的兩個 NIC：

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. 最後，使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 建立 VM：

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

## <a name="add-a-nic-to-an-existing-vm"></a>將 NIC 新增至現有的 VM
若要將虛擬 NIC 新增至現有 VM，請解除配置 VM、新增虛擬 NIC，然後啟動 VM。 不同的 [VM 大小](sizes.md) 支援不同數量的 NIC，因此可據以調整您的 VM。 如有需要，您可以[調整 VM 的大小](resize-vm.md)。

1. 使用 [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) 來解除配置 VM。 下列範例會解除配置 myResourceGroup 中名為 myVM 的 VM：

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. 使用 [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm) 取得 VM 的現有組態。 下列範例可取得 myResourceGroup 中名為 myVM 之 VM 的資訊：

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. 下列範例會使用 [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) 建立虛擬 NIC，其名稱為 myNic3 並已連結至 mySubnetBackEnd。 接著會使用 [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface)，將虛擬 NIC 連結至 myResourceGroup 中名為 myVM 的 VM：

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId | Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```

    ### <a name="primary-virtual-nics"></a>主要虛擬 NIC
    您必須在具有多個 NIC 的 VM 上將其中一個 NIC 設為主要。 如果 VM 上其中一個現有虛擬 NIC 已設定為主要，即可略過此步驟。 下列範例假設有兩個虛擬 NIC 現在出現在 VM 上，而您想要新增第一個 NIC (`[0]`) 作為主要：
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. 使用 [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm) 啟動 VM：

    ```powershell
    Start-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

## <a name="remove-a-nic-from-an-existing-vm"></a>從現有的 VM 移除 NIC
若要從現有的 VM 移除虛擬 NIC，您可以解除配置 VM，移除虛擬 NIC，然後啟動 VM。

1. 使用 [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) 來解除配置 VM。 下列範例會解除配置 myResourceGroup 中名為 myVM 的 VM：

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. 使用 [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm) 取得 VM 的現有組態。 下列範例可取得 myResourceGroup 中名為 myVM 之 VM 的資訊：

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. 使用 [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) 取得 NIC 移除相關資訊。 下列範例可取得 myNic3 相關資訊：

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. 使用 [Remove-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface) 移除 NIC，然後使用 [Update-AzureRmVm](/powershell/module/azurerm.compute/update-azurermvm) 更新 VM。 下列範例會移除前一個步驟中 `$nicId` 所取得的 myNic3：

    ```powershell
    Remove-AzureRmVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```   

5. 使用 [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm) 啟動 VM：

    ```powershell
    Start-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## <a name="create-multiple-nics-with-templates"></a>使用範本建立多個 NIC
Azure Resource Manager 範本提供一種方式，可在部署期間建立資源的多個執行個體，例如建立多個 NIC。 Resource Manager 範本會使用宣告式 JSON 檔案來定義您的環境。 如需詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/resource-group-overview.md)。 您可以使用 *copy* 來指定要建立的執行個體數目：

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

如需詳細資訊，請參閱[使用 copy 建立多個執行個體](../../resource-group-create-multiple.md)。 

您也可以使用 `copyIndex()`，在資源名稱後面附加一個數字。 接著可以建立 myNic1、MyNic2 等等。 下列程式碼顯示附加索引值的範例：

```json
"name": "[concat('myNic', copyIndex())]", 
```

您可以閱讀[使用 Resource Manager 範本建立多個 NIC](../../virtual-network/virtual-network-deploy-multinic-arm-template.md)的完整範例。

## <a name="next-steps"></a>後續步驟
嘗試建立具有多個 NIC 的 VM 時，請檢閱 [Windows VM 大小](sizes.md)。 請注意每個 VM 大小所支援的 NIC 數目上限。 


