---
title: "搭配 Linux 使用 Azure 檔案儲存體 | Microsoft Docs"
description: "了解如何透過 Linux 上的 SMB 掛接 Azure 檔案共用。"
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 6edc37ce-698f-4d50-8fc1-591ad456175d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/8/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: d8987082c559a374b8d19fd69e20cf5e81cb25ef
ms.contentlocale: zh-tw
ms.lasthandoff: 08/25/2017

---
# <a name="use-azure-file-storage-with-linux"></a>搭配 Linux 使用 Azure 檔案儲存體
[Azure 檔案儲存體](../storage-dotnet-how-to-use-files.md)是 Microsoft 容易使用的雲端檔案系統。 Azure 檔案共用可裝載於使用 [cifs-utils 封裝](https://wiki.samba.org/index.php/LinuxCIFS_utils) (來自 [Samba 專案](https://www.samba.org/)) 的 Linux 發行版本。 本文將說明掛接 Azure 檔案共用的兩種方式：使用 `mount` 命令的隨選掛接，以及建立項目 `/etc/fstab` 的開機掛接。

> [!NOTE]  
> 若要在 Azure 區域之外掛接 Azure 檔案共用，例如內部部署或是在不同的 Azure 區域，作業系統必須支援 SMB 3.0 的加密功能。 4.11 核心推出 Linux 的 SMB 3.0 適用的加密功能。 此功能讓您可從內部部署或不同 Azure 區域的 Azure 檔案共用進行掛接。 發佈時，這項功能已向前移植到 16.04 及以上版本的 Ubuntu。


## <a name="prerequisities-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>以 Linux 掛接 Azure 檔案共用和 cifs-utils 封裝的必要條件
* **挑選可安裝 cifs-utils 封裝的 Linux 發行版本**：Microsoft 建議使用 Azure 映像庫中的下列 Linux 發行版本：

    * Ubuntu Server 14.04+
    * RHEL 7+
    * CentOS 7+
    * Debian 8
    * openSUSE 13.2+
    * SUSE Linux Enterprise Server 12

* <a id="install-cifs-utils"></a>**安裝 cifs-utils 封裝**：可使用封裝管理員將 cifs-utils 安裝在所選擇的 Linux 發行版本上。 

    在 **Ubuntu** 和 **Debian 型**發行版本上，請使用 `apt-get` 封裝管理員：

    ```
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    在 **RHEL** 和 **CentOS** 上，請使用 `yum` 封裝管理員：

    ```
    sudo yum install samba-client samba-common cifs-utils
    ```

    在 **openSUSE** 上，請使用 `zypper` 封裝管理員：

    ```
    sudo zypper install samba*
    ```

    在其他發行版本上，請使用適當的封裝管理員或[從來源編譯](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)。

* **對於掛接的共用決定目錄/檔案權限**：下列範例使用 0777 提供所有使用者的讀取、寫入和執行權限。 您可以視需要將它取代為其他 [chmod 權限](https://en.wikipedia.org/wiki/Chmod)。 

* **儲存體帳戶名稱**：若要掛接 Azure 檔案共用，您需要儲存體帳戶的名稱。

* **儲存體帳戶金鑰**：若要掛接 Azure 檔案共用，您需要主要 (或次要) 金鑰。 掛接目前不支援 SAS 金鑰。

* **請確定已開啟連接埠 445**：SMB 透過 TCP 通訊埠 445 進行通訊 - 請檢查您的防火牆不會將 TCP 通訊埠 445 從用戶端電腦封鎖。

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>使用 `mount` 隨需掛接 Azure 檔案共用
1. **[在您的 Linux 發行版本上安裝 cifs-utils 封裝](#install-cifs-utils)**。

2. **建立掛接點的資料夾**：在檔案系統的任何位置均可完成此作業。

    ```
    mkdir mymountpoint
    ```

3. **使用 mount 命令掛接 Azure 檔案共用**：請記得要使用正確的資訊來取代 `<storage-account-name>`、`<share-name>` 和 `<storage-account-key>`。

    ```
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> ./mymountpoint -o vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> 使用 Azure 檔案共用後，即可使用 `sudo umount ./mymountpoint` 取消掛接共用。

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>使用 `/etc/fstab` 建立 Azure 檔案共用的持續掛接點
1. **[在您的 Linux 發行版本上安裝 cifs-utils 封裝](#install-cifs-utils)**。

2. **建立掛接點的資料夾**：這可以在檔案系統中的任何位置完成，但是您必須記下資料夾的絕對路徑。 下列範例會建立在根目錄下的資料夾。

    ```
    sudo mkdir /mymountpoint
    ```

3. **使用下列命令將下列一行附加至 `/etc/fstab`**：請記得要使用正確的資訊來取代 `<storage-account-name>`、`<share-name>` 和 `<storage-account-key>`。

    ```
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> 您可以使用 `sudo mount -a` 在編輯之後掛接 Azure 檔案共用`/etc/fstab`而不需要重新開機。

## <a name="feedback"></a>意見反應
Linux 使用者，歡迎您提供相關資訊！

Linux 使用者群組的 Azure 檔案儲存體提供論壇，讓您在 Linux 上評估並採用檔案儲存體的同時分享意見。 請寄電子郵件至 [Azure 檔案儲存體 Linux 使用者](mailto:azurefileslinuxusers@microsoft.com) 以加入使用者的群組。

## <a name="next-steps"></a>後續步驟
請參閱這些連結以取得 Azure 檔案儲存體的相關詳細資訊。
* [檔案服務 REST API 參考](http://msdn.microsoft.com/library/azure/dn167006.aspx)
* [如何搭配使用 AzCopy 與 Microsoft Azure 儲存體](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [使用 Azure CLI 搭配 Azure 儲存體](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [常見問題集](../storage-files-faq.md)
* [疑難排解](storage-troubleshoot-linux-file-connection-problems.md)

