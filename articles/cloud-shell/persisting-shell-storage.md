---
title: "在 Azure Cloud Shell (預覽) 中保存檔案 | Microsoft Docs"
description: "逐步解說 Azure Cloud Shell 如何保存檔案。"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 26428ad0d3acda959235ffa780294154ba61bca5
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---

# <a name="persist-files-in-azure-cloud-shell"></a>在 Azure Cloud Shell 中保存檔案
Cloud Shell 會利用 Azure 檔案儲存體在工作階段間保存檔案。

## <a name="set-up-a-clouddrive-file-share"></a>設定 `clouddrive` 檔案共用
在初始啟動時，Cloud Shell 會提示您關聯新的或現有的檔案共用，以在工作階段間保存檔案。

### <a name="create-new-storage"></a>建立新的儲存體

當您使用基本設定並只選取訂用帳戶時，Cloud Shell 會代表您在距離您最近的支援區域中建立三個資源：
* 資源群組：`cloud-shell-storage-<region>`
* 儲存體帳戶：`cs-uniqueGuid`
* 檔案共用：`cs-<user>-<domain>-com-uniqueGuid`

![訂用帳戶設定](media/basic-storage.png)

檔案共用會在您的 `$Home` 目錄中掛接為 `clouddrive`。 檔案共用也會用來儲存為您建立的 5 GB 映像，以便自動更新並保存您的 `$Home` 目錄。 這是一次性的動作，後續的工作階段會自動掛接檔案共用。

### <a name="use-existing-resources"></a>使用現有的資源

您可以使用進階選項來建立與現有資源的關聯。 當儲存體設定提示出現時，請選取 [顯示進階設定] 以檢視其他選項。 現有的檔案共用會收到用來保存 `$Home` 目錄的 5 GB 使用者映像。 下拉式功能表會針對您指派的 Cloud Shell 區域以及本地備援儲存體和異地備援儲存體帳戶進行篩選。

![資源群組設定](media/advanced-storage.png)

### <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>使用 Azure 資源原則限制資源建立
您在 Cloud Shell 中建立的儲存體帳戶都會標記 `ms-resource-usage:azure-cloud-shell`。 如果您想要禁止使用者在 Cloud Shell 中建立儲存體帳戶，請建立這個特定標籤所觸發之[標籤的 Azure 資源原則](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags)。

## <a name="how-cloud-shell-works"></a>Cloud Shell 的運作方式
Cloud Shell 透過下列兩種方法來保存檔案：
* 建立 `$Home` 目錄的磁碟映像，以保存該目錄內的所有內容。 此磁碟映像會在您指定的檔案共用中儲存為 `acc_<User>.img` (位於 `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`)，並自動同步變更。

* 在您的 `$Home` 目錄中，將指定的檔案共用掛接為 `clouddrive`，以便直接與檔案共用互動。 `/Home/<User>/clouddrive` 對應至 `fileshare.storage.windows.net/fileshare`。
 
> [!NOTE]
> `$Home` 目錄中的所有檔案 (例如 SSH 金鑰) 會都保存於已掛接檔案共用中儲存的使用者磁碟映像中。 當您在 `$Home` 目錄和已掛接的檔案共用中保存資訊時，請套用最佳做法。

## <a name="use-the-clouddrive-command"></a>使用 `clouddrive` 命令
Cloud Shell 可讓您執行名為 `clouddrive` 的命令，以手動更新掛接至 Cloud Shell 的檔案共用。
![執行 "clouddrive" 命令](media/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>掛接新的 `clouddrive`

### <a name="prerequisites-for-manual-mounting"></a>手動掛接的先決條件
您可以使用 `clouddrive mount` 命令來更新與 Cloud Shell 關聯的檔案共用。

如果您要掛接現有的檔案共用，儲存體帳戶必須為：
* 可支援檔案共用的本地備援儲存體或異地備援儲存體。
* 位於您的指派區域。 當您要上架時，您的指派區域會列在名為 `cloud-shell-storage-<region>` 的資源群組中。

### <a name="supported-storage-regions"></a>支援的儲存體區域
Azure 檔案必須位於與所掛接之目標 Cloud Shell 電腦相同的區域中。 Cloud Shell 電腦存在於下列區域：
|領域|區域|
|---|---|
|美洲|美國東部、美國中南部、美國西部|
|歐洲|北歐、西歐|
|亞太地區|印度中部、東南亞|

### <a name="the-clouddrive-mount-command"></a>`clouddrive mount` 命令

> [!NOTE]
> 如果您要掛接新的檔案共用，系統會為您的 `$Home` 目錄建立一個新的使用者映像，因為先前的 `$Home` 映像會保留在先前的檔案共用中。

執行 `clouddrive mount` 命令搭配下列參數：

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

若要檢視更多詳細資料，請執行 `clouddrive mount -h`，如下所示：

![執行 `clouddrive mount` 命令](media/mount-h.png)

## <a name="unmount-clouddrive"></a>卸載 `clouddrive`
您可以隨時將掛接至 Cloud Shell 的檔案共用卸載。 不過，由於 Cloud Shell 需要有掛接的檔案共用，因此如果已移除檔案共用，就會在下一個工作階段提示您建立並掛接新的檔案共用。

若要從 Cloud Shell 中移除檔案共用：
1. 執行 `clouddrive unmount`。
2. 了解並確認提示。

除非手動刪除，否則您的檔案共用將會繼續存在。 Cloud Shell 在後續的工作階段中將不再搜尋此檔案共用。

若要檢視更多詳細資料，請執行 `clouddrive unmount -h`，如下所示：

![執行 `clouddrive unmount` 命令](media/unmount-h.png)

> [!WARNING]
> 雖然執行此命令不會刪除任何資源，但手動刪除對應至 Cloud Shell 的資源群組、儲存體帳戶或檔案共用，將會清除您的 `$Home` 目錄磁碟映像及檔案共用中的任何檔案。 此動作無法復原。

## <a name="list-clouddrive-file-shares"></a>列出 `clouddrive` 檔案共用
若要探索已掛接為 `clouddrive` 的檔案共用，請執行下列 `df` 命令。 

clouddrive 檔案路徑會在 URL 中顯示您的儲存體帳戶名稱和檔案共用。 例如， `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

## <a name="transfer-local-files-to-cloud-shell"></a>將本機檔案傳輸至 Cloud Shell
`clouddrive` 目錄會與 Azure 入口網站儲存體刀鋒視窗同步。 使用此刀鋒視窗對檔案共用雙向傳輸本機檔案。 從 Cloud Shell 中更新的檔案，會在您重新整理刀鋒視窗時反映在檔案儲存體 GUI 中。

### <a name="download-files"></a>下載檔案

![本機檔案清單](media/download.png)
1. 在 Azure 入口網站中，移至掛接的檔案共用。
2. 選取目標檔案。
3. 選取 [下載] 按鈕。

### <a name="upload-files"></a>上傳檔案

![要上傳的本機檔案](media/upload.png)
1. 移至掛接的檔案共用。
2. 選取 [上傳] 按鈕。
3. 選取您要上傳的一或多個檔案。
4. 確認上傳。

您現在應會看到 Cloud Shell 的 `clouddrive` 目錄中可存取的檔案。

## <a name="next-steps"></a>後續步驟
[Cloud Shell 快速入門](quickstart.md) <br>
[了解 Azure 檔案儲存體](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[了解儲存體標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>

