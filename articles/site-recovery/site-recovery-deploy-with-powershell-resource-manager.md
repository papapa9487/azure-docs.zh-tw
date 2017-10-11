---
title: "使用 PowerShell 與 Azure Resource Manager 來複寫 Hyper-V VM | Microsoft Docs"
description: "使用 PowerShell 和 Azure Resource Manager，透過 Azure Site Recovery 將 Hyper-V VM 至 Azure 的複寫自動化。"
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhiag
editor: 
ms.assetid: 05e0d76e-c3f5-4845-8052-094019b6d102
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/31/2017
ms.author: bsiva
ms.openlocfilehash: dbd562b73b0caecd0feb993bd6fb796dddb0438c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-between-on-premises-hyper-v-virtual-machines-and-azure-by-using-powershell-and-azure-resource-manager"></a>使用 PowerShell 和 Azure Resource Manager 在內部部署 Hyper-V 虛擬機器與 Azure 之間複寫
> [!div class="op_single_selector"]
> * [Azure 入口網站](site-recovery-hyper-v-site-to-azure.md)
> * [PowerShell - 資源管理員](site-recovery-deploy-with-powershell-resource-manager.md)
> * [傳統入口網站](site-recovery-hyper-v-site-to-azure-classic.md)
>
>

## <a name="overview"></a>Overview
Azure Site Recovery 可在一些部署案例中協調虛擬機器的複寫、容錯移轉及復原，為您的商務持續性與災害復原做出貢獻。 如需完整的部署案例清單，請參閱 [Azure Site Recovery 概觀](site-recovery-overview.md)。

Azure PowerShell 是個模組，其提供了各種 Cmdlet 來透過 Windows PowerShell 管理 Azure。 它可以搭配兩種模組類型使用：Azure 設定檔模組或 Azure Resource Manager 模組。

Azure PowerShell for Azure Resource Manager 提供的 Site Recovery PowerShell Cmdlet 可讓您在 Azure 中保護與復原伺服器。

本文說明如何搭配 Azure Resource Manager 使用 Windows PowerShell 來部署 Site Recovery，以設定及協調 Azure 的伺服器保護。 本文中所用的範例會說明如何使用 Azure PowerShell 和 Azure Resource Manager 保護、容錯移轉及復原 Hyper-V 主機上的虛擬機器至 Azure。

> [!NOTE]
> Site Recovery PowerShell Cmdlet 目前可讓您設定下列各項︰從一個 Virtual Machine Manager 網站到另一個、Virtual Machine Manager 到 Azure 及 Hyper-V 網站至 Azure。
>
>

您不需要是 PowerShell 專家也能使用本文，但您必須了解模組、Cmdlet 和工作階段等基本概念。 如需 Windows PowerShell 的詳細資訊，請參閱 [開始使用 Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx)(英文)。

您也可以參閱 [搭配使用 Azure PowerShell 與 Azure Resource Manager](../powershell-azure-resource-manager.md)。

> [!NOTE]
> 屬於雲端方案提供者 (CSP) 計畫的 Microsoft 合作夥伴，可以在其客戶各自的 CSP 訂用帳戶 (租用戶訂用帳戶) 設定和管理客戶的伺服器保護。
>
>

## <a name="before-you-start"></a>開始之前
確認您已備妥這些必要條件：

* [Microsoft Azure](https://azure.microsoft.com/) 帳戶。 您可以從 [免費試用](https://azure.microsoft.com/pricing/free-trial/)開始。 此外，您可以參閱 [Azure Site Recovery 管理員價格](https://azure.microsoft.com/pricing/details/site-recovery/)。
* Azure PowerShell 1.0。 如需有關此版本以及如何安裝的資訊，請參閱 [Azure PowerShell 1.0。](https://azure.microsoft.com/)
* [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) 和 [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) 模組。 您可以從 [PowerShell](https://www.powershellgallery.com/)

本文會說明如何使用 Azure Powershell 與 Azure Resource Manager 來設定及管理伺服器保護。 本文中所用的範例會示範如何在 Hyper-V 主機執行保護 Azure 的虛擬機器。 下列必要條件為本範例專用。 如需一組更完整的各種 Site Recovery 案例需求，請參閱各案例文件。

* 執行 Windows Server 2012 R2 或 Microsoft Hyper-V Server 2012 R2 且包含一或多部虛擬機器的 Hyper-V 主機。
* 直接或透過 Proxy 連接到網際網路的 Hyper-V 伺服器。
* 您想要保護的虛擬機器應符合 [虛擬機器必要條件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)。

## <a name="step-1-sign-in-to-your-azure-account"></a>步驟 1：登入您的 Azure 帳戶
1. 開啟 PowerShell 主控台並執行這個命令，登入您的 Azure 帳戶。 此 cmdlet 會開啟網頁，提示您輸入您的帳戶認證。

        Login-AzureRmAccount

    或者，您也可以使用 `-Credential` 參數，以參數形式將您的帳戶認證加入 `Login-AzureRmAccount` Cmdlet。

    如果您是代表租用戶工作的 CSP 合作夥伴，請使用客戶的 tenantID 或租用戶主要網域名稱將客戶指定為租用戶。

        Login-AzureRmAccount -Tenant "fabrikam.com"
2. 一個帳戶可以有多個訂用帳戶，因此您應該建立要使用的訂用帳戶和帳戶的關聯性。

        Select-AzureRmSubscription -SubscriptionName $SubscriptionName
3. 使用下列命令確認您的訂用帳戶已註冊使用 Azure 復原服務提供者和 Site Recovery：

   * `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
   * `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

   如果 **RegistrationState** 在上述這些命令的輸出中設為 [已註冊]，您可以繼續執行步驟 2。 如果未設定，訂用帳戶中應該註冊遺漏的提供者。

   若要註冊 Site Recovery 和復原服務的 Azure 提供者，請執行下列命令：

       Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
       Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

   確認提供者成功使用 `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` 和 `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery` 命令註冊。

## <a name="step-2-set-up-the-recovery-services-vault"></a>步驟 2：設定復原服務保存庫
1. 建立您將在其中建立保存庫的 Azure Resource Manager 資源群組，或使用現有的資源群組。 您可以使用下列命令建立新的資源群組：

        New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

    $ResourceGroupName 變數包含您想要建立的資源群組名稱，而 $Geo 變數包含要在其中建立資源群組的 Azure 區域 (例如：巴西南部)。

    您可以使用 `Get-AzureRmResourceGroup` Cmdlet 在訂用帳戶中取得資源群組的清單。
2. 建立新的 Azure 復原服務保存庫，如下所示：

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    您可以使用 `Get-AzureRmRecoveryServicesVault` Cmdlet 擷取現有保存庫的清單。

> [!NOTE]
> 如果您想要使用傳統入口網站或 Azure 服務管理 PowerShell 模組在建立的 Site Recovery 保存庫上執行作業，您可以使用 `Get-AzureRmSiteRecoveryVault` Cmdlet 擷取這些保存庫的清單。 您應該為所有的新作業建立新的復原服務保存庫。 您稍早建立的 Site Recovery 保存庫仍會受到支援，但不會有最新的功能。
>
>

## <a name="step-3-set-the-recovery-services-vault-context"></a>步驟 3：設定復原服務保存庫內容
1. 執行下列命令來設定保存庫內容：

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-create-a-hyper-v-site-and-generate-a-new-vault-registration-key-for-the-site"></a>步驟 4：建立 Hyper-V 網站，並產生網站的新保存庫註冊金鑰。
1. 建立新的 Hyper-V 站台，如下所示：

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

    這個 Cmdlet 會啟動 Site Recovery 作業來建立網站，並傳回 Site Recovery 作業物件。 等待作業完成，並確認作業已成功完成。

    您可以擷取作業物件，並使用 Get-AzureRmSiteRecoveryJob Cmdlet 檢查目前的作業狀態。
2. 產生並下載網站的註冊金鑰，如下所示：

        $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path

    將下載的金鑰複製到 Hyper-V 主機。 您需要金鑰向網站註冊 Hyper-V 主機。

## <a name="step-5-install-the-azure-site-recovery-provider-and-azure-recovery-services-agent-on-your-hyper-v-host"></a>步驟 5：在 Hyper-V 主機上安裝 Azure Site Recovery 提供者和 Azure 復原服務代理程式
1. 從 [Microsoft](https://aka.ms/downloaddra)下載最新版提供者的安裝程式。
2. 在 Hyper-V 主機上執行安裝程式，然後在安裝結尾繼續註冊步驟。
3. 當系統提示時提供下載的網站註冊金鑰，並完成網站的 Hyper-V 主機註冊。
4. 確認 Hyper-V 主機使用下列命令向網站註冊：

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy-and-associate-it-with-the-protection-container"></a>步驟 6：建立複寫原則，並建立其與保護容器的關聯
1. 建立複寫原則，如下所示︰

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

    請檢查傳回的作業，以確保複寫原則建立成功。

   > [!IMPORTANT]
   > 指定的儲存體帳戶應與您的復原服務保存庫位於相同的 Azure 區域中，而且應該啟用異地複寫。
   >
   > * 如果指定的復原儲存體帳戶屬於 Azure Storage (Classic) 類型，受保護機器的容錯移轉會將機器復原到 Azure IaaS (Classic)。
   > * 如果指定的復原儲存體帳戶屬於 Azure Storage (Azure Resource Manager) 類型，受保護機器的容錯移轉會將機器復原到 Azure IaaS (Azure Resource Manager)。
   >
   >
2. 取得對應至網站的保護容器，如下所示：

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3. 啟動保護容器與複寫原則的關聯，如下所示：

     $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName   $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

   等待關聯作業完成，並確保成功完成。

## <a name="step-7-enable-protection-for-virtual-machines"></a>步驟 7：對虛擬機器啟用保護
1. 取得對應至您想要保護之 VM 的保護實體，如下所示：

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. 停止保護虛擬機器，如下所示：

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

   > [!IMPORTANT]
   > 指定的儲存體帳戶應與您的復原服務保存庫位於相同的 Azure 區域中，而且應該啟用異地複寫。
   >
   > * 如果指定的復原儲存體帳戶屬於 Azure Storage (Classic) 類型，受保護機器的容錯移轉會將機器復原到 Azure IaaS (Classic)。
   > * 如果指定的復原儲存體帳戶屬於 Azure Storage (Azure Resource Manager) 類型，受保護機器的容錯移轉會將機器復原到 Azure IaaS (Azure Resource Manager)。
   >
   > 如果您要保護的 VM 有多個連接到它的磁碟，請使用 OSDiskName  參數指定作業系統磁碟。
   >
   >
3. 在初始複寫後，等待虛擬機器達到受保護的狀態。 所需時間長短，受到要複寫的資料量和可用的 Azure 上游頻寬等因素影響。 當 VM 達到受保護的狀態時，作業的 State 和 StateDescription 就會更新，如下所示。

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. 更新 VM 角色大小等復原屬性，以及在容錯移轉時要附加虛擬機器網路介面卡的 Azure 網路。

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob

        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>步驟 8：執行測試容錯移轉
1. 執行測試容錯移轉作業，如下所示：

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. 確認已在 Azure 中建立測試 VM。 (在 Azure 中建立測試 VM 之後，測試容錯移轉作業已經暫停。 作業會藉由清除繼續進行作業時建立的成品來完成，如下一個步驟所示。)
3. 完成測試容錯移轉，如下所示：

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

## <a name="next-steps"></a>後續步驟
[閱讀更多](https://msdn.microsoft.com/library/azure/mt637930.aspx) 使用 Azure Resource Manager PowerShell Cmdlet 進行 Azure Site Recovery 的相關資訊。
