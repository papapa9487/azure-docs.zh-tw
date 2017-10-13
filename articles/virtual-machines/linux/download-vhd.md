---
title: "從 Azure 下載 Linux VHD | Microsoft Docs"
description: "使用 Azure CLI 和 Azure 入口網站來下載 Linux VHD。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: davidmu
ms.openlocfilehash: 3eb88478b43f8e3a36ae04bf3703f238e8cb1f3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="download-a-linux-vhd-from-azure"></a>從 Azure 下載 Linux VHD

本文說明如何使用 Azure CLI 和 Azure 入口網站，從 Azure 下載 [Linux 虛擬硬碟 (VHD)](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 檔案。 

Azure 中的虛擬機器 (VM) 會使用[磁碟](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)來儲存作業系統、應用程式和資料。 所有 Azure VM 都至少有兩個磁碟：一個 Windows 作業系統磁碟和一個暫存磁碟。 作業系統磁碟最初是從映像建立，且作業系統磁碟與該映像都是儲存在 Azure 儲存體帳戶中的 VHD。 虛擬機器也可以有一或多個資料磁碟，而這些磁碟也會儲存成 VHD。

如果尚未安裝 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2)，請先安裝。

## <a name="stop-the-vm"></a>停止 VM

如果 VHD 連接至執行中的 VM，便無法從 Azure 下載該 VHD。 您必須先停止 VM，才能下載 VHD。 如果您想要使用 VHD 作為[映像](tutorial-custom-images.md)，以新磁碟來建立其他 VM，則需要取消佈建並一般化包含在檔案中的作業系統，並停止 VM。 若要使用 VHD 作為現有 VM 或資料磁碟新執行個體的磁碟，您只需要停止並解除配置 VM。

若要使用 VHD 作為映像來建立其他 VM，請完成下列步驟：

1. 使用 SSH、帳戶名稱與 VM 的公用 IP 位址來連線至 VM，並取消佈建 VM。 +user 參數也會移除最後一個佈建的使用者帳戶。 如果要將帳戶認證備份至 VM 中，請省略此 +user 參數。 下列範例會移除最後一個佈建的使用者帳戶：

    ```bash
    ssh azureuser@40.118.249.235
    sudo waagent -deprovision+user -force
    exit 
    ```

2. 使用 [az login](https://docs.microsoft.com/cli/azure/#login) 來登入您的 Azure 帳戶。
3. 停止及解除配置 VM。

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. 一般化 VM。 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

若要使用 VHD 作為現有 VM 或資料磁碟新執行個體的磁碟，請完成下列步驟：

1.  登入 [Azure 入口網站](https://portal.azure.com/)。
2.  在 [中樞] 功能表上，按一下 [虛擬機器] 。
3.  從清單中選取 VM。
4.  在 VM 的刀鋒視窗中，按一下 [停止]。

    ![停止 VM](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>產生 SAS URL

若要下載 VHD 檔案，您需要產生[共用存取簽章 (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL。 產生 URL 時，會將到期時間指派給 URL。

1.  在 VM 刀鋒視窗的功能表中，按一下 [磁碟]。
2.  選取 VM 的作業系統磁碟，然後按一下匯出。
3.  按一下 [產生 URL]。

    ![產生 URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>下載 VHD

1.  在產生的 URL 之下，按一下 [下載 VHD 檔案]。

    ![下載 VHD](./media/download-vhd/export-download.png)

2.  您可能需要在瀏覽器中按一下 [儲存] 以開始下載。 VHD 檔案的預設名稱是 *abcd*。

    ![在瀏覽器中按一下 [儲存]](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>後續步驟

- 瞭解如何[使用 Azure CLI 2.0 從自訂磁碟上傳並建立 Linux VM](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 
- [使用 Azure CLI 管理 Azure 磁碟](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

