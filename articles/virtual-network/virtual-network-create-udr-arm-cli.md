---
title: "透過網路虛擬設備 Azure CLI，建立使用者定義的路由到路由網路流量 | Microsoft Docs"
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
ms.openlocfilehash: b41754e74708a095d95eee31da5e304771a6ae05
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2017
---
# <a name="create-a-user-defined-route---azure-cli"></a>建立使用者定義的路由 - Azure CLI

在本教學課程中，了解如何透過網路虛擬設備建立應立使用者定義的路由，以路由兩個[虛擬網路](virtual-networks-overview.md)子網路之間的流量。 網路虛擬設備是執行網路應用程式 (例如防火牆) 的虛擬機器。 若要深入了解您可以在 Azure 虛擬網路中部署的預先設定的網路虛擬設備，請參閱 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances)。

當您在虛擬網路中建立子網路時，Azure 會建立預設[系統路由](virtual-networks-udr-overview.md#system-routes)，讓所有子網路中的資源彼此通訊，如下圖所示：

![預設路由](./media/create-user-defined-route/default-routes.png)

在本教學課程中，您使用公用、私人和 DMZ 子網路建立虛擬網路，如下圖所示。 web 伺服器一般會部署到公用子網路，而應用程式或資料庫伺服器可能會部署到私人子網路。 您建立虛擬機器以做為 DMZ 子網路中的網路虛擬設備，並選擇性地在每個子網路中建立虛擬機器，以透過網路虛擬設備進行通訊。 公用和私人子網路之間的所有流量都會透過設備路由，如下圖所示：

![使用者定義的路由](./media/create-user-defined-route/user-defined-routes.png)

本文提供透過 Resource Manager 部署模型建立使用者定義路由的步驟，而此部署模型正是建立使用者定義路由時我們建議使用的部署模型。 如果您需要建立使用者定義的路由 (傳統)，請參閱[建立使用者定義的路由 (傳統)](virtual-network-create-udr-classic-cli.md)。 如果您不熟悉 Azure 部署模型，請參閱[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 若要深入了解使用者定義的路由，請參閱[使用者定義的路由概觀](virtual-networks-udr-overview.md#user-defined-routes)。

## <a name="create-routes-and-network-virtual-appliance"></a>建立路由和網路虛擬設備

無論您是從 Windows、Linux 或 macOS 執行命令，所使用的 Azure CLI 命令都相同。 不過，不同作業系統殼層的指令碼編寫會有差異。 下列步驟執行的指令碼需要安裝和執行 Bash 殼層中的 Azure CLI 命令。 您可以在 PC 上[安裝及設定 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)，或直接按一下任何指令碼中的 [試試看] 按鈕，在 Azure Cloud Shell 中執行指令碼。
 
1. **必要條件**：完成[建立虛擬網路](#create-a-virtual-network)的步驟，建立包含兩個子網路的一個虛擬網路。
2. 如果從電腦從執行 Azure CLI，請使用 `az login`命令登入 Azure。 如果使用 Cloud Shell，則您已經自動登入。
3. 設定在剩餘的步驟中使用的一些變數：

    ```azurecli-interactive
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    ```

4. 在必要條件中建立的虛擬網路中建立 *DMZ* 子網路：

    ```azurecli-interactive
    az network vnet subnet create \
      --name DMZ \
      --address-prefix 10.0.2.0/24 \
      --vnet-name myVnet \
      --resource-group $rgName
    ```

5. 建立 NVA 虛擬機器。 將靜態公用和私用 IP 位址指派給 CLI 所建立的網路介面。 靜態位址不會在虛擬機器的存留期間變更。 NVA 可以是執行 Linux 或 Windows 作業系統的虛擬機器。 若要建立虛擬機器，將任一作業系統的指令碼複製並貼到 CLI。 如果建立 Windows VM，將指令碼貼到文字編輯器，變更 *AdminPassword* 變數的值，然後將修改過的文字貼入 CLI。

    **Linux**

    ```azurecli-interactive
    az vm create \
      --resource-group $rgName \
      --name myVm-Nva \
      --image UbuntuLTS \
      --private-ip-address 10.0.2.4 \
      --public-ip-address myPublicIp-myVm-Nva \
      --public-ip-address-allocation static \
      --subnet DMZ \
      --vnet-name myVnet \
      --admin-username azureuser \
      --generate-ssh-keys
    ```

    **Windows**

    ```azurecli-interactive
    AdminPassword=ChangeToYourPassword
    az vm create \
      --resource-group $rgName \
      --name myVm-Nva \
      --image win2016datacenter \
      --private-ip-address 10.0.2.4 \
      --public-ip-address myPublicIp-myVm-Nva \
      --public-ip-address-allocation static \
      --subnet DMZ \
      --vnet-name myVnet \
      --admin-username azureuser \
      --admin-password $AdminPassword      
    ```

6. 啟用 NVA 網路介面的 IP 轉送。 啟用網路介面的 IP 轉送會導致 Azure 檢查來源/目的地 IP 位址。 如果未啟用此設定，則 Azure 會捨棄 NIC 接收之外的 IP 位址流量。

    ```azurecli-interactive
    az network nic update \
      --name myVm-NvaVMNic \
      --resource-group $rgName \
      --ip-forwarding true
    ```

7. 建立*公用*子網路的路由表。

    ```azurecli-interactive
    az network route-table create \
      --name myRouteTable-Public \
      --resource-group $rgName
    ```
    
8. 根據預設，Azure 會路由虛擬網路內所有子網路之間的流量。 建立路由來變更 Azure 的預設路由，讓公用子網路至私人子網路的流量是透過 NVA 路由，而不是直接路由至私人子網路。

    ```azurecli-interactive    
    az network route-table route create \
      --name ToPrivateSubnet \
      --resource-group $rgName \
      --route-table-name myRouteTable-Public \
      --address-prefix 10.0.1.0/24 \
      --next-hop-type VirtualAppliance \
      --next-hop-ip-address 10.0.2.4
    ```

9. 建立 *myRouteTable-Public* 路由表至*公用*子網路的關聯。 路由表與子網路產生關聯後，會讓 Azure 根據路由表中的路由來路由所有子網路的輸出流量。 路由表可以與零個或多個子網路產生關聯，而子網路可以有零個或一個相關聯的路由表。

    ```azurecli-interactive
    az network vnet subnet update \
      --name Public \
      --vnet-name myVnet \
      --resource-group $rgName \
      --route-table myRouteTable-Public
    ```

10. 建立*私人*子網路的路由表。

    ```azurecli-interactive
    az network route-table create \
      --name myRouteTable-Private \
      --resource-group $rgName
    ```
      
11. 透過 NVA 虛擬機器，建立將流量從*私人*子網路路由傳送至*公用*子網路的路由。

    ```azurecli-interactive
    az network route-table route create \
      --name ToPublicSubnet \
      --resource-group $rgName \
      --route-table-name myRouteTable-Private \
      --address-prefix 10.0.0.0/24 \
      --next-hop-type VirtualAppliance \
      --next-hop-ip-address 10.0.2.4
    ```

12. 使路由表與*私人*子網路產生關聯。

    ```azurecli-interactive
    az network vnet subnet update \
      --name Private \
      --vnet-name myVnet \
      --resource-group $rgName \
      --route-table myRouteTable-Private
    ```
    
13. **選擇性：**在公用與私人子網路中建立虛擬機器，並完成[驗證路由](#validate-routing)中的步驟，驗證虛擬機器之間的通訊是透過網路虛擬設備路由傳送的。
14. **選擇性︰**若要刪除您在本教學課程中所建立的資源，請完成[刪除資源](#delete-resources)中的步驟。

## <a name="validate-routing"></a>驗證路由

1. 如果您還沒有驗證路由，請完成[建立路由和網路虛擬設備](#create-routes-and-network-virtual-appliance)中的步驟。
2. 在接下來的方塊中，按一下 [試試看] 按鈕，隨即開啟 Azure Cloud Shell。 如果出現提示，請使用您的 [Azure 帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登入 Azure。 如果您沒有 Azure 帳戶，您可以註冊 [免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)。 Azure Cloud Shell 是已經預先安裝 Azure 命令列介面的免費 Bash 殼層。 

    下列指令碼會建立兩個虛擬機器，其中一個位於*公用*子網路，而另一個則位於*私人*子網路。 這些指令碼也會針對 NVA 作業系統內的網路介面啟用 IP 轉送，讓作業系統透過網路介面路由傳送流量。 實際執行 NVA 通常會先檢查流量，再路由傳送，但是在本教學課程中，簡單的 NVA 只會路由傳送流量而不會加以檢查。 

    在接下來的 **Linux** 或 **Windows** 指令碼中，按一下 [複製] 按鈕，然後將指令碼內容貼到文字編輯器中。 變更 *adminPassword* 變數的密碼，然後將指令碼貼到 Azure Cloud Shell 中。 針對您在[建立路由和網路虛擬設備](#create-routes-and-network-virtual-appliance)的步驟 5 中建立網路虛擬設備時所選取的作業系統，執行指令碼。 

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
    - 連線至 *myVm-Private* 虛擬機器，並 ping *myVm-Public* 虛擬機器，以完成上述步驟。 追蹤路由會顯示經過 10.0.2.4 再到達 10.0.0.4 (公用子網路中的虛擬機器) 的通訊。
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

完成本教學課程之後，可考慮刪除所建立的資源，以免產生使用費。 刪除資源群組同時會刪除其內含的所有資源。 在 CLI 工作階段中，輸入下列命令：

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>後續步驟

- 建立[高可用性的網路虛擬設備](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- 網路虛擬設備通常會獲指派多個網路介面和 IP 位址。 了解如何[將網路介面新增至現有的虛擬機器](virtual-network-network-interface-vm.md#vm-add-nic)以及如何[將 IP 位址新增至現有的網路介面](virtual-network-network-interface-addresses.md#add-ip-addresses)。 雖然所有虛擬機器大小至少都可以包含兩個附加的網路介面，但是每個虛擬機器大小都支援網路介面數目的上限。 若要了解如何每個虛擬機器大小支援多少個網路介面，請參閱 [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) 和 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 的虛擬機器大小。 
- 建立使用者定義的路由，以強制透過[站台對站台 VPN 連線](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)進行通道流量內部部署。
