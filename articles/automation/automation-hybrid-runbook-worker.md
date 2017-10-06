---
title: "Azure 自動化混合式 Runbook 背景工作 | Microsoft Docs"
description: "本文提供有關安裝和使用混合式 Runbook 背景工作角色的相關資訊，它是 Azure 自動化的一項功能，可讓您在本機資料中心內或雲端提供者的機器上執行 Runbook。"
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 06227cda-f3d1-47fe-b3f8-436d2b9d81ee
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/21/2017
ms.author: magoedte;bwren
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 67aa0f407fd669df559ce1a8d411650158462aef
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---

# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>使用混合式 Runbook 背景工作角色將資料中心內或雲端的資源自動化
Azure 自動化中的 Runbook 無法存取其他雲端或內部部署環境中的資源，因為其在 Azure 雲端中執行。  Azure 自動化的混合式 Runbook 背景工作角色功能可讓您直接在裝載角色的電腦上，以及針對環境中的資源執行 Runbook，從而管理這些本機資源。 Runbook 會儲存並在 Azure 自動化中管理，接著傳遞至一或多個指定的電腦。  

下圖說明這項功能：<br>  

![混合式 Runbook 背景工作概觀](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

如需混合式 Runbook 背景工作角色和部署考量的技術概觀，請參閱[自動化架構概觀](automation-offering-get-started.md#automation-architecture-overview)。    

## <a name="hybrid-runbook-worker-groups"></a>混合式 Runbook 背景工作群組
每一個混合式 Runbook 背景工作是您安裝代理程式時指定的混合式 Runbook 背景工作群組的成員。  群組可包含單一代理程式，但您可以在群組中安裝多個代理程式以獲得高可用性。

在 Hybrid Runbook Worker 上啟動 Runbook 時，您會指定要執行它的群組。  群組的成員決定由哪一個背景工作角色處理要求。  您無法指定特定的背景工作。

## <a name="relationship-to-service-management-automation"></a>與 Service Management Automation 的關聯性
[Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) 可讓您在本機資料中心內執行 Azure 自動化支援的相同 Runbook。 SMA 通常與 Windows Azure Pack 一同部署，因為 Windows Azure Pack 包含 SMA 管理的圖形化介面。 不同於 Azure 自動化，SMA 需要本機安裝，安裝中包含了裝載 API 的 Web 伺服器、包含 Runbook 和 SMA 組態的資料庫、用以執行 Runbook 作業的 Runbook 背景工作角色。 Azure 自動化在雲端中提供這些服務，並只要求您在本機環境中維護混合式 Runbook 背景工作。

如果您是現有 SMA 使用者，可以將您的 Runbook 移至 Azure 自動化，以搭配混合式 Runbook 背景工作角色使用，而不必變更，假設他們已依[在混合式 Runbook 背景工作角色中執行 Runbook](automation-hrw-run-runbooks.md) 中所述，向資源進行驗證。  SMA 中的 Runbook 會在背景工作伺服器上服務帳戶的內容中執行，其可能為 Runbook 提供該驗證。

您可以使用下列準則來判斷 Azure 自動化搭配混合式 Runbook 背景工作或 Service Management Automation 更適合您的需求。

* SMA 需要本機安裝其基礎元件，如果需要圖形化管理介面，這些元件會連接到 Windows Azure Pack。 相較於 Azure 自動化，這需要更多的本機資源和更高的維護成本；Azure 自動化只需要在本機 Runbook 背景工作角色上安裝代理程式。 代理程式由 Operations Management Suite 管理，進而減少您的維護成本。
* Azure 自動化會將其 Runbook 儲存在雲端中，並將它們提供給內部部署的 Hybrid Runbook 背景工作角色。 如果您的安全性原則不允許這種行為，您應該使用 SMA。
* SMA 隨附於 System Center，因此需要 System Center 2012 R2 授權。 Azure 自動化是採用分層式的訂用帳戶模型。
* Azure 自動化具備 SMA 中並未提供的圖形 Runbook 等進階功能。

## <a name="installing-the-windows-hybrid-runbook-worker"></a>安裝 Windows 混合式 Runbook 背景工作角色 

安裝和設定 Windows 混合式 Runbook 背景工作角色有兩種可用的方法。  建議的方法是使用自動化 Runbook，將設定 Windows 電腦所需的程序完全自動化。  第二種方法採取逐步程序來手動安裝和設定角色。  

> [!NOTE]
> 若要透過「預期狀態設定」(DSC) 管理支援 Hybrid Runbook Worker 之伺服器的設定，您需要將它們新增為 DSC 節點。  如需有關讓它們上線以透過 DSC 進行管理的詳細資訊，請參閱[讓機器上線以透過 Azure 自動化 DSC 進行管理](automation-dsc-onboarding.md)。           
><br>
>如果您啟用[更新管理解決方案](../operations-management-suite/oms-solution-update-management.md)，則任何連線到 OMS 工作區的 Windows 電腦都會自動設定為 Hybrid Runbook Worker，以支援此解決方案所包含的 Runbook。  不過，它不會向您已在自動化帳戶中定義的任何 Hybrid Worker 群組註冊。  您可以將電腦新增到您「自動化」帳戶中的 Hybrid Runbook Worker 群組來支援「自動化」Runbook，只要解決方案和 Hybrid Runbook Worker 群組成員資格兩者所用的帳戶相同即可。  此功能已新增至 Hybrid Runbook Worker 7.2.12024.0 版。  

請檢閱下列有關[硬體和軟體需求](automation-offering-get-started.md#hybrid-runbook-worker)和[的資訊，在開始部署混合式 Runbook 背景工作角色之前準備您的網路](automation-offering-get-started.md#network-planning)。  在您成功部署 Runbook 背景工作角色後，請檢閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)，以了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化。  
 
### <a name="automated-deployment"></a>自動化部署

執行下列步驟，以將 Windows 混合式背景工作角色的安裝和設定自動化。  

1. 直接在執行 Hybrid Runbook Worker 角色的電腦上，從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.0/DisplayScript)下載 *New-OnPremiseHybridWorker.ps1* 指令碼，或從環境中的另一部電腦下載，再複製到背景工作角色。  

    *New-OnPremiseHybridWorker.ps1* 指令碼在執行期間需要下列參數：

  * *AutomationAccountName* (必要) - 您的自動化帳戶名稱。  
  * *ResourceGroupName* (必要) - 與您的自動化帳戶相關聯的資源群組名稱。  
  * *HybridGroupName* (必要) - 針對支援此案例的 Runbook，您指定作為目標的 Hybrid Runbook Worker 群組名稱。 
  *  *SubscriptionID* (必要) - 您的自動化帳戶所在的 Azure 訂用帳戶識別碼。
  *  *WorkspaceName* (選擇性) - OMS 工作區名稱。  如果您沒有 OMS 工作區，此指令碼會建立並設定一個 OMS 工作區。  

     > [!NOTE]
     > 目前，支援與 OMS 整合的自動化區域只包括 - **澳大利亞東南部**、**美國東部 2**、**東南亞**和**西歐**。  如果您的自動化帳戶不在其中一個區域，此指令碼會建立 OMS 工作區，但會警告您，指出無法將它們連結在一起。
     > 
2. 在您的電腦上，從 [開始] 畫面以系統管理員模式啟動 **Windows PowerShell**。  
3. 從 PowerShell 命令列殼層，瀏覽至您下載的指令碼所在的資料夾，然後執行該指令碼，並變更參數 *-AutomationAccountName*、*-ResourceGroupName*、*-HybridGroupName*、*-SubscriptionId* 和 *-WorkspaceName* 的值。

     > [!NOTE] 
     > 執行指令碼之後，您會收到向 Azure 進行驗證的提示。  您**必須**以訂用帳戶管理員角色成員和訂用帳戶共同管理員的帳戶登入。  
     >  
    
        .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> `
        -ResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
        -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfOMSWorkspace>

4. 系統會提示您同意安裝 **NuGet**，也會提示使用您的 Azure 認證進行驗證。<br><br> ![Execution of New-OnPremiseHybridWorker script](media/automation-hybrid-runbook-worker/new-onpremisehybridworker-scriptoutput.png)

5. 指令碼完成之後，[混合式背景工作角色群組] 刀鋒視窗會顯示新的群組和成員數目，或者，如果是現有的群組，則成員數目會遞增。  您可以在 [Hybrid Worker 群組] 刀鋒視窗從清單中選取群組，然後選取 [Hybrid Worker] 圖格。  在 [Hybrid Worker] 刀鋒視窗上，您會看到列出群組的每個成員。  

### <a name="manual-deployment"></a>手動部署 
對您的自動化環境執行一次前兩個步驟，再對每一台背景工作角色電腦重複其餘步驟。

#### <a name="1-create-operations-management-suite-workspace"></a>1.建立 Operations Management Suite 工作區
如果您還沒有 Operations Management Suite 工作區，請使用[管理您的工作區](../log-analytics/log-analytics-manage-access.md)中的指示建立工作區。 如果您已經有工作區，可以使用現有的工作區。

#### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>2.將自動化解決方案加入至 Operations Management Suite 工作區
解決方案會將功能加入至 Operations Management Suite。  自動化解決方案會增加 Azure 自動化的功能，包括支援 Hybrid Runbook Worker。  將解決方案新增至工作區時，它會自動將背景工作角色元件往下推送給您在下一步將安裝的代理程式電腦。

請依照 [使用解決方案資源庫新增解決方案](../log-analytics/log-analytics-add-solutions.md) 中的指示，將 **自動化** 解決方案新增至 Operations Management Suite 工作區。

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3.安裝 Microsoft Monitoring Agent
Microsoft Monitoring Agent 可將電腦連線至 Operations Management Suite。  將代理程式安裝在內部部署電腦，並連接到您的工作區時，它會自動下載 Hybrid Runbook Worker 所需的元件。

請依照[將 Windows 電腦連接到 Log Analytics](../log-analytics/log-analytics-windows-agents.md) 中的指示，將代理程式安裝在內部部署電腦上。  您可以對多部電腦重複此程序，將多個背景工作角色加入至您的環境。

當代理程式成功連接到 Operations Management Suite 時，它會列在 Operations Management Suite [設定] 窗格的 [已連接的來源] 索引標籤上。  當 C:\Program Files\Microsoft Monitoring Agent\Agent 中出現 **AzureAutomationFiles** 資料夾時，就可確認代理程式已正確下載自動化解決方案。  若要確認 Hybrid Runbook Worker 版本，您可以瀏覽至 C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\，並記下 \\version 子資料夾。   

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4.安裝 Runbook 環境並連接到 Azure 自動化
將代理程式新增至 Operations Management Suite 時，自動化解決方案會往下推送包含 **Add-HybridRunbookWorker** Cmdlet 的 **HybridRegistration** PowerShell 模組。  您可以使用這個 Cmdlet 在電腦上安裝 Runbook 環境並向 Azure 自動化進行註冊。

以系統管理員模式開啟 PowerShell 工作階段，並執行下列命令來匯入模組。

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

然後使用下列語法執行 **Add-HybridRunbookWorker** Cmdlet：

    Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>

您可以從 Azure 入口網站的 [管理金鑰]  刀鋒視窗取得這個 Cmdlet 所需的資訊。  在您的自動化帳戶中，從 [設定] 刀鋒視窗選取 [金鑰]，以開啟此刀鋒視窗。

![混合式 Runbook 背景工作概觀](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** 是混合式 Runbook 背景工作角色群組的名稱。 如果自動化帳戶中已有這個群組，那麼會將目前的電腦加入。  如果尚不存在，則會加入。
* **EndPoint** 是 [管理金鑰] 刀鋒視窗中的 [URL] 欄位。
* **Token** 是 [管理金鑰] 刀鋒視窗中的 [主要存取金鑰]。  

在 **Add-HybridRunbookWorker** 中新增 **-Verbose** 參數可接收安裝的詳細資訊。

#### <a name="5-install-powershell-modules"></a>5.安裝 PowerShell 模組
Runbook 可以使用 Azure 自動化環境中安裝的模組中定義的任何活動和 Cmdlet。  不過，這些模組不會自動部署至內部部署機器，所以您必須手動安裝它們。  例外狀況是預設安裝的 Azure 模組，可提供 Azure 自動化所有 Azure 服務和活動的 Cmdlet 存取權。

由於 Hybrid Runbook Worker 功能的主要目的是要管理本機資源，您很可能必須安裝支援這些資源的模組。  您可以參考 [安裝模組](http://msdn.microsoft.com/library/dd878350.aspx) 來取得安裝 Windows PowerShell 模組的詳細資訊。  安裝的模組必須位於 PSModulePath 環境變數所參考的位置，才能由 Hybrid 背景工作角色自動匯入。  如需詳細資訊，請參閱[修改 PSModulePath 安裝路徑](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx)。 

## <a name="removing-hybrid-runbook-worker"></a>移除 Hybrid Runbook Worker 
您可以移除群組中的一或多個 Hybrid Runbook 背景工作角色，或移除該群組，視您的需求而定。  若要從內部部署電腦中移除 Hybrid Runbook Worker，請執行下列步驟。

1. 在 Azure 入口網站中，瀏覽至您的自動化帳戶。  
2. 從 [設定] 刀鋒視窗中，選取 [金鑰]，記下欄位 [URL] 和 [主要存取金鑰] 的值。  下一個步驟需要此資訊。
3. 在系統管理員模式中開啟 PowerShell 工作階段，並執行下列命令 - `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`。  使用 **-Verbose** 參數可取得移除程序的詳細記錄。

> [!NOTE]
> 這不會移除電腦上的 Microsoft Monitoring Agent，只會移除 Hybrid Runbook 背景工作角色的功能和組態。  

## <a name="remove-hybrid-worker-groups"></a>移除混合式背景工作角色群組
若要移除群組，您必須先使用先前所示的程序，從群組的每一部成員電腦中移除 Hybrid Runbook Worker，然後執行下列步驟移除群組。  

1. 在 Azure 入口網站中，開啟自動化帳戶。
2. 選取 [Hybrid 背景工作角色] 刀鋒視窗中的 [Hybrid 背景工作角色群組] 圖格，然後選取要刪除的群組。  選取特定的群組之後，[Hybrid 背景工作角色群組] 屬性刀鋒視窗隨即出現。<br> ![Hybrid Runbook 背景工作角色群組刀鋒視窗](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
3. 在所選群組的屬性刀鋒視窗中，按一下 [刪除]。  將會出現一則訊息要求您確認此動作，如果您確定要繼續，請選取 [是]。<br> ![建立群組確認對話方塊](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> 此程序需要幾秒鐘才能完成，您可以在功能表的 [通知] 底下追蹤其進度。  

## <a name="troubleshooting"></a>疑難排解 
混合式 Runbook 背景工作角色取決於與自動化帳戶通訊的 Microsoft Monitoring Agent，來註冊背景工作角色、接收 Runbook 作業及報告狀態。 如果註冊背景工作角色失敗，可參考以下一些可能的錯誤原因：  

1. 混合式背景工作角色位於 Proxy 或防火牆後方。  
    確認電腦可透過連接埠 443 輸出存取 *.azure-automation.net。  

2. 混合式背景工作角色執行所在的電腦未滿足最低硬體[需求](automation-offering-get-started.md#hybrid-runbook-worker)。  
    執行 Hybrid Runbook Worker 的電腦應滿足最低硬體需求，您才能指派它來裝載這項功能。 否則，根據其他背景處理序的資源使用率和 Runbook 在執行期間造成的競爭，電腦可能會變得過度使用，導致 Runbook 作業延遲或逾時。
   確認指派來執行混合式 Runbook 背景工作角色功能的電腦滿足最低硬體需求。  如果滿足最低硬體需求，請監視 CPU 和記憶體使用率，判斷 Hybrid Runbook Worker 處理序之效能和 Windows 之間是否有任何相互關聯。  如果有記憶體或 CPU 壓力，這表示電腦可能需要升級或增加額外處理器，或增加記憶體來解決資源瓶頸及解決錯誤。 您也可以選擇其他可支援最低需求，也能在工作負載需求指出需要增加資源時擴充的計算資源。
    
3. Microsoft Monitoring Agent 服務未執行。  
    如果 Microsoft Monitoring Agent Windows 服務並未執行，這可避免混合式 Runbook 背景工作角色與 Azure 自動化進行通訊。  在 PowerShell 中輸入下列命令，確認代理程式正在執行：`get-service healthservice`。  如果服務已停止，在 PowerShell 中輸入下列命令可啟動服務：`start-service healthservice`。  

4. 在 **Application and Services Logs\Operations Manager** 事件記錄中，您會看到事件 4502 和 EventMessage 包含 **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**，並具有如下描述：*<wsid>.oms.opinsights.azure.com 服務所提供的憑證並非由 Microsoft 服務所使用的憑證授權單位發行。請連絡網路管理員，以查看它們是否正在執行可攔截 TLS/SSL 通訊的 Proxy。文章 KB3126513 內提供針對連線能力問題的其他疑難排解資訊。*
    這可能是因 Proxy 或網路防火牆封鎖 Microsoft Azure 的通訊所引起。  確認電腦可透過連接埠 443 輸出存取 *.azure-automation.net。

記錄檔儲存每一個混合式背景工作角色本機的 C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes 中。  您可以檢查是否有任何警告或錯誤事件寫入 **Application and Services Logs\Microsoft-SMA\Operations** 和 **Application and Services Logs\Operations Manager** 事件記錄，可能表示有連線能力或其他會影響角色上架到 Azure 自動化的問題，或執行正常作業時的問題。  

## <a name="next-steps"></a>後續步驟
請檢閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)，以了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化。

