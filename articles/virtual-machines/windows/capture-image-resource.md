---
title: "在 Azure 中建立受控映像 | Microsoft Docs"
description: "在 Azure 中建立一般化 VM 或 VHD 的受控映像。 映像可用來建立多部使用受控磁碟的 VM。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: e428b755f6696bd6d4047ad77579a8e9665dfbd8
ms.contentlocale: zh-tw
ms.lasthandoff: 06/09/2017

---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>在 Azure 中建立一般化 VM 的受控映像

您可以從在儲存體帳戶中儲存為受控磁碟或非受控磁碟的一般化 VM，建立受控映像資源。 映像接著便可用來建立多個 VM。 


## <a name="generalize-the-windows-vm-using-sysprep"></a>使用 Sysprep 將 Windows VM 一般化

Sysprep 會移除您的所有個人帳戶資訊以及其他項目，並準備電腦以做為映像。 如需 Sysprep 的詳細資訊，請參閱 [如何使用 Sysprep：簡介](http://technet.microsoft.com/library/bb457073.aspx)。

請確定 Sysprep 支援電腦上執行的伺服器角色。 如需詳細資訊，請參閱 [Sysprep Support for Server Roles (伺服器角色的 Sysprep 支援)](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> 如果您是第一次在將 VHD 上傳至 Azure 之前執行 Sysprep，請確定您已[準備好 VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 再執行 Sysprep。 
> 
> 

1. 登入 Windows 虛擬機器。
2. 以系統管理員身分開啟 [命令提示字元] 視窗。 切換至 **%windir%\system32\sysprep** 目錄，然後執行 `sysprep.exe`。
3. 在 [系統準備工具] 對話方塊中，選取 [進入系統全新體驗 (OOBE)]，並確認已勾選 [一般化] 核取方塊。
4. 在 [關機選項] 中選取 [關機]。
5. 按一下 [確定] 。
   
    ![啟動 Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Sysprep 完成時，會關閉虛擬機器。 不要重新啟動 VM。


## <a name="create-a-managed-image-in-the-portal"></a>在入口網站中建立受控映像 

1. 開啟[入口網站](https://portal.azure.com)。
2. 按一下加號以建立新的資源。
3. 在篩選搜尋中，輸入 [映像]。
4. 選取結果中的 [映像]。
5. 在 [映像] 刀鋒視窗中，按一下 [建立]。
6. 在 [名稱] 中，輸入映像的名稱。
7. 如果您有多個訂用帳戶，請從 [訂用帳戶] 下拉式清單中選取正確的一個。
7. 在 [資源群組] 中選取 [新建] 並輸入名稱，或選取 [從現有的]，然後從下拉式清單中選取所要使用的資源群組。
8. 在 [位置] 中，選擇資源群組的位置。
9. 在 [OS 類型] 中，選取作業系統的類型 (Windows 或 Linux)。
11. 在 [儲存體 blob] 中，按一下 [瀏覽] 以尋找您 Azure 儲存體中的 VHD。
12. 在 [帳戶類型] 中選擇 Standard_LRS 或 Premium_LRS。 標準使用硬碟機，而進階使用固態磁碟機。 兩者都使用本機備援的儲存體。
13. 在 [磁碟快取] 中選取適當的磁碟快取選項。 選項包括 [無]、[唯讀] 和 [讀取\寫入]。
14. 選擇性︰您也可以將現有的資料磁碟新增至映像，只要按一下 [+ 新增資料磁碟]。  
15. 完成選取之後，請按一下 [建立]。
16. 建立映像之後，您會在所選資源群組的資源清單中看見它顯示為 [映像] 資源。



## <a name="create-a-managed-image-of-a-vm-using-powershell"></a>使用 Powershell 建立 VM 的受控映像

直接從 VM 建立映像，可確保映像包含 VM 的所有相關磁碟，包括 OS 磁碟和任何資料磁碟。


開始之前，請確定您擁有最新版的 AzureRM.Compute PowerShell 模組。 執行下列命令來安裝它。

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
如需詳細資訊，請參閱 [Azure PowerShell 版本控制](/powershell/azure/overview)。


1. 建立一些變數。

    ```powershell
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. 確定已解除配置 VM。

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. 將虛擬機器的狀態設定為 [一般化] 。 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. 取得虛擬機器。 

    ```powershell
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. 建立映像組態。

    ```powershell
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. 建立映像。

    ```powershell
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 



## <a name="create-a-managed-image-of-a-vhd-in-powershell"></a>在 PowerShell 中建立 VHD 的受控映像

使用一般化 OS VHD 建立受控映像。


1.  首先，設定一般參數：

    ```powershell
    $rgName = "myResourceGroupName"
    $vmName = "myVM"
    $location = "West Central US" 
    $imageName = "yourImageName"
    $osVhdUri = "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. 停止\解除配置 VM。

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. 將 VM 標示為一般化。

    ```powershell
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized 
    ```
4.  使用一般化 OS VHD 建立映像。

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```


## <a name="create-a-managed-image-from-a-snapshot-using-powershell"></a>使用 Powershell 從快照集建立受控映像

您也可以從一般化 VM 中的 VHD 快照集建立受控映像。

    
1. 建立一些變數。 

    ```powershell
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. 取得快照集。

   ```powershell
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. 建立映像組態。

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. 建立映像。

    ```powershell
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 
    

## <a name="next-steps"></a>後續步驟
- 您現在可以[從一般化受控映像建立 VM](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。  


