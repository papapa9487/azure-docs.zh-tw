---
title: "透過網路虛擬設備，建立使用者定義的路由來路由網路流量 - Azure 入口網站 | Microsoft Docs"
description: "了解如何建立使用者定義的路由，透過網路虛擬設備來路由網路流量以覆寫 Azure 的預設路由。"
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
ms.openlocfilehash: 736e48f9651d89a1f4e8e0ae72cdffebb8e9c6e0
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="create-a-user-defined-route---azure-portal"></a>建立使用者定義的路由 - Azure 入口網站

在本教學課程中，了解如何透過網路虛擬設備建立使用者定義的路由，以路由兩個[虛擬網路](virtual-networks-overview.md)子網路之間的流量。 網路虛擬設備是執行網路應用程式 (例如防火牆) 的虛擬機器。 若要深入了解您可以在 Azure 虛擬網路中部署之預先設定的網路虛擬設備，請參閱 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) \(英文\)。

當您在虛擬網路中建立子網路時，Azure 會建立預設[系統路由](virtual-networks-udr-overview.md#system-routes)，讓所有子網路中的資源彼此通訊，如下圖所示：

![預設路由](./media/create-user-defined-route/default-routes.png)

在本教學課程中，您使用公用、私人和 DMZ 子網路建立虛擬網路，如下圖所示。 網頁伺服器通常可能會部署到公用子網路，而應用程式或資料庫伺服器則可能會部署到私人子網路。 您可以建立虛擬機器作為 DMZ 子網路中的網路虛擬設備，並選擇性地在每個子網路中建立可透過網路虛擬設備進行通訊的虛擬機器。 公用和私人子網路之間的所有流量都會透過設備路由，如下圖所示：

![使用者定義的路由](./media/create-user-defined-route/user-defined-routes.png)

本文提供透過 Resource Manager 部署模型建立使用者定義路由的步驟，而此部署模型正是建立使用者定義路由時我們建議使用的部署模型。 如果您需要建立使用者定義的路由 (傳統)，請參閱[建立使用者定義的路由 (傳統)](virtual-network-create-udr-classic-ps.md)。 如果您不熟悉 Azure 部署模型，請參閱[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 若要深入了解使用者定義的路由，請參閱[使用者定義的路由概觀](virtual-networks-udr-overview.md#user-defined)。

## <a name="create-routes-and-network-virtual-appliance"></a>建立路由和網路虛擬設備

1. **必要條件**：完成[建立虛擬網路](#create-a-virtual-network)中的步驟，以建立包含兩個子網路的虛擬網路。
2. 建立虛擬網路之後，使用網際網路瀏覽器前往 [Azure 入口網站](https://portal.azure.com)。 使用您的 [Azure 帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登入。
3. 在入口網站頂端的 [搜尋資源] 方塊中，輸入 *myResourceGroup*。 當搜尋結果中出現 **MyResourceGroup** 時，按一下該項目。 此資源群組是在必要條件步驟中所建立。
4. 按一下 [myVnet]，如下圖所示：

    ![網路介面設定](./media/create-user-defined-route/virtual-network.png)

5. 建立網路虛擬設備的子網路：
 
    - 在 [myVnet] 下，按一下左側 [設定] 底下的 [子網路]。
    - 按一下 [+ 子網路]，如下圖所示：

        ![子網路](./media/create-user-defined-route/subnets.png) 
    - 在 [新增子網路] 底下輸入下列值，然後按一下 [確定]：

        |設定|值|
        |-----|-----|
        |名稱|DMZ|
        |位址範圍 (CIDR 區塊)|10.0.2.0/24|

6. 建立網路虛擬設備虛擬機器：

    - 在入口網站的左側，依序按一下 [+ 新增] 和 [計算]，然後按一下 [Windows Server 2016 Datacenter] 或 [Ubuntu Server 16.04 LTS]。
    - 在出現的 [基本] 刀鋒視窗中，輸入下列值，然後按一下 [確定]。

        |設定|值|
        |---|---|
        |名稱|myVm-Nva|
        |使用者名稱|azureuser|
        |密碼和確認密碼|您選擇的密碼|
        |訂用帳戶|選取您的訂用帳戶|
        |資源群組|按一下 [使用現有的]，然後選取 [myResourceGroup]|
        |位置|美國東部|
    - 在出現的 [選擇大小] 刀鋒視窗上，按一下 [DS1_V2 標準]，然後按一下 [選取]。
    - 在出現的 [設定] 刀鋒視窗上，按一下 [虛擬網路]。 在出現的 [選擇虛擬網路] 刀鋒視窗中，按一下 [myVnet]。
    - 在 [設定] 刀鋒視窗上，按一下 [子網路]。 在出現的 [選擇子網路] 刀鋒視窗上，按一下 [DMZ]。 
    - 保留其餘設定的預設值，然後按一下 [確定]。
    - 在出現的 [建立] 刀鋒視窗上，按一下 [建立]。 虛擬機器的部署需要數分鐘的時間。

    > [!NOTE]
    > 除了建立虛擬機器之外，Azure 入口網站預設還可以建立公用 IP 位址，並將其指派給虛擬機器。 如果您已在生產環境中部署虛擬機器，可以選擇不將公用 IP 位址指派給虛擬機器。 相反地，您可以從虛擬網路內的其他虛擬機器來存取網路虛擬設備。 若要深入了解公用 IP 位址，請參閱[管理公用 IP 位址](virtual-network-public-ip-address.md)。

7. 指派靜態私人 IP 位址，然後為入口網站在先前步驟中建立的網路介面，啟用 IP 轉送。 
    - 在頁面頂端的 [搜尋資源] 方塊上，輸入 *myVm-Nva*。
    - 當 myVm-Nva 出現在搜尋結果中時，按一下 [myVm-Nva]。
    - 按一下左側 [設定] 底下的 [網路]。
    - 在 [myVm-Nva - 網路介面] 底下，按一下網路介面的名稱。 名稱是 **myvm-nva***X*，其中 *X* 是入口網站所指派的編號。
    - 在網路介面中，按一下 [設定] 底下的 [IP 組態]，如下圖所示：

        ![網路介面設定](./media/create-user-defined-route/network-interface-settings.png)
        
    - 針對 [IP 轉送] 設定，按一下 [啟用]，然後按一下 [儲存]。 接收未定址至獲指派 IP 位址之流量的每個網路介面，都必須啟用 IP 轉送。 啟用 IP 轉送時，會針對網路介面停用 Azure 的來源/目的地檢查。
    - 按一下 IP 組態清單中的 [ipconfig1]。
    - 在 [ipconfig1] 底下，針對私人 IP 位址的 [指派]，按一下 [靜態]，如下圖所示：

        ![IP 組態](./media/create-user-defined-route/ip-configuration.png)
    - 如上圖所示，在 [私人 IP 位址設定] 的 [IP 位址] 底下，輸入 *10.0.2.4*。 將靜態 IP 位址指派給網路介面時，可確保位址不會在網路介面所連接之虛擬機器的存留期間變更。 所輸入的位址目前未指派給網路介面所在 DMZ 子網路中的其他資源。 
    - 若要儲存組態，請按一下 [ipconfig1] 底下的 [儲存]。 請不要關閉 [ipconfig1] 方塊，直到您在入口網站中收到網路介面已儲存的通知為止。
 
8. 建立兩個路由表。 路由表包含零個或多個路由：

    - 在入口網站的左側，按一下 [+ 新增] > [網路] > [路由表]。
    - 在 [建立路由表] 底下輸入下列值，然後按一下 [建立]：

        |設定|值|
        |---|---|
        |名稱|myRouteTable-Public|
        |訂用帳戶|選取您的訂用帳戶|
        |資源群組|選取 [使用現有的]，然後選取 [myResourceGroup]|
        |位置|美國東部|
    
    - 再次完成先前步驟 8 的子步驟，但將路由表命名為 *myRouteTable-Private*。
9. 將路由新增至 *myRouteTable-Public* 路由表，然後建立該路由表與公用子網路的關聯：

    - 在入口網站頂端的 [搜尋資源] 方塊中，輸入 *myRouteTable-Public*。 當搜尋結果中出現 **myRouteTable-Public** 時，按一下該項目。
    - 在 **myRouteTable-Public** 底下，按一下 [設定] 清單中的 [路由]。
    - 按一下 [myRouteTable-Public - 路由] 底下的 [+ 新增]。
    -  根據預設，Azure 會路由虛擬網路內所有子網路之間的流量。 建立路由來變更 Azure 的預設路由，讓公用子網路的流量透過 NVA 路由，而不是直接路由至私人子網路。 在出現的 [新增路由] 刀鋒視窗上，輸入下列值，然後按一下 [確定]：

        |設定|值|說明|
        |---|---|---|
        |路由名稱|ToPrivateSubnet|此路由會將流量透過網路虛擬設備，導向至私人子網路。|
        |位址首碼|10.0.1.0/24| 傳送到此位址首碼 (10.0.1.0 - 10.0.1.254) 內任何位址的任何流量都會傳送至網路虛擬設備。|
        |下一個躍點類型| 選取 [虛擬設備]||
        |下一個躍點位址|10.0.2.4| 連接至網路虛擬設備之網路介面的靜態私人 IP 位址。 您可以為其指定位址的唯一躍點類型為 [虛擬設備]。|

    - 在 **myRouteTable-Public** 下，按一下 [設定] 底下的 [子網路]。 
    - 按一下 [myRouteTable-Public - 子網路] 底下的 [+ 關聯]。
    - 按一下 [關聯子網路] 底下的 [虛擬網路]，然後按一下 [myVnet]。
    - 按一下 [關聯子網路] 底下的 [子網路]，然後按一下 [選擇子網路] 底下的 [公用]。 
    - 若要儲存組態，請按一下 [關聯子網路] 底下的 [確定]。 子網路可以有零個或一個與其相關聯的路由表。
10. 搜尋 **myRouteTable-Private**、建立具有下列設定的路由，然後建立路由表和 **myVnet** 虛擬網路之私人子網路的關聯，以便再次完成步驟 9：

    |設定|值|說明|
    |---|---|---|
    |路由名稱|ToPublicSubnet|此路由會將流量透過網路虛擬設備，導向至公用子網路。|
    |位址首碼|10.0.0.0/24| 傳送到此位址首碼 (10.0.0.0 - 10.0.1.254) 內任何位址的任何流量都會傳送至網路虛擬設備。|
    |下一個躍點類型| 選取 [虛擬設備]||
    |下一個躍點位址|10.0.2.4||

    私人和公用子網路中任何資源之間的網路流量都會流經網路虛擬設備。 
11. **選擇性：**在公用與私人子網路中建立虛擬機器，並完成[驗證路由](#validate-routing)中的步驟，驗證虛擬機器之間的通訊是透過網路虛擬設備路由傳送的。 
12. **選擇性︰**完成[刪除資源](#delete-resources)中的步驟，以刪除您在本教學課程中所建立的資源。

## <a name="validate-routing"></a>驗證路由

1. 如果您還沒有驗證路由，請完成[建立路由和網路虛擬設備](#create-routes-and-network-virtual-appliance)中的步驟。
2. 在接下來的方塊中，按一下 [試試看] 按鈕，隨即開啟 Azure Cloud Shell。 如果出現提示，請使用您的 [Azure 帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登入 Azure。 如果您沒有 Azure 帳戶，您可以註冊 [免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)。 Azure Cloud Shell 是已經預先安裝 Azure 命令列介面的免費 Bash 殼層。 

    下列指令碼會建立兩個虛擬機器，其中一個位於公用子網路，而另一個則位於私人子網路。 這些指令碼也會針對 NVA 作業系統內的網路介面啟用 IP 轉送，讓作業系統透過網路介面路由傳送流量。 實際執行 NVA 通常會先檢查流量，再路由傳送，但是在本教學課程中，簡單的 NVA 只會路由傳送流量而不會加以檢查。 

    在接下來的 **Linux** 或 **Windows** 指令碼中，按一下 [複製] 按鈕，然後將指令碼內容貼到文字編輯器中。 變更 *adminPassword* 變數的密碼，然後將指令碼貼到 Azure Cloud Shell 中。 針對您在[建立路由和網路虛擬設備](#create-routes-and-network-virtual-appliance)的步驟 6 中建立網路虛擬設備時選取的作業系統，執行指令碼。 

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
    - **選擇性**：若要驗證 Azure 內兩個虛擬機器之間的下一個躍點，請使用 Azure 網路監看員的下一個躍點功能。 使用網路監看員之前，您必須先針對想要使用網路監看員的區域，[建立 Azure 網路監看員執行個體](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 在本教學課程中，使用的是美國東部區域。 一旦您啟用區域的網路監看員執行個體之後，請輸入下列命令，查看公用與私人子網路中虛擬機器之間的下一個躍點資訊：
     
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

完成本教學課程之後，可考慮刪除所建立的資源，以免產生使用費。 刪除資源群組同時會刪除其內含的所有資源。 開啟入口網站，然後完成下列步驟：

1. 在入口網站的搜尋方塊中，輸入 **myResourceGroup**。 在搜尋結果中按一下 [myResourceGroup]。
2. 在 [myResourceGroup] 刀鋒視窗中，按一下 [刪除] 圖示。
3. 若要確認刪除動作，請在 [輸入資源群組名稱] 方塊中輸入 **myResourceGroup**，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

- 建立[高可用性的網路虛擬設備](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- 網路虛擬設備通常會獲指派多個網路介面和 IP 位址。 了解如何[將網路介面新增至現有的虛擬機器](virtual-network-network-interface-vm.md#vm-add-nic)以及如何[將 IP 位址新增至現有的網路介面](virtual-network-network-interface-addresses.md#add-ip-addresses)。 雖然所有虛擬機器大小至少都可以包含兩個附加的網路介面，但是每個虛擬機器大小都支援網路介面數目的上限。 若要了解如何每個虛擬機器大小支援多少個網路介面，請參閱 [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) 和 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 的虛擬機器大小。 
- 建立使用者定義的路由，以強制透過[站台對站台 VPN 連線](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)進行通道流量內部部署。
