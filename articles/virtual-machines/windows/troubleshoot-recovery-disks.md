---
title: "使用 Windows 疑難排解 VM 與 Azure PowerShell | Microsoft Docs"
description: "了解如何在 Azure 中使用 Azure PowerShell 將 OS 磁碟連接至復原 VM，以針對 Windows VM 問題進行疑難排解"
services: virtual-machines-windows
documentationCenter: 
authors: genlin
manager: timlt
editor: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/26/2017
ms.author: genli
ms.openlocfilehash: 8b7821b4285e73d461af426bfdfb3fdcc4454517
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-azure-powershell"></a>使用 Azure PowerShell 將 OS 磁碟連結至復原 VM，以針對 Windows VM 進行疑難排解
如果 Azure 中的 Windows 虛擬機器 (VM) 發生開機或磁碟錯誤，您可能需要對虛擬硬碟本身執行疑難排解步驟。 常見的例子是應用程式更新無效，導致 VM 無法成功開機。 本文詳細說明如何使用 Azure PowerShell 將虛擬硬碟連接至另一個 Windows VM，以修正任何錯誤，然後重新建立原始 VM。


## <a name="recovery-process-overview"></a>復原程序概觀
疑難排解程序如下所示︰

1. 刪除遇到問題的 VM，保住虛擬硬碟。
2. 將虛擬硬碟連結和掛接至另一個 Windows VM，以進行疑難排解。
3. 連接至疑難排解 VM。 編輯檔案或執行任何工具來修正原始虛擬硬碟的問題。
4. 從疑難排解 VM 卸載並中斷連結虛擬硬碟。
5. 使用原始虛擬硬碟建立 VM。

確定您已安裝[最新的 Azure PowerShell](/powershell/azure/overview) 並登入您的訂用帳戶。

```powershell
Login-AzureRMAccount
```

在下列範例中，請以您自己的值取代參數名稱。 範例參數名稱包含 `myResourceGroup`、`mystorageaccount` 和 `myVM`。


## <a name="determine-boot-issues"></a>判斷開機問題
您可以在 Azure 中檢視 VM 的螢幕擷取畫面，以協助疑難排解開機問題。 這個螢幕擷取畫面有助於找出 VM 無法開機的原因。 下列範例會從名為 `myResourceGroup` 的資源群組中名為 `myVM` 的 Windows VM 取得螢幕擷取畫面：

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup `
    -Name myVM -Windows -LocalPath C:\Users\ops\
```

檢閱螢幕擷取畫面來判斷 VM 為何無法開機。 請注意任何特定的錯誤訊息或提供的錯誤代碼。


## <a name="view-existing-virtual-hard-disk-details"></a>檢視現有的虛擬硬碟詳細資料
您需要先識別虛擬硬碟 (VHD) 的名稱，才能將虛擬硬碟連結至另一個 VM。

下列範例會針對資源群組 `myResourceGroup` 中的 VM `myVM` 取得相關資訊：

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

從上述命令的輸出尋找 `StorageProfile` 區段內的 `Vhd URI`。 下列截斷範例輸出顯示向程式碼區塊結束的 `Vhd URI`︰

```powershell
RequestId                     : 8a134642-2f01-4e08-bb12-d89b5b81a0a0
StatusCode                    : OK
ResourceGroupName             : myResourceGroup
Id                            : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
Name                          : myVM
Type                          : Microsoft.Compute/virtualMachines
...
StorageProfile                :
  ImageReference              :
    Publisher                 : MicrosoftWindowsServer
    Offer                     : WindowsServer
    Sku                       : 2016-Datacenter
    Version                   : latest
  OsDisk                      :
    OsType                    : Windows
    Name                      : myVM
    Vhd                       :
      Uri                     : https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
    Caching                   : ReadWrite
    CreateOption              : FromImage
```


## <a name="delete-existing-vm"></a>刪除現有的 VM
虛擬硬碟和 VM 在 Azure 中是兩個不同的資源。 虛擬硬碟中儲存作業系統本身、應用程式和設定。 VM 本身只是定義大小或位置的中繼資料，還會參考資源，例如虛擬硬碟或虛擬網路介面卡 (NIC)。 每個虛擬硬碟連結至 VM 時會獲派租用。 雖然即使 VM 正在執行時也可以連結和中斷連結資料磁碟，但除非刪除 VM 資源，否則無法中斷連結 OS 磁碟。 即使 VM 處於已停止和解除配置的狀態，租用仍會持續讓 OS 磁碟與 VM 產生關聯。

復原 VM 的第一個步驟是刪除 VM 資源本身。 刪除 VM 時，虛擬硬碟會留在儲存體帳戶中。 刪除 VM 之後，您需要將虛擬硬碟連結至另一個 VM，以進行疑難排解並解決錯誤。

下列範例會從資源群組 `myResourceGroup` 中刪除 VM `myVM`：

```powershell
Remove-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
```

請等到 VM 完成刪除之後，再將虛擬硬碟連結至另一個 VM。 在虛擬硬碟上，將它與 VM 產生關聯的租用必須先釋放，您才能將虛擬硬碟連結至另一個 VM。


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>將現有的虛擬硬碟連結至另一個 VM
在接下來幾個步驟中，您將使用另一個 VM 進行疑難排解。 您將現有的虛擬硬碟連結至這個疑難排解 VM，以瀏覽並編輯磁碟的內容。 舉例來說，此程序可讓您更正任何設定錯誤，或檢閱其他應用程式記錄檔或系統記錄檔。 選擇或建立另一個 VM 以進行疑難排解。

當您連結現有的虛擬硬碟時，請指定在先前的 `Get-AzureRmVM` 命令中取得的磁碟 URL。 下列範例會將現有的虛擬硬碟連結至資源群組 `myResourceGroup` 中的疑難排解 VM `myVMRecovery`：

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
Add-AzureRmVMDataDisk -VM $myVM -CreateOption "Attach" -Name "DataDisk" -DiskSizeInGB $null `
    -VhdUri "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

> [!NOTE]
> 新增磁碟要求您指定磁碟的大小。 因為我們連接現有磁碟，`-DiskSizeInGB` 指定為 `$null`。 此值可確保正確連接資料磁碟，也不需要判斷資料磁碟的真實大小。


## <a name="mount-the-attached-data-disk"></a>掛接已連結的資料磁碟

1. 使用適當的認證以 RDP 登入疑難排解 VM。 下列範例會下載名為 `myResourceGroup` 的資源群組中名為 `myVMRecovery`的 VM 之 RDP 連接檔案，並將它下載至 `C:\Users\ops\Documents`」

    ```powershell
    Get-AzureRMRemoteDesktopFile -ResourceGroupName "myResourceGroup" -Name "myVMRecovery" `
        -LocalPath "C:\Users\ops\Documents\myVMRecovery.rdp"
    ```

2. 會自動偵測並連接資料磁碟。 檢視已連接磁碟區的清單來判斷磁碟機代號，如下所示︰

    ```powershell
    Get-Disk
    ```

    下列範例輸出會顯示虛擬硬碟已連接磁碟 **2**。 (您也可以使用 `Get-Volume` 檢視磁碟機代號)：

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Online       127 GB MBR
    ```

## <a name="fix-issues-on-original-virtual-hard-disk"></a>修正原始虛擬硬碟的問題
已掛接現有的虛擬硬碟掛，您現在可以視需要執行任何維護和疑難排解步驟。 解決問題之後，請繼續進行下列步驟。


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>卸載並中斷連結原始虛擬硬碟
一旦解決錯誤，您就要從疑難排解 VM 卸載中斷連結並現有的虛擬硬碟。 直到將虛擬硬碟連結至疑難排解 VM 的租用釋放，您才能將虛擬硬碟用於其他任何 VM。

1. 從您的 RDP 工作階段內卸載您復原 VM 上的資料磁碟。 您需要先前 `Get-Disk` cmdlet 的磁碟編號。 然後，使用 `Set-Disk` 將磁碟設為離線︰

    ```powershell
    Set-Disk -Number 2 -IsOffline $True
    ```

    再次使用 `Get-Disk` 確認磁碟現已設為離線。 下列範例輸出顯示磁碟現在設為離線：

    ```powershell
    Number   Friendly Name   Serial Number   HealthStatus   OperationalStatus   Total Size   Partition
                                                                                             Style
    ------   -------------   -------------   ------------   -----------------   ----------   ----------
    0        Virtual HD                                     Healthy             Online       127 GB MBR
    1        Virtual HD                                     Healthy             Online       50 GB MBR
    2        Msft Virtu...                                  Healthy             Offline      127 GB MBR
    ```

2. 結束您的 RDP 工作階段。 從 Azure PowerShell 工作階段，從疑難排解 VM 中移除虛擬硬碟。

    ```powershell
    $myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMRecovery"
    Remove-AzureRmVMDataDisk -VM $myVM -Name "DataDisk"
    Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
    ```


## <a name="create-vm-from-original-hard-disk"></a>從原始硬碟建立 VM
若要從原始虛擬硬碟建立 VM，請使用[這個 Azure Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet)。 實際的 JSON 範本位於下列連結︰

- https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json

此範本會使用來自先前命令的 VHD URL，將 VM 部署至現有的虛擬網路。 下列範例會將範本部署至名為 `myResourceGroup` 的資源群組：

```powershell
New-AzureRmResourceGroupDeployment -Name myDeployment -ResourceGroupName myResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd-existing-vnet/azuredeploy.json
```

回答範本的提示，例如 VM 名稱、OS 類型和 VM 大小。 `osDiskVhdUri` 同於先前將現有的虛擬硬碟連結至疑難排解 VM 時所使用的值。


## <a name="re-enable-boot-diagnostics"></a>重新啟用開機診斷

當您從現有的虛擬硬碟建立 VM 時，可能不會自動啟用開機診斷。 下列範例會在資源群組 `myResourceGroup` 中的 VM `myVMDeployed` 上啟用診斷擴充：

```powershell
$myVM = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVMDeployed"
Set-AzureRmVMBootDiagnostics -ResourceGroupName myResourceGroup -VM $myVM -enable
Update-AzureRmVM -ResourceGroup "myResourceGroup" -VM $myVM
```

## <a name="next-steps"></a>後續步驟
如果連接至 VM 時發生問題，請參閱[針對 Azure VM 的 RDP 連接進行疑難排解](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 如果存取 VM 上執行的應用程式時發生問題，請參閱[針對 Windows VM 上的應用程式連線問題進行疑難排解](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

如需使用 Resource Manager 的詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
