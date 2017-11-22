---
title: "透過網路虛擬設備 PowerShell，建立使用者定義的路由來路由網路流量 | Microsoft Docs"
description: "了解如何建立使用者定義的路由來覆寫 Azure 的預設路由，方法是透過網路虛擬設備來路由網路流量。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: 70ddec1c7ba76ef7f42048896079e5c5fa2bf60c
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-user-defined-route---powershell"></a>建立使用者定義的路由 - PowerShell

在本教學課程中，了解如何透過網路虛擬設備建立使用者定義的路由，以路由兩個[虛擬網路](virtual-networks-overview.md)子網路之間的流量。 網路虛擬設備是執行網路應用程式 (例如防火牆) 的虛擬機器。 若要深入了解您可以在 Azure 虛擬網路中部署之預先設定的網路虛擬設備，請參閱 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) \(英文\)。

當您在虛擬網路中建立子網路時，Azure 會建立預設[系統路由](virtual-networks-udr-overview.md#system-routes)，讓所有子網路中的資源彼此通訊，如下圖所示：

![預設路由](./media/create-user-defined-route/default-routes.png)

在本教學課程中，您使用公用、私人和 DMZ 子網路建立虛擬網路，如下圖所示。 網頁伺服器通常可能會部署到公用子網路，而應用程式或資料庫伺服器則可能會部署到私人子網路。 您可以建立虛擬機器作為 DMZ 子網路中的網路虛擬設備，並選擇性地在每個子網路中建立可透過網路虛擬設備進行通訊的虛擬機器。 公用和私人子網路之間的所有流量都會透過設備路由，如下圖所示：

![使用者定義的路由](./media/create-user-defined-route/user-defined-routes.png)

本文提供透過 Resource Manager 部署模型建立使用者定義路由的步驟，而此部署模型正是建立使用者定義路由時我們建議使用的部署模型。 如果您需要建立使用者定義的路由 (傳統)，請參閱[建立使用者定義的路由 (傳統)](virtual-network-create-udr-classic-ps.md)。 如果您不熟悉 Azure 部署模型，請參閱[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 若要深入了解使用者定義的路由，請參閱[使用者定義的路由概觀](virtual-networks-udr-overview.md#user-defined)。

## <a name="create-routes-and-network-virtual-appliance"></a>建立路由和網路虛擬設備

您可以在 PC 上安裝及設定 PowerShell [AzureRM](https://www.powershellgallery.com/packages/AzureRM/)模組的最新版本，或直接按一下任何指令碼中的 [試試看] 按鈕，在 Azure Cloud Shell 中執行指令碼。 Cloud Shell 已安裝 PowerShell AzureRM 模組。
 
1. **必要條件**：完成[建立虛擬網路](#create-a-virtual-network)中的步驟，以建立包含兩個子網路的虛擬網路。
2. 如果 PowerShell 在您的電腦上執行，請使用 `login-azurermaccount` 命令搭配您的 [Azure 帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登入 Azure。 如果使用 Cloud Shell，則您已經自動登入。 Cloud Shell 可能需要重新啟動，才能變更建立必要虛擬網路時使用的 Bash 殼層。
3. 設定步驟中使用的一些變數：

    ```azurepowershell-interactive
    $rgName="myResourceGroup"
    $location="eastus"
    ```

4. 在現有虛擬網路中建立 DMZ 子網路：

    ```azurepowershell-interactive
    $virtualNetwork = Get-AzureRmVirtualNetwork `
      -Name myVnet `
      -ResourceGroupName $rgName 
    Add-AzureRmVirtualNetworkSubnetConfig `
      -Name 'DMZ' `
      -AddressPrefix "10.0.2.0/24" `
      -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzureRmVirtualNetwork
    ```

5. 建立網路虛擬設備虛擬機器的靜態公用 IP 位址。

    ```azurepowershell-interactive
    $Pip = New-AzureRmPublicIpAddress `
      -AllocationMethod Static `
      -ResourceGroupName $rgName `
      -Location $location `
      -Name myPublicIp-myVm-Nva
    
6. Create a network interface in the *DMZ* subnet, assign a static private IP address to it, and enable IP forwarding for the network interface. By assigning a static IP address to the network interface, you ensure that it doesn't change for the life of the virtual machine the network interface is attached to. IP forwarding must be enabled for each network interface that receives traffic not addressed to an IP address assigned to it.

    ```azurepowershell-interactive
    $virtualNetwork = Get-AzureRmVirtualNetwork `
      -Name myVnet `
      -ResourceGroupName $rgName
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig `
      -Name DMZ `
      -VirtualNetwork $virtualNetwork
    $nic = New-AzureRmNetworkInterface `
      -ResourceGroupName $rgName `
      -Location $location `
      -Name 'myNic-Nva' `
      -SubnetId $subnet.Id `
      -PrivateIpAddress 10.0.2.4 `
      -EnableIPForwarding `
      -PublicIpAddressId $Pip.Id 
    ```

7. 建立 NVA 虛擬機器。 NVA 可以是執行 Linux 或 Windows 作業系統的虛擬機器。 若要建立虛擬機器，將任一作業系統的指令碼複製並貼到 PowerShell 工作階段。 如果建立 Windows VM，將指令碼貼到文字編輯器，變更$cred 變數的「值」，然後將修改過的文字貼到 PowerShell 工作階段：

    **Linux**

    ```azurepowershell-interactive
    # Define the admin user name and a blank password.
    $securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

    # Define the virtual machine configuration.
    $vmConfig = New-AzureRmVMConfig `
      -VMName 'myVm-Nva' `
      -VMSize Standard_DS2 | `
      Set-AzureRmVMOperatingSystem -Linux `
      -ComputerName 'myVm-Nva' `
      -Credential $cred -DisablePasswordAuthentication| `
      Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 14.04.2-LTS `
      -Version latest | `
      Add-AzureRmVMNetworkInterface -Id $nic.Id

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"    

    # Create the virtual machine
    $vm = New-AzureRmVM `
      -ResourceGroupName $rgName `
      -Location $location `
      -VM $vmConfig
    ```

    **Windows**

    ```azurepowershell-interactive
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."

    $vmConfig = New-AzureRmVMConfig `
      -VMName 'myVm-Nva' `
      -VMSize Standard_DS2 | `
      Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName 'myVm-Nva' `
      -Credential $cred | `
      Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
      Add-AzureRmVMNetworkInterface -Id $nic.Id
    
    $vm = New-AzureRmVM `
      -ResourceGroupName $rgName `
      -Location $location `
      -VM $vmConfig
    ```

8. 根據預設，Azure 會路由虛擬網路內所有子網路之間的流量。 建立路由來變更 Azure 的預設路由，讓*公用*子網路的流量是路由至 NVA，而不是直接路由至*私人*子網路。

    ```azurepowershell-interactive    
    $routePrivate = New-AzureRmRouteConfig `
      -Name 'ToPrivateSubnet' `
      -AddressPrefix 10.0.1.0/24 `
      -NextHopType VirtualAppliance `
      -NextHopIpAddress $nic.IpConfigurations[0].PrivateIpAddress
    ```

9. 建立*公用*子網路的路由表。

    ```azurepowershell-interactive
    $routeTablePublic = New-AzureRmRouteTable `
      -Name 'myRouteTable-Public' `
      -ResourceGroupName $rgName `
      -location $location `
      -Route $routePrivate
    ```
    
10. 使路由表與公用子網路產生關聯。 路由表與子網路產生關聯後，會讓 Azure 根據路由表中的路由來路由所有子網路的輸出流量。 路由表可以與零個或多個子網路產生關聯，而子網路可以有零個或一個相關聯的路由表。

    ```azurepowershell-interactive
    Set-AzureRmVirtualNetworkSubnetConfig `
      -VirtualNetwork $virtualNetwork `
      -Name 'Public' `
      -AddressPrefix 10.0.0.0/24 `
      -RouteTable $routeTablePublic | `
    Set-AzureRmVirtualNetwork
    ```

11. 透過 NVA 虛擬機器，建立從*私人*子網路至*公用*子網路的流量路由。

    ```azurepowershell-interactive    
    $routePublic = New-AzureRmRouteConfig `
      -Name 'ToPublicSubnet' `
      -AddressPrefix 10.0.0.0/24 `
      -NextHopType VirtualAppliance `
      -NextHopIpAddress $nic.IpConfigurations[0].PrivateIpAddress
    ```

12. 建立*私人*子網路的路由表。

    ```azurepowershell-interactive
    $routeTablePrivate = New-AzureRmRouteTable `
      -Name 'myRouteTable-Private' `
      -ResourceGroupName $rgName `
      -location $location `
      -Route $routePublic
    ```
      
13. 使路由表與*私人*子網路產生關聯。

    ```azurepowershell-interactive
    Set-AzureRmVirtualNetworkSubnetConfig `
      -VirtualNetwork $virtualNetwork `
      -Name 'Private' `
      -AddressPrefix 10.0.1.0/24 `
      -RouteTable $routeTablePrivate | `
    Set-AzureRmVirtualNetwork
    ```
    
14. **選擇性：**在公用與私人子網路中建立虛擬機器，並完成[驗證路由](#validate-routing)中的步驟，驗證虛擬機器之間的通訊是透過網路虛擬設備路由傳送的。
15. **選擇性︰**若要刪除您在本教學課程中所建立的資源，請完成[刪除資源](#delete-resources)中的步驟。

## <a name="validate-routing"></a>驗證路由

1. 如果您還沒有驗證路由，請完成[建立路由和網路虛擬設備](#create-routes-and-network-virtual-appliance)中的步驟。
2. 在接下來的方塊中，按一下 [試試看] 按鈕，隨即開啟 Azure Cloud Shell。 如果出現提示，請使用您的 [Azure 帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登入 Azure。 如果您沒有 Azure 帳戶，您可以註冊 [免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)。 Azure Cloud Shell 是已經預先安裝 Azure 命令列介面的免費 Bash 殼層。 

    下列指令碼會建立兩個虛擬機器，其中一個位於公用子網路，而另一個則位於私人子網路。 這些指令碼也會針對 NVA 作業系統內的網路介面啟用 IP 轉送，讓作業系統透過網路介面路由傳送流量。 實際執行 NVA 通常會先檢查流量，再路由傳送，但是在本教學課程中，簡單的 NVA 只會路由傳送流量而不會加以檢查。 

    在接下來的 **Linux** 或 **Windows** 指令碼中，按一下 [複製] 按鈕，然後將指令碼內容貼到文字編輯器中。 變更 *adminPassword* 變數的密碼，然後將指令碼貼到 Azure Cloud Shell 中。 針對您在[建立路由和網路虛擬設備](#create-routes-and-network-virtual-appliance)的步驟 7 中建立網路虛擬設備時選取的作業系統，執行指令碼。 

    **Linux**

    ```azurecli-interactive
    #!/bin/bash

    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.    
    az vm extension set \
      --resource-group $rgName \
      --vm-name myVm-Nva \
      --name customScript \
      --publisher Microsoft.Azure.Extensions \
      --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
    ```

    **Windows**

    ```azurecli-interactive

    #!/bin/bash
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Public \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Private \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1"}'

    # Restart the NVA virtual machine.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Restart-Computer -ComputerName myVm-Nva -Force"}'
    ```

3. 驗證公用與私人子網路中的虛擬機器之間的通訊。 

    - 開啟 [SSH](../virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Linux) 或[遠端桌面](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Windows) 與 *myVm-Public* 虛擬機器公用 IP 位址之間的連線。
    - 在 *myVm-Public* 虛擬機器上的命令提示字元中，輸入 `ping myVm-Private`。 您之所以收到回覆，是因為 NVA 會將流量從公用子網路路由傳送至私人子網路。
    - 從 *myVm-Public* 虛擬機器，執行公用和私人子網路中的虛擬機器之間的路由追蹤。 請根據您在公用與私人子網路中的虛擬機器上所安裝的作業系統，輸入下列適當的命令之一：
        - **Windows**：在命令提示字元中，執行 `tracert myvm-private` 命令。
        - **Ubuntu**：執行 `tracepath myvm-private` 命令。
      流量會先通過 10.0.2.4 (NVA)，然後到達 10.0.1.4 (私人子網路中的虛擬機器)。 
    - 連線至 *myVm-Private* 虛擬機器，並 Ping *myVm-Public* 虛擬機器，以完成上述步驟。 追蹤路由會顯示經過 10.0.2.4 再到達 10.0.0.4 (公用子網路中的虛擬機器) 的通訊。
    
      > [!NOTE]
      > 先前的步驟可讓您確認 Azure 私人 IP 位址之間的路由。 如果您想要透過網路虛擬應用裝置將流量轉送 (或 proxy 處理) 至公用 IP 位址：
      > - 應用裝置必須提供網路位址轉譯或 proxy 功能。 如果是網路位址轉譯，裝置必須將來源 IP 位址轉譯為其自己的位址，然後將該要求轉送至公用 IP 位址。 不論應用裝置是否有將來源位址轉譯為網路位址 (或 proxy 處理)，Azure 會將網路虛擬應用裝置的私人 IP 位址轉譯為公用 IP 位址。 如需 Azure 用來將私人 IP 位址轉譯成公用 IP 位址之不同方法的詳細資訊，請參閱[了解輸出連線](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
      > - 路由表中的其他路由，例如首碼：0.0.0.0/0、下一個躍點類型 VirtualAppliance，與下一個躍點 IP 位址 10.0.2.4 (在先前的範例指令碼)。
      >
    - **選擇性**：若要驗證 Azure 內兩部虛擬機器之間的下一個躍點，請使用 Azure 網路監看員的 [下一個躍點] 功能。 使用網路監看員之前，您必須先針對您想要使用網路監看員的區域，[建立 Azure 網路監看員執行個體](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 在本教學課程中，使用的是美國東部區域。 一旦您啟用區域的網路監看員執行個體之後，請輸入下列命令，查看公用與私人子網路中虛擬機器之間的下一個躍點資訊：
     
        ```azurecli-interactive
        az network watcher show-next-hop --resource-group myResourceGroup --vm myVm-Public --source-ip 10.0.0.4 --dest-ip 10.0.1.4
        ```

       輸出會傳回 *10.0.2.4* 作為 **nextHopIpAddress**，並傳回 *VirtualAppliance* 作為 **nextHopType**。

> [!NOTE]
> 為了說明本教學課程中的概念，已將公用 IP 位址指派給公用和私人子網路中的虛擬機器，而在 Azure 內也啟用了這兩部虛擬機器的所有網路連接埠存取。 建立用於實際執行的虛擬機器時，您不得將公用 IP 位址指派給這些虛擬機器，但可在私人子網路前面部署網路虛擬設備，或將網路安全性群組指派給子網路、網路介面或兩者，來篩選私人子網路的網路流量。 若要深入了解網路安全性群組，請參閱[網路安全性群組](virtual-networks-nsg.md)。

## <a name="create-a-virtual-network"></a>建立虛擬網路

本教學課程需要有包含兩個子網路的現有虛擬網路。 在接下來的方塊中按一下 [試試看] 按鈕，可快速建立虛擬網路。 按一下 [試試看] 按鈕可開啟 [Azure Cloud Shell](../cloud-shell/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 雖然 Cloud Shell 會執行 PowerShell 或 Bash 殼層，但在本節中會使用 Bash 殼層來建立虛擬網路。 Bash 殼層已安裝 Azure 命令列介面。 如果出現 Cloud Shell 的提示，請使用您的 [Azure 帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登入 Azure。 如果您沒有 Azure 帳戶，您可以註冊 [免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)。 若要建立本教學課程中所使用的虛擬網路，請在下列方塊中按一下 [複製] 按鈕，然後將指令碼貼到 Azure Cloud Shell 中：

```azurecli-interactive
#!/bin/bash

#Set variables used in the script.
rgName="myResourceGroup"
location="eastus"

# Create a resource group.
az group create \
  --name $rgName \
  --location $location

# Create a virtual network with one subnet named Public.
az network vnet create \
  --name myVnet \
  --resource-group $rgName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24

# Create an additional subnet named Private in the virtual network.
az network vnet subnet create \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --vnet-name myVnet \
  --resource-group $rgName
```

若要深入了解如何使用入口網站、PowerShell 或 Azure Resource Manager 範本來建立虛擬網路，請參閱[建立虛擬網路](virtual-networks-create-vnet-arm-pportal.md)。

## <a name="delete-resources"></a>刪除資源

完成本教學課程之後，可考慮刪除所建立的資源，以免產生使用費。 刪除資源群組同時會刪除其內含的所有資源。 在 PowerShell 中，輸入下列命令：

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>後續步驟

- 建立[高可用性的網路虛擬設備](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- 網路虛擬設備通常會獲指派多個網路介面和 IP 位址。 了解如何[將網路介面新增至現有的虛擬機器](virtual-network-network-interface-vm.md#vm-add-nic)以及如何[將 IP 位址新增至現有的網路介面](virtual-network-network-interface-addresses.md#add-ip-addresses)。 雖然所有虛擬機器大小至少都可以包含兩個附加的網路介面，但是每個虛擬機器大小都支援網路介面數目的上限。 若要了解如何每個虛擬機器大小支援多少個網路介面，請參閱 [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) 和 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 的虛擬機器大小。 
- 建立使用者定義的路由，以強制透過[站台對站台 VPN 連線](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)進行通道流量內部部署。
