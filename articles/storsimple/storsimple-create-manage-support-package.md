---
title: "建立 StorSimple 支援封裝 | Microsoft Docs"
description: "了解如何建立、解密和編輯 StorSimple 裝置的支援封裝。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: eac76f5f-5db1-4c92-af8c-54053b91e66c
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: addfb01998271ee3a431d92bf2a6fec70f0577a6
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2017
---
# <a name="create-and-manage-a-storsimple-support-package"></a>建立及管理 StorSimple 支援封裝
> [!NOTE]
> StorSimple 的傳統入口網站已過時。 按照淘汰排程，StorSimple 裝置管理員會自動移至新的 Azure 入口網站。 您將收到關於此移動的電子郵件和入口網站通知。 本文件也很快就會淘汰。 若要檢視適用於新 Azure 入口網站的文章版本，請移至[建立及管理 StorSimple 支援封裝](storsimple-8000-create-manage-support-package.md)。 若有關於移動的任何問題，請參閱[常見問題集：移至 Azure 入口網站](storsimple-8000-move-azure-portal-faq.md)。

## <a name="overview"></a>概觀
StorSimple 支援封裝是一種簡便的機制，可收集所有相關的記錄以協助 Microsoft 支援服務針對任何 StorSimple 裝置問題進行疑難排解。 收集的記錄都會經過加密並壓縮。

本教學課程包含建立和管理支援封裝的逐步指示：

## <a name="create-and-upload-a-support-package-in-the-azure-classic-portal"></a>在 Azure 傳統入口網站中建立與上傳支援封裝
您可以在 Azure 傳統入口網站中透過此服務的 [維護]  頁面，建立支援封裝並上傳至 Microsoft 支援服務網站。

> [!NOTE]
> 上傳需要支援密碼。 支援工程師應在電子郵件中提供此密碼給您。
> 
> 

建立加密和壓縮的支援封裝 (.cab 檔案) 並上傳至「支援服務」網站。 支援工程師就能從支援服務網站擷取此封裝以針對此問題進行疑難排解。

在 Azure 傳統入口網站中執行下列步驟，以建立支援封裝。

#### <a name="to-create-a-support-package-in-the-azure-classic-portal"></a>在 Azure 傳統入口網站中建立支援封裝
1. 選取 [裝置] > [維護]。
2. 在 [支援封裝] 區段中，選取 [建立及上傳支援封裝]。
3. 在 [ **建立及上傳支援封裝** ] 對話方塊中，執行下列動作：
   
    ![建立支援封裝](./media/storsimple-create-manage-support-package/IC740923.png)
   
   * 在 [支援密碼]  文字方塊中，輸入密碼。 Microsoft 支援工程師應在電子郵件中將此密碼傳送給您。
   * 選取此核取方塊以同意自動上傳支援封裝至 Microsoft 支援服務網站。
   * 按一下核取圖示  ![核取圖示](./media/storsimple-create-manage-support-package/IC740895.png)。

## <a name="manually-create-a-support-package"></a>手動建立支援封裝
在某些情況下，您必須透過 Windows PowerShell for StorSimple 手動建立支援封裝。 例如：

* 如果您在與 Microsoft 支援服務共用您的記錄檔之前，必須從其中移除機密資訊。
* 如果您在上傳封裝時，因為連線能力問題而遇到困難。

您可以透過電子郵件與 Microsoft 支援服務共用手動產生的支援封裝。 執行下列步驟在 Windows PowerShell for StorSimple 中建立支援封裝。

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>在 Windows PowerShell for StorSimple 中建立支援封裝
1. 若要在用來連接至 StorSimple 裝置的遠端電腦上，以系統管理員身分啟動 Windows PowerShell 工作階段，請輸入下列命令：
   
    `Start PowerShell`
2. 在 Windows PowerShell 工作階段中，連接到裝置的 SSAdmin 主控台：
   
   * 在命令提示字元中，輸入：
     
       `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`
   * 在開啟的對話方塊中，輸入您的裝置系統管理員密碼。 預設密碼為：
     
      `Password1`
     
      ![[PowerShell 認證] 對話方塊](./media/storsimple-create-manage-support-package/IC740962.png)
   * 選取 [確定] 。
   * 在命令提示字元中，輸入：
     
      `Enter-PSSession $MS`
3. 在開啟的工作階段中，輸入適當的命令。
   
   * 對於受密碼保護的網路共用，請輸入：
     
       `Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`
     
       系統會提示您輸入密碼、網路共用的資料夾路徑及加密複雜密碼 (因為支援封裝已加密)。 然後會在指定的資料夾中建立支援封裝。
   * 對於不受密碼保護的共用內容，您不需要 `-Credential` 參數。 輸入以下資訊：
     
       `Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`
     
       在指定的網路共用資料夾中，同時為兩個控制器建立支援封裝。 這是加密的壓縮檔案，可傳送給 Microsoft 支援服務進行疑難排解。 如需詳細資訊，請參閱 [連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)。

### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>The Export-HcsSupportPackage cmdlet 參數
您可以使用下列參數搭配 Export-HcsSupportPackage cmdlet。

| 參數 | 必要/選用 | 說明 |
| --- | --- | --- |
| `-Path` |必要 |用來提供存放支援封裝的網路共用資料夾位置。 |
| `-EncryptionPassphrase` |必要 |用來提供複雜密碼，以協助加密支援封裝。 |
| `-Credential` |選用 |用來提供網路共用資料夾的存取認證。 |
| `-Force` |選用 |用來略過加密複雜密碼確認步驟。 |
| `-PackageTag` |選用 |用來指定 *Path* 下存放支援封裝的目錄。 預設值是 [裝置名稱]-[目前日期和時間：yyyy-MM-dd-HH-mm-ss]。 |
| `-Scope` |選用 |指定為 **Cluster** (預設值) 可以為兩個控制器建立支援封裝。 如果您只想針對目前的控制器建立封裝，請指定 **Controller**。 |

## <a name="edit-a-support-package"></a>編輯支援封裝
在您產生支援封裝之後，可能需要編輯封裝以移除機密資訊。 這可能包括磁碟區名稱、裝置 IP 位址，以及記錄檔中的備份名稱。

> [!IMPORTANT]
> 您只能編輯透過 Windows PowerShell for StorSimple 產生的支援封裝。 您無法編輯在 Azure 傳統入口網站中以 StorSimple Manager 服務建立的封裝。
> 
> 

在 Microsoft 支援網站上傳支援封裝之前，若要編輯支援封裝，請先解密支援封裝、編輯檔案，然後重新加密。 請執行下列步驟：

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>在 Windows PowerShell for StorSimple 中編輯支援封裝
1. 產生支援封裝，如稍早的 [在 Windows PowerShell for StorSimple 中建立支援封裝](#to-create-a-support-package-in-windows-powershell-for-storsimple)中所述。
2. [下載指令碼](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) 。
3. 匯入 Windows PowerShell 模組。 指定下載指令碼時的本機資料夾的路徑。 若要匯入模組，請輸入：
   
    `Import-module <Path to the folder that contains the Windows PowerShell script>`
4. 所有檔案都是已壓縮和加密的 *.aes* 檔案。 若要解壓縮並解密檔案，請輸入︰
   
    `Open-HcsSupportPackage <Path to the folder that contains support package files>`
   
    請注意，所有檔案現在顯示實際的副檔名。
   
    ![編輯支援封裝](./media/storsimple-create-manage-support-package/IC750706.png)
5. 當系統提示您輸入加密複雜密碼時，請輸入您在建立支援封裝時使用的複雜密碼。
   
        cmdlet Open-HcsSupportPackage at command pipeline position 1
   
        Supply values for the following parameters:EncryptionPassphrase: ****
6. 瀏覽至包含記錄檔的資料夾。 因為記錄檔現在已解壓縮並解密，所以會顯示原始的副檔名。 修改這些檔案來移除客戶特定資訊，例如磁碟區名稱和裝置 IP 位址，然後儲存檔案。
7. 關閉檔案會使用 Gzip 壓縮檔案，並使用 AES-256 加密。 這是為了透過網路傳輸支援封裝時提高速度和安全性。 若要壓縮及加密檔案，請輸入下列內容︰
   
    `Close-HcsSupportPackage <Path to the folder that contains support package files>`
   
    ![編輯支援封裝](./media/storsimple-create-manage-support-package/IC750707.png)
8. 出現提示時，提供加密複雜密碼給修改過的支援封裝。
   
        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****
9. 記下新的複雜密碼，當接到要求時就能提供給 Microsoft 支援服務。

### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>範例：在受密碼保護的共用中編輯支援封裝中的檔案
下列範例將示範如何解密、編輯和重新加密支援封裝。

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>後續步驟
* 了解如何 [使用支援封裝和裝置記錄對裝置部署進行疑難排解](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting)。
* 了解如何 [使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-manager-service-administration.md)。

