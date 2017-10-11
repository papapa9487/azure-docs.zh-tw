---
title: "建立具有靜態公用 IP 位址的 VM - Azure CLI 2.0 | Microsoft Docs"
description: "了解如何使用 Azure 命令列介面 (CLI) 2.0 建立具有靜態公用 IP 位址的 VM。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a4c32694949880037f01bb2b6b9779d2cbb9809c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-cli-20"></a>使用 Azure CLI 2.0 建立具有靜態公用 IP 位址的 VM

> [!div class="op_single_selector"]
> * [Azure 入口網站](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI 2.0](virtual-network-deploy-static-pip-arm-cli.md)
> * [Azure CLI 1.0](virtual-network-deploy-static-pip-cli-nodejs.md)
> * [範本](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (傳統)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

Azure 建立和處理資源的部署模型有二種： [資源管理員和傳統](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 本文涵蓋之內容包括使用 Resource Manager 部署模型，Microsoft 建議大部分的新部署使用此模型，而不是傳統部署模型。

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name = "create"></a>建立 VM

您可以使用 Azure CLI 2.0 (本文) 或 [Azure CLI 1.0](virtual-network-deploy-static-pip-cli-nodejs.md) 完成這項工作。 後續步驟所含變數之 "" 中的值，會使用案例中的設定建立資源。 請針對您的環境適當地變更值。

1. 如果尚未安裝 [Azure CLI 2.0](/cli/azure/install-az-cli2)，請先安裝此軟體。
2. 完成[建立 Linux VM 的 SSH 公用和私用金鑰組](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json)中的步驟，為 Linux VM 建立 SSH 公用和私用金鑰組。
3. 從命令殼層使用命令 `az login` 進行登入。
4. 在 Linux 或 Mac 電腦上執行後續的指令碼以建立 VM。 Azure 公用 IP 位址、虛擬網路、網路介面和 VM 資源必須全都位於相同的位置。 雖然資源不需要全都位於相同的資源群組中，但在下列指令碼中，它們卻是如此。

```bash
RgName="IaaSStory"
Location="westus"

# Create a resource group.

az group create \
--name $RgName \
--location $Location

# Create a public IP address resource with a static IP address using the --allocation-method Static option.
# If you do not specify this option, the address is allocated dynamically. The address is assigned to the
# resource from a pool of IP adresses unique to each Azure region. The DnsName must be unique within the
# Azure location it's created in. Download and view the file from https://www.microsoft.com/en-us/download/details.aspx?id=41653#
# that lists the ranges for each region.

PipName="PIPWEB1"
DnsName="iaasstoryws1"
az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--allocation-method Static \
--dns-name $DnsName

# Create a virtual network with one subnet

VnetName="TestVNet"
VnetPrefix="192.168.0.0/16"
SubnetName="FrontEnd"
SubnetPrefix="192.168.1.0/24"
az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $SubnetName \
--subnet-prefix $SubnetPrefix

# Create a network interface connected to the VNet with a static private IP address and associate the public IP address
# resource to the NIC.

NicName="NICWEB1"
PrivateIpAddress="192.168.1.101"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $SubnetName \
--vnet-name $VnetName \
--private-ip-address $PrivateIpAddress \
--public-ip-address $PipName

# Create a new VM with the NIC

VmName="WEB1"

# Replace the value for the VmSize variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-sizes article.
VmSize="Standard_DS1"

# Replace the value for the OsImage variable with a value for *urn* from the output returned by entering
# the `az vm image list` command. 

OsImage="credativ:Debian:8:latest"
Username='adminuser'

# Replace the following value with the path to your public key file.
SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
# If creating a Windows VM, remove the previous line and you'll be prompted for the password you want to configure for the VM.
```

除了建立 VM 外，該指令碼還會建立︰
- 單一的進階受控磁碟 (預設)，但有其他選項可讓您選擇可以建立的磁碟類型。 如需詳細資料，請閱讀[使用 Azure CLI 2.0 建立 Linux VM](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 一文。
- 虛擬網路、子網路、NIC 和公用 IP 位址資源。 或者，您可以使用「現有」虛擬網路、子網路、NIC 或公用 IP 位址資源。 若要了解如何使用現有網路資源，而不是另外建立資源，請輸入 `az vm create -h`。

## <a name = "validate"></a>驗證 VM 建立和公用 IP 位址

1. 輸入命令 `az resource list --resouce-group IaaSStory --output table` 以查看指令碼所建立的資源清單。 所傳回的輸出中應該會有五個資源︰網路介面、磁碟、公用 IP 位址、虛擬網路及虛擬機器。
2. 輸入命令 `az network public-ip show --name PIPWEB1 --resource-group IaaSStory --output table`。 請注意，在傳回的輸出中，**IpAddress** 的值和 **PublicIpAllocationMethod** 的值是 Static。
3. 在執行下列命令之前，請移除 <>，以指令碼中的 **Username** 變數所用之名稱取代 *Username*，並以上一個步驟中的 **ipAddress** 取代 *ipAddress*。 執行下列命令以連線至 VM：`ssh -i ~/.ssh/azure_id_rsa <Username>@<ipAddress>`。 

## <a name= "clean-up"></a>移除 VM 和相關聯的資源

如果您不會在生產環境使用這個練習中所建立的資源，建議您刪除它們。 VM、公用 IP 位址和磁碟資源在佈建後就會產生費用。 若要刪除這個練習當中所建立的資源，請完成下列步驟：

1. 若要檢視資源群組中的資源，請執行 `az resource list --resource-group IaaSStory` 命令。
2. 確認資源群組中除了本文指令碼所建立的資源外，沒有其他資源。 
3. 若要刪除本練習中建立的所有資源，請執行 `az group delete -n IaaSStory` 命令。 此命令會刪除資源群組以及其中包含的所有資源。

## <a name="next-steps"></a>後續步驟

任何網路流量均可流入和流出本文所建立的 VM。 您可以在 NSG 內定義輸入和輸出規則，以限制網路介面和 (或) 子網路可以流入和流出的流量。 若要深入了解 NSG，請閱讀 [NSG 概觀](virtual-networks-nsg.md)一文。