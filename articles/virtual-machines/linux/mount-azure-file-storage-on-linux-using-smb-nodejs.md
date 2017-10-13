---
title: "透過 Azure CLI 1.0 使用 SMB 在 Linux VM 上掛接 Azure 檔案儲存體 | Microsoft Docs"
description: "如何使用 SMB 在 Linux VM 上掛接 Azure 檔案儲存體"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/07/2016
ms.author: v-livech
ms.openlocfilehash: 4951860630f0aad107d0846d52ebe4423ee0b91c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="mount-azure-file-storage-on-linux-vms-by-using-smb-with-azure-cli-10"></a>透過 Azure CLI 1.0 使用 SMB 在 Linux VM 上掛接 Azure 檔案儲存體

本文說明如何使用伺服器訊息區 (SMB) 通訊協定在 Linux VM 上掛接 Azure 檔案儲存體。 檔案儲存體可透過標準的 SMB 通訊協定在雲端中提供檔案共用。 這些需求包括：

* [Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)
* [安全殼層 (SSH) 公開金鑰和私密金鑰檔案](mac-create-ssh-keys.md)

## <a name="cli-versions-to-use"></a>要使用的 CLI 版本
您可以使用下列其中一個命令列介面 (CLI) 版本來完成工作︰

- [Azure CLI 1.0](#quick-commands) – 適用於傳統和資源管理部署模型的 CLI (本文章)
- [Azure CLI 2.0](mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 適用於資源管理部署模型的新一代 CLI


## <a name="quick-commands"></a>快速命令
若要快速完成工作，請遵循本節中的步驟。 如需詳細資訊和內容，請從＜[詳細的逐步解說](mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough)＞一節來開始。

### <a name="prerequisites"></a>必要條件
* 資源群組
* Azure 虛擬網路
* 具有 SSH 輸入的網路安全性群組
* 子網路
* Azure 儲存體帳戶
* Azure 儲存體帳戶金鑰
* Azure 檔案儲存體共用
* Linux VM

將任何範例換成您自己的設定。

### <a name="create-a-directory-for-the-local-mount"></a>建立本機掛接的目錄

```bash
mkdir -p /mnt/mymountpoint
```

### <a name="mount-the-file-storage-smb-share-to-the-mount-point"></a>掛接檔案儲存體 SMB 共用至掛接點

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

### <a name="persist-the-mount-after-a-reboot"></a>在重新開機之後保留掛接
將下面這行新增至 `/etc/fstab`：

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>詳細的逐步解說

檔案儲存體可在使用標準 SMB 通訊協定的雲端中提供檔案共用。 有了最新版本的檔案儲存體，您也可以從支援 SMB 3.0 的任何作業系統掛接檔案共用。 在 Linux 上使用 SMB 掛接時，您可以輕鬆地將備份放到有 SLA 支援的強大、永久封存儲存體位置。

將檔案從 VM 移至裝載在檔案儲存體上的 SMB 掛接，可方便您對記錄進行偵錯。 這是因為相同的 SMB 共用也可掛接至 Mac、Linux 或 Windows 工作站的本機上。 SMB 並非即時串流 Linux 或應用程式記錄的最佳解決方案，因為 SMB 通訊協定的建置目的不是要處理如此繁重的記錄職責。 專用的整合記錄層級工具，像是 Fluentd，是比透過 SMB 收集 Linux 和應用程式記錄輸出更好的選擇。

對於此詳細逐步解說，我們會建立必要的先決條件，先建立檔案儲存體共用，然後透過 SMB 在 Linux VM 上掛接它。

1. 使用下列程式碼來建立 Azure 儲存體帳戶︰

    ```azurecli
    azure storage account create myStorageAccount \
    --sku-name lrs \
    --kind storage \
    -l westus \
    -g myResourceGroup
    ```

2. 顯示儲存體帳戶金鑰。

    在建立儲存體帳戶時，帳戶金鑰會成對建立，因此您可以輪替金鑰，而不會干擾到服務。 當您將金鑰切換為金鑰組中的第二個金鑰時，您會建立新的金鑰組。 新的儲存體帳戶金鑰一律會成對建立，確保您永遠有至少一個準備切換到的未使用儲存體金鑰。 若要顯示儲存體帳戶金鑰，請使用下列程式碼︰

    ```azurecli
    azure storage account keys list myStorageAccount \
    --resource-group myResourceGroup
    ```
3. 建立檔案儲存體共用。

    檔案儲存體共用包含 SMB 共用。 配額永遠是以 GB 表示。 若要建立檔案儲存體共用，請使用下列程式碼：

    ```azurecli
    azure storage share create mystorageshare \
    --quota 10 \
    --account-name myStorageAccount \
    --account-key nPOgPR<--snip-->4Q==
    ```

4. 建立掛接點目錄。

    您必須在 Linux 檔案系統上建立本機目錄，以供 SMB 共用來掛接。 任何從本機掛接目錄寫入或讀取的項目會轉送給裝載於檔案儲存體上的 SMB 共用。 若要建立目錄，請使用下列程式碼：

    ```bash
    sudo mkdir -p /mnt/mymountdirectory
    ```

5. 使用下列程式碼來掛接 SMB 共用︰

    ```azurecli
    sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
    ```

6. 透過重新開機持續 SMB 掛接。

    當您重新開機 Linux VM 時，掛接的 SMB 共用會在關機期間取消掛接。 若要在開機時重新掛接 SMB 共用，您必須新增一行至 Linux /etc/fstab。 Linux 會使用 fstab 檔案來列出它在開機程序期間所必須掛接的檔案系統。 新增 SMB 共用可確保檔案儲存體共用是 Linux VM 的永久掛接檔案系統。 當您使用 cloud-init 時，便可以將檔案儲存體 SMB 共用新增至新的 VM。

    ```bash
    //myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
    ```

## <a name="next-steps"></a>後續步驟

- [在建立期間使用 cloud-init 自訂 Linux VM](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [在 Linux VM 中新增磁碟](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [使用 Azure CLI 將 Linux VM 上的磁碟加密](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
