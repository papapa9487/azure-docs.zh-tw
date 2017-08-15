---
title: "Azure 自動化混合式 Runbook 背景工作 | Microsoft Docs"
description: "本文提供有關安裝和使用混合式 Runbook 背景工作角色的相關資訊，它是 Azure 自動化的一項功能，可讓您在本機資料中心內或雲端提供者的機器上執行 Runbook。"
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 06227cda-f3d1-47fe-b3f8-436d2b9d81ee
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/05/2017
ms.author: magoedte;bwren
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 783fb22b0154915f2e3d8574ab95538dbd646705
ms.contentlocale: zh-tw
ms.lasthandoff: 08/09/2017

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

## <a name="installing-linux-hybrid-runbook-worker"></a>安裝 Linux 混合式 Runbook 背景工作角色
在 Linux 上安裝和設定混合式 Runbook 背景工作角色的程序很直接，那就是手動安裝和設定此角色。  您必須在 OMS 工作區中啟用 [自動化混合式背景工作角色] 解決方案，然後執行一組命令將電腦註冊為背景工作角色，再將它新增至新的或現有的群組。 

1.  在 OMS 中啟用 [自動化混合式背景工作角色] 解決方案。 執行方式可以是下列任一項：

   1. 從 [OMS 入口網站](https://mms.microsoft.com)中的 [方案庫]，啟用 [自動化混合式背景工作角色] 解決方案
   2. 執行下列 Cmdlet：

        ```$null = Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```
2.  Run the following command with the proper parameters (endpoint and key can be taken from the portal from the automation account linked to the workspace used in the steps above):
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <OMSworkspaceId> -k <automationsharedkey> --groupname <hybridgroupname> -e <automationendpoint>


## Removing Hybrid Runbook Worker 
You can remove one or more Hybrid Runbook Workers from a group or you can remove the group, depending on your requirements.  To remove a Hybrid Runbook Worker from an on-premises computer, perform the following steps.

1. In the Azure portal, navigate to your Automation account.  
2. From the **Settings** blade, select **Keys** and note the values for field **URL** and **Primary Access Key**.  You need this information for the next step.
3. Open a PowerShell session in Administrator mode and run the following command - `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`.  Use the **-Verbose** switch for a detailed log of the removal process.

> [!NOTE]
> This does not remove the Microsoft Monitoring Agent from the computer, only the functionality and configuration of the Hybrid Runbook Worker role.  

## Remove Hybrid Worker groups
To remove a group, you first need to remove the Hybrid Runbook Worker from every computer that is a member of the group using the procedure shown earlier, and then you perform the following steps to remove the group.  

1. Open the Automation account in the Azure portal.
2. Select the **Hybrid Worker Groups** tile and in the **Hybrid Worker Groups** blade, select the group you wish to delete.  After selecting the specific group, the **Hybrid worker group** properties blade is displayed.<br> ![Hybrid Runbook Worker Group Blade](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
3. On the properties blade for the selected group, click **Delete**.  A message appears asking you to confirm this action, select **Yes** if you are sure you want to proceed.<br> ![Delete Group Confirmation Dialog](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> This process can take several seconds to complete and you can track its progress under **Notifications** from the menu.  

## Troubleshooting 
The Hybrid Runbook Worker depends on the Microsoft Monitoring Agent to communicate with your Automation account to register the worker, receive runbook jobs, and report status. If  registration of the worker fails, here are some possible causes for the error:  

1. The hybrid worker is behind a proxy or firewall.  
    Verify the computer has outbound access to *.azure-automation.net on port 443.  

2. The computer the hybrid worker is running on has less than the minimum hardware [requirements](automation-offering-get-started.md#hybrid-runbook-worker).  
    Computers running the Hybrid Runbook Worker should meet the minimum hardware requirements before designating it to host this feature. Otherwise, depending on the resource utilization of other background processes and contention caused by runbooks during execution, the computer will become over utilized and cause runbook job delays or timeouts.
    Confirm the computer designated to run the Hybrid Runbook Worker feature meets the minimum hardware requirements.  If it does, monitor CPU and memory utilization to determine any correlation between the performance of Hybrid Runbook Worker processes and Windows.  If there is memory or CPU pressure, this may indicate the need to upgrade or add additional processors, or increase memory to address the resource bottleneck and resolve the error. Alternatively, select a different compute resource that can support the minimum requirements and scale when workload demands indicate an increase is necessary.
    
3. The Microsoft Monitoring Agent service is not running.  
    If the Microsoft Monitoring Agent Windows service is not running, this prevents the Hybrid Runbook Worker from communicating with Azure Automation.  Verify the agent is running by entering the following command in PowerShell: `get-service healthservice`.  If the service is stopped, enter the following command in PowerShell to start the service: `start-service healthservice`.  

4. In the **Application and Services Logs\Operations Manager** event log, you see event 4502  and EventMessage containing **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** with the following description:  *The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication. The article KB3126513 has additional troubleshooting information for connectivity issues.*
    This can be caused by your proxy or network firewall blockking communication to Microsoft Azure.  Verify the computer has outbound access to *.azure-automation.net on ports 443.

Logs are stored locally on each hybrid worker at C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.  You can check if there are any warning or error events written to the **Application and Services Logs\Microsoft-SMA\Operations** and **Application and Services Logs\Operations Manager** event log that would indicate a connectivity or other issue affecting onboarding of the role to Azure Automation or issue while performing normal operations.  

## Next steps
Review [run runbooks on a Hybrid Runbook Worker](automation-hrw-run-runbooks.md) to learn how to configure your runbooks to automate processes in your on-premises datacenter or other cloud environment.

