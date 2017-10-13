---
title: "Azure 備份 - 離線備份或使用 Azure 匯入/匯出服務進行初始植入 | Microsoft Docs"
description: "了解 Azure 備份如何讓您使用 Azure 匯入/匯出服務在網路上傳送資料。 此文章說明如何使用 Azure 匯入/匯出服務離線植入初始備份資料。"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: ada19c12-3e60-457b-8a6e-cf21b9553b97
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 4/20/2017
ms.author: saurse;nkolli;trinadhk
ms.openlocfilehash: 074d21269206b243f8b0e8747811544132805229
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="offline-backup-workflow-in-azure-backup"></a>在 Azure 備份中離線備份工作流程
Azure 備份有數個可提升效率的內建功能，能在資料初始完整備份至 Azure 的期間節省網路和儲存體成本。 初始完整備份通常會傳輸大量資料，且需要較多網路頻寬，相較之下，後續備份只會傳輸差異/增量部分。 Azure 備份會壓縮初始備份。 透過離線植入程序，Azure 備份可以使用磁碟將壓縮後的初始備份資料離線上傳至 Azure。  

Azure 備份的離線植入程序與 [Azure 匯入/匯出服務](../storage/common/storage-import-export-service.md) 緊密整合，可讓您使用磁碟將資料傳輸到 Azure 之中。 如果您的初始備份資料大小達到 TB，且需要透過高延遲和低頻寬網路傳輸時，您可以使用離線植入工作流程將初始備份複本送至 Azure 資料中心的一個或多個硬碟上。 本文章提供可完成此工作流程的步驟概觀。

## <a name="overview"></a>Overview
透過 Azure 備份的離線植入功能和 Azure 匯入/匯出，可以簡單地使用磁碟將資料離線上傳至 Azure。 您不需透過網路傳輸初始完整複本，備份資料會寫入至 *預備位置*。 使用 Azure 匯入/匯出工具完成複製到預備位置的作業後，此資料就會寫入至一個或多個 SATA 磁碟機 (視資料量而定)。 這些磁碟機最終都會寄送到最近的 Azure 資料中心。

[Azure 備份的 2016 年 8 月更新 (和更新版本)](http://go.microsoft.com/fwlink/?LinkID=229525) 包含名為 AzureOfflineBackupDiskPrep 的 *Azure 磁碟準備工具*︰

* 可協助您使用 Azure 匯入/匯出工具為磁碟機做好執行 Azure 匯入的準備。
* 可在 [Azure 傳統入口網站](https://manage.windowsazure.com) 上自動建立 Azure 匯入/匯出服務的 Azure 匯入作業，而不是像舊版 Azure 備份一樣透過手動方式建立相同的作業。

將備份資料上傳至 Azure 的作業完成之後，Azure 備份就會將備份資料複製至備份保存庫，並排程增量備份。

> [!NOTE]
> 若要使用 Azure 磁碟準備工具，請確定您已安裝 Azure 備份的 2016 年 8 月更新 (或更新版本)，並執行其工作流程的所有步驟。 如果您使用舊版的 Azure 備份，則可以使用本文後面幾節會詳述的 Azure 匯入/匯出工具準備 SATA 磁碟機。
>
>

## <a name="prerequisites"></a>必要條件
* [熟悉 Azure 匯入/匯出工作流程](../storage/common/storage-import-export-service.md)。
* 初始化工作流程之前，請確定下列事項︰
  * 已建立 Azure 備份保存庫。
  * 已下載保存庫認證。
  * 已在 Windows Server/Windows 用戶端或 System Center Data Protection Manager 伺服器上安裝 Azure 備份代理程式，並向 Azure 備份保存庫註冊電腦。
* [下載 Azure 發佈檔案設定](https://manage.windowsazure.com/publishsettings) 。
* 準備可能是網路共用或電腦上其他磁碟機的預備位置。 預備位置是暫時性儲存體，因此在此工作流程期間會暫時使用。 請確定預備位置有足夠的磁碟空間來保存您的初始複本。 例如：若您正在嘗試備份 500 GB 的檔案伺服器，請確定預備區域至少有 500 GB 的空間  (由於壓縮的關係，實際使用量會較少)。
* 確定您使用的是支援的磁碟機。 只有 2.5 英吋的 SSD 或 2.5 英吋或 3.5 英吋的 SATA II/III 內接式硬碟能夠用於匯入/匯出服務。 您可以使用高達 10 TB 的硬碟。 檢查 [Azure 匯入/匯出服務文件](../storage/common/storage-import-export-service.md#hard-disk-drives)以取得服務所支援的最新磁碟機組合。
* 在 SATA 磁碟機寫入器所連接的電腦上啟用 BitLocker。
* [下載 Azure 匯入/匯出工具](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) 至 SATA 磁碟機寫入器所連接的電腦上。 如果您已下載並安裝 Azure 備份的 2016 年 8 月更新 (或更新版本)，就不需要此步驟。

## <a name="workflow"></a>工作流程
本節資訊可協助您完成離線備份工作流程，以便將您的資料傳遞至 Azure 資料中心，並上傳至 Azure 儲存體。 若您有關於匯入服務或處理程序任何層面的問題，請參閱稍早的 [匯入服務概觀](../storage/common/storage-import-export-service.md) 參考文件。

### <a name="initiate-offline-backup"></a>起始離線備份
1. 當您排程備份時，您會看到下列畫面 (在 Windows Server、Windows 用戶端或 System Center Data Protection Manager 中)。

    ![匯入畫面](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    以下是在 System Center Data Protection Manager 的對應畫面︰ <br/>
    ![DPM 匯入畫面](./media/backup-azure-backup-import-export/dpmoffline.png)

    輸入的說明如下：

    * **預備位置**：初始備份所寫入的暫時儲存體位置。 這可能是在網路共用或本機電腦上。 如果複本電腦和來源電腦不同，則建議您指定預備位置的完整網路路徑。
    * **Azure 匯入作業名稱**：Azure 匯入服務和 Azure 備份在追蹤磁碟上傳送至 Azure 之資料的傳輸活動時所使用的唯一名稱。
    * **Azure 發佈設定**：包含訂用帳戶設定檔相關資訊的 XML 檔案。 檔案中也包含與訂用帳戶相關聯的安全認證。 您可以 [下載檔案](https://manage.windowsazure.com/publishsettings)。 提供發佈設定檔案的本機路徑。
    * **Azure 訂用帳戶識別碼**：打算起始 Azure 匯入作業之訂用帳戶的 Azure 訂用帳戶識別碼。 如果您有多個 Azure 訂用帳戶，請使用要與匯入作業相關聯之訂用帳戶的識別碼。
    * **Azure 儲存體帳戶**：所提供的 Azure 訂用帳戶中將會與 Azure 匯入作業相關聯的傳統類型儲存體帳戶。
    * **Azure 儲存體容器**：Azure 儲存體帳戶中匯入此作業資料之目的地儲存體 Blob 的名稱。

    > [!NOTE]
    > 如果您已從 [Azure 入口網站](https://portal.azure.com) 在 Azure 復原服務保存庫中為您的備份註冊伺服器，而且您不是在雲端解決方案提供者 (CSP) 訂用帳戶上，您仍可從 Azure 入口網站中建立傳統類型的儲存體帳戶，然後將它用於離線備份工作流程。
    >
    >

     請將此資訊全部儲存起來，因為您需要在後續步驟中再次輸入此資訊。 如果使用「Azure 磁碟準備工具」來準備磁碟，則只需要「預備位置」  。    
2. 完成工作流程，然後在 Azure 備份管理主控台中選取 [立即備份]  ，以起始離線備份複製。 作為此步驟的一部分，初始備份會寫入預備區域。

    ![立即備份](./media/backup-azure-backup-import-export/backupnow.png)

    若要在 System Center Data Protection Manager 完成對應的工作流程，請以滑鼠右鍵按一下 [保護群組]，然後選擇 [建立復原點] 選項。 您接著要選擇 [線上保護]  選項。

    ![DPM 立即備份](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    作業完成之後，預備位置便已就緒可供用於準備磁碟。

    ![備份進度](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-a-sata-drive-and-create-an-azure-import-job-by-using-the-azure-disk-preparation-tool"></a>使用「Azure 磁碟準備工具」準備 SATA 磁碟機和建立 Azure 匯入作業
「Azure 磁碟準備工具」可在復原服務代理程式 (2016 年 8 月更新和更新版本) 的安裝目錄取得，其路徑如下。

   *\Microsoft* *Azure* *Recovery* *Services* *Agent\Utils\*

1. 前往目錄，並將 **AzureOfflineBackupDiskPrep** 目錄複製到掛接了要準備之磁碟機的複本電腦上。 確認下列與複本電腦有關的事項：

    * 複本電腦可使用在 **起始離線備份** 工作流程中所提供的相同網路路徑，存取離線植入工作流程的預備位置。
    * 電腦上會啟用 BitLocker。
    * 電腦可以存取 Azure 入口網站。

    必要時，複本電腦可以與來源電腦相同。
2. 在複本電腦上以 Azure 磁碟準備工具目錄做為目前的目錄來開啟提高權限的命令提示字元，並執行下列命令：

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to PublishSettingsFile*>]`

    | 參數 | 說明 |
    | --- | --- |
    | s:&lt;*預備位置路徑*&gt; |強制性輸入內容，用來提供在 **起始離線備份** 工作流程中所輸入的預備位置路徑。 |
    | p:&lt;*PublishSettingsFile 的路徑*&gt; |選擇性輸入內容，用來提供在**起始離線備份**工作流程中所輸入的 **Azure 發佈設定**檔案路徑。 |

    > [!NOTE]
    > 複製電腦與來源電腦不同時，&lt;PublishSettingFile 的路徑&gt; 為必要值。
    >
    >

    在執行命令時，此工具會要求選擇需要準備之磁碟機所對應的 Azure 匯入作業。 如果只有一個與提供的預備位置相關聯的匯入作業，您會看到類似下面的畫面。

    ![Azure 磁碟準備工具的輸入](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
3. 輸入想要準備傳輸到 Azure 之掛接磁碟的磁碟機代號 (不含結尾冒號)。 在出現提示時確認您要格式化磁碟機。

    工具接著便會開始以備份資料準備磁碟。 當工具顯示提示時，您可能需要連接額外的磁碟，以免所提供的磁碟沒有足夠空間來容納備份資料。 <br/>

    在工具順利執行結束時，您所提供的一或多個磁碟便已準備好可以寄送到 Azure。 此外，Azure 傳統入口網站上會建立以您在建立 **起始離線備份** 工作流程期間所提供的名稱來命名的匯入作業。 最後，工具上還會顯示磁碟所要寄送到之 Azure 資料中心的寄送地址，以及用來找到 Azure 傳統入口網站上之匯入作業的連結。

    ![Azure 磁碟準備完成](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

4. 將磁碟寄送到工具所提供的地址，並保留追蹤號碼以供日後參考。<br/>

5. 當您前往此工具顯示的連結時，您會看到您在 **起始離線備份** 工作流程中所指定的 Azure 儲存體帳戶。 在下圖中，您可以看到儲存體帳戶的 [匯入/匯出]  索引標籤中新建立的匯入作業。

    ![建立的匯入作業](./media/backup-azure-backup-import-export/ImportJobCreated.png)<br/>

6. 按一下頁面底部的 [寄送資訊]  來更新 [連絡人詳細資料]，如下列畫面所示。 當匯入作業完成時，Microsoft 會使用此資訊將磁碟寄回給您。

    ![連絡資訊](./media/backup-azure-backup-import-export/contactInfoAddition.PNG)<br/>

7. 在下一個畫面中輸入寄送詳細資料。 提供寄送到 Azure 資料中心之磁碟的對應 [貨運業者] 和 [追蹤號碼] 詳細資料。

    ![寄送資訊](./media/backup-azure-backup-import-export/shippingInfoAddition.PNG)<br/>

### <a name="complete-the-workflow"></a>完成工作流程
匯入作業完成後，儲存體帳戶中就會有初始備份資料可供使用。 復原服務代理程式接著會將此帳戶中的資料內容複製到備份保存庫或復原服務保存庫 (視何者較適合)。 到了下一個排程的備份時間時，Azure 備份代理程式會透過初始備份複本執行增量備份。

> [!NOTE]
> 下列各節適用於無法存取「Azure 磁碟準備工具」的舊版 Azure 備份使用者。
>
>

### <a name="prepare-a-sata-drive"></a>準備 SATA 磁碟機
1. 將 [Microsoft Azure 匯入/匯出工具](http://go.microsoft.com/fwlink/?linkid=301900&clcid=0x409) 下載至複本電腦。 請確定預備位置可從您打算執行下一組命令的電腦進行存取。 必要時，複本電腦可以與來源電腦相同。

2. 解壓縮 WAImportExport.zip 檔案。 執行 WAImportExport 工具，以格式化 SATA 磁碟機、將備份資料寫入 SATA 磁碟機並進行加密。 執行下列命令之前請確定電腦上已啟用 BitLocker。 <br/>

    `*.\WAImportExport.exe PrepImport /j:<*JournalFile*>.jrn /id: <*SessionId*> /sk:<*StorageAccountKey*> /BlobType:**PageBlob** /t:<*TargetDriveLetter*> /format /encrypt /srcdir:<*staging location*> /dstdir: <*DestinationBlobVirtualDirectory*>/*`

    > [!NOTE]
    > 如果您已安裝 Azure 備份的 2016 年 8 月更新 (或更新版本)，請確認所輸入的預備位置和 [立即備份] 畫面上的位置相同，且包含 AIB 和 Base Blob 檔案。
    >
    >

| 參數 | 說明 |
| --- | --- |
| /j:<*JournalFile*> |日誌檔案的路徑。 每個磁碟機必須只有一個日誌檔案。 日誌檔案不得位於目標磁碟機上。 日誌檔案的副檔名是 .jrn，且作為執行此命令的一部分而建立。 |
| /id:<*SessionId*> |工作階段識別碼會識別複製工作階段。 其用來確保正確復原中斷的複製工作階段。 在複製工作階段中複製的檔案會儲存在以目標磁碟機上工作階段識別碼命名的目錄。 |
| /sk:<*StorageAccountKey*> |要匯入資料之儲存體帳戶的帳戶金鑰。 金鑰必須相同，因為它是在備份原則/保護群組建立時輸入的。 |
| /BlobType |Blob 的類型。 已指定 **PageBlob** 時，此工作流程才會成功。 這不是預設選項，且應該在此命令中進行描述。 |
| /t:<*TargetDriveLetter*> |目前複製工作階段中目標硬碟的磁碟機代號，不包含結尾的冒號。 |
| /format |用來格式化磁碟機的選項。 當磁碟機需要進行格式化時請指定此參數；否則請省略。 此工具格式化磁碟機之前，會提示您從主控台進行確認。 若要隱藏確認，請指定 /silentmode 參數。 |
| /encrypt |用來加密磁碟機的選項。 當磁碟機尚未使用 BitLocker 加密，且需要透過工具加密時，請指定此參數。 若已使用 BitLocker 加密磁碟機，則省略此參數並指定 /bk 參數，然後提供現有的 BitLocker 金鑰。 若您指定 /format 參數，則也必須指定 /encrypt 參數。 |
| /srcdir:<*SourceDirectory*> |包含檔案的來源目錄會複製至目標磁碟機。 請確定指定的目錄名稱為完整路徑，而非相對路徑。 |
| /dstdir:<*DestinationBlobVirtualDirectory*> |Azure 儲存體帳戶中的目的地虛擬目錄路徑。 指定目的地虛擬目錄或 Blob 時，請確定使用有效的容器名稱。 請記住容器名稱必須是小寫。  此容器名稱應該是備份原則/保護群組建立期間所輸入的名稱。 |

> [!NOTE]
> 日誌檔案會在擷取整個工作流程資訊的 WAImportExport 資料夾中進行建立。 在 Azure 入口網站中建立匯入作業時，您需要此檔案。
>
>

  ![PowerShell 輸出](./media/backup-azure-backup-import-export/psoutput.png)

### <a name="create-an-import-job-in-the-azure-portal"></a>在 Azure 入口網站中建立匯入作業
1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中前往您的儲存體帳戶，按一下 [匯入/匯出]，然後按一下工作窗格中的 [建立匯入作業]。

    ![Azure 入口網站中的匯入/匯出索引標籤](./media/backup-azure-backup-import-export/azureportal.png)

2. 在精靈的步驟 1，指出您已備妥磁碟機並有可用的磁碟機日誌。

3. 在精靈的步驟 2 中，提供負責處理此匯入作業的人員連絡資訊。

4. 在步驟 3 中，上傳在上一個章節中您所取得的磁碟機日誌檔案。

5. 在步驟 4 中，為備份原則/保護群組建立期間所輸入的匯入作業輸入描述性名稱。 您輸入的名稱只能包含小寫字母、數字、連字號和底線，必須以字母開頭，且不得包含空格。 當作業正在進行中和完成之後，將會使用您所選的名稱來追蹤作業。

6. 接著，從清單中選取資料中心區域。 資料中心區域會指出您的包裹必須送達的資料中心和地址。

    ![選取資料中心區域](./media/backup-azure-backup-import-export/dc.png)

7. 在步驟 5 中，從清單中選取您的寄回貨運公司，並輸入貨運公司客戶編號。 在匯入作業完成之後，Microsoft 會透過此廠商將磁碟機寄還給您。

8. 運送磁碟，並輸入追蹤號碼以便追蹤運送狀態。 磁碟到達資料中心後，其會複製到儲存體帳戶並更新狀態。

    ![已完成的狀態](./media/backup-azure-backup-import-export/complete.png)

### <a name="complete-the-workflow"></a>完成工作流程
儲存體帳戶中有初始備份資料可供使用之後，Microsoft Azure 復原服務代理程式就會將此帳戶中的資料內容複製到備份保存庫或復原服務保存庫 (視何者較適合)。 在下一步的排程備份時間，Azure 備份代理程式會透過初始備份複本執行增量備份。

## <a name="next-steps"></a>後續步驟
* 如有任何關於 Azure 匯入/匯出工作流程的問題，請參閱 [使用 Microsoft Azure 匯入/匯出服務將資料傳輸至 Blob 儲存體](../storage/common/storage-import-export-service.md)。
* 若您有關於工作流程的任何問題，請參閱 Azure 備份 [常見問題集](backup-azure-backup-faq.md) 的＜離線備份＞章節。
