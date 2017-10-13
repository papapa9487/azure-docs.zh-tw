---
title: "使用 PowerShell (Azure Resource Manager) 將 VMM 中的 Hyper-V VM 複寫到次要站台 | Microsoft Docs"
description: "描述如何使用 PowerShell (Resource Manager) 部署 Azure Site Recovery，以協調將 VMM 雲端中的 Hyper-V VM 複寫、容錯移轉和復原至次要 VMM 站台"
services: site-recovery
documentationcenter: 
author: sujaytalasila
manager: rochakm
editor: raynew
ms.assetid: 9d38e9c3-217c-4e44-830c-575e9a4141f2
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2017
ms.author: sutalasi
ms.openlocfilehash: 5a6e00877b0a2b139d5322f610c1901ad76a710f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-powershell-resource-manager"></a>使用 PowerShell 將位於 VMM 雲端中的 Hyper-V 虛擬機器複寫至次要 VMM 站台 (Resource Manager)
> [!div class="op_single_selector"]
> * [Azure 入口網站](site-recovery-vmm-to-vmm.md)
> * [傳統入口網站](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell - 資源管理員](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

歡迎使用 Azure Site Recovery！ 如果您想要將 System Center Virtual Machine Manager (VMM) 雲端中管理的內部部署 Hyper-V 虛擬機器複寫至次要站台，請使用本文。

本文說明如何使用 PowerShell 自動化您在設定 Azure Site Recovery 將 System Center VMM 雲端中的 Hyper-V 虛擬機器，複寫到次要站台中的 System Center VMM 時常需要執行的工作。

本文包含案例的必要條件，並對您示範

* 如何設定復原服務保存庫
* 在來源 VMM 伺服器與目標 VMM 伺服器上安裝 Azure Site Recovery 提供者
* 在保存庫中註冊 VMM 伺服器
* 設定 VMM 雲端的複寫原則。 原則中的複寫設定將套用到所有受保護的虛擬機器
* 為虛擬機器啟用保護。
* 個別或在復原計劃中測試 VM 的容錯移轉，確定一切如預期般運作。
* 個別或在復原計劃中執行 VM 計劃性或非計劃性的容錯移轉，確定一切如預期般運作。

您在設定此案例如有任何問題，可將問題張貼到 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

> [!NOTE]
> Azure 建立和處理資源的 [部署模型](../azure-resource-manager/resource-manager-deployment-model.md) 有二種：Azure Resource Manager 和傳統。 Azure 也有兩個入口網站 – 支援傳統部署模型的 Azure 傳統入口網站，以及支援兩種部署模型的 Azure 入口網站。 本文涵蓋之內容包括資源管理員部署模型。
>
>

## <a name="on-premises-prerequisites"></a>內部部署必要條件
以下是您需要在主要和次要內部部署站台中部署此案例的情況：

| **必要條件** | **詳細資料** |
| --- | --- |
| **VMM** |建議您在主要站台與次要站台各部署一部 VMM 伺服器。<br/><br/> 您也可以[在單一 VMM 伺服器上的雲端之間進行複寫](site-recovery-vmm-to-vmm.md#prepare-for-single-server-deployment)。 若要這樣做，您至少需要在 VMM 伺服器上設定兩個雲端。<br/><br/> VMM 伺服器至少應執行含有最新更新的 System Center 2012 SP1。<br/><br/> 每部 VMM 伺服器都必須設定一或多個雲端，而所有雲端都必須設定 Hyper-V 容量設定檔。 <br/><br/>雲端必須包含一或多個 VMM 主機群組。<br/><br/>若要深入了解如何設定 VMM 雲端，請參閱[設定 VMM 雲端網狀架構](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)和[Walkthrough: Creating private clouds with System Center 2012 SP1 VMM (逐步解說：使用 System Center 2012 SP1 VMM 建立私人雲端)](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)。<br/><br/> VMM 伺服器必須能夠存取網際網路。 |
| **Hyper-V** |Hyper-V 伺服器必須至少執行具備 Hyper-V 角色並已安裝最新更新的 Windows Server 2012。<br/><br/> Hyper-V 伺服器應該包含一或多部 VM。<br/><br/>  Hyper-V 主機伺服器應該位於主要和次要 VMM 雲端的主機群組中。<br/><br/> 如果您是在 Windows Server 2012 R2 上的叢集中執行 Hyper-V，則應該安裝[更新 2961977](https://support.microsoft.com/kb/2961977)。<br/><br/> 如果您是在 Windows Server 2012 上的叢集中執行 Hyper-V，請注意，當您的叢集是靜態 IP 位址型叢集時，並不會自動建立叢集代理。 您必須手動設定叢集代理。 [閱讀更多資訊](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)。 |
| **提供者** |在 Site Recovery 部署期間，您會在 VMM 伺服器上安裝 Azure Site Recovery Provider。 Provider 會透過 HTTPS 443 與 Site Recovery 通訊來協調複寫。 資料複寫是透過 LAN 或 VPN 連線在主要和次要 Hyper-V 伺服器之間進行。<br/><br/> 在 VMM 伺服器上執行的提供者需要能夠存取下列 URL：*.hypervrecoverymanager.windowsazure.com、*.accesscontrol.windows.net、*.backup.windowsazure.com、*.blob.core.windows.net、*.store.core.windows.net。<br/><br/> 此外，還要允許從 VMM 伺服器到 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)的防火牆通訊，並允許 HTTPS (443) 通訊協定。 |

### <a name="network-mapping-prerequisites"></a>網路對應的必要條件
網路對應會在主要和次要 VMM伺服器上的 VMM VM 網路之間進行對應：

* 容錯移轉之後，選擇性地在次要 Hyper-V 主機上放置複本 VM。
* 將複本 VM 連接到適當的 VM 網路。
* 如果您沒有設定網路對應，複本 VM 將不會在容錯移轉之後連接到任何網路。
* 如果您想要在 Site Recovery 部署期間設定網路對應，就需要執行下列動作：

  * 確認來源 Hyper-V 主機伺服器上的 VM 已連接到 VMM VM 網路。 該網路應該連結到與雲端相關聯的邏輯網路。
  * 確認您將用於復原的次要雲端已設定相對應的 VM 網路。 該 VM 網路應該連結到與次要雲端相關聯的邏輯網路。

從以下文章深入了解設定 VMM 網路

* [如何在 VMM 中設定邏輯網路](http://go.microsoft.com/fwlink/p/?LinkId=386307)
* [如何在 VMM 中設定 VM 網路和閘道](http://go.microsoft.com/fwlink/p/?LinkId=386308)

[深入了解](site-recovery-vmm-to-vmm.md#prepare-for-network-mapping) 網路對應的運作方式。

### <a name="powershell-prerequisites"></a>PowerShell 必要條件
確定 Azure PowerShell 已經準備就緒。 如果您已經使用 PowerShell，您必須升級至 0.8.10 版或更新版本。 如需設定 PowerShell 的資訊，請參閱 [安裝和設定 Azure PowerShell 指南](/powershell/azureps-cmdlets-docs)。 一旦已安裝並設定 PowerShell，您可以檢視 [這裡](/powershell/azure/overview)之服務的所有可用的 Cmdlet。

如需了解可協助您使用這些 Cmdlet 的提示 (例如參數值、輸入及輸出在 Azure PowerShell 中的處理方式)，請參閱 [Azure Cmdlet 使用者入門](/powershell/azure/get-started-azureps)。

## <a name="step-1-set-the-subscription"></a>步驟 1：設定訂用帳戶
1. 從 Azure powershell，登入您的 Azure 帳戶︰使用下列 Cmdlet

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. 取得您的訂用帳戶清單。 這樣也會列出每個訂用帳戶的 subscriptionID。 記下您要建立復原服務保存庫之訂用帳戶的 subscriptionID    

        Get-AzureRmSubscription
3. 設定藉由提及訂用帳戶識別碼在其中建立復原服務保存庫的訂用帳戶

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="step-2-create-a-recovery-services-vault"></a>步驟 2：建立復原服務保存庫
1. 如果您還沒有 Azure Resource Manager 資源群組，請建立一個

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. 建立新的復原服務保存庫，並儲存在變數 (稍後使用) 中建立的 ASR 保存庫物件。 您也可以使用 Get-AzureRMRecoveryServicesVault Cmdlet 擷取建立後的 ASR 保存庫物件：-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>步驟 3：設定復原服務保存庫內容
1. 如果您已建立保存庫，請執行下列命令來取得保存庫。

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. 執行下面的命令來設定保存庫內容。

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-install-the-azure-site-recovery-provider"></a>步驟 4：安裝 Azure Site Recovery 提供者
1. 在 VMM 機器上，執行下列命令來建立目錄：

       New-Item c:\ASR -type directory
2. 執行下列命令，使用已下載的提供者將檔案解壓縮

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. 使用下列命令安裝提供者：

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

   等待安裝完成。
4. 使用下列命令在保存庫中註冊伺服器：

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="step-5-create-and-associate-a-replication-policy"></a>步驟 5︰建立複寫原則並建立關聯
1. 執行下列命令來建立 Hyper-V 2012 R2 複寫原則：

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > VMM 雲端可以包含執行不同 Windows Server 版本 (如 Hyper-V 先決條件所述) 的 Hyper-V 主機，但複寫原則會依據特定的作業系統版本。 如果您有執行不同作業系統版本的主機，則請針對每一種作業系統版本建立不同的複寫原則。 例如︰如果您有五部主機在 Windows Server 2012 上執行，有三部在 Windows Server 2012 R2 上執行，則請建立兩個複寫原則 – 每一種作業系統版本使用一個。

1. 執行下列命令，取得主要保護容器 (主要 VMM 雲端) 和復原保護容器 (復原 VMM 雲端)︰

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
2. 擷取您在步驟 1 使用好記的原則名稱所建立的原則

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. 開始建立保護容器 (VMM 雲端) 與複寫原則的關聯：

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
4. 等候原則關聯工作完成。 您可以使用下列 PowerShell 程式碼片段檢查工作是否已完成。

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

   完成工作處理之後，執行下列命令：

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

若要檢查作業是否完成，請執行 [監視活動](#monitor)中的步驟。

## <a name="step-6-configure-network-mapping"></a>步驟 6：設定網路對應
1. 第一個命令會取得目前的 Azure Site Recovery 保存庫的伺服器。 命令會將 Microsoft Azure Site Recovery 伺服器儲存在 $Servers 陣列變數。

        $Servers = Get-AzureRmSiteRecoveryServer
2. 下列命令取得來源 VMM 伺服器和目標 VMM 伺服器的站台復原網路。

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > 來源 VMM 伺服器在伺服器陣列中可以是第一部或第二部伺服器。 檢查 VMM 伺服器的名稱，並適當地取得網路


1. 最終的 Cmdlet 會在主要網路與復原網路之間建立對應。 Cmdlet 將主要網路指定為 $PrimaryNetworks 的第一個元素，將復原網路指定為 $RecoveryNetworks 的第一個元素。

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]

## <a name="step-7-configure-storage-mapping"></a>步驟 7：設定儲存體對應
1. 下列命令將能把儲存體分類清單置入 $storageclassifications 變數中。

        $storageclassifications = Get-AzureRmSiteRecoveryStorageClassification
2. 下列命令將能把來源分類置入 $SourceClassificaion 變數中，並把目標分類置入 $TargetClassification 變數中。

        $SourceClassificaion = $storageclassifications[0]

        $TargetClassification = $storageclassifications[1]

    > [!NOTE]
    > 來源和目標分類可以是陣列中的任何元素。 請參考下列命令的輸出，以了解 $storageclassifications 陣列中來源和目標分類的目錄。

    > Get-AzureRmSiteRecoveryStorageClassification | Select-Object -Property FriendlyName, Id | Format-Table


1. 下列 Cmdlet 能在來源分類和目標分類之間建立對應。

        New-AzureRmSiteRecoveryStorageClassificationMapping -PrimaryStorageClassification $SourceClassificaion -RecoveryStorageClassification $TargetClassification

## <a name="step-8-enable-protection-for-virtual-machines"></a>步驟 8：對虛擬機器啟用保護
正確設定伺服器、雲端和網路後，您就可以對雲端中的虛擬機器啟用保護。

1. 若要啟用保護，請執行下列命令以取得保護容器：

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. 執行下列命令以取得保護實體 (VM)：

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. 執行下列命令以啟用 VM 的複寫：

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="test-your-deployment"></a>測試您的部署
若要測試部署，您可以對單一虛擬機器執行測試容錯移轉，或者建立包含多部虛擬機器的復原方案，再對這個方案執行測試容錯移轉。 測試容錯移轉會在隔離的網路中模擬您的容錯移轉與復原機制。

> [!NOTE]
> 您可以在 Azure 入口網站中為應用程式建立復原方案。
>
>

若要檢查作業是否完成，請執行 [監視活動](#monitor)中的步驟。

### <a name="run-a-test-failover"></a>執行測試容錯移轉
1. 執行下列 Cmdlet 以取得您要測試 VM 容錯移轉的 VM 網路。

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]
2. 執行下列內容執行 VM 的測試容錯移轉︰

       $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
3. 執行下列內容執行復原方案的測試容錯移轉︰

       $recoveryplanname = "test-recovery-plan"

       $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

### <a name="run-a-planned-failover"></a>執行計劃性容錯移轉
1. 執行下列內容執行 VM 的計劃性容錯移轉︰

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity
2. 執行下列內容執行復原方案的計劃性容錯移轉︰

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

### <a name="run-an-unplanned-failover"></a>執行非計劃性容錯移轉
1. 執行下列內容執行 VM 的非計劃性容錯移轉︰

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

2. 執行下列內容執行復原方案的非計劃性容錯移轉︰

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name=monitor></a>監視活動
使用下列命令來監視活動。 請注意，您必須在工作之間等候處理程序完成。

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
            $isJobLeftForProcessing = $true;
        }

    if($isJobLeftForProcessing)
        {
            Start-Sleep -Seconds 60
        }
    }While($isJobLeftForProcessing)



## <a name="next-steps"></a>後續步驟
[閱讀更多](/powershell/module/azurerm.recoveryservices.backup/#recovery) 使用 Azure Resource Manager PowerShell Cmdlet 進行 Azure Site Recovery 的相關資訊。
