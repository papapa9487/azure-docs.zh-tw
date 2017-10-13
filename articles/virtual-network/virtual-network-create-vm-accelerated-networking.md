---
title: "使用加速網路來建立 Azure 虛擬機器 | Microsoft Docs"
description: "了解如何使用加速網路來建立虛擬機器。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/10/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2e887230a102f5c6289ca2eec0e4700a0e1fdfde
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2017
---
# <a name="create-a-virtual-machine-with-accelerated-networking"></a>使用加速網路來建立虛擬機器

在本教學課程中，您將了解如何使用加速網路來建立 Azure 虛擬機器 (VM)。 加速網路是 GA for Windows，而且針對特定 Linux 發佈則處於公開預覽狀態。 加速網路可以對 VM 啟用 Single Root I/O Virtualization (SR-IOV)，大幅提升其網路效能。 這個高效能路徑會略過資料路徑的主機而減少延遲、抖動和 CPU 使用率，供支援的 VM 類型上最嚴苛的網路工作負載使用。 下圖顯示有和沒有加速網路的兩部虛擬機器 (VM) 之間的通訊︰

![比較](./media/virtual-network-create-vm-accelerated-networking/image1.png)

如果沒有加速網路，進出 VM 的所有網路流量都必須周遊主機和虛擬交換器。 虛擬交換器對網路流量提供所有原則強制執行，例如網路安全性群組、存取控制清單、隔離性以及其他網路虛擬化服務。 若要深入了解虛擬交換器，請閱讀 [Hyper-V Network Virtualization and Virtual Switch (Hyper-V 網路虛擬化和虛擬交換器)](https://technet.microsoft.com/library/jj945275.aspx) 文章。

如果使用加速網路，網路流量就會先送達 VM 的網路介面 (NIC)，然後轉送到 VM。 虛擬交換器在不使用加速網路時套用的所有網路原則會卸載並在硬體中套用。 在硬體中套用原則會讓 NIC 略過主機和虛擬交換器，同時在主機中維護套用的所有原則，直接將網路流量轉送到 VM。

加速網路的優點只適用於已啟用此功能的 VM。 為了獲得最佳結果，最好在至少兩部連線到相同 Azure 虛擬網路 (VNet) 的 VM 上啟用此功能。 當透過 VNet 通訊或連線內部部署時，此功能對整體延遲的影響可以降到最低。

> [!WARNING]
> Linux 公開預覽可能沒有與正式發行版本功能相同層級的可用性和可靠性。 不支援該功能、可能已經限制功能，以及可能無法在所有 Azure 位置提供使用。 如需此功能可用性和狀態的最新通知，請查看 Azure 虛擬網路更新頁面。

## <a name="benefits"></a>優點
* **較低的延遲 / 較高的每秒封包數目 (pps)：** 從資料路徑移除虛擬交換器會減少主機中封包在處理原則時所花的時間，並增加 VM 內可處理的封包數目。
* **減少抖動︰** 虛擬交換器處理視需要套用的原則數量和正在進行處理的 CPU 工作負載而定。 將原則強制執行卸載到硬體透過將封包直接傳遞到 VM、移除主機到 VM 的通訊，以及所有軟體插斷和環境切換，而減少變化。
* **降低 CPU 使用率︰** 略過主機中的虛擬交換器可減少處理網路流量的 CPU 使用率。

## <a name="Limitations"></a>限制
使用這項功能時，有下列限制︰

* **網路介面建立︰**您只能對新的 NIC 啟用加速網路。 無法對現有 NIC 來啟用。
* **VM 建立：**啟用加速網路的 NIC 只能在 VM 建立之後附加至 VM。 NIC 無法附加至現有的 VM。
* **區域︰**大多數 Azure 區域都有提供具有加速網路的 Windows VM。 多個區域都提供具有加速網路的 Linux VM。 我們會擴大可使用這項功能的區域。 如需最新資訊，請參閱下方的 Azure 虛擬網路更新部落格。   
* **支援的作業系統︰**Windows：Microsoft Windows Server 2012 R2 Datacenter 和 Windows Server 2016。 Linux：Ubuntu Server 16.04 LTS (含核心 4.4.0-77 或更高版本)、SLES 12 SP2、RHEL 7.3 和 CentOS 7.3 (由 “Rogue Wave Software” 發佈)。
* **VM 大小︰**一般用途和具有八個以上核心的計算最佳化執行個體大小。 如需詳細資訊，請參閱 [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小的相關文章。 未來將增加支援的 VM 執行個體大小。
* **僅限透過 Azure Resource Manager (ARM) 進行部署：**您無法透過 ASM/RDFE 部署加速網路。

這些限制如有變更，將會透過 [Azure 虛擬網路更新](https://azure.microsoft.com/updates/accelerated-networking-in-expanded-preview/)頁面發佈。

## <a name="create-a-windows-vm"></a>建立 Windows VM
您可以使用 Azure 入口網站或 Azure [PowerShell](#windows-powershell) 來建立 VM。

### <a name="windows-portal"></a>入口網站

1. 從網際網路瀏覽器開啟 Azure [入口網站](https://portal.azure.com)，並以您的 Azure [帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)進行登入。 如果您還沒有帳戶，則可以註冊[免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)帳戶。
2. 在入口網站中，按一下 [+ 新增] > [計算] > [Windows Server 2016 Datacenter]。 
3. 在出現的 [Windows Server 2016 Datacenter] 刀鋒視窗中，讓 [選取部署模型] 下的 [Resource Manager] 保持選取狀態，然後按一下 [建立]。
4. 在出現的 [基本概念] 刀鋒視窗中輸入下列值，保留其餘的預設選項或是選取或輸入您自己的值，然後按一下 [確定] 按鈕︰

    |設定|值|
    |---|---|
    |名稱|MyVm|
    |資源群組|讓 [新建] 保持選取狀態，並輸入 MyResourceGroup|
    |位置|美國西部 2|

    如果您不熟悉 Azure，請深入了解[資源群組](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)、[訂用帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)和[位置](https://azure.microsoft.com/regions) (這也稱為區域)。
5. 在出現的 [選擇大小] 刀鋒視窗中，於 [最小核心數] 方塊中輸入 8，然後按一下 [檢視全部]。
6. 按一下 [DS4_V2 標準] 或任何支援的 VM，然後按一下 [選取] 按鈕。
7. 在出現的 [設定] 刀鋒視窗中保留所有設定，但在 [加速網路] 下按一下 [啟用]，然後按一下 [確定] 按鈕。 **注意︰**如果您在先前的步驟中所選取的 VM 大小、作業系統或位置等值不是本文的[限制](#Limitations)一節所列出的值，您就不會看到 [加速網路]。
8. 在出現的 [摘要] 刀鋒視窗中，按一下 [確定] 按鈕。 Azure 會開始建立 VM。 VM 需要花幾分鐘的時間來建立。
9. 若要安裝 Windows 的加速網路驅動程式，請完成本文之[設定 Windows](#configure-windows) 一節的步驟。

### <a name="windows-powershell"></a>PowerShell
1. 安裝最新版的 Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模組。 如果您不熟悉 Azure PowerShell，請閱讀 [Azure PowerShell 概觀](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。
2. 按一下 Windows 的 [開始] 按鈕，輸入 **powershell**，然後按一下搜尋結果中的 [PowerShell]，以啟動 PowerShell 工作階段。
3. 在 PowerShell 視窗中輸入 `login-azurermaccount` 命令，以使用您的 Azure [帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)進行登入。 如果您還沒有帳戶，則可以註冊[免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)帳戶。
4. 在瀏覽器中複製下列指令碼：
    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Windows `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    #
    ```
5. 在 PowerShell 視窗中，以滑鼠右鍵按一下來貼上並開始執行指令碼。 系統會提示您輸入使用者名稱和密碼。 當您在下一個步驟連線到 VM 時，請使用這些認證來登入 VM。 如果指令碼失敗，而且您在執行指令碼之前變更過指令碼的值，請確認您所使用的 VM 大小、作業系統和位置的值，有列在本文的[限制](#Limitations)一節中。
6. 若要安裝 Windows 的加速網路驅動程式，請完成本文之[設定 Windows](#configure-windows) 一節的步驟。

### <a name="configure-windows"></a>設定 Windows
在 Azure 中建立 VM 後，您必須安裝 Windows 的加速網路驅動程式。 在完成下列步驟之前，您必須已使用本文的[入口網站](#windows-portal)或 [PowerShell](#windows-powershell) 步驟來建立具有加速網路的 Windows VM。 

1. 從網際網路瀏覽器開啟 Azure [入口網站](https://portal.azure.com)，並以您的 Azure [帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)進行登入。 如果您還沒有帳戶，則可以註冊[免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)帳戶。
2. 在 Azure 入口網站頂端包含「搜尋資源」文字的方塊中，輸入「MyVm」。 當「MyVm」出現於搜尋結果時，按一下它。
3. 在出現的 [MyVm] 刀鋒視窗中，按一下刀鋒視窗左上角的 [連線] 按鈕。 **注意︰**如果在 [連線] 按鈕底下看到 [建立中]，則表示 Azure 還未建立好 VM。 請在 [連線] 按鈕底下已沒有 [建立中] 字樣時，才按下 [連線]。
4. 允許瀏覽器下載 **MyVm.rdp** 檔案。  下載完成後，按一下檔案來加以開啟。 
5. 在為了通知您找不到遠端連線發行者而出現的 [遠端桌面連線] 方塊中，按一下 [連線] 按鈕。
6. 在出現的 [Windows 安全性] 方塊中，按一下 [更多選擇]，然後按一下 [使用不同帳戶]。 輸入您在步驟 4 中所輸入的使用者名稱和密碼，然後按一下 [確定] 按鈕。
7. 在為了通知您無法驗證遠端電腦身分識別而出現的 [遠端桌面連線] 方塊中，按一下 [是] 按鈕。
8. 以滑鼠右鍵按一下 Windows 的 [開始] 按鈕，然後按一下 [裝置管理員]。 展開 [網路介面卡] 節點。 確認 **Mellanox ConnectX-3 Virtual Function Ethernet Adapter** 有出現，如下圖所示︰
   
    ![裝置管理員](./media/virtual-network-create-vm-accelerated-networking/image2.png)

9. 現在已啟用您 VM 的加速網路。

## <a name="create-a-linux-vm"></a>建立 Linux VM
您可以使用 Azure 入口網站或 Azure [PowerShell](#linux-powershell) 來建立 Ubuntu 或 SLES VM。 RHEL 和 CentOS VM 的工作流程不同。  請參閱下面的指示。

### <a name="linux-portal"></a>入口網站
1. 完成本文之[建立 Linux VM - PowerShell](#linux-powershell) 一節的步驟 1 至 5，以註冊 Linux 的加速網路 (預覽版)。  您無法在入口網站中註冊預覽版。
2. 完成本文之[建立 Windows VM - 入口網站](#windows-portal)一節的步驟 1 至 8。 在步驟 2 中，按一下 [Ubuntu Server 16.04 LTS] 而不是 [Windows Server 2016 Datacenter]。 在本教學課程中，選擇使用密碼而不是 SSH 金鑰，但實際上兩者皆可用於生產部署。 當您完成本文之[建立 Windows VM - 入口網站](#windows-portal)一節的步驟 7 時，如果**加速網路**未出現，原因很可能是下列其中之一︰
    - 您尚未註冊預覽版。 確認您的註冊狀態是**已註冊**，如本文之[建立 Linux VM - PowerShell](#linux-powershell) 一節的步驟 4 所述。 **注意︰**如果您參與測試 Windows VM 的加速網路預覽版 (不再需要註冊就能使用 Windows VM 加速網路)，您並不會自動註冊 Linux VM 的加速網路預覽版。 您必須註冊 Linux VM 的加速網路預覽版才能參與測試此功能。
    - 您尚未選取本文的[限制](#limitations)一節所列出的 VM 大小、作業系統或位置。
3. 若要安裝 Linux 的加速網路驅動程式，請完成本文之[設定 Linux](#configure-linux) 一節的步驟。

### <a name="linux-powershell"></a>PowerShell

>[!WARNING]
>如果您在訂用帳戶中建立具有加速網路功能的 Linux VM，然後嘗試在相同的訂用帳戶中建立具有加速網路功能的 Windows VM，則可能無法建立 Windows VM。 在預覽版測時期間，建議您在不同的訂用帳戶中測試具有加速網路功能的 Linux 和 Windows VM。
>

1. 安裝最新版的 Azure PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) 模組。 如果您不熟悉 Azure PowerShell，請閱讀 [Azure PowerShell 概觀](/powershell/azure/get-started-azureps?toc=%2fazure%2fvirtual-network%2ftoc.json)一文。
2. 按一下 Windows 的 [開始] 按鈕，輸入 **powershell**，然後按一下搜尋結果中的 [PowerShell]，以啟動 PowerShell 工作階段。
3. 在 PowerShell 視窗中輸入 `login-azurermaccount` 命令，以使用您的 Azure [帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)進行登入。 如果您還沒有帳戶，則可以註冊[免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)帳戶。
4. 完成下列步驟來註冊 Azure 的加速網路功能預覽版︰
    - 傳送電子郵件給 [axnpreview@microsoft.com](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e)，並附上您的 Azure 訂用帳戶識別碼與用途。 請等候來自 Microsoft 有關正在啟用您訂用帳戶的電子郵件確認。
    - 輸入下列命令以確認您已註冊預覽版︰
    
        ```powershell
        Get-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingForLinux -ProviderNamespace Microsoft.Network
        ```

        在輸入上述命令後，若輸出中未出現**已註冊**，請勿繼續進行步驟 5。 必須看到如下的輸出才能繼續︰
    
        ```powershell
        FeatureName                        ProviderName      RegistrationState
        -----------                        ------------      -----------------
        AllowAcceleratedNetworkingForLinux Microsoft.Network Registered
        ```
        
      >[!NOTE]
      >如果您參與測試 Windows VM 的加速網路預覽版 (不再需要註冊就能使用 Windows VM 加速網路)，您並不會自動註冊 Linux VM 的加速網路預覽版。 您必須註冊 Linux VM 的加速網路預覽版才能參與測試此功能。
      >
5. 在瀏覽器中，複製下列指令碼，以視需要替代 Ubuntu 或 SLES。  同樣地，Redhat 和 CentOS 具有不同的工作流程，如下所述：

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name $RgName `
      -Location $Location
    
    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -Name MySubnet `
      -AddressPrefix 10.0.0.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName $RgName `
      -Location $Location `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet

    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
      -Name MyPublicIp `
      -ResourceGroupName $RgName `
      -Location $Location `
      -AllocationMethod Static

    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
      -Name MyNic `
      -ResourceGroupName $RgName `
      -Location $Location `
      -SubnetId $Vnet.Subnets[0].Id `
      -PublicIpAddressId $Pip.Id `
      -EnableAcceleratedNetworking
     
    # Create a new Storage account and define the new VM’s OSDisk name and its URI
    # Must end with ".vhd" extension
    $OSDiskName = "MyOsDiskName.vhd"
    # Storage account name must be between 3 and 24 characters in length and use numbers and lower-case letters only.
    $OSDiskSAName = "thestorageaccountname"  
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $RgName -Name $OSDiskSAName -Type "Standard_GRS" -Location $Location
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName
 
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential

    # Create a virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
      -VMName MyVM -VMSize Standard_DS4_v2 | `
      Set-AzureRmVMOperatingSystem `
      -Linux `
      -ComputerName myVM `
      -Credential $Cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName Canonical `
      -Offer UbuntuServer `
      -Skus 16.04-LTS `
      -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk -Name $OSDiskName `
      -VhdUri $OSDiskUri `
      -CreateOption FromImage 

    # Create the virtual machine.    
    New-AzureRmVM `
      -ResourceGroupName $RgName `
      -Location $Location `
      -VM $VmConfig
    ```
    
6. 在 PowerShell 視窗中，以滑鼠右鍵按一下來貼上並開始執行指令碼。 系統會提示您輸入使用者名稱和密碼。 當您在下一個步驟連線到 VM 時，請使用這些認證來登入 VM。 如果指令碼失敗，請確認︰
    - 您已註冊預覽版，如步驟 4 所述
    - 如果您在執行指令碼之前變更過指令碼的 VM 大小、作業系統類型或位置等值，請確認該值有列在本文的[限制](#Limitations)一節中。
7. 若要安裝 Linux 的加速網路驅動程式，請完成本文之[設定 Linux](#configure-linux) 一節的步驟。

### <a name="configure-linux"></a>設定 Linux

在 Azure 中建立 VM 後，您必須安裝 Linux 的加速網路驅動程式。 在完成下列步驟之前，您必須已使用本文的[入口網站](#linux-portal)或 [PowerShell](#linux-powershell) 步驟來建立具有加速網路的 Linux VM。 

1. 從網際網路瀏覽器開啟 Azure [入口網站](https://portal.azure.com)，並以您的 Azure [帳戶](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account)進行登入。 如果您還沒有帳戶，則可以註冊[免費試用](https://azure.microsoft.com/offers/ms-azr-0044p)帳戶。
2. 在入口網站頂端的 [搜尋資源] 列右邊，按一下 [>_] 圖示以啟動 Bash Cloud Shell (預覽版)。 [Bash Cloud Shell] 窗格會出現在入口網站底部，並在幾秒後顯示 **username@Azure:~$** 提示字元。 雖然您可以透過 SSH 從您的電腦 (而不是 Cloud Shell) 連線到 VM，但本教學課程會假設您是使用 Cloud Shell 並提供這方面的指示。
3. 在入口網站頂端含有「搜尋資源」文字的方塊中，輸入「MyVm」。 當「MyVm」出現於搜尋結果時，按一下它。
4. 在出現的 [MyVm] 刀鋒視窗中，按一下刀鋒視窗左上角的 [連線] 按鈕。 **注意︰**如果在 [連線] 按鈕底下看到 [建立中]，則表示 Azure 還未建立好 VM。 請在 [連線] 按鈕底下已沒有 [建立中] 字樣時，才按下 [連線]。
5. Azure 會開啟方塊來請您輸入 `ssh adminuser@<ipaddress>`。 在 Cloud Shell 中輸入這個命令 (或從步驟 4 所出現的方塊中複製此命令，然後將它貼到 Cloud Shell)，然後按 Enter 鍵。
6. 在詢問您是否要繼續連線的問題中輸入 [是]，然後按 Enter 鍵。
7. 輸入您在建立 VM 時所輸入的密碼。 成功登入 VM 後，您會看到 adminuser@MyVm:~$ 提示字元。 您現在已透過 Cloud Shell 工作階段登入 VM 了。 **注意︰**Cloud Shell 工作階段會在閒置 10 分鐘後逾時。

此時，指示會根據您正在使用的發佈而不同。 

#### <a name="ubuntusles"></a>Ubuntu/SLES

1. 在提示中，輸入 `uname -r`，並確認下列項目的版本：

    * Ubuntu 是 "4.4.0-77-generic" 或更新版本
    * SLES 是 "4.4.59-92.20-default" 或更新版本

2. 執行接下來的命令，以在標準網路 vNIC 和加速網路 vNIC 之間建立繫結。 網路流量會使用效能較高的加速網路 vNIC，而繫結動作則可確保網路流量不會因為某些設定有所變更而中斷。
          
     ```bash
     wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/configure_hv_sriov.sh
     chmod +x ./configure_hv_sriov.sh
     sudo ./configure_hv_sriov.sh
     ```
3. 執行指令碼之後，會在暫停 60 秒之後重新啟動 VM。
4. 在 VM 重新啟動後，再次完成步驟 5 至 7 以重新連線到 VM。
5. 執行 `ifconfig` 命令，並確認 bond0 已出現，而且介面顯示為 UP。 
 
 >[!NOTE]
      >使用加速網路的應用程式必須透過 bond0 介面而非 eth0 來通訊。  在加速網路功能正式推出之前，介面名稱可能會變更。

#### <a name="rhelcentos"></a>RHEL/CentOS

建立 Red Hat Enterprise Linux 或 CentOS 7.3 VM 時需要一些額外的步驟，才能載入 SR-IOV 所需的最新驅動程式以及網路卡的虛擬函式 (VF) 驅動程式。 指示的第一個階段會準備映像，以用來建立一或多個已預先載入驅動程式的虛擬機器。

##### <a name="phase-one-prepare-a-red-hat-enterprise-linux-or-centos-73-base-image"></a>第一個階段：準備 Red Hat Enterprise Linux 或 CentOS 7.3 基礎映像。 

1.  在 Azure 上佈建非 SRIOV CentOS 7.3 VM

2.  安裝 LIS 4.2.2：
    
    ```bash
    wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.2-2.tar.gz
    tar -xvf lis-rpms-4.2.2-2.tar.gz
    cd LISISO && sudo ./install.sh
    ```

3.  下載設定檔
    
    ```bash
    cd /etc/udev/rules.d/  
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/60-hyperv-vf-name.rules 
    cd /usr/sbin/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/hv_vf_name 
    sudo chmod +x hv_vf_name
    cd /etc/sysconfig/network-scripts/
    sudo wget https://raw.githubusercontent.com/LIS/lis-next/master/tools/sriov/ifcfg-vf1   
    ```

4.  取消佈建此 VM

    ```bash
    sudo waagent -deprovision+user 
    ```

5.  從 Azure 入口網站停止此 VM，並移至 VM 的 [磁碟]，然後擷取 OSDisk 的 VHD URI。 此 URI 包含基礎映像的 VHD 名稱和其儲存體帳戶。 
 
##### <a name="phase-two-provision-new-vms-on-azure"></a>第二個階段：在 Azure 上佈建新的 VM

1.  使用第一個階段中所擷取的基礎映像 VHD，並在 vNIC 上啟用 AcceleratedNetworking，佈建以 New-AzureRMVMConfig 為基礎的新 VM：

    ```powershell
    $RgName="MyResourceGroup"
    $Location="westus2"
    
    # Create a resource group
    New-AzureRmResourceGroup `
     -Name $RgName `
     -Location $Location

    # Create a subnet
    $Subnet = New-AzureRmVirtualNetworkSubnetConfig `
     -Name MySubnet `
     -AddressPrefix 10.0.0.0/24

    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
     -ResourceGroupName $RgName `
     -Location $Location `
     -Name MyVnet `
     -AddressPrefix 10.0.0.0/16 `
     -Subnet $Subnet
    
    # Create a public IP address
    $Pip = New-AzureRmPublicIpAddress `
     -Name MyPublicIp `
     -ResourceGroupName $RgName `
     -Location $Location `
     -AllocationMethod Static
    
    # Create a virtual network interface and associate the public IP address to it
    $Nic = New-AzureRmNetworkInterface `
     -Name MyNic `
     -ResourceGroupName $RgName `
     -Location $Location `
     -SubnetId $Vnet.Subnets[0].Id `
     -PublicIpAddressId $Pip.Id `
     -EnableAcceleratedNetworking
    
    # Specify the base image's VHD URI (from phase one step 5). 
    # Note: The storage account of this base image vhd should have "Storage service encryption" disabled
    # See more from here: https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption
    # This is just an example URI, you will need to replace this when running this script
    $sourceUri="https://myexamplesa.blob.core.windows.net/vhds/CentOS73-Base-Test120170629111341.vhd" 

    # Specify a URI for the location from which the new image binary large object (BLOB) is copied to start the virtual machine. 
    # Must end with ".vhd" extension
    $OsDiskName = "MyOsDiskName.vhd" 
    $destOsDiskUri = "https://myexamplesa.blob.core.windows.net/vhds/" + $OsDiskName
    
    # Define a credential object for the VM. PowerShell prompts you for a username and password.
    $Cred = Get-Credential
    
    # Create a custom virtual machine configuration
    $VmConfig = New-AzureRmVMConfig `
     -VMName MyVM -VMSize Standard_DS4_v2 | `
    Set-AzureRmVMOperatingSystem `
     -Linux `
     -ComputerName myVM `
     -Credential $Cred | `
    Add-AzureRmVMNetworkInterface -Id $Nic.Id | `
    Set-AzureRmVMOSDisk `
     -Name $OsDiskName `
     -SourceImageUri $sourceUri `
     -VhdUri $destOsDiskUri `
     -CreateOption FromImage `
     -Linux
    
    # Create the virtual machine.    
    New-AzureRmVM `
     -ResourceGroupName $RgName `
     -Location $Location `
     -VM $VmConfig
    ```

2.  VM 開機之後，請透過 "lspci" 檢查 VF 裝置，並檢查 Mellanox 項目。 例如，我們應該會在 lspci 輸出中看到這個項目：
    
    ```
    0001:00:02.0 Ethernet controller: Mellanox Technologies MT27500/MT27520 Family [ConnectX-3/ConnectX-3 Pro Virtual Function]
    ```
    
3.  執行 bonding 指令碼，方式為：

    ```bash
    sudo bondvf.sh
    ```

4.  重新啟動新的 VM：

    ```bash
    sudo reboot
    ```

VM 的開頭應該設定為 bond0，並啟用加速網路路徑。  執行 `ifconfig` 進行確認。
