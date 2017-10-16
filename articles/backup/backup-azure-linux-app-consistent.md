---
title: "Azure 備份：Linux VM 的應用程式一致備份 | Microsoft Docs"
description: "使用指令碼針對 Linux 虛擬機器保證應用程式會一致地備份到 Azure。 指令碼僅適用於資源管理員部署中的 Linux VM，不適用於 Windows VM 或服務管理員部署。 這篇文章會帶領您完成設定指令碼的步驟，包括疑難排解。"
services: backup
documentationcenter: dev-center-name
author: anuragmehrotra
manager: shivamg
keywords: "應用程式一致備份; 應用程式一致 Azure VM 備份; Linux VM 備份; Azure 備份"
ms.assetid: bbb99cf2-d8c7-4b3d-8b29-eadc0fed3bef
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 4/12/2017
ms.author: anuragm;markgal
ms.openlocfilehash: 378c65bec8fd1f880ed459e76f5e4b5d85e49d2a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="application-consistent-backup-of-azure-linux-vms-preview"></a>Azure Linux VM 的應用程式一致備份 (預覽)

本文討論 Linux 前置指令碼和後置指令碼架構，以及如何使用它來取得 Azure Linux VM 的應用程式一致備份。

> [!Note]
> 僅 Azure Resource Manager 部署的 Linux 虛擬機器支援前置指令碼和後置指令碼架構。 Service Manager 所部署虛擬機器或 Windows 虛擬機器不支援應用程式一致指令碼。
>

## <a name="how-the-framework-works"></a>架構的運作方式

此架構讓您在擷取 VM 快照集時可選擇執行自訂前置指令碼和後置指令碼。 前置指令碼就在您擷取 VM 快照之前執行，而後置指令碼會緊接在您擷取 VM 快照後執行。 這讓您在擷取 VM 快照時能夠彈性地控制您的應用程式與環境。

在此案例中，務必確保應用程式一致的 VM 備份。 前置指令碼會叫用應用程式原生 API 以停止 IO，並將記憶體中的內容排清到磁碟。 這可確保快照為應用程式一致 (亦即，應用程式會在 VM 於還原後開機時出現)。 後置指令碼可以用來解除凍結 IO。 做法是使用應用程式原生 API，讓應用程式可在 VM 快照集後繼續正常作業。

## <a name="steps-to-configure-pre-script-and-post-script"></a>設定前指令碼和後置指令碼的步驟

1. 以根使用者身分登入您想要備份的 Linux VM。

2. 從 [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) 下載 VMSnapshotScriptPluginConfig.json，然後複製到所有您要備份的 VM 上的 /etc/azure 資料夾。 建立 **/etc/azure** 目錄 (如果不存在的話)。

3. 在所有您打算備份的 VM 上複製應用程式複製的前置指令碼與後置指令碼。 您可以將指令碼複製到 VM 上的任何位置。 請務必更新 **VMSnapshotScriptPluginConfig.json** 檔案中指令碼檔案的完整路徑。

4. 請確定這些檔案的下列權限︰

   - **VMSnapshotScriptPluginConfig.json**：權限 “600”。 例如，只有「根」使用者能夠擁有此檔案的「讀取」與「寫入」權限，且沒有任何使用者應擁有「執行」權限。

   - **前置指令碼檔案**：權限 “700”。  例如，只有「根」使用者擁有此檔案的「讀取」、「寫入」及「執行」權限。
  
   - **後置指令碼**：權限 “700”。 例如，只有「根」使用者擁有此檔案的「讀取」、「寫入」及「執行」權限。

   > [!Important]
   > 此架構讓使用者擁有強大的能力。 此架構必須是安全的，且只有「根」使用者能夠存取重要的 JSON 與指令碼檔案。
   > 如果不符合前述需求，指令碼便不會執行。 這能產生檔案系統/損毀一致的備份。
   >

5. 如以下所述設定 VMSnapshotScriptPluginConfig.json：
    - **pluginName**：將此欄位保留原狀，否則您的指令碼可能無法如預期般運作。

    - **preScriptLocation** ：在要備份的 VM 上提供前置指令碼完整路徑。

    - **postScriptLocation**：在要備份的 VM 上提供後置指令碼完整路徑。

    - **preScriptParams**：提供必須傳遞給前置指令碼的選擇性參數。 所有參數都應該以引號括住，如果有多個參數，則應以逗號分隔。

    - **postScriptParams**：提供必須傳遞給前置指令碼的選擇性參數。 所有參數都應該以引號括住，如果有多個參數，則應以逗號分隔。

    - **preScriptNoOfRetries**：設定在終止前如果發生任何錯誤，應重試前置指令碼的次數。 零表示只嘗試一次，若系統故障則不重試。

    - **postScriptNoOfRetries**：設定在終止前如果發生任何錯誤，應重試後置指令碼的次數。 零表示只嘗試一次，若系統故障則不重試。
    
    - **timeoutInSeconds**：指定前置指令碼與後置指令碼的個別逾時值。

    - **continueBackupOnFailure**：如果您在前置或後置指令碼失敗時想要 Azure 備份回復使用檔案系統一致/損毀一致備份，請將這個值設定為 true。 若將此設定為 false，則當指令碼失敗會使備份失敗 (但當您有無論此設定為何都會回復為損毁一致備份的單磁碟 VM 時除外)。

    - **fsFreezeEnabled**：指定當您在擷取 VM 快照時是否應該呼叫 Linux fsfreeze，以確保檔案系統一致性。 我們建議您將這個設定保持為 true，除非您的應用程式在已停用的 fsfreeze 上具有相依性。

6. 此指令碼架構現在已設定完成。 如果已經設定 VM 備份，則下次備份會叫用指令碼，並觸發應用程式一致備份。 如果未設定 VM 備份，則使用[將 Azure 虛擬機器備份到復原服務保存庫](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)來設定。

## <a name="troubleshooting"></a>疑難排解

確定您在寫入前置指令碼和後置指令碼時新增適當的記錄，並檢閱您的指令碼記錄以修正任何指令碼的問題。 如果仍有執行指令碼的問題，請參閱下列表格以取得詳細資訊。

| 錯誤 | 錯誤訊息 | 建議的動作 |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |前置指令碼傳回錯誤，所以備份可能無法應用程式一致。   | 查看您指令碼的失敗記錄來修正此問題。|  
|   Post-ScriptExecutionFailed |    後置指令碼傳回可能會影響應用程式狀態的錯誤。 |    查看您指令碼的失敗記錄並檢查應用程式狀態來修正此問題。 |
| Pre-ScriptNotFound |  在 VMSnapshotScriptPluginConfig.json 組態檔中指定的位置找不到前置指令碼。 |   確定前置指令碼會出現在組態檔中所指定的路徑，以確保應用程式一致的備份。|
| Post-ScriptNotFound | 在 VMSnapshotScriptPluginConfig.json 組態檔中指定的位置找不到後置指令碼。 |   確定後置指令碼會出現在組態檔中所指定的路徑，以確保應用程式一致的備份。|
| IncorrectPluginhostFile | VmSnapshotLinux 延伸模組隨附的 Pluginhost 檔案已損毀，因此前置指令碼和後置指令碼無法執行，且備份為應用程式不一致。 | 解除安裝 VmSnapshotLinux 延伸模組，它會自動與下一次備份重新安裝以解決問題。 |
| IncorrectJSONConfigFile | VMSnapshotScriptPluginConfig.json 檔不正確，所以前置指令碼和後置指令碼無法執行，且備份為應用程式不一致。 | 從 [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) 下載副本並再設定一次。 |
| InsufficientPermissionforPre-Script | 針對執行指令碼，「根」使用者應該是檔案擁有者，且檔案應具有 “700” 權限 (也就是應只有擁有者具有「讀取」、「寫入」和「執行」權限)。 | 請確定「根」使用者是指令碼檔案的「擁有者」，且只有擁有者具有「讀取」、「寫入」和「執行」權限。 |
| InsufficientPermissionforPost-Script | 針對執行指令碼，根使用者應該是檔案擁有者，且檔案應具有 “700” 權限 (也就是應只有擁有者具有「讀取」、「寫入」和「執行」權限)。 | 請確定「根」使用者是指令碼檔案的「擁有者」，且只有擁有者具有「讀取」、「寫入」和「執行」權限。 |
| Pre-ScriptTimeout | 執行應用程式一致備份前置指令碼逾時。 | 請檢查指令碼，並增加位於 /etc/azure 的 VMSnapshotScriptPluginConfig.json 檔案中的逾時。 |
| Post-ScriptTimeout | 執行應用程式一致備份後置指令碼逾時。 | 請檢查指令碼，並增加位於 /etc/azure 的 VMSnapshotScriptPluginConfig.json 檔案中的逾時。 |

## <a name="next-steps"></a>後續步驟
[設定 VM 備份至復原服務保存庫](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
