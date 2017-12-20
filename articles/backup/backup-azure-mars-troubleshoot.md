---
title: "針對 Azure 備份代理程式 (MARS 代理程式) 進行疑難排解 | Microsoft Docs"
description: "針對 Azure 備份代理程式的安裝和註冊進行疑難排解"
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shreeshd
editor: 
ms.assetid: 778c6ccf-3e57-4103-a022-367cc60c411a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/4/2017
ms.author: saurse;markgal;
ms.openlocfilehash: da297177ca56822f7d50dbe06ec022640bf5d0cb
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2017
---
# <a name="troubleshooting-azure-backup-agent-configurationregistration-issues"></a>針對 Azure 備份代理程式設定/註冊問題進行疑難排解
## <a name="recommended-steps"></a>建議的步驟
請參閱以下指定的建議動作，以解決在 Azure 備份代理程式的設定或註冊期間會發生的相對應錯誤。

## <a name="error-invalid-vault-credentials-provided-the-file-is-either-corrupted-or-does-not-have-the-latest-credentials-associated-with-recovery-service"></a>錯誤：提供的保存庫認證無效。 檔案已損毀或沒有與復原服務關聯的最新認證。
| 錯誤詳細資料 | 可能的原因 | 建議的動作 |
| ---     | ---     | ---    |
| **錯誤** </br> *提供的保存庫認證無效。檔案已損毀或沒有與復原服務關聯的最新認證。(識別碼：34513)* | <ol><li> 保存庫認證無效 (也就是， 在將它們下載超過 48 小時之後才註冊)。<li>   Azure 備份代理程式無法將暫存檔案下載到 Windows 的 Temp 資料夾。 <li>保存庫認證位於網路位置上。 <li>TLS 1.0 已停用<li> 已設定的 Proxy 伺服器正在封鎖連線 <br> |  <ol><li>下載新的保存庫認證<li>移至 [網際網路選項] > [安全性] > [網際網路] > 按一下 [自訂等級] > 捲動直到您看到 [檔案下載] 區段為止，然後選取 [啟用]。<li>您也必須將該網站新增到[信任的網站](https://docs.microsoft.com/en-us/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)。<li>變更設定以使用 Proxy，並提供 Proxy 詳細資料 <li> 使日期和時間 (UTC) 與您的電腦相符<li>移至 C:/Windows/Temp，並檢查是否有超過 60,000 或 65,000 個 (具有 .tmp 副檔名的) 檔案，並刪除這些檔案。<li>您可以在伺服器上執行 SDP 套件來進行測試。 如果您收到的顯示不允許檔案下載的錯誤，就能合理地確定與 C:/Windows/Temp 目錄中的大量檔案有關。<li>確定您已安裝 .NET Framework 4.6.2。 <li>如果您因 PCI 合規性而停用 TLS 1.0，請參閱這個[疑難排解連結](https://support.microsoft.com/help/4022913)。 <li>如果您已在伺服器中安裝防毒程式，請從防毒程式掃描中排除下列檔案。 <ol><li>CBengine.exe<li>CSC.exe (與 .NET Framework 相關。 伺服器上所安裝的每個 .NET 版本都有一個 CSC.exe。 請排除受影響伺服器上與所有 .NET Framework 版本相關聯的所有 CSC.exe 檔案。) <li>臨時資料夾或快取位置。 <br>*臨時資料夾或快取位置路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*

## <a name="error-the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>錯誤：Microsoft Azure 復原服務代理程式無法連線至 Microsoft Azure 備份

| 錯誤詳細資料 | 可能的原因 | 建議的動作 |
| ---     | ---     | ---    |
| **錯誤** </br><ol><li>*Microsoft Azure 復原服務代理程式無法連線至 Microsoft Azure 備份。(識別碼：100050) 請檢查您的網路設定，並確保您能夠連線至網際網路*<li>*(407) 需要 Proxy 驗證* |Proxy 正在封鎖連線* |  <ol><li>移至 [網際網路選項] > [安全性] > [網際網路] > 按一下 [自訂等級] > 捲動直到您看到 [檔案下載] 區段為止，然後選取 [啟用]。<li>您也必須將該網站新增到[信任的網站](https://docs.microsoft.com/en-us/azure/backup/backup-try-azure-backup-in-10-mins#network-and-connectivity-requirements)。<li>變更設定以使用 Proxy，並提供 Proxy 詳細資料 <li>如果您已在伺服器中安裝防毒程式，請從防毒程式掃描中排除下列檔案。 <ol><li>CBengine.exe<li>(而不是 dpmra.exe)<li>CSC.exe (與 .NET Framework 相關。 伺服器上所安裝的每個 .NET 版本都有一個 CSC.exe。 請排除受影響伺服器上與所有 .NET Framework 版本相關聯的所有 CSC.exe 檔案。) <li>臨時資料夾或快取位置 <br>*臨時資料夾或快取位置路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*

## <a name="error-failed-to-set-the-encryption-key-for-secure-backups"></a>錯誤：無法設定安全備份的加密金鑰

| 錯誤詳細資料 | 可能的原因 | 建議的動作 |
| ---     | ---     | ---    |      
| **錯誤** </br>*無法設定安全備份的加密金鑰。由於發生內部服務錯誤「無效輸入錯誤」，導致目前的操作失敗。請在一段時間之後重試此操作。如果問題持續發生，請連絡 Microsoft 支援服務* |伺服器已經向另一個保存庫註冊| 從該保存庫中將伺服器取消註冊，然後重新註冊。

## <a name="error-the-activation-did-not-complete-successfully-the-current-operation-failed-due-to-an-internal-service-error-0x1fc07"></a>錯誤：啟動沒有成功完成。 由於發生內部服務錯誤 [0x1FC07]，導致目前的操作失敗

| 錯誤詳細資料 | 可能的原因 | 建議的動作 |
| ---     | ---     | ---    |          
| **錯誤** </br><ol><li>*啟動沒有成功完成。由於發生內部服務錯誤 [0x1FC07]，導致目前的操作失敗。請在一段時間之後重試此操作。如果問題持續發生，請連絡 Microsoft 支援服務* <li>*錯誤 34506。未正確設定儲存於此電腦上的加密複雜密碼* | <li> 臨時資料夾所在的磁碟區沒有足夠的空間 <li> 未正確地將臨時資料夾移至其他位置 <li> 遺漏 OnlineBackup.KEK 檔案 | <li>將臨時資料夾或快取位置移至具有相當於備份資料總大小之 5-10% 可用空間的磁碟區。 請參閱[本文](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup)中所述的步驟，以正確地移動快取位置。<li> 確定 OnlineBackup.KEK 檔案存在 <br>*臨時資料夾或快取位置路徑的預設位置是 C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員
如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

## <a name="next-steps"></a>後續步驟
* 在[使用 Azure 備份代理程式備份您的 Windows 伺服器](tutorial-backup-windows-server-to-azure.md)上取得更多詳細資料。
* 如果您需要還原備份，請使用本文來 [還原檔案到 Windows 電腦](backup-azure-restore-windows-server.md)。
