---
title: "建立具有多個 NIC 的 VM - Azure CLI 2.0 | Microsoft Docs"
description: "了解如何使用 Azure CLI 2.0 建立具有多個 NIC 的 VM。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8e906a4b-8583-4a97-9416-ee34cfa09a98
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 19b1757dd694e756cfd2d0d6cd67e64f43ccab7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-multiple-nics-using-the-azure-cli-20"></a>使用 Azure CLI 2.0 建立具有多個 NIC 的 VM

[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

> [!NOTE]
> Azure 建立和處理資源的部署模型有二種：[Resource Manager 和傳統](../resource-manager-deployment-model.md)。  本文涵蓋之內容包括使用 Resource Manager 部署模型，Microsoft 建議大部分的新部署使用此模型，而不是[傳統部署模型](virtual-network-deploy-multinic-classic-cli.md)。
>

## <a name="create"></a>建立 VM

您可以使用 Azure CLI 2.0 (本文) 或 [Azure CLI 1.0](virtual-network-deploy-multinic-cli-nodejs.md) 完成這項工作。 後續步驟所含變數之 "" 中的值，會使用案例中的設定建立資源。 請針對您的環境適當地變更值。

1. 如果尚未安裝 [Azure CLI 2.0](/cli/azure/install-az-cli2)，請先安裝此軟體。
2. 完成[建立 Linux VM 的 SSH 公用和私用金鑰組](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json)中的步驟，為 Linux VM 建立 SSH 公用和私用金鑰組。
3. 從命令殼層使用命令 `az login` 進行登入。
4. 在 Linux 或 Mac 電腦上執行後續的指令碼以建立 VM。 該指令碼會建立資源群組、一個具有兩個子網路的虛擬網路 (VNet)、兩個 NIC，以及連接了兩個 NIC 的 VM。 其中一個 NIC 會連線到一個子網路，並獲指派靜態的公用和私人 IP 位址。 另一個 NIC 則連線到另一個子網路，並獲指派靜態的私人 IP 位址，但沒有公用 IP 位址。 NIC、公用 IP 位址、虛擬網路和 VM 資源必須全都位於相同的位置和訂用帳戶。 雖然資源不需要全都位於相同的資源群組中，但在下列指令碼中，它們卻是如此。

```bash
#!/bin/sh

RgName="Multi-NIC-VM"
Location="westus"

# Create a resource group.
az group create \
--name $RgName \
--location $Location

# The address is assigned to the resource from a pool of IP adresses unique to each Azure region. 
# Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists
# the ranges for each region.

PipName="PIP-WEB"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static

# Create a virtual network with one subnet

VnetName="VNet1"
VnetPrefix="10.0.0.0/16"
VnetSubnet1Name="Front-End"
VnetSubnet1Prefix="10.0.0.0/24"
az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnet1Name \
--subnet-prefix $VnetSubnet1Prefix

# Create a second subnet within the VNet

VnetSubnet2Name="Back-end"
VnetSubnet2Prefix="10.0.1.0/24"
az network vnet subnet create \
--vnet-name $VnetName \
--resource-group $RgName \
--name $VnetSubnet2Name \
--address-prefix $VnetSubnet2Prefix

# Create a network interface connected to one of the subnets. The NIC is assigned a single dynamic private and
# public IP address by default, but you can instead, assign static addresses, or no public IP address at all.
# You can also assign multiple private or public IP addresses to each NIC. To learn more about IP addressing
# options for NICs, enter the az network nic create -h command.

Nic1Name="NIC-FE"
PrivateIpAddress1="10.0.0.5"

az network nic create \
--name $Nic1Name \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress1 \
--public-ip-address $PipName

# Create a second network interface and connect it to the other subnet. Though multiple NICs attached to the same
# VM can be connected to different subnets, the subnets must all be within the same VNet. Add additional NICs as necessary.

Nic2Name="NIC-BE"
PrivateIpAddress2="10.0.1.5"

az network nic create \
--name $Nic2Name \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet2Name \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress2

# Create a VM and attach the two NICs.

VmName="WEB"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article. Not all VM sizes support
# more than one NIC, so be sure to select a VM size that supports the number of NICs you want to attach to the VM.
# You must create the VM with at least two NICs if you want to add more after VM creation. If you create a VM with
# only one NIC, you can't add additional NICs to the VM after VM creation, regardless of how many NICs the VM supports.
# The VM size specified in the following variable supports two NICs.

VmSize="Standard_DS2"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# az vm image list command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file.

SshKeyValue="~/.ssh/id_rsa.pub"

# Before executing the following command, add variable names of additional NICs you may have added to the script that
# you want to attach to the VM. If creating a Windows VM, remove the **ssh-key-value** line and you'll be prompted for
# the password you want to configure for the VM.

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $Nic1Name $Nic2Name \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

除了建立具有兩個 NIC 的 VM 外，該指令碼還會建立︰
- 單一的進階受控磁碟 (預設)，但有其他選項可讓您選擇可以建立的磁碟類型。 如需詳細資料，請閱讀[使用 Azure CLI 2.0 建立 Linux VM](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文。
- 具有兩個子網路和一個公用 IP 位址的虛擬網路。 或者，您可以使用「現有」虛擬網路、子網路、NIC 或公用 IP 位址資源。 若要了解如何使用現有網路資源，而不是另外建立資源，請輸入 `az vm create -h`。

## <a name = "validate"></a>驗證 VM 建立和 NIC

1. 輸入命令 `az resource list --resouce-group Multi-NIC-VM --output table` 以查看指令碼所建立的資源清單。 所傳回的輸出中應該會有六個資源︰兩個 NIC、一個磁碟、一個公用 IP 位址、一個虛擬網路，以及一部虛擬機器。
2. 輸入命令 `az network public-ip show --name PIP-WEB --resource-group Multi-NIC-VM --output table`。 請注意，在傳回的輸出中，**IpAddress** 的值和 **PublicIpAllocationMethod** 的值是 Static。
3. 在執行下列命令之前，請移除 <>，以指令碼中的 **Username** 變數所用之名稱取代 *Username*，並以上一個步驟中的 **ipAddress** 取代 *ipAddress*。 執行下列命令以連線至 VM：`ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`。 
4. 在連線到 VM 後，執行 `sudo ifconfig` 命令以查看 eth0 和 eth1 介面。 每個 NIC 都已獲指派指令碼中由 Azure DHCP 伺服器所指定的靜態私人 IP 位址。 在刪除 VM 之前，指派給 NIC 的 IP 和 MAC 位址不會變更。 建議您不要變更作業系統內的 IP 位址，因為這會停用電腦的連線。 公用 IP 位址不會出現在作業系統中，因為它們是由 Azure 基礎結構轉譯為私人 IP 位址的網路位址，以及從私人 IP 位址轉譯而來的網路位址。

## <a name= "clean-up"></a>移除 VM 和相關聯的資源

如果您不會在生產環境使用這個練習中所建立的資源，建議您刪除它們。 VM、公用 IP 位址和磁碟資源在佈建後就會產生費用。 若要刪除這個練習當中所建立的資源，請完成下列步驟：
1. 若要檢視資源群組中的資源，請執行 `az resource list --resource-group Multi-NIC-VM` 命令。
2. 確認資源群組中除了本文指令碼所建立的資源外，沒有其他資源。 
3. 若要刪除本練習中建立的所有資源，請執行 `az group delete --name Multi-NIC-VM` 命令。 此命令會刪除資源群組以及其中包含的所有資源。

## <a name="next-steps"></a>後續步驟

任何網路流量均可流入和流出本文所建立的 VM。 您可以在 NSG 內定義輸入和輸出規則，以限制每個網路介面和 (或) 每個子網路可以流入和流出的流量。 若要深入了解 NSG，請閱讀 [NSG 概觀](virtual-networks-nsg.md)一文。