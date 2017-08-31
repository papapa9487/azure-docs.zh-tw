
---
title: "在 Azure 中使用 SSL 憑證來保護 IIS | Microsoft Docs"
description: "了解如何在 Azure 中的 Windows VM 上使用 SSL 憑證來保護 IIS 網頁伺服器"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/14/2017
ms.author: iainfou
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 6567853e9ef3cad63595dc0afe7a793bdc5d972c
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---

# <a name="secure-iis-web-server-with-ssl-certificates-on-a-windows-virtual-machine-in-azure"></a>在 Azure 中的 Windows 虛擬機器上使用 SSL 憑證來保護 IIS 網頁伺服器
若要保護網頁伺服器，您可以使用安全通訊端層 (SSL) 憑證來將網路流量加密。 這些 SSL 憑證可儲存在 Azure Key Vault，並且能夠讓您將憑證安全地部署到 Azure 中的 Windows 虛擬機器 (VM)。 在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立 Azure Key Vault
> * 產生或上傳憑證至 Key Vault
> * 建立 VM 並安裝 IIS 網頁伺服器
> * 將憑證插入 VM 並使用 SSL 繫結來設定 IIS

本教學課程需要 Azure PowerShell 模組 3.6 版或更新版本。 執行 ` Get-Module -ListAvailable AzureRM` 以尋找版本。 如果您需要升級，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-azurerm-ps)。


## <a name="overview"></a>概觀
Azure Key Vault 會保護密碼編譯金鑰和祕密，這類憑證或密碼。 Key Vault 有助於簡化憑證管理程序，並可讓您掌控用來存取這些憑證的金鑰。 您可以在 Key Vault 內建立自我簽署憑證，或上傳您目前已經擁有的受信任憑證。

您不必使用包含了內建憑證的自訂 VM 映像，而是要將憑證插入執行中的 VM。 此程序可確保您在部署期間安裝在網頁伺服器上的憑證會是最新的。 如果您更新或取代憑證，您就不必另外再建立新的自訂 VM 映像。 當您建立其他 VM 時，系統會自動插入最新的憑證。 在整個過程中，憑證絕對不會離開 Azure 平台，或在指令碼、命令列記錄或範本中公開。


## <a name="create-an-azure-key-vault"></a>建立 Azure Key Vault
建立 Key Vault 和憑證之前，請先使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 來建立資源群組。 下列範例會在「美國東部」位置建立名為 myResourceGroupSecureWeb 的資源群組：

```powershell
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

接著，使用 [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) 建立 Key Vault。 每個 Key Vault 需要唯一的名稱，而且應該全部小寫。 使用您自己唯一的 Key Vault 名稱來取代下列範例中的 `<mykeyvault>`：

```powershell
$keyvaultName="<mykeyvault>"
New-AzureRmKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>產生憑證並儲存於 Key Vault
若要在生產環境中使用，您應該使用 [Import-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate) 來匯入由受信任的提供者所簽署的有效憑證。 在本教學課程中，下列範例示範如何使用 [Add-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificate) 來產生自我簽署憑證，而且該憑證會使用透過 [New-AzureKeyVaultCertificatePolicy](/powershell/module/azurerm.keyvault/new-azurekeyvaultcertificatepolicy) 所得到的預設憑證原則。 

```powershell
$policy = New-AzureKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzureKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>建立虛擬機器
使用 [Get-credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 來設定 VM 的系統管理員使用者名稱和密碼：

```powershell
$cred = Get-Credential
```

現在您可以使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 建立 VM。 下列範例會建立必要的虛擬網路元件、作業系統設定，然後建立名為 myVM 的 VM：

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name "myVnet" `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4 `
    -Name "myPublicIP"

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRuleRDP"  `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

# Create an inbound network security group rule for port 443
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRuleWWW"  `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority 1001 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 443 `
    -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $nsgRuleRDP,$nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
    -Name "myNic" `
    -ResourceGroupName $resourceGroup `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIP.Id `
    -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2" | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName "myVM" -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2016-Datacenter" -Version "latest" | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

# Use the Custom Script Extension to install IIS
Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

系統需要花幾分鐘的時間來建立 VM。 最後一個步驟會使用 Azure 自訂指令碼擴充功能，利用 [Set-AzureRmVmExtension](/powershell/module/azurerm.compute/set-azurermvmextension) 來安裝 IIS 網頁伺服器。


## <a name="add-a-certificate-to-vm-from-key-vault"></a>將憑證從 Key Vault 新增至 VM
若要將憑證從 Key Vault 新增至 VM，請使用 [Get-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret) 來取得憑證的識別碼。 使用 [Add-AzureRmVMSecret](/powershell/module/azurerm.compute/add-azurermvmsecret) 將憑證新增至 VM：

```powershell
$certURL=(Get-AzureKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzureRMVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzureRmKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzureRmVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>設定 IIS 以使用憑證
再次搭配 [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) 來使用自訂指令碼擴充功能，以更新 IIS 組態。 這次的更新會套用從 Key Vault 插入到 IIS 的憑證，並設定 Web 繫結：

```powershell
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>測試安全的 Web 應用程式
使用 [Get-AzureRmPublicIPAddress](/powershell/resourcemanager/azurerm.network/get-azurermpublicipaddress) 取得 VM 的公用 IP 位址。 下列範例會取得稍早建立之 `myPublicIP` 的 IP 位址︰

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIP" | select "IpAddress"
```

現在，您可以開啟 Web 瀏覽器，並在網址列輸入 `https://<myPublicIP>`。 若要在使用自我簽署憑證時接受安全性警告，請依序按一下 [詳細資料] 與 [繼續瀏覽網頁]：

![接受 Web 瀏覽器安全性警告](./media/tutorial-secure-web-server/browser-warning.png)

接著會顯示受保護的 IIS 網站，如下列範例所示：

![檢視執行中安全的 IIS 網站](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用儲存在 Azure Key Vault 中的 SSL 憑證來保護 IIS 網頁伺服器。 您已了解如何︰

> [!div class="checklist"]
> * 建立 Azure Key Vault
> * 產生或上傳憑證至 Key Vault
> * 建立 VM 並安裝 IIS 網頁伺服器
> * 將憑證插入 VM 並使用 SSL 繫結來設定 IIS

用以下連結查看預先建立的虛擬機器指令碼範例。

> [!div class="nextstepaction"]
> [Windows 虛擬機器指令碼範例](./powershell-samples.md)
