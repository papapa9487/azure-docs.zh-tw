---
title: "整合 Azure 自動化與 Visual Stuido Team Services 原始檔控制 | Microsoft Docs"
description: "案例將逐步引導您設定 Azure 自動化帳戶與 Visual Stuido Team Services 原始檔控制的整合。"
services: automation
documentationcenter: 
author: eamono
manager: 
editor: 
keywords: "azure powershell, VSTS, 原始檔控制, 自動化"
ms.assetid: a43b395a-e740-41a3-ae62-40eac9d0ec00
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.openlocfilehash: 01f9c01c9e04e02dbb548b68cf99684ba6ddd57e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-visual-studio-team-services"></a>Azure 自動化案例 - 自動化原始檔控制與 Visual Studio Team Services 的整合

在此案例中，您有 Visual Studio Team Services 專案可用來管理原始檔控制下的 Azure 自動化 Runbook 或 DSC 組態。
本文說明如何整合 VSTS 與 Azure 自動化環境，以便每次簽入時都能持續整合。

## <a name="getting-the-scenario"></a>取得案例

此案例包含兩個 PowerShell Runbook，可直接從 Azure 入口網站的 [Powerbook 資源庫](automation-runbook-gallery.md)匯入，也可以從 [PowerShell 資源庫](https://www.powershellgallery.com)下載。

### <a name="runbooks"></a>Runbook

Runbook | 說明| 
--------|------------|
Sync-VSTS | 完成簽入時，從 VSTS 原始檔控制匯入 Runbook 或組態。 如果以手動執行，它會將所有 Runbook 或組態匯入並發佈到自動化帳戶。| 
Sync-VSTSGit | 完成簽入時，從 Git 原始檔控制下的 VSTS 匯入 Runbook 或組態。 如果以手動執行，它會將所有 Runbook 或組態匯入並發佈到自動化帳戶。|

### <a name="variables"></a>變數

變數 | 說明|
-----------|------------|
VSToken | 您將建立的安全變數資產，其中包含 VSTS 個人存取權杖。 您可以了解如何在 [VSTS 驗證頁面 (VSTS authentication page)](https://www.visualstudio.com/en-us/docs/integrate/get-started/auth/overview) 建立 VSTS 個人存取權杖。 
## <a name="installing-and-configuring-this-scenario"></a>安裝和設定此案例

在 VSTS 中建立[個人存取權杖 (personal access token)](https://www.visualstudio.com/en-us/docs/integrate/get-started/auth/overview)，用來將 Runbook 或組態同步處理到您的自動化帳戶。

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPersonalToken.png) 

在自動化帳戶中建立[安全變數](automation-variables.md)以儲存個人存取權杖，讓 Runbook 可向 VSTS 驗證並將 Runbook 或組態同步處理到自動化帳戶。 您可以將此命名為 VSToken。 

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSTokenVariable.png)

匯入的 Runbook 會將您的 Runbook 或組態同步處理到自動化帳戶。 如果您使用 VSTS 原始檔控制，可以使用來自 PowerShellGallery.com 的 [VSTS 範例 Runbook (VSTS sample runbook)](https://www.powershellgallery.com/packages/Sync-VSTS/1.0/DisplayScript)，如果搭配 Git 使用 VSTS，則可以使用 [VSTS with Git 範例 Runbook] (https://www.powershellgallery.com/packages/Sync-VSTSGit/1.0/DisplayScript)，然後再部署至自動化帳戶。

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPowerShellGallery.png)

您現在可以[發佈](automation-creating-importing-runbook.md#publishing-a-runbook)此 Runbook，以便建立 Webhook. 
![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPublishRunbook.png)

建立此 Sync-VSTS Runbook 的 [Webhook](automation-webhooks.md)，並填入參數，如下所示。 請務必複製 Webhook URL，因為您需要將它當做 VSTS 中的服務勾點。 VSAccessTokenVariableName 是您稍早建立的安全變數名稱 (VSToken)，可保存個人存取權杖。 

與 VSTS (Sync-VSTS.ps1) 整合將需要下列參數。
### <a name="sync-vsts-parameters"></a>Sync-VSTS Parameters

參數 | 說明| 
--------|------------|
WebhookData | 這將包含從 VSTS 服務勾點傳送的簽入資訊。 您應該將此參數保留為空白。| 
ResourceGroup | 這是自動化帳戶所在資源群組的名稱。|
AutomationAccountName | 與 VSTS 同步處理的自動化帳戶名稱。|
VSFolder | VSTS 中有 Runbook 與組態存在的資料夾名稱。|
VSAccount | Visual Studio Team Services 帳戶的名稱。| 
VSAccessTokenVariableName | 保留 VSTS 個人存取權杖的安全變數 (VSToken) 的名稱。| 


![](media/automation-scenario-source-control-integration-with-VSTS/VSTSWebhook.png)

如果您搭配 GIT 使用 VSTS (Sync-VSTSGit.ps1)，將需要下列參數。

參數 | 說明|
--------|------------|
WebhookData | 這將包含從 VSTS 服務勾點傳送的簽入資訊。 您應該將此參數保留為空白。| ResourceGroup | 這是自動化帳戶所在資源群組的名稱。|
AutomationAccountName | 與 VSTS 同步處理的自動化帳戶名稱。|
VSAccount | Visual Studio Team Services 帳戶的名稱。|
VSProject | VSTS 中有 Runbook 與組態存在的專案名稱。|
GitRepo | Git 儲存機制的名稱。|
GitBranch | VSTS Git 儲存機制中分支的名稱。|
資料夾 | VSTS Git 分支中資料夾的名稱。|
VSAccessTokenVariableName | 保留 VSTS 個人存取權杖的安全變數 (VSToken) 的名稱。|

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSGitWebhook.png)

針對會在程式碼簽入時觸發此 Webhook 的資料夾，在 VSTS 中建立服務掛勾以供簽入使用。 選取 Webhook 作為建立新的訂用帳戶時要與之整合的服務。 您可以在 [VSTS 服務掛勾說明文件](https://www.visualstudio.com/en-us/docs/marketplace/integrate/service-hooks/get-started)深入了解服務掛勾。
![](media/automation-scenario-source-control-integration-with-VSTS/VSTSServiceHook.png)

您現在應能執行 Runbook 和組態的所有簽入至 VSTS，並讓這些自動同步處理至您的自動化帳戶。

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSSyncRunbookOutput.png)

如果您以手動執行而非由 VSTS 觸發此 Runbook，您可以將 webhookdata 參數保留為空白，它會從指定的 VSTS 資料夾執行完整同步處理。

如果您想要將案例解除安裝，請從 VSTS 移除其服務掛勾，刪除 Runbook 和 VSToken 變數。
