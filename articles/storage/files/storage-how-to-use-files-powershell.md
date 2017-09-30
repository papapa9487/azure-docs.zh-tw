---
title: "如何使用 PowerShell 來管理 Azure 檔案服務 | Microsoft Docs"
description: "了解如何使用 PowerShell 來管理 Azure 檔案服務。"
services: storage
documentationcenter: 
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: f919e1880f709b416867a29de14f1dcc63a165fe
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="how-to-use-powershell-to-manage-azure-files"></a>如何使用 PowerShell 來管理 Azure 檔案服務
您可以使用 Azure PowerShell 建立及管理檔案共用。

## <a name="install-the-powershell-cmdlets-for-azure-storage"></a>安裝適用於 Azure 儲存體的 PowerShell Cmdlet
若要準備使用 PowerShell，請下載並安裝 Azure PowerShell Cmdlet。 如需安裝點和安裝指示的詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azureps-cmdlets-docs) 。

> [!NOTE]
> 建議您下載和安裝或升級至最新的 Azure PowerShell 模組。
> 
> 

透過按一下 [啟動]，然後輸入 **Windows PowerShell** 來開啟 Azure PowerShell 視窗。 PowerShell 視窗便會為您載入 Azure Powershell 模組。

## <a name="create-a-context-for-your-storage-account-and-key"></a>建立儲存體帳戶和金鑰的內容
建立儲存體帳戶內容。 內容包含儲存體帳戶名稱和帳戶金鑰。 如需從 [Azure 入口網站](https://portal.azure.com)複製帳戶金鑰的指示，請參閱[檢視和複製儲存體存取金鑰](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#view-and-copy-storage-access-keys)。

使用下列範例中的儲存體帳戶名稱和金鑰來取代 `storage-account-name` 和 `storage-account-key`。

```powershell
# create a context for account and key
$ctx=New-AzureStorageContext storage-account-name storage-account-key
```

## <a name="create-a-new-file-share"></a>建立新的檔案共用
建立名為 `logs` 的新共用。

```powershell
# create a new share
$s = New-AzureStorageShare logs -Context $ctx
```

現在，您的檔案儲存體中便有一個檔案共用。 接下來，我們將新增目錄和檔案。

> [!IMPORTANT]
> 您的檔案共用名稱必須是全部小寫。 如需有關為檔案共用與檔案命名的完整詳細資料，請參閱 [命名和參考共用、目錄、檔案及中繼資料](https://msdn.microsoft.com/library/azure/dn167011.aspx)。
> 
> 

## <a name="create-a-directory-in-the-file-share"></a>在檔案共用中建立目錄
在共用中建立目錄。 在下列範例中，目錄的名稱為 `CustomLogs`。

```powershell
# create a directory in the share
New-AzureStorageDirectory -Share $s -Path CustomLogs
```

## <a name="upload-a-local-file-to-the-directory"></a>上傳本機檔案至目錄
現在，請上傳本機檔案至目錄。 下列範例會從 `C:\temp\Log1.txt` 上傳檔案。 編輯檔案路徑，以指向本機機器上的有效檔案。

```powershell
# upload a local file to the new directory
Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs
```

## <a name="list-the-files-in-the-directory"></a>列出目錄中的檔案
若要查看目錄中的檔案，您可以列出目錄的所有檔案。 此命令會傳回 CustomLogs 目錄中的檔案和子目錄 (如果有的話)。

```powershell
# list files in the new directory
Get-AzureStorageFile -Share $s -Path CustomLogs | Get-AzureStorageFile
```

Get-AzureStorageFile 會傳回已傳入任何目錄物件的檔案和目錄清單。 "Get-AzureStorageFile -Share $s" 會傳回根目錄中的檔案和目錄清單。 若要取得子目錄中的檔案清單，您必須將子目錄傳遞至 Get-AzureStorageFile。 這就是其作用 -- 命令的第一個部分 (由管道決定) 會傳回 CustomLogs 子目錄的目錄執行個體。 然後該執行個體會傳入 Get-AzureStorageFile，進而傳回 CustomLogs 中的檔案和目錄。

## <a name="copy-files"></a>複製檔案
從 Azure PowerShell 0.9.7 版開始，您可以將檔案複製到另一個檔案、將檔案複製到 Blob 或將 Blob 複製到檔案。 下列示範如何使用 PowerShell Cmdlet 執行這些複製作業。

```powershell
# copy a file to the new directory
Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx

# copy a blob to a file directory
Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx
```
## <a name="next-steps"></a>後續步驟
請參閱這些連結，以取得 Azure 檔案服務的相關詳細資訊。

* [常見問題集](../storage-files-faq.md)
* [在 Windows 上進行疑難排解](storage-troubleshoot-windows-file-connection-problems.md)      
* [在 Linux 上進行疑難排解](storage-troubleshoot-linux-file-connection-problems.md)    
