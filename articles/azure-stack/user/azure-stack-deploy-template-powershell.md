---
title: "以 PowerShell 部署 Azure Stack 中的範本 | Microsoft Docs"
description: "了解如何使用 Resource Manager 範本和 PowerShell 部署虛擬機器。"
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: e4837be016b569dbd0b4bf8e071e6381b8daa85f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-templates-in-azure-stack-using-powershell"></a>使用 PowerShell 部署 Azure Stack 中的範本

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

使用 PowerShell 將 Azure Resource Manager 範本部署到 Azure Stack 開發套件。  Resource Manager 範本可藉由單一協調作業，來部署和佈建應用程式的所有資源。

## <a name="run-azurerm-powershell-cmdlets"></a>執行 AzureRM PowerShell Cmdlet
在此範例中，您將會執行指令碼，使用 Resource Manager 範本將虛擬機器部署至 Azure Stack 開發套件。  在您繼續之前，請確保您已 [設定 PowerShell](azure-stack-powershell-configure-user.md)  

在此範例範本中使用的 VHD 是 WindowsServer-2012-R2-Datacenter。

1. 前往 <http://aka.ms/AzureStackGitHub>，搜尋 **101-simple-windows-vm** 範本，讓後將其儲存到下列位置：c:\\templates\\azuredeploy-101-simple-windows-vm.json。
2. 在 PowerShell 中，執行下列的部署指令碼。 以您的使用者名稱和密碼來取代「username」和「password」。 後續使用時，遞增「$myNum」參數的值，避免覆寫您的部署。
   
   ```PowerShell
       # Set Deployment Variables
       $myNum = "001" #Modify this per deployment
       $RGName = "myRG$myNum"
       $myLocation = "local"
   
       # Create Resource Group for Template Deployment
       New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
       # Deploy Simple IaaS Template
       New-AzureRmResourceGroupDeployment `
           -Name myDeployment$myNum `
           -ResourceGroupName $RGName `
           -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
           -NewStorageAccountName mystorage$myNum `
           -DnsNameForPublicIP mydns$myNum `
           -AdminUsername <username> `
           -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
           -VmName myVM$myNum `
           -WindowsOSVersion 2012-R2-Datacenter
   ```
3. 開啟 Azure Stack 入口網站，依序按一下 [瀏覽]、[虛擬機器]，然後尋找您的新虛擬機器 (myDeployment001)。


## <a name="next-steps"></a>後續步驟
[使用 Visual Studio 部署範本](azure-stack-deploy-template-visual-studio.md)

