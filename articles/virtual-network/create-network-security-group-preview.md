---
title: "使用 Azure 網路和應用程式安全性群組 (預覽) 來篩選網路流量 | Microsoft Docs"
description: "了解如何建立網路和應用程式安全性群組 (預覽) 來限制進出虛擬機器的網路流量類型。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: jdial
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 035eb44432081ef52c758a5d311b4d2ba2c6108d
ms.contentlocale: zh-tw
ms.lasthandoff: 09/26/2017

---
# <a name="filter-network-traffic-with-network-and-application-security-groups-preview"></a>使用網路和應用程式安全性群組 (預覽) 來篩選網路流量

在本教學課程中，您將會了解如何建立網路安全性群組，以使用應用程式安全性群組來篩選進出一組虛擬機器的網路流量。 若要深入了解網路安全性群組和應用程式安全性群組，請參閱[安全性概觀](security-overview.md)。 

下列各節包含可供您執行的步驟，以讓您使用 Azure 命令列介面 ([Azure CLI](#azure-cli)) 和 [Azure PowerShell](#powershell) 建立網路安全性群組。 無論您使用哪一種工具來建立網路安全性群組，結果都會相同。 按一下工具連結即可前往教學課程中關於該工具的章節。 

本文提供透過 Resource Manager 部署模型建立網路安全性群組的步驟，而此部署模型正是建立網路安全性群組時我們建議使用的部署模型。 如果您必須建立網路安全性群組 (傳統)，請參閱[建立網路安全性群組 (傳統)](virtual-networks-create-nsg-classic-ps.md)。 如果您不熟悉 Azure 部署模型，請參閱[了解 Azure 部署模型](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

> [!NOTE]
> 本教學課程會利用目前仍為預覽版本的網路安全性群組功能。 預覽版本的功能並沒有與一般版本中的功能相同的可用性和可靠性。 這些預覽版本的功能只在下列區域提供：美國中西部。 如果您只想要使用一般版本的功能來實作網路安全性群組，請參閱[建立網路安全性群組](virtual-networks-create-nsg-arm-pportal.md)。 

## <a name="azure-cli"></a>Azure CLI

無論您是從 Windows、Linux 或 macOS 執行命令，所使用的 Azure CLI 命令都相同。 不過，不同作業系統殼層的指令碼編寫會有差異。 下列步驟中的指令碼會在 Bash 殼層中執行。 

1. [安裝和設定 Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。
2. 輸入 `az --version` 命令來確定您使用 2.0.18 版或更高版本的 Azure CLI。 如果不是，請安裝最新版本。
3. 使用 `az login` 命令登入 Azure。
4. 輸入下列命令來註冊預覽版︰
    
    ```azurecli-interactive
    az feature register --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    ``` 

5. 藉由輸入下列命令，確認您已註冊預覽版︰

    ```azurecli-interactive
    az feature show --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    ```

    在前面的命令所傳回之輸出的 **state** 中出現「已註冊」前，請勿繼續進行其餘步驟。 如果您在完成註冊前就繼續進行，其餘步驟將會失敗。
6. 執行下列 bash 指令碼以建立資源群組：

    ```azurecli-interactive
    #!/bin/bash
    
    az group create \
      --name myResourceGroup \
      --location westcentralus
    ```

7. 建立三個應用程式安全性群組，每種伺服器類型一個：

    ```azurecli-interactive
    az network asg create \
      --resource-group myResourceGroup \
      --name WebServers \
      --location westcentralus  

    az network asg create \
      --resource-group myResourceGroup \
      --name AppServers \
      --location westcentralus

    az network asg create \
      --resource-group myResourceGroup \
      --name DatabaseServers \
      --location westcentralus
    ```

8. 建立網路安全性群組：

    ```azurecli-interactive
    az network nsg create \
      --resource-group myResourceGroup \
      --name myNsg \
      --location westcentralus
    ```

9. 建立 NSG 內的安全性規則，將應用程式安全性群組設定為目的地：
    
    ```azurecli-interactive    
    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name WebRule \
      --priority 200 \
      --access "Allow" \
      --direction "inbound" \
      --source-address-prefixes "Internet" \
      --destination-asgs "WebServers" \
      --destination-port-ranges 80 \
      --protocol "TCP"

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name AppRule \
      --priority 300 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "WebServers" \
      --destination-asgs "AppServers" \
      --destination-port-ranges 443 \
      --protocol "TCP"  

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name DatabaseRule \
      --priority 400 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "AppServers" \
      --destination-asgs "DatabaseServers" \
      --destination-port-ranges 1336 \
      --protocol "TCP" 
    ``` 

10. 建立虛擬網路： 
    
    ```azurecli-interactive
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name mySubnet \
      --address-prefix 10.0.0.0/16 \
      --location westcentralus
    ```

11. 建立網路安全性群組與虛擬網路中子網路的關聯：

    ```azurecli-interactive
    az network vnet subnet update \
      --name mySubnet \
      --resource-group myResourceGroup \
      --vnet-name myVnet \
      --network-security-group myNsg
    ```
    
12. 建立三個網路介面，每種伺服器類型一個： 

    ```azurecli-interactive
    az network nic create \
      --resource-group myResourceGroup \
      --name myNic1 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --network-security-group myNsg \
      --location westcentralus \
      --application-security-groups "WebServers" "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic2 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --network-security-group myNsg \
      --location westcentralus \
      --application-security-groups "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic3 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --network-security-group myNsg \
      --location westcentralus \
      --application-security-groups "DatabaseServers"
    ```

    根據網路介面所屬於的應用程式安全性群組，只有您在步驟 9 中建立的對應安全性規則會套用到網路介面。 例如，只有 WebRule 會對 myWebNic 起作用，因為該網路介面是 WebServers 應用程式安全性群組的成員，而且該規則會指定讓 WebServers 應用程式安全性群組作為其目的地。 AppRule 和 DatabaseRule 規則不會套用至 myWebNic，因為該網路介面不是 AppServers 和 DatabaseServers 應用程式安全性群組的成員。

13. 為每種伺服器類型建立一個虛擬機器，並對每個虛擬機器連結對應的網路介面。 這個範例會建立 Windows 虛擬機器，但您可以將 win2016datacenter 變更為 UbuntuLTS 來改為建立 Linux 虛擬機器。

    ```azurecli-interactive
    # Update for your admin password
    AdminPassword=ChangeYourAdminPassword1

    az vm create \
      --resource-group myResourceGroup \
      --name myWebVm \
      --location westcentralus \
      --nics myNic1 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword

    az vm create \
      --resource-group myResourceGroup \
      --name myAppVm \
      --location westcentralus \
      --nics myNic2 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword

    az vm create \
      --resource-group myResourceGroup \
      --name myDatabaseVm \
      --location westcentralus \
      --nics myNic3 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword    
    ```

14. **選擇性︰**完成[刪除資源](#delete-cli)中的步驟，以刪除您在本教學課程中所建立的資源。

## <a name="powershell"></a>PowerShell

1. 安裝並設定 [PowerShell](/powershell/azure/install-azurerm-ps)。
2. 確定您已安裝 AzureRM 模組 4.4.0 版或更新版本。 您可以輸入 `Get-Module -ListAvailable AzureRM` 命令來檢查您目前安裝的版本。 如果您需要安裝或升級，請從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureRM)安裝最新版的 AzureRM 模組。
3. 在 PowerShell 工作階段中，使用 `login-azurermaccount` 命令搭配您的 [Azure 帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)登入 Azure。
4. 輸入下列命令來註冊預覽版︰
    
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ``` 

5. 藉由輸入下列命令，確認您已註冊預覽版︰

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    ```

    在前面的命令所傳回之輸出的 **RegistrationState** 資料行中出現「已註冊」前，請勿繼續進行其餘步驟。 如果您在完成註冊前就繼續進行，其餘步驟將會失敗。
        
6. 建立資源群組：

    ```powershell
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location westcentralus
    ```

7. 建立三個應用程式安全性群組，每種伺服器類型一個：

    ```powershell
    $webAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name WebServers `
      -Location westcentralus
  
    $appAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name AppServers `
      -Location westcentralus

    $databaseAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name DatabaseServers `
      -Location westcentralus
    ```

8. 為每種伺服器類型建立安全性規則。
    
    ```powershell
    $webRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "WebRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 200 `
      -SourceAddressPrefix Internet `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $webAsg.id `
      -DestinationPortRange 80  

    $appRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "AppRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 300 `
      -SourceApplicationSecurityGroupId $webAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $appAsg.id `
      -DestinationPortRange 443 

    $databaseRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "DatabaseRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 400 `
      -SourceApplicationSecurityGroupId $appAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $databaseAsg.id `
      -DestinationPortRange 1336    
    ``` 

9. 建立網路安全性群組：

    ```powershell
    $nsg = New-AzureRmNetworkSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Name myNsg `
      -SecurityRules $WebRule,$AppRule,$DatabaseRule
    ```

10. 建立子網路組態，並讓網路安全性群組與此組態產生關聯：
    
    ```powershell
    $subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -AddressPrefix 10.0.0.0/24 `
      -Name mySubnet `
      -NetworkSecurityGroup $nsg
    ```

11. 建立虛擬網路： 
    
    ```powershell
    $vNet = New-AzureRmVirtualNetwork `
      -Name myVnet `
      -AddressPrefix '10.0.0.0/16' `
      -Subnet $subnet `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus
    ```

12. 建立三個網路介面，每種伺服器類型一個。 

    ```powershell
    $nic1 = New-AzureRmNetworkInterface `
      -Name myNic1 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -NetworkSecurityGroup $nsg `
      -ApplicationSecurityGroup $webAsg,$appAsg

    $nic2 = New-AzureRmNetworkInterface `
      -Name myNic2 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -NetworkSecurityGroup $nsg `
      -ApplicationSecurityGroup $appAsg

    $nic3 = New-AzureRmNetworkInterface `
      -Name myNic3 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -NetworkSecurityGroup $nsg `
      -ApplicationSecurityGroup $databaseAsg
    ```

    根據網路介面所屬於的應用程式安全性群組，只有您在步驟 8 中建立的對應安全性規則會套用到網路介面。 例如，只有 WebRule 會對 myWebNic 起作用，因為該網路介面是 WebServers 應用程式安全性群組的成員，而且該規則會指定讓 WebServers 應用程式安全性群組作為其目的地。 AppRule 和 DatabaseRule 規則不會套用至 myWebNic，因為該網路介面不是 AppServers 和 DatabaseServers 應用程式安全性群組的成員。

13. 為每種伺服器類型建立一個虛擬機器，並對每個虛擬機器連結對應的網路介面。 此範例會建立 Windows 虛擬機器，但您可以在執行指令碼之前，將 -Windows 變更為 -Linux、將 MicrosoftWindowsServer 變更為 Canonical、將 WindowsServer 變更為 UbuntuServer，以及將 2016-Datacenter 變更為 14.04.2-LTS，來改為建立 Linux 虛擬機器。

    ```powershell
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."
   
    # Create the web server virtual machine configuration and virtual machine.
    $webVmConfig = New-AzureRmVMConfig `
      -VMName myWebVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myWebVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic1.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $webVmConfig

    # Create the app server virtual machine configuration and virtual machine.
    $appVmConfig = New-AzureRmVMConfig `
      -VMName myAppVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myAppVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic2.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $appVmConfig

    # Create the database server virtual machine configuration and virtual machine.
    $databaseVmConfig = New-AzureRmVMConfig `
      -VMName myDatabaseVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName mydatabaseVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic3.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $databaseVmConfig
    ```

14. **選擇性︰**完成[刪除資源](#delete-cli)中的步驟，以刪除您在本教學課程中所建立的資源。

## <a name="delete"></a>刪除資源

完成本教學課程之後，可考慮刪除所建立的資源，以免產生使用費。 刪除資源群組同時會刪除其內含的所有資源。

### <a name="delete-portal"></a>Azure 入口網站

1. 在入口網站的搜尋方塊中，輸入 **myResourceGroup**。 在搜尋結果中按一下 [myResourceGroup]。
2. 在 [myResourceGroup] 刀鋒視窗中，按一下 [刪除] 圖示。
3. 若要確認刪除動作，請在 輸入資源群組名稱 方塊中輸入 **myResourceGroup**，然後按一下刪除。

### <a name="delete-cli"></a>Azure CLI

在 CLI 工作階段中，輸入下列命令：

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

在 PowerShell 工作階段中，輸入下列命令：

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```


