---
title: "使用 Azure 入口網站建立分區 Windows VM | Microsoft Docs"
description: "使用 Azure 入口網站在可用性區域中建立 Windows VM"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: danlep
ms.custom: 
ms.openlocfilehash: 4d48aff7d29def9fa54438a11885b4ff4fba54cc
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2017
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>使用 Azure 入口網站在可用性區域中建立 Windows 虛擬機器

本文逐步引導您使用 Azure 入口網站在 Azure 可用性區域中建立虛擬機器。 [可用性區域](../../availability-zones/az-overview.md)是指 Azure 區域內實際上分隔的區域。 萬一整個資料中心失敗或遺失，使用可用性區域可保護您的應用程式和資料免於受害。

[!INCLUDE [availability-zones-preview-statement.md](../../../includes/availability-zones-preview-statement.md)]


## <a name="log-in-to-azure"></a>登入 Azure 

登入 Azure 入口網站，網址是 https://portal.azure.com。

## <a name="create-virtual-machine"></a>Create virtual machine

1. 按一下 Azure 入口網站左上角的 [新增] 按鈕。

2. 選取 [計算]，然後選取 [Windows Server 2016 Datacenter]。 

3. 輸入虛擬機器資訊。 在此輸入的使用者名稱和密碼用於登入虛擬機器。 完成時，按一下 [確定]。

    ![在入口網站刀鋒視窗中輸入 VM 的基本資訊](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. 選取 VM 的大小。 若要查看更多大小，請選取 [檢視全部] 或變更 [支援的磁碟類型] 篩選條件。 請慎選在可用性區域預覽中支援的其中一種大小，例如 *DS1_v2 Standard*。 

    ![顯示 VM 大小的螢幕擷取畫面](./media/create-portal-availability-zone/create-linux-vm-portal-sizes.png)  

5. 在 [設定] > [高可用性] 底下，從 [可用性區域] 下拉式清單中選取其中一個編號的區域、保留其餘的預設值，並按一下 [確定]。

    ![選取可用性區域](./media/create-portal-availability-zone/create-linux-vm-portal-availability-zone.png)

6. 在 [摘要] 頁面上，按一下 [購買] 來啟動虛擬機器部署。

7. 系統會將 VM 釘選到 Azure 入口網站儀表板。 一旦完成部署後，VM 摘要就會自動開啟。


## <a name="zone-for-ip-address-and-managed-disk"></a>IP 位址和受控磁碟的區域

在可用性區域中部署虛擬機器時，會在相同的可用性區域中部署 IP 位址和受管理磁碟的資源。 您可以使用 Azure PowerShell 來確認區域設定。 如果您需要安裝或升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。

在下列範例中會取得名為 *myResourceGroup* 的資源群組中資源的相關資訊。 請取代您用來建立虛擬機器的資源群組名稱。

以 [Get-AzureRmPublicIpAddress](/en-us/powershell/module/azurerm.network/get-azurermpublicipaddress) 來找出公用 IP 位址的區域：

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup
```
輸出中的 `Zones` 設定會顯示與 VM 相同的可用性區域中的公用 IP 位址：

```powershell
Name                     : myVM-ip
ResourceGroupName        : myResourceGroup
Location                 : eastus2
Id                       : /subscriptions/e44f251c-c67e-4760-9ed6-bf99a306ecff/resourceGroups/danlep0911/providers/Micr
                           osoft.Network/publicIPAddresses/myVM-ip
Etag                     : W/"b67e14c0-7e8a-4d12-91c5-da2a5dfad132"
ResourceGuid             : 314bf57d-9b25-4474-9282-db3561d536aa
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Dynamic
IpAddress                : 13.68.16.25
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                             "Id": "/subscriptions/e44f251c-c67e-4760-9ed6-bf99a306ecff/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVM11842/ipConfigurations/ipconfig1"
                           }
DnsSettings              : null
Zones                    : {2}
```


也會在相同的可用性區域中建立 VM 的受管理磁碟資源。 您可使用 [Get AzureRmDisk](/powershell/module/azurerm.compute/get-azurermdisk) 來確認這點：

```powershell
Get-AzureRmDisk -ResourceGroupName myResourceGroup
```

輸出會顯示與 VM 相同的可用性區域中的受控磁碟：

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```

## <a name="next-steps"></a>後續步驟

在本文中，您已學到如何在可用性區域中建立 VM。 深入了解 Azure VM 的[區域和可用性](regions-and-availability.md)。
