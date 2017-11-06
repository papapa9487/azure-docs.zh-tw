---
title: "驗證 Azure 自動化帳戶組態 | Microsoft Docs"
description: "本文說明如何確認已正確設定您的自動化帳戶組態。"
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: magoedte
ms.openlocfilehash: 55f5d5524019ac63565e5ddd1f47dbdd65f05065
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="test-azure-automation-run-as-account-authentication"></a>測試 Azure 自動化執行身分帳戶驗證
成功建立自動化帳戶之後，您可以執行簡單的測試，確認您能夠在 Azure Resource Manager 或 Azure 傳統部署中使用新建立或已更新的自動化執行身分帳戶成功進行驗證。    

## <a name="automation-run-as-authentication"></a>自動化執行身分驗證
使用下面範例程式碼來[建立 PowerShell Runbook](automation-creating-importing-runbook.md)，以使用執行身分帳戶進行驗證，並且同時在自訂 Runbook 中使用您的自動化帳戶驗證及管理 Resource Manage 資源。   

    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

        "Logging in to Azure..."
        Add-AzureRmAccount `
           -ServicePrincipal `
           -TenantId $servicePrincipalConnection.TenantId `
           -ApplicationId $servicePrincipalConnection.ApplicationId `
           -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
       if (!$servicePrincipalConnection)
       {
          $ErrorMessage = "Connection $connectionName not found."
          throw $ErrorMessage
      } else{
          Write-Error -Message $_.Exception
          throw $_.Exception
      }
    }

    #Get all ARM resources from all resource groups
    $ResourceGroups = Get-AzureRmResourceGroup 

    foreach ($ResourceGroup in $ResourceGroups)
    {    
       Write-Output ("Showing resources in resource group " + $ResourceGroup.ResourceGroupName)
       $Resources = Find-AzureRmResource -ResourceGroupNameContains $ResourceGroup.ResourceGroupName | Select ResourceName, ResourceType
       ForEach ($Resource in $Resources)
       {
          Write-Output ($Resource.ResourceName + " of type " +  $Resource.ResourceType)
       }
       Write-Output ("")
    } 

請注意，Runbook 中用來驗證的 Cmdlet (**Add-AzureRmAccount**) 會使用 ServicePrincipalCertificate 參數集。  它藉由使用服務主體憑證 (而非認證) 進行驗證。  

當您[執行 Runbook](automation-runbook-execution.md) 來驗證您的執行身分帳戶時，已建立 [Runbook 作業](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal)、顯示 [作業] 刀鋒視窗，而作業狀態會顯示在 **[作業摘要]** 圖格中。 作業狀態一開始會顯示為 [已排入佇列]  ，表示其正在等候雲端中的 Runbook 背景工作變為可用狀態。 然後當背景工作宣告該工作時，狀態將變更為 [正在開始]，然後 Runbook 實際開始執行時再變更為 [執行中]。  Runbook 作業完成時，我們應該會看到 [完成] 狀態。

若要查看 Runbook 的詳細結果，請按一下 [輸出]  圖格。  在 [輸出] 刀鋒視窗上，您應會看到它已成功驗證並傳回您的訂用帳戶中所有資源群組中的所有資源清單。  

當您對 Runbook 重複使用程式碼時，請記得移除以 `#Get all ARM resources from all resource groups` 註解開頭的程式碼區塊。

## <a name="classic-run-as-authentication"></a>傳統執行身分驗證
使用下面範例程式碼來[建立 PowerShell Runbook](automation-creating-importing-runbook.md)，以使用傳統執行身分帳戶進行驗證，並且同時在自訂 Runbook 中驗證及管理傳統部署模型中的資源。  

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID
    
    #Get all VMs in the subscription and return list with name of each
    Get-AzureVM | ft Name

當您[執行 Runbook](automation-runbook-execution.md) 來驗證您的執行身分帳戶時，已建立 [Runbook 作業](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal)、顯示 [作業] 刀鋒視窗，而作業狀態會顯示在 **[作業摘要]** 圖格中。 作業狀態一開始會顯示為 [已排入佇列]  ，表示其正在等候雲端中的 Runbook 背景工作變為可用狀態。 然後當背景工作宣告該工作時，狀態將變更為 [正在開始]，然後 Runbook 實際開始執行時再變更為 [執行中]。  Runbook 作業完成時，我們應該會看到 [完成] 狀態。

若要查看 Runbook 的詳細結果，請按一下 [輸出]  圖格。  在 [輸出] 刀鋒視窗上，您應會看到它已驗證成功並傳回您訂用帳戶中部署的所有 Azure VM 清單 (依 VMName 列出)。  

當您對 Runbook 重複使用程式碼時，請記得移除 **Get-AzureVM** Cmdlet。

## <a name="next-steps"></a>後續步驟
* 若要開始使用 PowerShell Runbook，請參閱[我的第一個 PowerShell Runbook](automation-first-runbook-textual-powershell.md)。
* 若要深入了解圖形化編寫，請參閱 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)。
