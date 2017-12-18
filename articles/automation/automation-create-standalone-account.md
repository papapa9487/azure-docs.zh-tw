---
title: "建立獨立的 Azure 自動化帳戶 | Microsoft Docs"
description: "引導您在 Azure 自動化中建立、測試和舉例使用安全性主體驗證的逐步解說教學課程。"
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/18/2017
ms.author: magoedte
ms.openlocfilehash: dc4bfa4a94eaa2fb4e0e821c4931dcd1963f3109
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2017
---
# <a name="create-a-standalone-azure-automation-account"></a>建立獨立的 Azure 自動化帳戶
如果您想要評估並了解 Azure 自動化，但不包含其他管理解決方案或與 OMS Log Analytics 整合，本主題說明如何從 Azure 入口網站建立自動化帳戶，以提供 Runbook 作業的進階監視。  您可以新增這些管理解決方案，或在未來的任何時間點與 Log Analytics 整合。  使用自動化帳戶，您可以驗證在 Azure Resource Manager 或 Azure 傳統部署中管理資源的 Runbook。

當您在 Azure 入口網站中建立自動化帳戶時，它會自動建立：

* 執行身分帳戶，以在 Azure Active Directory 中建立新的服務主體、憑證，以及指派參與者角色型存取控制 (RBAC)，其使用 Runbook 來管理 Resource Manager 資源。   
* 傳統執行身分帳戶 (藉由上傳管理憑證)，其使用 Runbook 來管理傳統資源。  

這個帳戶可為您簡化程序，協助您快速開始建置和部署 Runbook 以支援您的自動化需求。  

## <a name="permissions-required-to-create-automation-account"></a>建立自動化帳戶所需的權限
若要建立或更新自動化帳戶，您必須具有下列特定權限，才能完成本主題。   
 
* 若要建立自動化帳戶，您的 AD 使用者帳戶必須新增至具備與 Microsoft.Automation 資源的擁有者角色同等權限的角色 (如[Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)一文所述)。  
* 如果應用程式註冊設定為 [是]，Azure AD 租用戶中的非系統管理使用者可以[註冊 AD 應用程式](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions)。  如果應用程式註冊設定為 [否]，則執行此動作的使用者必須是 Azure AD 中的全域管理員。 

若您在新增至訂用帳戶的全域管理員/共同管理員角色之前，並非訂用帳戶 Active Directory 執行個體的成員，系統會將您以來賓身分新增至 Active Directory。 在此情況下，您會在 [新增自動化帳戶] 刀鋒視窗中看到 「您沒有權限建立...」的警告。 新增至全域管理員/共同管理員角色的使用者可以先從訂用帳戶的 Active Directory 執行個體中移除並重新新增，使其成為 Active Directory 中的完整使用者。 若要確認這種情況，請從 Azure 入口網站的 [Azure Active Directory] 窗格，選取 [使用者和群組]、選取 [所有使用者]，然後在選取特定使用者之後，選取 [設定檔]。 使用者設定檔之下 [使用者類型] 屬性的值不得等於 [來賓]。

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>從 Azure 入口網站建立新的自動化帳戶
在本節中，執行下列步驟以在 Azure 入口網站中建立 Azure 自動化帳戶。    

1. 以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入 Azure 入口網站。
2. 按一下 [新增] 。<br><br> ![在 Azure 入口網站中選取 [新增] 選項](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  
3. 搜尋**自動化**，然後在搜尋結果中選取 [自動化與控制]*。<br><br> ![在 Marketplace 中搜尋並選取自動化](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)<br> 
3. 在 [自動化帳戶] 刀鋒視窗中，按一下 [新增]。<br><br>![加入自動化帳戶](media/automation-create-standalone-account/automation-create-automationacct-properties.png)


   > [!NOTE]
   > 如果您在 [新增自動化帳戶] 刀鋒視窗中看到下列警告，這是因為您的帳戶不是訂用帳戶管理員角色的成員和訂用帳戶的共同管理員。<br><br>![加入自動化帳戶警告](media/automation-create-standalone-account/create-account-without-perms.png)
   > 
   > 
4. 在 [新增自動化帳戶] 刀鋒視窗的 [名稱] 方塊中，輸入新的自動化帳戶的名稱。
5. 如果您有多個訂用帳戶，請針對新的自動化帳戶、新的或現有的**資源群組**和 Azure 資料中心的**位置**指定一個訂用帳戶。
6. 確認已為 [建立 Azure 執行身分帳戶] 選項選取 [是] 這個值，然後按一下 [建立] 按鈕。  
   
   > [!NOTE]
   > 如果您選取選項 [否] 以選擇不要建立執行身分帳戶，則會在 [新增自動化帳戶] 刀鋒視窗中看到一則警告訊息。  雖然此帳戶建立於 Azure 入口網站中，但在傳統或 Resource Manager 訂用帳戶目錄服務內沒有對應的驗證身分識別，因此無法存取您訂用帳戶中的資源。  這會導致所有參考此帳戶的 Runbook 均無法進行驗證，也無法對這些部署模型中的資源執行工作。
   > 
   > ![加入自動化帳戶警告](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)<br>
   > 若未建立服務主體，則不會指派參與者角色。
   > 

7. 在 Azure 建立自動化帳戶時，您可以在功能表的 [通知]  底下追蹤進度。

### <a name="resources-included"></a>包含的資源
順利建立自動化帳戶時，系統會自動為您建立幾項資源。  下表摘要說明執行身分帳戶的資源。<br>

| 資源 | 說明 |
| --- | --- |
| AzureAutomationTutorial Runbook |此為圖形化 Runbook 範例，示範如何使用執行身分帳戶進行驗證以及取得所有 Resource Manager 資源。 |
| AzureAutomationTutorialScript Runbook |此為 PowerShell Runbook 範例，示範如何使用執行身分帳戶進行驗證以及取得 Resource Manager 資源。 |
| AzureAutomationTutorialPython2 Runbook |此為 python Runbook 範例，示範如何使用「執行身分帳戶」進行驗證，然後列出指定的訂用帳戶中存在的資源群組。 |
| AzureRunAsCertificate |在建立自動化帳戶期間自動建立，或使用下列適用於現有帳戶的 PowerShell 指令碼建立的憑證資產。  它可讓您向 Azure 進行驗證，以便從 Runbook 管理 Azure Resource Manager 資源。  此憑證有一年的有效期。 |
| AzureRunAsConnection |在建立自動化帳戶期間自動建立，或使用下列適用於現有帳戶的 PowerShell 指令碼建立的連線資產。 |

下表摘要說明傳統執行身分帳戶的資源。<br>

| 資源 | 說明 |
| --- | --- |
| AzureClassicAutomationTutorial Runbook |此為圖形化 Runbook 範例，可使用傳統執行身分帳戶 (憑證) 取得訂用帳戶中的所有傳統 VM，然後輸出 VM 名稱和狀態。 |
| AzureClassicAutomationTutorial 指令碼 Runbook |此為 PowerShell Runbook 範例，可使用傳統執行身分帳戶 (憑證) 取得訂用帳戶中的所有傳統 VM，然後輸出 VM 名稱和狀態。 |
| AzureClassicRunAsCertificate |自動建立的憑證資產，其用來向 Azure 進行驗證，以便從 Runbook 管理 Azure 傳統資源。  此憑證有一年的有效期。 |
| AzureClassicRunAsConnection |自動建立的連線資產，其用來向 Azure 進行驗證，以便從 Runbook 管理 Azure 傳統資源。 |


## <a name="next-steps"></a>後續步驟
* 若要深入了解圖形化編寫，請參閱 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)。
* 若要開始使用 PowerShell Runbook，請參閱[我的第一個 PowerShell Runbook](automation-first-runbook-textual-powershell.md)。
* 若要開始使用 PowerShell 工作流程 Runbook，請參閱 [我的第一個 PowerShell 工作流程 Runbook](automation-first-runbook-textual.md)。
* 若要開始使用 Python2 Runbook，請參閱[我的第一個 Python2 Runbook](automation-first-runbook-textual-python2.md)。
