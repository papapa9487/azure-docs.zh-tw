---
title: "如何從 Azure 入口網站管理 Azure 檔案服務 | Microsoft Docs"
description: "了解使用 Azure 入口網站來管理 Azure 檔案服務。"
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
ms.openlocfilehash: e56f8bf1057a8bc2cfcde841f69022104bafff27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-azure-files-from-the-azure-portal"></a>如何從 Azure 入口網站使用 Azure 檔案服務
[Azure 入口網站](https://portal.azure.com)提供可管理 Azure 檔案服務的使用者介面。 您可以從網路瀏覽器執行下列動作：

* 建立檔案共用
* 上傳檔案至檔案共用以及從檔案共用下載檔案。
* 監視每個檔案共用的實際使用狀況。
* 調整檔案室共用大小配額。
* 複製並使用掛接命令以從 Windows 或 Linux 用戶端掛接檔案共用。

## <a name="create-file-share"></a>建立檔案共用
1. 登入 Azure 入口網站。
2. 在導覽功能表中，按一下 [儲存體帳戶] 或 [儲存體帳戶 (傳統)]。
    
    ![示範如何在入口網站中建立檔案共用的螢幕擷取畫面](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share1.png)

3. 選擇儲存體帳戶

    ![示範如何在入口網站中建立檔案共用的螢幕擷取畫面](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share2.png)

4. 選擇「檔案」服務。

    ![示範如何在入口網站中建立檔案共用的螢幕擷取畫面](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share3.png)

5. 按一下 [檔案共用] 並依連結建立您的第一個檔案共用。

    ![示範如何在入口網站中建立檔案共用的螢幕擷取畫面](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share4.png)

6. 填寫檔案共用名稱和檔案共用大小 (最多 5120 GB)，以建立第一個檔案共用。 建立檔案共用之後，您可以從任何支援 SMB 2.1 或 SMB 3.0 的檔案系統掛接此共用。 您可以按一下檔案共用上的 [配額]  以變更檔案大小 (最多 5120 GB)。 請參閱 [Azure 價格計算機](https://azure.microsoft.com/pricing/calculator/)來估計使用 Azure 檔案服務的儲存體成本。

    ![示範如何在入口網站中建立檔案共用的螢幕擷取畫面](./media/storage-how-to-use-files-portal/use-files-portal-create-file-share5.png)

## <a name="upload-and-download-files"></a>上傳及下載檔案
1. 選擇一個您已建立的檔案共用。

    ![示範如何從入口網站上傳和下載檔案](./media/storage-how-to-use-files-portal/use-files-portal-upload-file1.png)

2. 按一下 [上傳]  以開啟使用者介面以進行檔案上傳。

    ![示範如何從入口網站上傳檔案](./media/storage-how-to-use-files-portal/use-files-portal-upload-file2.png)

## <a name="connect-to-file-share"></a>連線至檔案共用
-  按一下 [連線] 以取得從 Windows 和 Linux 掛接檔案共用的命令列。 針對 Linux 使用者，您也可以參閱[如何搭配使用 Azure 檔案服務與 Linux](../storage-how-to-use-files-linux.md)以取得更多 Linux 散發套件的掛接指示。

    ![示範如何掛接檔案共用的螢幕擷取畫面](./media/storage-how-to-use-files-portal/use-files-portal-connect.png)
-  您可以在 Windows 或 Linux 上複製掛接檔案共用的命令，並從您的 Azure VM 或內部部署機器執行。

    ![顯示適用於 Windows 和 Linux 掛接命令的螢幕擷取畫面](./media/storage-how-to-use-files-portal/use-files-portal-show-mount-commands.png)

**秘訣：**  
要尋找掛接的儲存體帳戶存取金鑰，按一下連線頁面底部的 [檢視此儲存體帳戶的存取金鑰]。

## <a name="see-also"></a>另請參閱
請參閱這些連結，以取得 Azure 檔案服務的詳細資訊。

* [常見問題集](../storage-files-faq.md)
* [在 Windows 上進行疑難排解](storage-troubleshoot-windows-file-connection-problems.md)      
* [在 Linux 上進行疑難排解](storage-troubleshoot-linux-file-connection-problems.md)    
