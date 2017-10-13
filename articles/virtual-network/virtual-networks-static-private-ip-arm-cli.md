---
title: "設定 VM 的私人 IP 位址 - Azure CLI 2.0 | Microsoft Docs"
description: "了解如何使用 Azure 命令列介面 (CLI) 2.0 設定虛擬機器的私人 IP 位址。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 071156367c1f819a00d31f1d0335e301391fda81
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli-20"></a>使用 Azure CLI 2.0 設定虛擬機器的私人 IP 位址

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]


## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本 

您可以使用下列其中一個 CLI 版本來完成工作︰ 

- [Azure CLI 1.0](virtual-networks-static-private-ip-cli-nodejs.md) – 適用於傳統和資源管理部署模型的 CLI 
- [Azure CLI 2.0](#specify-a-static-private-ip-address-when-creating-a-vm) - 適用於資源管理部署模型的新一代 CLI (本文章)

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

本文涵蓋之內容包括資源管理員部署模型。 您也可以 [管理傳統部署模型中的靜態私人 IP 位址](virtual-networks-static-private-ip-classic-cli.md)。

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> 下列範例 Azure CLI 2.0 命令會預期已經建立簡單的環境。 如果您想要執行如本文件中所顯示的命令，請先建置 [建立 vnet](virtual-networks-create-vnet-arm-cli.md)中所說明的測試環境。

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>建立 VM 時指定靜態私人 IP 位址

若要在名為 TestVNet 之 VNet 的FrontEnd子網路中建立名為 DNS01 且其靜態私人 IP 為 192.168.1.101 的 VM，請遵循下列步驟：

1. 安裝及設定最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2) (若您尚未這麼做)，並使用 [az login](/cli/azure/#login) 來登入 Azure 帳戶。 

2. 使用 [az network public-ip create](/cli/azure/network/public-ip#create) 命令來建立 VM 的公用 IP。 輸出後顯示的清單可說明所使用的參數。

    > [!NOTE]
    > 視您的環境而定，您可能會想要或需要為此步驟和後續步驟中的引數使用不同的值。
   
    ```azurecli
    az network public-ip create \
    --name TestPIP \
    --resource-group TestRG \
    --location centralus \
    --allocation-method Static
    ```

    預期的輸出：
   
   ```json
   {
        "publicIp": {
            "idleTimeoutInMinutes": 4,
            "ipAddress": "52.176.43.167",
            "provisioningState": "Succeeded",
            "publicIPAllocationMethod": "Static",
            "resourceGuid": "79e8baa3-33ce-466a-846c-37af3c721ce1"
        }
    }
    ```

   * `--resource-group`︰要在其中建立公用 IP 之資源群組的名稱。
   * `--name`：公用 IP 的名稱。
   * `--location`：要在其中建立公用 IP 的 Azure 區域。

3. 執行 [az network nic create](/cli/azure/network/nic#create) 命令以建立具有靜態私人 IP 的 NIC。 輸出後顯示的清單可說明所使用的參數。 
   
    ```azurecli
    az network nic create \
    --resource-group TestRG \
    --name TestNIC \
    --location centralus \
    --subnet FrontEnd \
    --private-ip-address 192.168.1.101 \
    --vnet-name TestVNet
    ```

    預期的輸出：
   
    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.101",
                "privateIPAllocationMethod": "Static",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>"
        }
    }
    ```
    
    參數：

    * `--private-ip-address`：NIC 的靜態私人 IP 位址。
    * `--vnet-name`：要在其中建立 NIC 之 VNet 的名稱。
    * `--subnet`：要在其中建立 NIC 之子網路的名稱。

4. 執行 [azure vm create](/cli/azure/vm/nic#create) 命令來使用上面建立的公用 IP 和 NIC 來建立 VM。 輸出後顯示的清單可說明所使用的參數。
   
    ```azurecli
    az vm create \
    --resource-group TestRG \
    --name DNS01 \
    --location centralus \
    --image Debian \
    --admin-username adminuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics TestNIC
    ```

    預期的輸出：
   
    ```json
    {
        "fqdns": "",
        "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01",
        "location": "centralus",
        "macAddress": "00-0D-3A-92-C1-66",
        "powerState": "VM running",
        "privateIpAddress": "192.168.1.101",
        "publicIpAddress": "",
        "resourceGroup": "TestRG"
    }
    ```
   
   基本 [az vm create](/cli/azure/vm#create) 參數以外的參數。

   * `--nics`︰VM 所連接至之 NIC 的名稱。
   

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>擷取 VM 的靜態私人 IP 位址資訊

若要檢視所建立的靜態私人 IP 位址，請執行下列 Azure CLI 命令並觀察 Private IP alloc-method 和 Private IP address 的值：

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

預期的輸出：

```json
"192.168.1.101"
```

若要顯示該 VM 之 NIC 的特定 IP 資訊，請查詢 NIC，特別是：

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

輸出會像這樣：

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>移除 VM 的靜態私人 IP 位址

您無法從 Azure CLI 的 NIC 移除資源管理員部署的靜態私人 IP 位址。 您必須：
- 建立使用動態 IP 的新 NIC
- 在新建立 NIC 的 VM 上設定 NIC。 

若要變更上述命令中使用之 VM 的 NIC，請遵循下列步驟。

1. 執行 **azure network nic create** 命令來建立使用新 IP 位址動態配置 IP 的新 NIC。 請注意，因為未指定任何 IP 位址，配置方法為**動態**。

    ```azurecli
    az network nic create     \
    --resource-group TestRG     \
    --name TestNIC2     \
    --location centralus     \
    --subnet FrontEnd    \
    --vnet-name TestVNet
    ```        
   
    預期的輸出：

    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.4",
                "privateIPAllocationMethod": "Dynamic",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "0808a61c-476f-4d08-98ee-0fa83671b010"
        }
    }
    ```

2. 執行 **azure vm set** 命令來變更 VM 所使用的 NIC。
   
    ```azurecli
    azure vm set -g TestRG -n DNS01 -N TestNIC2
    ```

    預期的輸出：
   
    ```json
    [
        {
            "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC3",
            "primary": true,
            "resourceGroup": "TestRG"
        }
    ]
    ```

    > [!NOTE]
    > 如果 VM 夠大而可容納多個 NIC，請執行 **azure network nic delete** 命令，以刪除舊 NIC。
   
## <a name="next-steps"></a>後續步驟
* 深入了解 [保留的公用 IP](virtual-networks-reserved-public-ip.md) 位址。
* 深入了解 [執行個體層級公用 IP (ILPIP)](virtual-networks-instance-level-public-ip.md) 位址。
* 請參閱 [保留 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)。

