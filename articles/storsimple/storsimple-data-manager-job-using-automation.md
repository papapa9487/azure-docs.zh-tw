---
title: "使用 Azure 自動化來觸發作業 | Microsoft Docs"
description: "了解如何使用 Azure 自動化來觸發 StorSimple 資料管理員作業 (私人預覽)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
ms.openlocfilehash: 9691408bcd80afb6eba534f26749b76dd3bfe315
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-automation-to-trigger-a-job-private-preview"></a>使用 Azure 自動化來觸發作業 (私人預覽)

此文章說明如何使用 Azure 自動化來觸發 StorSimple 資料管理員作業。

## <a name="prerequisites"></a>必要條件

開始之前，請確定您有︰

*   已安裝 Azure Powershell。 [下載 Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)。
*   用於初始化資料轉換作業的組態設定 (這裡包含取得這些設定的指示)。
*   已在資源群組內的混合式資料資源中正確設定的工作定義。
*   從 GitHub 存放庫下載 `DataTransformationApp.zip` [zip](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) 檔案。
*   從 GitHub 存放庫下載 `Get-ConfigurationParams.ps1` [指令碼](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Get-ConfigurationParams.ps1)。
*   從 GitHub 存放庫下載 `Trigger-DataTransformation-Job.ps1` [指令碼](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1)。

## <a name="step-by-step"></a>逐步說明

### <a name="get-azure-active-directory-permissions-for-the-automation-job-to-run-the-job-definition"></a>取得自動化作業的 Azure Active Directory 權限，以執行工作定義

1. 若要擷取 Active directory 的組態參數，請執行下列步驟︰

    1. 在本機電腦中開啟 Windows PowerShell。 確認 [Azure PowerShell](https://azure.microsoft.com/downloads/) 已安裝。
    1. 執行 `Get-ConfigurationParams.ps1` 指令碼 (在上述您所下載的資料夾中)。 在 PowerShell 視窗中輸入下列命令：

        ```
        .\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```

        ActiveDirectoryKey 是您稍後使用的密碼。 輸入您選擇的密碼。 AppName 可以是任何字串。

2. 此指令碼會輸出下列值，這些值應該在觸發自動化 Runbook 時使用。 記下這些值。

    - 用戶端識別碼
    - 租用戶識別碼
    - Active Directory 金鑰 (與上面輸入的金鑰相同)
    - 訂用帳戶識別碼

### <a name="set-up-the-automation-account"></a>設定自動化帳戶

1. 登入 Azure，並開啟您的自動化帳戶。
2. 按一下 [資產] 磚，以開啟資產清單。
3. 按一下 [模組] 磚，以開啟模組清單。
4. 按一下 [+ 加入模組] 按鈕，會啟動 [加入模組] 刀鋒視窗。

    ![自動化帳戶設定](./media/storsimple-data-manager-job-using-automation/add-module1m.png)

5. 當您從本機電腦選取 `DataTransformationApp.zip` 檔案之後，按一下 [確定] 匯入模組。

   當 Azure 自動化將模組匯入到您的帳戶時，會擷取有關模組的中繼資料。 此作業可能需要幾分鐘的時間。

   ![自動化帳戶設定](./media/storsimple-data-manager-job-using-automation/add-module2m.png)

   

6. 您會在部署模組時收到一則通知，而當程序完成時會收到另一則通知。  您也可以在 [模組] 磚中檢查狀態。

### <a name="to-import-the-runbook-that-triggers-the-job-definition"></a>匯入觸發工作定義的 Runbook

1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 按一下 [Runbook] 磚，以開啟 Runbook 清單。
3. 按一下 [+ 加入 Runbook]，然後按一下 [匯入現有 Runbook]。

   ![匯入現有 Runbook](./media/storsimple-data-manager-job-using-automation/import-a-runbook.png)

4. 按一下 [Runbook 檔案] 並選取要匯入 `Trigger-DataTransformation-Job.ps1` 的檔案。
5. 按一下 [建立] 以匯入 Runbook。 新的 Runbook 會出現在自動化帳戶的 Runbook 清單中。
7. 按一下 Trigger-DataTransformation-Job Runbook，然後按一下編輯。
8. 按一下 [發行]，並在系統提示您確認時按一下 [是]。


### <a name="to-run-the-runbook"></a>執行 Runbook：
1. 在 Azure 入口網站中，開啟您的自動化帳戶。
2. 按一下 [Runbook]  磚以開啟 Runbook 的清單。
3. 按一下 [Trigger-DataTransformation-Job]。
4. 按一下 [開始]  以啟動 Runbook。

   ![啟動 Runbook](./media/storsimple-data-manager-job-using-automation/run-runbook1m.png)

5. 在 [啟動 Runbook] 刀鋒視窗中，輸入所有參數。 按一下 [確定]，以提交資料轉換作業。

   ![啟動 Runbook](./media/storsimple-data-manager-job-using-automation/run-runbook2m.png)


## <a name="next-steps"></a>後續步驟

[使用 StorSimple Data Manager UI 來轉換資料](storsimple-data-manager-ui.md)。