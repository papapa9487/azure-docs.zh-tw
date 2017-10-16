---
title: "Azure 自動化原始檔控制與 GitHub Enterprise 的整合 | Microsoft Docs"
description: "詳細描述如何設定自動化 Runbook 原始檔控制與 GitHub Enterprise 的整合。"
services: automation
documentationCenter: 
authors: eslesar
manager: jwhit
editor: 
ms.assetid: e01d817c-7d38-421c-adf5-647a4b526eb4
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: magoedte
ms.openlocfilehash: 2b3ca90382a1c4fb6446799ad539ab8ee603aefc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-github-enterprise"></a>Azure 自動化案例 - 自動化原始檔控制與 GitHub Enterprise 的整合

自動化目前支援原始檔控制整合，可讓您將自動化帳戶中的 Runbook 與 GitHub 原始檔控制儲存機制產生關聯。  不過，部署 [GitHub Enterprise](https://enterprise.github.com/home) 以支援 DevOps 做法的客戶，也會想要使用它來管理為了自動化商務程序和服務管理作業生命週期而開發的 Runbook。  

在此情節中，您要將資料中心的一部 Windows 電腦設定為 Hybird Runbook Workder，並安裝 Azure Resource Manager 模組和 Git 工具。  混合式背景工作角色電腦有一份本機 Git 存放庫的副本。  在混合式背景工作角色上執行 Runbook 時，Git 目錄會同步處理，且 Runbook 檔案內容會匯入至自動化帳戶。

本文說明如何在 Azure 自動化環境中設定此組態。 首先，我們會以安全性認證設定自動化、設定支援此案例所需的 Runbook，並將混合式 Runbook 背景工作角色部署至資料中心，以執行 Runbook 並存取 GitHub Enterprise 存放庫，以便同步處理 Runbook 與自動化帳戶。  


## <a name="getting-the-scenario"></a>取得案例

此案例包含兩個 PowerShell Runbook，可直接從 Azure 入口網站的 [Powerbook 資源庫](automation-runbook-gallery.md)匯入，也可以從 [PowerShell 資源庫](https://www.powershellgallery.com)下載。

### <a name="runbooks"></a>Runbook

Runbook | 說明| 
--------|------------|
Export-RunAsCertificateToHybridWorker | Runbook 會將 RunAs 憑證從自動化帳戶匯出至混合式背景工作角色，讓背景工作角色上執行的 Runbook 可以向 Azure 進行驗證，以便將 Runbook 匯入自動化帳戶中。| 
Sync-LocalGitFolderToAutomationAccount | Runbook 會同步處理混合式電腦上的本機 Git 資料夾，然後將 Runbook 檔案 (*.ps1) 匯入自動化帳戶中。|

### <a name="credentials"></a>認證

認證 | 說明|
-----------|------------|
GitHRWCredential | 您所建立的認證資產，內含使用者的使用者名稱和密碼，該使用者具有混合式背景工作角色的權限。|

## <a name="installing-and-configuring-this-scenario"></a>安裝和設定此案例

### <a name="prerequisites"></a>必要條件

1. Sync-LocalGitFolderToAutomationAccount Runbook 會使用 [Azure 執行身分帳戶](automation-sec-configure-azure-runas-account.md)進行驗證。 

2. 還需要有一個已啟用和設定 Azure 自動化解決方案的 Microsoft Operations Management Suite (OMS) 工作區。  如果用來安裝和設定此情節的自動化帳戶沒有相關聯的工作區，當您從混合式 Runbook 背景工作角色執行 **New-OnPremiseHybridWorker.ps1** 指令碼時，將會為您建立並設定此工作區。        

    > [!NOTE]
    > 目前，下列區域僅支援整合自動化與 OMS - **澳大利亞東南部**、**美國東部 2**、**東南亞**和**西歐**。 

3. 可作為專用 Hybrid Runbook Worker 的電腦，也將在檔案系統的來源目錄中裝載 GitHub 軟體並維護 Runbook 檔案 (*runbook*.ps1)，以同步處理 GitHub 與自動化帳戶。

### <a name="import-and-publish-the-runbooks"></a>匯入和發佈 Runbook

若要在 Azure 入口網站使用您的自動化帳戶，從 Runbook 資源庫匯入 Export-RunAsCertificateToHybridWorker 和 Sync-LocalGitFolderToAutomationAccount Runbook，請依照[從 Runbook 資源庫匯入 Runbook](automation-runbook-gallery.md#to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal) 中的程序進行。 發佈已成功匯入至自動化帳戶的 Runbook。

### <a name="deploy-and-configure-hybrid-runbook-worker"></a>部署和設定 Hybrid Runbook Worker

如果您尚未於資料中心部署 Hypbrid Runbook Worker，請檢閱需求，並使用 [Azure 自動化 Hybrid Runbook Worker - 自動化安裝和設定](automation-hybrid-runbook-worker.md#automated-deployment)中的程序，執行自動化安裝步驟。  在電腦上順利安裝混合式背景工作角色之後，執行下列步驟，以完成組態來支援此案例。

1. 以具有本機系統管理權限的帳戶，登入裝載混合式 Runbook 背景工作角色的電腦，然後建立目錄來存放 Git Runbook 檔案。  將內部 Git 儲存機制複製到此目錄。
2. 如果您尚未建立 RunAs 帳戶，或想要針對此用途建立專用的新帳戶，請從 Azure 入口網站瀏覽至自動化帳戶，選取您的自動化帳戶，然後建立[認證資產](automation-credentials.md)，其中包含使用者的使用者名稱和密碼，該使用者必須具有混合式背景工作角色的權限。  
3. 從您的自動化帳戶，[編輯 Runbook](automation-edit-textual-runbook.md)  **Export-RunAsCertificateToHybridWorker**，並將 *$Password* 變數值的修改為強式密碼。  修改此值之後，按一下 [發佈]，以發佈 Runbook 的草稿版本。 
5. 啟動 Runbook **Export-RunAsCertificateToHybridWorker**，在 [啟動 Runbook] 刀鋒視窗的 [執行設定] 選項下選取 [混合式背景工作角色]，然後在下拉式清單中，選取您稍早針對此案例建立的混合式背景工作角色群組。  

    這會將憑證匯出混合式背景工作角色中，讓背景工作角色上執行的 Runbook 可以利用「執行身分」連線向 Azure 進行驗證，從而管理 Azure 資源 (具體而言，在此情節中 - 將 Runbook 匯入自動化帳戶中)。

4. 從您的自動化帳戶中，選取稍早建立的混合式背景工作角色群組，針對混合式背景工作角色群組[指定 RunAs 帳戶](automation-hrw-run-runbooks.md#runas-account)，然後選擇您剛才或早已建立的認證資產。  如此可確保同步處理 Runbook 可以執行 Git 命令。 
5. 啟動 Runbook **Sync-LocalGitFolderToAutomationAccount**，提供以下必要的輸入參數值，在 [啟動 Runbook] 刀鋒視窗的 [執行設定] 選項下選取 [混合式背景工作角色]，然後在下拉式清單中，選取您稍早針對此案例建立的混合式背景工作角色群組：
    * *ResourceGroup* - 與您的自動化帳戶相關聯的資源群組名稱
    * *AutomationAccountName* - 您的自動化帳戶名稱
    * *GitPath* -Hybrid Runbook Worker 上的本機資料夾或檔案，而 Git 已設定為將最新變更提取到其中

    這會同步處理混合式背景工作角色電腦上的本機 Git 資料夾，然後將 .ps1 檔案從來源目錄匯入至自動化帳戶。

    ![Start Sync-LocalGitFolderToAutomationAccount Runbook](media/automation-scenario-source-control-integration-with-github-ent/start-runbook-synclocalgitfoldertoautoacct.png)<br>

7. 從自動化帳戶的 [Runbook] 刀鋒視窗中，選取此 Runbook 來檢視作業摘要詳細資料，然後選取 [作業] 圖格。  選取 [所有記錄檔] 圖格並檢閱詳細的記錄檔資料流，確認已成功完成。  

## <a name="next-steps"></a>後續步驟

-  若要深入了解 Runbook 類型、其優點和限制，請參閱 [Azure 自動化 Runbook 類型](automation-runbook-types.md)
-  如需 PowerShell 指令碼支援功能的詳細資訊，請參閱 [Azure 自動化中的原生 PowerShell 指令碼支援](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
