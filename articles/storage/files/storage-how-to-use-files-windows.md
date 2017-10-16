---
title: "掛接 Azure 檔案共用並在 Windows 中存取共用 |Microsoft 文件"
description: "掛接 Azure 檔案共用並在 Windows 中存取共用。"
services: storage
documentationcenter: na
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: 111b925de9ca2155e2d3631979272170ed614816
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="mount-an-azure-file-share-and-access-the-share-in-windows"></a>掛接 Azure 檔案共用並在 Windows 中存取共用
[Azure 檔案服務](storage-files-introduction.md)是 Microsoft 易於使用的雲端檔案系統。 Azure 檔案共用可在 Windows 和 Windows Server 中掛接。 本文將說明在 Windows 中掛接 Azure 檔案共用的三種不同方式：使用檔案總管 UI、透過 PowerShell，以及透過命令提示字元。 

若要在 Azure 區域之外掛接 Azure 檔案共用，例如內部部署或是在不同的 Azure 區域，作業系統必須支援 SMB 3.0。 

您可以在於 Azure VM 或內部部署環境中執行的 Windows 安裝上掛接 Azure 檔案共用。 下表說明哪些作業系統版本在哪個環境中支援掛接檔案共用：

| Windows 版本        | SMB 版本 | 可在 Azure VM 中掛接 | 可在內部部署環境掛接 |
|------------------------|-------------|-----------------------|----------------------|
| Windows 10<sup>1</sup>  | SMB 3.0 | 是 | 是 |
| Windows Server 2016    | SMB 3.0     | 是                   | 是                  |
| Windows 8.1            | SMB 3.0     | 是                   | 是                  |
| Windows Server 2012 R2 | SMB 3.0     | 是                   | 是                  |
| Windows Server 2012    | SMB 3.0     | 是                   | 是                  |
| Windows 7              | SMB 2.1     | 是                   | 否                   |
| Windows Server 2008 R2 | SMB 2.1     | 是                   | 否                   |

<sup>1</sup>Windows 10 版本 1507、1511、1607、1703 和 1709。

> [!Note]  
> 我們一律建議針對您的 Windows 版本採取最新的 KB。

## <a name="aprerequisites-for-mounting-azure-file-share-with-windows"></a></a>使用 Windows 掛接 Azure 檔案共用的必要條件 
* **儲存體帳戶名稱**：若要掛接 Azure 檔案共用，您需要儲存體帳戶的名稱。

* **儲存體帳戶金鑰**：若要掛接 Azure 檔案共用，您需要主要 (或次要) 金鑰。 掛接目前不支援 SAS 金鑰。

* **確定已開啟連接埠 445**：Azure 檔案服務會使用 SMB 通訊協定。 SMB 透過 TCP 通訊埠 445 進行通訊 - 請檢查您的防火牆不會將 TCP 通訊埠 445 從用戶端電腦封鎖。

## <a name="mount-the-azure-file-share-with-file-explorer"></a>使用檔案總管掛接 Azure 檔案共用
> [!Note]  
> 請注意，下列指示會顯示在 Windows 10 上，與較舊版本可能稍有不同。 

1. **開啟檔案總管**：作法是從 [開始] 功能表中開啟，或按 Win + E 捷徑。

2. **瀏覽至視窗左側的「本機」項目。這會變更功能區中所提供的功能表。在 [電腦] 功能表中，選取 [連線網路磁碟機]**。
    
    ![「連線網路磁碟機」下拉式功能表的螢幕擷取畫面](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

3. **從 Azure 入口網站中的 [連線] 窗格複製 UNC 路徑**：您可以在[這裡](storage-how-to-use-files-portal.md#connect-to-file-share)找到如何尋找這項資訊的詳細描述。

    ![[Azure 檔案服務連線] 窗格中的 UNC 路徑](./media/storage-how-to-use-files-windows/portal_netuse_connect.png)

4. **選取磁碟機代號並輸入 UNC 路徑。** 
    
    ![「連線網路磁碟機」對話方塊的螢幕擷取畫面](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

5. **使用前面加上 `Azure\` 的儲存體帳戶名稱作為使用者名稱，並使用儲存體帳戶金鑰作為密碼。**
    
    ![網路認證對話方塊的螢幕擷取畫面](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

6. **視需要使用 Azure 檔案共用**。
    
    ![現在已掛接 Azure 檔案共用](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

7. **當您準備好要卸載 (或中斷連線) Azure 檔案共用時，您可以以滑鼠右鍵按一下 [檔案總管] 中「網路位置」下的共用項目，然後選取 [中斷連線]**。

## <a name="mount-the-azure-file-share-with-powershell"></a>使用 PowerShell 掛接 Azure 檔案共用
1. **使用下列命令來掛接 Azure 檔案共用**：請記得要使用正確的資訊來取代 `<storage-account-name>`、`<share-name>`、`<storage-account-key>` 以及 `<desired-drive-letter>`。

    ```PowerShell
    $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
    New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\<share-name>" -Credential $credential
    ```

2. **視需要使用 Azure 檔案共用**。

3. **當您完成時，使用下列命令卸載 Azure 檔案共用**。

    ```PowerShell
    Remove-PSDrive -Name <desired-drive-letter>
    ```

> [!Note]  
> 當仍掛接時，您可以在 `New-PSDrive`上使用 `-Persist` 參數對其餘的 OS 顯示 Azure 檔案共用。

## <a name="mount-the-azure-file-share-with-command-prompt"></a>使用命令提示字元掛接 Azure 檔案共用
1. **使用下列命令來掛接 Azure 檔案共用**：請記得要使用正確的資訊來取代 `<storage-account-name>`、`<share-name>`、`<storage-account-key>` 以及 `<desired-drive-letter>`。

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. **視需要使用 Azure 檔案共用**。

3. **當您完成時，使用下列命令卸載 Azure 檔案共用**。

    ```
    net use <desired-drive-letter>: /delete
    ```

> [!Note]  
> 您可以藉由保存 Windows 的認證，設定在重新開機時自動重新連線 Azure 檔案共用。 下列命令將會保存認證：
>   ```
>   cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>
>   ```

## <a name="next-steps"></a>後續步驟
請參閱這些連結，以取得 Azure 檔案服務的詳細資訊。

* [常見問題集](../storage-files-faq.md)
* [在 Windows 上進行疑難排解](storage-troubleshoot-windows-file-connection-problems.md)      

### <a name="conceptual-articles-and-videos"></a>概念性文章和影片
* [Azure 檔案服務：相容於 Windows 和 Linux 的雲端 SMB 檔案系統](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [如何搭配使用 Azure 檔案服務與 Linux](../storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-azure-files"></a>Azure 檔案服務的工具支援
* [如何搭配使用 AzCopy 與 Microsoft Azure 儲存體](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [使用 Azure CLI 搭配 Azure 儲存體](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [針對 Azure 檔案服務問題進行疑難排解 - Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [針對 Azure 檔案服務問題進行疑難排解 - Linux](storage-troubleshoot-linux-file-connection-problems.md)

### <a name="blog-posts"></a>部落格文章
* [Azure 檔案服務現在已正式推出](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Azure 檔案服務的內部觀點](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Microsoft Azure 檔案服務簡介](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [將資料移轉至 Azure 檔案](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>參考
* [Storage Client Library for .NET 參考資料](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [檔案服務 REST API 參考](http://msdn.microsoft.com/library/azure/dn167006.aspx)
