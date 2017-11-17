---
title: "使用 Azure Stack 原則模組 | Microsoft Docs"
description: "瞭解如何將 Azure 訂用帳戶的行為限制為與 Azure Stack 訂用帳戶類似"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: sngun
ms.openlocfilehash: 030cb837dd2082f472275bcc42dbd39b7f8b30c0
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2017
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>使用 Azure Stack 原則模組管理 Azure 原則

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

Azure Stack 原則模組可讓您將 Azure 訂用帳戶，設定與 Azure Stack 具有相同的版本和服務可用性。  此模組會使用 **New-AzureRMPolicyAssignment** cmdlet 來建立 Azure 原則，限制訂用帳戶中可用的資源類型和服務。  完成後，您可以使用您的 Azure 訂用帳戶來開發 Azure Stack 的目標應用程式。  

## <a name="install-the-module"></a>安裝模組
1. 依據[安裝 Azure Stack 的 PowerShell](azure-stack-powershell-install.md) 步驟 1 的說明，安裝必要的 AzureRM PowerShell 模組版本。   
2. [從 GitHub 下載 Azure Stack 工具](azure-stack-powershell-download.md)  
3. [設定 PowerShell 以便搭配 Azure Stack 使用](azure-stack-powershell-configure-user.md)

4. 匯入 AzureStack.Policy.psm1 模組：

   ```PowerShell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-subscription"></a>將原則套用至訂用帳戶
下列命令可針對您的 Azure 訂用帳戶來套用預設 Azure Stack 原則。 執行前，以您的 Azure 訂用帳戶取代 *Azure 訂用帳戶名稱*。

```PowerShell
Login-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
$rgName = 'AzureStack'
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>將原則套用至資源群組
您可能想要以更細微的方式套用原則。  例如，您在相同的訂用帳戶中可能有其他正在執行的資源。  您可以將原則應用程式領域套用至特定的資源群組，可讓您使用 Azure 資源測試 Azure Stack 的應用程式。 執行前，以您的 Azure 訂用帳戶名稱取代 *Azure 訂用帳戶名稱*。

```PowerShell
Login-AzureRmAccount
$resourceGroupName = ‘myRG01’
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName

```

## <a name="policy-in-action"></a>動作中的原則
部署 Azure 原則後，當您嘗試部署原則禁止的資源時會收到錯誤。  

![資源部署結果因原則限制而失敗](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>後續步驟
[使用 PowerShell 部署範本](azure-stack-deploy-template-powershell.md)

[使用 Azure CLI 部署範本](azure-stack-deploy-template-command-line.md)

[使用 Visual Studio 部署範本](azure-stack-deploy-template-visual-studio.md)
