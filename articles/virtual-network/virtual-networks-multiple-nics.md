---
title: "使用 PowerShell 建立具有多個 NIC 的 VM (傳統) | Microsoft Docs"
description: "深入了解如何使用 PowerShell 建立與設定具有多個 NIC 的 VM。"
services: virtual-network, virtual-machines
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: a1a3952c-2dcc-4977-bd7a-52d623c1fb07
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.openlocfilehash: 68ccc1cac22e593b099729fe68c6bee63df44d9b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-vm-classic-with-multiple-nics"></a>建立具有多個 NIC 的 VM (傳統)
您可以在 Azure 中建立虛擬機器 (VM) 並將多個網路介面 (NIC) 連接至每個 VM。 多個 NIC 是許多網路虛擬應用裝置的需求，例如應用程式傳遞和 WAN 最佳化解決方案。 多個 NIC 也能隔離 NIC 之間的流量。

![適用於 VM 的多個 NIC](./media/virtual-networks-multiple-nics/IC757773.png)

圖中顯示具有三個 NIC 的 VM，每個都連接到不同的子網路。

> [!IMPORTANT]
> Azure 建立和處理資源的部署模型有二種：[Resource Manager 和傳統](../resource-manager-deployment-model.md)。 本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議大部分的新部署使用 Resource Manager。

* 只有「預設」NIC 上才支援網際網路對向的 VIP (傳統部署)。 只有一個  VIP 可以連接到預設 NIC 的 IP。
* 執行個體層級公用 IP (LPIP) 位址 (傳統部署) 目前不支援多個 NIC 的 VM。
* VM 內部的 NIC 順序是隨機的，而且也可透過 Azure 基礎結構更新來變更。 不過，IP 位址及對應的乙太網路 MAC 位址會維持不變。 例如，假設 **Eth1** 具有 IP 位址 10.1.0.100 和 MAC 位址 00-0D-3A-B0-39-0D；在 Azure 基礎結構更新並重新開機之後，就無法變更為 **Eth2**，但是 IP 和 MAC 配對會保持相同。 當客戶起始重新啟動時，NIC 順序會保持相同。
* 每個 VM 上的每個 NIC 位址都必須位於子網路中，單一 VM 上的多個 NIC 每個都可以指派位於相同子網路的位址。
* VM 大小會決定您可以為 VM 建立的 NIC 數目。 請參考 [Windows Server](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 和 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 的VM 大小文章，以確定每個 VM 大小支援的 NIC 數目。 

## <a name="network-security-groups-nsgs"></a>網路安全性群組 (NSG)
在資源管理員部署中，VM 上的任何 NIC 可能會關聯至網路安全性群組 (NSG)，包括已啟用多個 NIC 功能的 VM 上的任何 NIC。 如果已為 NIC 指派子網路 (該子網路會關聯至 NSG) 內的位址，則子網路 NSG 中的規則也適用於該 NIC。 除了將子網路關聯至 NSG，您也可以將 NIC 關聯至 NSG。

如果將子網路關聯至 NSG，而且該子網路內的 NIC 會個別關聯至 NSG，則相關聯的 NSG 規則會根據傳入或傳出 NIC 的流量方向套用 **流程順序** ：

* **連入流量** 的目的地是上述流量中的 NIC，首先會通過子網路，並在傳入 NIC 前觸發子網路的 NSG 規則，然後再觸發 NIC 的 NSG 規則。
* **連出流量** 的來源是有問題的流量中第一次從 NIC 流出的 NIC，會在流經子網路之前觸發 NIC 的 NSG 規則，然後觸發子網路的 NSG 規則。

深入了解 [網路安全性群組](virtual-networks-nsg.md) 和其如何根據與子網路、VM 和 NIC 之關聯而套用。

## <a name="how-to-configure-a-multi-nic-vm-in-a-classic-deployment"></a>如何在傳統部署中設定有多個 NIC 的 VM
下列指示將協助您建立多個 NIC 的 VM，其中包含 3 個 NIC：一個預設的 NIC 和兩個額外的 NIC。 組態步驟將建立 VM，此 VM 將根據下列服務組態檔片段來設定：

    <VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
    <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
    … Skip over the remainder section …
    </VirtualNetworkSite>


嘗試執行此範例的 PowerShell 命令之前，您需要具備下列必要條件。

* Azure 訂用帳戶。
* 已設定的虛擬網路。 如需 VNet 的詳細資訊，請參閱 [虛擬網路概觀](virtual-networks-overview.md) 。
* 已下載並安裝最新版的 Azure PowerShell。 請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。

若要建立具有多個 NIC 的 VM，請完成下列步驟，在單一 PowerShell 工作階段中輸入每個命令︰

1. 從 Azure VM 映像庫中選取 VM 映像 請注意，映像經常變更且可依區域取得。 以下範例中指定的映像可能會變更，或者可能不在您的區域中，因此，請務必指定您需要的映像。

    ```powershell
    $image = Get-AzureVMImage `
    -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"
    ```

2. 建立 VM 組態。

    ```powershell
    $vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
    -Image $image.ImageName –AvailabilitySetName "MyAVSet"
    ```

3. 建立預設的系統管理員登入。

    ```powershell
    Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
    -Password "<YourAdminPassword>"
    ```

4. 在 VM 組態中加入其他 NIC。

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
    -SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm
    Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
    -SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm
    ```

5. 指定預設 NIC 的子網路和 IP 位址。

    ```powershell
    Set-AzureSubnet -SubnetNames "Frontend" -VM $vm
    Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm
    ```

6. 在虛擬網路中建立 VM。

    ```powershell
    New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm
    ```

    > [!NOTE]
    > 您在此處指定的 VNet 必須已經存在 (如必要條件中所提及)。 下面範例指定名稱為 **MultiNIC-VNet**的虛擬網路。
    >

## <a name="limitations"></a>限制
使用多個 NIC 功能時有下列限制︰

* 具多個 NIC 的 VM 必須建立在 Azure 虛擬網路 (VNet) 中。 非 VNet 的 VM 無法設定為具有多個 NIC。
* 可用性設定組中的所有 VM 都必須使用多個 NIC 或單一 NIC。 在可用性設定組內無法混用多個 NIC VM 和單一 NIC VM。 雲端服務中的 VM 適用相同規則。 具多個 NIC 的 VM 不需要有相同數目的 NIC，只要它們各自都有至少兩個即可。
* 一旦部署 VM 之後，如果沒有刪除後再重新建立，則無法使用多個 NIC 設定包含單一 NIC 的 VM (反之亦然)。

## <a name="secondary-nics-access-to-other-subnets"></a>其他子網路的次要 NIC 存取
根據預設，將不會使用預設閘道設定次要 NIC，因為次要 NIC 上的流量將會限制在相同的子網路內。 如果使用者想要啟用次要 NIC 在其子網路外部通訊，他們必須在路由表中新增項目以設定閘道，如下所述。

> [!NOTE]
> 在 2015 年 7 月之前建立的 VM 可能已經為所有 NIC 設定預設閘道。 在這些 VM 重新開機之前，將不會移除次要 NIC 的預設閘道。 在使用弱式主機路由模型的作業系統 (例如 Linux) 中，如果輸入和輸出流量使用不同的 NIC，網際網路連線可能會中斷。
> 

### <a name="configure-windows-vms"></a>設定 Windows VM
假設您有包含兩個 NIC 的 Windows VM，如下所示：

* 主要 NIC IP 位址：192.168.1.4
* 次要 NIC IP 位址：192.168.2.5

此 VM 的 IPv4 路由表看起來像這樣：

    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
              0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
      127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
          192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
          192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
        192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
          192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
          192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
        192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
            224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
            224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
      255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
      255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
    ===========================================================================

請注意，預設路由 (0.0.0.0) 僅提供主要 NIC 使用。 您將無法存取次要 NIC 子網路外部的資源，如下所示：

    C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5

    Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.
    PING: transmit failed. General failure.

若要在次要 NIC 上新增預設路由，請遵循下列步驟：

1. 從命令提示字元執行下列命令來識別次要 NIC 的索引編號：
   
        C:\Users\Administrator>route print
        ===========================================================================
        Interface List
         29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
         27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
          1...........................Software Loopback Interface 1
         14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
         20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
        ===========================================================================
2. 請注意資料表中的第二個項目，索引為 27 (此範例中)。
3. 透過下列方式，經由命令提示字元執行 **route add** 命令。 在此範例中，您指定 192.168.2.1 做為次要 NIC 的預設閘道：
   
        route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27
4. 若要測試連線，請移至命令提示字元並嘗試 ping 不同的次要 NIC 子網路，如下列範例所示：
   
        C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
   
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
        Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
        Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
5. 您也可以檢查您的路由表，來檢查新加入的路由，如下所示：
   
        C:\Users\Administrator>route print
   
        ...
   
        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                  0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
                  0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### <a name="configure-linux-vms"></a>設定 Linux VM
若是 Linux VM，因為預設行為是使用弱式主機路由，建議您將次要 NIC 的流量限制在相同的子網路中。 不過，如果某些情況要求子網路外部的連線，使用者應該根據路由啟用原則，以確保輸入和輸出流量都使用相同的 NIC。

## <a name="next-steps"></a>後續步驟
* 部署 [在資源管理員部署中的 2 層應用程式案例之多個 NIC VM](virtual-network-deploy-multinic-arm-template.md)。
* 部署 [在傳統部署中的 2 層應用程式案例之多個 NIC VM](virtual-network-deploy-multinic-classic-ps.md)。

