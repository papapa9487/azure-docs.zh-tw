---
title: "將資料磁碟連結到 Linux VM | Microsoft Docs"
description: "使用入口網站將新的或現有的資料磁碟附加至 Linux VM。"
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
ms.date: 09/25/2017
ms.author: cynthn
ms.translationtype: HT
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: 787f729732accd74c212b3be9520af50a2f04261
ms.contentlocale: zh-tw
ms.lasthandoff: 09/27/2017

---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>使用入口網站將資料磁碟附加至 Linux VM 
本文示範如何透過 Azure 入口網站將新的及現有的磁碟連結到 Linux 虛擬機器。 您也可以[在 Azure 入口網站中將資料磁碟連結到 Windows VM](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 

將磁碟附加至 VM 之前，請檢閱下列提示︰

* 虛擬機器的大小會控制您可以連接的資料磁碟數目。 如需詳細資訊，請參閱 [虛擬機器的大小](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 若要使用進階儲存體，您需要 DS 系列或 GS 系列的虛擬機器。 您可以使用進階或標準磁碟搭配這些虛擬機器。 僅特定地區可用進階儲存體。 如需詳細資訊，請參閱[進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../../storage/common/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 附加至虛擬機器的磁碟實際上是 Azure 中儲存的 .vhd 檔案。 如需詳細資訊，請參閱 [有關虛擬機器的磁碟和 VHD](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。


## <a name="find-the-virtual-machine"></a>尋找虛擬機器
1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在左側功能表上，按一下 [虛擬機器]。
3. 然後從清單中選取虛擬機器。
4. 在 [虛擬機器] 頁面的 [基本資訊] 中，按一下 [磁碟]。
   
    ![開啟磁碟設定](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>附加新的磁碟

1. 在 [磁碟] 窗格上，按一下 [+ 新增資料磁碟]。
2. 按一下 [名稱] 的下拉式選單，然後選取 [建立磁碟]：

    ![建立 Azure 受控磁碟](./media/attach-disk-portal/create-new-md.png)

3. 輸入受控磁碟的名稱。 檢閱預設設定，視需要進行更新，然後按一下建立。
   
   ![檢閱磁碟設定](./media/attach-disk-portal/create-new-md-settings.png)

4. 按一下 [儲存] 以建立受控磁碟並更新 VM 組態︰

   ![儲存新的 Azure 受控磁碟](./media/attach-disk-portal/confirm-create-new-md.png)

5. 在 Azure 建立磁碟並將其連接至虛擬機器之後，該新磁碟就會列在虛擬機器之磁碟設定中的 [資料磁碟] 底下。 當受控磁碟是最上層資源時，磁碟會出現在資源群組的根目錄︰

   ![資源群組中的 Azure 受控磁碟](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>連接現有磁碟
1. 在 [磁碟] 窗格上，按一下 [+ 新增資料磁碟]。
2. 按一下 [名稱] 的下拉式選單，以檢視您的 Azure 訂用帳戶可存取的現有受控磁碟清單。 選取要附加的受控磁碟︰

   ![附加現有的 Azure 受控磁碟](./media/attach-disk-portal/select-existing-md.png)

3. 按一下 [儲存] 以附加現有的受控磁碟並更新 VM 組態︰
   
   ![儲存 Azure 受控磁碟更新](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Azure 將磁碟連接至虛擬機器之後，該磁碟會列在虛擬機器磁碟設定中的 [資料磁碟] 下面。



## <a name="next-steps"></a>後續步驟
您也可以使用 Azure CLI [附加資料磁碟](add-disk.md)。

