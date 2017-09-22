---
title: "如何使用 PowerShell 設定 Azure VM 上的 MSI"
description: "使用 PowerShell 在 Azure VM 上設定「受管理的服務身分識別 (MSI)」的逐步指示。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 104c43e6fab2c3f18824a00860c30c8d6f82bbc4
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>使用 PowerShell 設定「VM 受管理的服務身分識別 (MSI)」

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

在 Azure Active Directory 中，「受管理的服務身分識別」會提供自動受管理的身分給 Azure 服務。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您將了解如何使用 PowerShell 啟用和移除 Azure Windows VM 的 MSI，。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

此外，如果您尚未安裝 [Azure PowerShell 4.3.1 版本](https://www.powershellgallery.com/packages/AzureRM/4.3.1)，則請先安裝。

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>在建立 Azure VM 時啟用 MSI

使用指定的組態參數，在新資源群組中新建已啟用 MSI 的 Windows 虛擬機器資源。 請注意，其中多個 Cmdlet 會執行 30 秒 (或以上) 才回傳，而完成最終 VM 的建立也會費時數分鐘。

1. 使用 `Login-AzureRmAccount` 登入 Azure。 使用您想部署 VM 且已與 Azure 訂用帳戶相關聯的帳戶。

   ```powershell
   Login-AzureRmAccount
   ```

2. 使用 `New-AzureRmResourceGroup` Cmdlet，為您的 VM 和其相關資源建立[資源群組](../azure-resource-manager/resource-group-overview.md#terminology)。 如果您已經有想要使用的資源群組，您可以略過此步驟：

   ```powershell
   New-AzureRmResourceGroup -Name myResourceGroup -Location WestUS
   ```
3. 建立 VM 的網路資源。

   a. 建立虛擬網路、子網路和公用 IP 位址。 這些資源用來提供虛擬機器的網路連線能力，並可將它連線到網際網路。

   ```powershell
   # Create a subnet configuration
   $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location WestUS -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

   # Create a public IP address and specify a DNS name
   $pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location WestUS -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
   ```

   b. 建立網路安全性群組和網路安全性群組規則。 網路安全性群組可使用輸入和輸出規則來保護虛擬機器。 在此情況下，會建立連接埠 3389 的輸入規則，以允許連入的遠端桌面連線。 我們也會建立連接埠 80 的輸入規則，以允許連入的 Web 流量：

   ```powershell
   # Create an inbound network security group rule for port 3389
   $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   # Create an inbound network security group rule for port 80
   $nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80 -Access Allow

   # Create a network security group
   $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location WestUS -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
   ```

   c. 建立虛擬機器的虛擬網路卡。 網路卡可讓虛擬機器連線到子網路、網路安全性群組和公用 IP 位址：

   ```powershell
   # Create a virtual network card and associate with public IP address and NSG
   $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location WestUS -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
   ```

4. 建立 VM。

   a. 建立可設定的 VM 物件。 這些設定會在部署虛擬機器時使用，例如虛擬機器映像、大小和驗證組態。 在 [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) Cmdlet 中使用 `-IdentityType "SystemAssigned"` 參數，可讓 VM 和 MSI 同時部署。 `Get-Credential` Cmdlet 會提示您提供認證，也就是為虛擬機器設定的使用者名稱和密碼：

   ```powershell
   # Define a credential object (prompts for user/password to be used for VM authentication)
   $cred = Get-Credential

   # Create a configurable VM object with a Managed Service Identity
   $vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 -IdentityType "SystemAssigned" | Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
   ```

   b. 佈建新 VM：

   ```powershell
   New-AzureRmVM -ResourceGroupName myResourceGroup -Location WestUS -VM $vmConfig
   ```

5. 在 [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) Cmdlet 使用 `-Type "ManagedIdentityExtensionForWindows"` 參數新增 MSI VM 延伸模組。 `-Settings` 參數會指定 OAuth 權杖端點所使用的連接埠，以用來取得權杖：

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>在現有的 Azure VM 上啟用 MSI

如果您要在現有的虛擬機器上啟用 MSI：

1. 使用 `Login-AzureRmAccount` 登入 Azure。 使用您想部署 VM 且已與 Azure 訂用帳戶相關聯的帳戶：

   ```powershell
   Login-AzureRmAccount
   ```

2. 先使用 `Get-AzureRmVM` Cmdlet 擷取 VM 屬性。 然後在 [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) Cmdlet 上使用 `-IdentityType` 參數啟用 MSI。

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. 在 [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) Cmdlet 使用 `-Type "ManagedIdentityExtensionForWindows"` 參數新增 MSI VM 延伸模組。 `-Settings` 參數會指定 OAuth 權杖端點所使用的連接埠，以用來取得權杖。 請比對現有 VM 位置，確認指定正確的 `-Location` 參數：

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>從 Azure VM 移除 MSI

如果您的虛擬機器不再需要 MSI，您可以使用 `RemoveAzureRmVMExtension` Cmdlet 從 VM 移除 MSI：

1. 搭配使用 `-Name "ManagedIdentityExtensionForWindows"` 參數和 [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension) Cmdlet：

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>相關內容

- [受管理的服務識別概觀](msi-overview.md)
- 本文改編自[使用 PowerShell 建立 Windows 虛擬機器](../virtual-machines/windows/quick-create-powershell.md)快速入門，為包含 MSI 特定指示而修改。 

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。

















