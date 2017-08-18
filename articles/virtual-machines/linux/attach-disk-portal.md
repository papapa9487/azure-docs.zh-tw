---
title: "將資料磁碟連結到 Linux VM | Microsoft Docs"
description: "如何在 Azure 入口網站中，使用資源管理員部署模型，將新的或現有的資料磁碟連結到 Linux VM。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: cynthn
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: fb3b4ea612777d0ba801342dacf4b2ece46afed6
ms.contentlocale: zh-tw
ms.lasthandoff: 08/11/2017

---
# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>如何在 Azure 入口網站中將資料磁碟連結到 Linux VM
本文示範如何透過 Azure 入口網站將新的及現有的磁碟連結到 Linux 虛擬機器。 您也可以[在 Azure 入口網站中將資料磁碟連結到 Windows VM](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 您可以選擇使用 Azure 受控磁碟或非受控磁碟。 受控磁碟是由 Azure 平台處理，不需要任何準備或位置來儲存它們。 非受控磁碟需要儲存體帳戶且[套用一些配額和限制](../../azure-subscription-service-limits.md#storage-limits)。 如需 Azure 受控磁碟的詳細資訊，請參閱 [Azure 受控磁碟概觀](../../storage/storage-managed-disks-overview.md)。

將磁碟附加至 VM 之前，請檢閱下列提示︰

* 虛擬機器的大小會控制您可以連接的資料磁碟數目。 如需詳細資訊，請參閱 [虛擬機器的大小](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 若要使用進階儲存體，您需要 DS 系列或 GS 系列的虛擬機器。 您可以使用進階或標準磁碟搭配這些虛擬機器。 僅特定地區可用進階儲存體。 如需詳細資訊，請參閱[進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 附加至虛擬機器的磁碟實際上是 Azure 中儲存的 .vhd 檔案。 如需詳細資訊，請參閱 [有關虛擬機器的磁碟和 VHD](../../storage/storage-about-disks-and-vhds-linux.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。


## <a name="find-the-virtual-machine"></a>尋找虛擬機器
1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在 [中樞] 功能表上，按一下 [虛擬機器] 。
3. 然後從清單中選取虛擬機器。
4. 在 [虛擬機器] 刀鋒視窗上的 [程式集] 中，按一下 [磁碟]。
   
    ![開啟磁碟設定](./media/attach-disk-portal/find-disk-settings.png)

接著，依照指示來附加[受控磁碟](#use-azure-managed-disks)或[非受控磁碟](#use-unmanaged-disks)。

## <a name="use-azure-managed-disks"></a>使用 Azure 受控磁碟

### <a name="attach-a-new-disk"></a>附加新的磁碟

1. 在 [磁碟] 刀鋒視窗上，按一下 [+ 新增資料磁碟]。
2. 按一下 [名稱] 的下拉式選單，然後選取 [建立磁碟]：

    ![建立 Azure 受控磁碟](./media/attach-disk-portal/create-new-md.png)

3. 輸入受控磁碟的名稱。 檢閱預設設定，視需要進行更新，然後按一下 [建立]。
   
   ![檢閱磁碟設定](./media/attach-disk-portal/create-new-md-settings.png)

4. 按一下 [儲存] 以建立受控磁碟並更新 VM 組態︰

   ![儲存新的 Azure 受控磁碟](./media/attach-disk-portal/confirm-create-new-md.png)

5. 在 Azure 建立磁碟並將其連接至虛擬機器之後，該新磁碟就會列在虛擬機器之磁碟設定中的 [資料磁碟] 底下。 當受控磁碟是最上層資源時，磁碟會出現在資源群組的根目錄︰

   ![資源群組中的 Azure 受控磁碟](./media/attach-disk-portal/view-md-resource-group.png)

### <a name="attach-an-existing-disk"></a>連接現有磁碟
1. 在 [磁碟] 刀鋒視窗上，按一下 [+ 新增資料磁碟]。
2. 按一下 [名稱] 的下拉式選單，以檢視您的 Azure 訂用帳戶可存取的現有受控磁碟清單。 選取要附加的受控磁碟︰

   ![附加現有的 Azure 受控磁碟](./media/attach-disk-portal/select-existing-md.png)

3. 按一下 [儲存] 以附加現有的受控磁碟並更新 VM 組態︰
   
   ![儲存 Azure 受控磁碟更新](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Azure 將磁碟連接至虛擬機器之後，該磁碟會列在虛擬機器磁碟設定中的 [資料磁碟] 下面。

## <a name="use-unmanaged-disks"></a>使用非受控磁碟

### <a name="attach-a-new-disk"></a>附加新的磁碟

1. 在 [磁碟] 刀鋒視窗上，按一下 [+ 新增資料磁碟]。
2. 檢閱預設設定，視需要進行更新，然後按一下 [確定] 。
   
   ![檢閱磁碟設定](./media/attach-disk-portal/attach-new.png)
3. 在 Azure 建立磁碟並將其連接至虛擬機器之後，該新磁碟就會列在虛擬機器之磁碟設定中的 [資料磁碟] 底下。

### <a name="attach-an-existing-disk"></a>連接現有磁碟
1. 在 [磁碟] 刀鋒視窗上，按一下 [+ 新增資料磁碟]。
2. 在 [連結現有磁碟] 底下，按一下 [VHD 檔案]。
   
   ![連接現有磁碟](./media/attach-disk-portal/attach-existing.png)
3. 在 [儲存體帳戶] 底下，選取持有該 .vhd 檔案的帳戶和容器。
   
   ![尋找 VHD 位置](./media/attach-disk-portal/find-storage-container.png)
4. 選取 .vhd 檔案。
5. 在 [連結現有磁碟] 底下，您剛才選取的檔案會列在 [VHD 檔案] 底下。 按一下 [確定] 。
6. Azure 將磁碟連接至虛擬機器之後，該磁碟會列在虛擬機器磁碟設定中的 [資料磁碟] 下面。


## <a name="next-steps"></a>後續步驟
加入磁碟後，您需要準備它以便使用。 如需詳細資訊，請參閱這篇[做法：在 Linux 中初始化新的資料磁碟](add-disk.md)。

