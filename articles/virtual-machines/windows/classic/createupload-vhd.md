---
title: "使用 Powershell 建立並上傳 VM 映像 | Microsoft Docs"
description: "了解如何使用傳統部署模型和 Azure Powershell 來建立並上傳一般化 Windows Server 映像 (VHD)。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 8c4a08fe-7714-4bf0-be87-c728a7806d3f
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: bc75c8cdd98b0ea0fbff6483c0e3c9d4468d3941
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="create-and-upload-a-windows-server-vhd-to-azure"></a>建立並上傳 Windows Server VHD 到 Azure
本文說明如何上傳您自己的一般化 VM 映像做為虛擬硬碟 (VHD)，以便使用它來建立虛擬機器。 如需 Microsoft Azure 中磁碟和 VHD 的詳細資訊，請參閱[關於虛擬機器的磁碟和 VHD](../about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

> [!IMPORTANT]
> Azure 建立和處理資源的部署模型有二種： [資源管理員和傳統](../../../resource-manager-deployment-model.md)。 本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議讓大部分的新部署使用資源管理員模式。 您也可以使用 Resource Manager 模型來[上傳](../upload-generalized-managed.md)虛擬機器。

## <a name="prerequisites"></a>必要條件
本文假設您具有：

* **Azure 訂用帳戶** - 如果您沒有帳戶，您可以 [免費申請 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)。
* **[Microsoft Azure PowerShell](/powershell/azure/overview)** - 您已安裝 Microsoft Azure PowerShell 模組，並設定成使用您的訂用帳戶。
* **.VHD 檔案** - 儲存在 .vhd 檔案中並連接至虛擬機器的受支援 Windows 作業系統。 檢查以查看 Sysprep 是否支援在 VHD 上執行的伺服器角色。 如需詳細資訊，請參閱 [Sysprep Support for Server Roles (伺服器角色的 Sysprep 支援)](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)。

    > [!IMPORTANT]
    > Microsoft Azure 不支援 VHDX 格式。 您可以使用 Hyper-V 管理員或 [Convert-VHD Cmdlet](http://technet.microsoft.com/library/hh848454.aspx)，將磁碟轉換為 VHD 格式。 如需詳細資料，請參閱 [部落格文章](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx)。

## <a name="step-1-prep-the-vhd"></a>步驟 1：準備 VHD
將 VHD 上傳至 Azure 之前，必須使用 Sysprep 工具來一般化。 這要準備 VHD 以做為映像。 如需 Sysprep 的詳細資訊，請參閱 [如何使用 Sysprep：簡介](http://technet.microsoft.com/library/bb457073.aspx)。 執行 Sysprep 前，請先備份 VM。

從安裝作業系統的虛擬機器，完成下列程序：

1. 登入作業系統。
2. 以系統管理員身分開啟 [命令提示字元] 視窗。 切換至 **%windir%\system32\sysprep** 目錄，然後執行 `sysprep.exe`。

    ![開啟 [命令提示字元] 視窗](./media/createupload-vhd/sysprep_commandprompt.png)
3. [系統準備工具]  對話方塊隨即出現。

   ![啟動 Sysprep](./media/createupload-vhd/sysprepgeneral.png)
4. 在 [系統準備工具] 中，選取 [進入系統全新體驗 (OOBE)]，並確認已勾選 [一般化]。
5. 在 [關機選項] 中選取 [關機]。
6. 按一下 [確定] 。

## <a name="step-2-create-a-storage-account-and-a-container"></a>步驟 2：建立儲存體帳戶和容器
您需要 Azure 中的儲存體帳戶，讓您有空間可上傳 .vhd 檔案。 此步驟說明如何建立帳戶，或從現有的帳戶取得您需要的資訊。 使用您自己的資訊來取代 &lsaquo; 括號 &rsaquo;中的變數。

1. 登入

    ```powershell
    Add-AzureAccount
    ```

2. 設定您的 Azure 訂用帳戶

    ```powershell
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

3. 建立新的儲存體帳戶。 儲存體帳戶的名稱應該是唯一的且包含 3-24 個字元。 名稱可以是字母和數字的任意組合。 您也必須指定一個位置，例如「美國東部」。

    ```powershell
    New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>
    ```

4. 將新儲存體帳戶設定為預設值。

    ```powershell
    Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>
    ```

5. 建立新的容器。

    ```powershell
    New-AzureStorageContainer -Name <ContainerName> -Permission Off
    ```

## <a name="step-3-upload-the-vhd-file"></a>步驟 3：上傳 .vhd 檔案
使用 [Add-AzureVhd](https://docs.microsoft.com/en-us/powershell/module/azure/add-azurevhd) 來上傳 VHD。

從您在上一個步驟中使用的 Azure PowerShell 視窗中，輸入下列命令並以您自己的資訊取代 &lsaquo; 括號 &rsaquo; 中的變數。

```powershell
Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>
```

## <a name="step-4-add-the-image-to-your-list-of-custom-images"></a>步驟 4：將映像新增到您的自訂映像清單
使用 [Add-AzureVMImage](https://docs.microsoft.com/en-us/powershell/module/azure/add-azurevmimage) Cmdlet 將映像新增到您的自訂映像清單。

```powershell
Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"
```

## <a name="next-steps"></a>後續步驟
您現在可以使用上傳的映像來[建立自訂的 VM](createportal.md)。

