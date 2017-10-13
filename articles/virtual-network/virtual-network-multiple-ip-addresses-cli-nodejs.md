---
title: "使用 Azure CLI 1.0 建立具有多個 IP 位址的 VM | Microsoft Docs"
description: "了解如何使用 Azure CLI 1.0 對虛擬機器指派多個 IP 位址 | Resource Manager。"
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
ms.openlocfilehash: 9f085dfa1fe4db36d58cb976bb550a46bf241ac7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli-10"></a>使用 Azure CLI 1.0 將多個 IP 位址指派給虛擬機器

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

本文說明如何使用 Azure CLI 1.0 透過 Azure Resource Manager 部署模型建立虛擬機器 (VM)。 無法將多個 IP 位址指派給透過傳統部署模型建立的資源。 若要深入了解 Azure 部署模型，請參閱[了解部署模型](../resource-manager-deployment-model.md)文章。

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>建立有多個 IP 位址的 VM

您可以使用 Azure CLI 1.0 (本文) 或 [Azure CLI 2.0](virtual-network-multiple-ip-addresses-cli.md) 完成這項工作。 後續步驟說明如何使用多個 IP 位址建立範例 VM，如案例中所述。 視您的實作而定，變更變數名稱和 IP 位址類型。

1. 依照[安裝和設定 Azure CLI](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 文章中的步驟來安裝和設定 Azure CLI 1.0，並使用 `azure-login` 命令登入 Azure 帳戶。

2. 建立資源群組：
    
    ```azurecli
    RgName=myResourceGroup
    Location=westcentralus
    azure group create --name $RgName --location $Location
    ```
3. 建立虛擬網路：

    ```azurecli
    azure network vnet create --resource-group $RgName --location $Location --name myVNet \
    --address-prefixes 10.0.0.0/16
    ```
4. 在虛擬網路中建立子網路：

    ```azurecli
    azure network vnet subnet create --name mySubnet --resource-group $RgName --vnet-name myVNet \
    --address-prefix 10.0.0.0/24
    ```
    
5. 為 VM 建立儲存體帳戶。 在執行下列命令之前，請先使用一個唯一的名稱來取代 *mystorageaccount*。 此名稱在整個 Azure 中必須是唯一的：

    ```azurecli
    az storage account create --resource-group $RgName --location $Location --name mystorageaccount \
    --kind Storage --sku Standard_LRS
    ```

6. 建立 IP 組態、NIC，然後將 IP 組態指派給該 NIC。 您可以視需要新增、移除或變更組態。 案例中描述下列組態︰

    **IPConfig-1**

    輸入後續命令以建立︰

    - 使用靜態公用 IP 位址的公用 IP 位址資源
    - NIC，將公用 IP 位址和靜態私人 IP 位址指派給此 NIC。
    
    使用在 Azure 位置內唯一的名稱來取代 *mypublicdns*。

      ```azurecli
      azure network public-ip create --resource-group $RgName --location $Location --name myPublicIP1 \
      --domain-name-label mypublicdns --allocation-method Static
        
      azure network nic create --resource-group $RgName --location $Location --name myNic1 \
      --private-ip-address 10.0.0.4 --subnet-name mySubnet --subnet-vnet-name myVNet \
      --subnet-name mySubnet --public-ip-name myPublicIP1
      ```

      > [!NOTE]
      > 公用 IP 位址需要少許費用。 若要深入了解 IP 位址定價，請閱讀 [IP 位址定價](https://azure.microsoft.com/pricing/details/ip-addresses) 頁面。 訂用帳戶中可使用的公用 IP 位址數目有限制。 若要深入了解限制，請參閱 [Azure 限制](../azure-subscription-service-limits.md#networking-limits)文章。

    **IPConfig-2**

     輸入下列命令來建立具有靜態公用 IP 位址和靜態私人 IP 位址的新公用 IP 位址資源，以及新 IP 組態︰
    
      ```azurecli
      azure network public-ip create --resource-group $RgName --location $Location --name myPublicIP2
      --domain-name-label mypublicdns2 --allocation-method Static

      azure network nic ip-config create --resource-group $RgName --nic-name myNic1 --name IPConfig-2
      --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
      ```

    **IPConfig-3**

    輸入下列命令來建立具有靜態私人 IP 位址但無公用 IP 位址的 IP 組態︰

      ```azurecli
      azure network nic ip-config create --resource-group $RgName --nic-name myNic1 --private-ip-address 10.0.0.6 \
      --name IPConfig-3
      ```

    >[!NOTE] 
    >雖然本文會將所有 IP 組態指派給單一 NIC，您也可以指派多個 IP 組態給 VM 中的任何 NIC。 若要了解如何建立具有多個 NIC 的 VM，請參閱「建立具有多個 NIC 的 VM」文章。

7. 建立 Linux VM 

    ```azurecli
    az vm create --resource-group $RgName --name myVM1 --location $Location --nics myNic1 \
    --image UbuntuLTS --ssh-key-value ~/.ssh/id_rsa.pub --admin-username azureuser
    ```

    例如，若要將 VM 大小變更為標準 DS2 v2，只要在步驟 6 中將下列屬性 `--vm-size Standard_DS3_v2` 新增至 `azure vm create` 命令即可。

8. 輸入下列命令，以檢視 NIC 和相關聯的 IP 設定︰

    ```azurecli
    azure network nic show --resource-group $RgName --name myNic1
    ```
9. 完成本文的[將 IP 位址新增至 VM 作業系統](#os-config)一節中適用於您的作業系統的步驟，將私人 IP 位址新增至 VM 作業系統。

## <a name="add"></a>將 IP 位址新增至 VM

您可以完成後續步驟，將其他私用和公用 IP 位址新增至現有的 NIC。 範例以本文章所述的[案例](#Scenario)為基礎。

1. 開啟 Azure CLI，並在單一的 CLI 工作階段內完成本章節的其餘步驟。 如果您尚未安裝和設定 Azure CLI，請完成[安裝和設定 Azure CLI](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 文章中的步驟，並登入 Azure 帳戶。

2. 根據您的需求，完成下列其中一個章節中的步驟︰

    - **新增私人 IP 位址**
    
        若要將私人 IP 位址新增至 NIC，您必須使用下列命令建立 IP 設定。 靜態位址必須是未使用的子網路位址。

        ```azurecli
        azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 \
        --private-ip-address 10.0.0.7 --name IPConfig-4
        ```
        使用唯一組態名稱和私人 IP 位址 (適用於具有靜態 IP 位址的組態)，視需要建立最多的組態。

    - **新增公用 IP 位址**
    
        新增公用 IP 位址的方法是將它與新的 IP 組態或現有的 IP 組態產生關聯。 視需要完成後續其中一節的步驟。

        > [!NOTE]
        > 公用 IP 位址需要少許費用。 若要深入了解 IP 位址定價，請閱讀 [IP 位址定價](https://azure.microsoft.com/pricing/details/ip-addresses) 頁面。 訂用帳戶中可使用的公用 IP 位址數目有限制。 若要深入了解限制，請參閱 [Azure 限制](../azure-subscription-service-limits.md#networking-limits)文章。
        >

        **將資源與新的 IP 組態產生關聯**
    
        每當您在新的 IP 組態中新增公用 IP 位址時，也必須新增私人 IP 位址，因為所有的 IP 組態都必須有一個私人 IP 位址。 您可以新增現有的公用 IP 位址資源，或建立一個新的資源。 若要建立新的公用 IP 位址資源，請輸入下列命令：

        ```azurecli
        azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 \
        --domain-name-label mypublicdns3
        ```

        若要建立具有靜態私人 IP 位址和相關 *myPublicIP3* 公用 IP 位址資源的新 IP 組態，請輸入下列命令︰

        ```azurecli
        azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 \
        --private-ip-address 10.0.0.8 --public-ip-name myPublicIP3
        ```

        **將資源與現有的 IP 組態產生關聯**

        公用 IP 位址資源只能與尚未關聯的 IP 組態產生關聯。 您可以輸入下列命令，判斷 IP組態是否有相關聯的公用 IP 位址︰

        ```azurecli
        azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
        ```

        在傳回的輸出中，尋找類似下列一行中的 IPConfig-3：

        ```         
        Name               Provisioning state  Primary  Private IP allocation Private IP version  Private IP address  Subnet    Public IP
        default-ip-config  Succeeded           true     Static                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Static                IPv4                10.0.0.6            mySubnet
        ```
          
        由於 IpConfig-3 的 [公用 IP] 欄是空白，表示目前沒有相關聯的公用 IP 位址資源。 您可以將現有的公用 IP 位址資源新增至 IpConfig-3，或輸入下列命令以建立一個︰

        ```azurecli
        azure network public-ip create --resource-group  myResourceGroup --location westcentralus \
        --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
        ```

        輸入下列命令，將公用 IP 位址資源與名為 *IpConfig-3* 的現有 IP 組態產生關聯：
        ```azurecli
        azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 \
        --public-ip-name myPublicIP3
        ```

3. 輸入下列命令，以檢視指派給 NIC 的私人 IP 位址和公用 IP 位址資源︰

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

      傳回的輸出大致如下：
      ```
      Name               Provisioning state  Primary  Private IP allocation Private IP version  Private IP address  Subnet    Public IP
        
      default-ip-config  Succeeded           true     Static                IPv4                10.0.0.4            mySubnet  myPublicIP
      IPConfig-2         Succeeded           false    Static                IPv4                10.0.0.5            mySubnet  myPublicIP2
      IPConfig-3         Succeeded           false    Static                IPv4                10.0.0.6            mySubnet  myPublicIP3
      ```
4. 依照本文的[將 IP 位址新增至 VM 作業系統](#os-config)一節中的指示，將您新增至 NIC 的私人 IP 位址新增至 VM 作業系統。 請勿將公用 IP 位址新增至作業系統。

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
