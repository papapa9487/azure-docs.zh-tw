---
title: "移轉自動化帳戶和資源 | Microsoft Docs"
description: "本文章說明如何將 Azure 自動化中的自動化帳戶和相關聯的資源，從某一個訂用帳戶移到另一個訂用帳戶。"
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 9c2db4a2-f324-48dc-8ce7-3343bf7230d5
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: 6a6765753e0dadf31692819a9d8f2ca7e77c7f10
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-automation-account-and-resources"></a>移轉自動化帳戶和資源
對於您在 Azure 入口網站中建立並且想要在資源群組或訂用帳戶之間移轉的自動化帳戶及其相關資源 (亦即，資產、Runbook、模組等)，您可以使用 Azure 入口網站中可用的[移動資源](../azure-resource-manager/resource-group-move-resources.md)功能，輕鬆完成這項作業。 不過，在繼續此動作之前，您應該先檢閱下列[移動資源之前的檢查清單](../azure-resource-manager/resource-group-move-resources.md#checklist-before-moving-resources)，還有以下特別關於自動化的清單。   

1. 目的地訂用帳戶/資源群組必須位於與來源相同的區域。  這表示，無法跨區域移動自動化帳戶。
2. 移動資源 (如 Runbook、作業等) 時，會在作業期間鎖定來源群組和目標群組。 群組上的寫入和刪除作業將會封鎖，直到移動完成。  
3. 在移轉完成之後，應該更新任何參考現有訂用帳戶中資源或訂用帳戶識別碼的 Runbook 或變數。   

> [!NOTE]
> 這項功能不支援移動傳統自動化資源。
>
>

## <a name="to-move-the-automation-account-using-the-portal"></a>使用入口網站移動自動化帳戶
1. 從您的自動化帳戶，按一下頁面頂端的 [移動]。<br> ![移動選項](media/automation-migrate-account-subscription/automation-menu-move.png)<br>
2. 在 [移動資源] 窗格上，請注意會顯示與您的自動化帳戶和資源群組相關的資源。  從下拉式清單中選取 [訂用帳戶] 和 [資源群組] ，或選取 [建立新的資源群組] 選項並且在提供的欄位中輸入新的資源群組名稱。  
3. 檢閱並選取核取方塊，以確認您「了解工具和指令碼需要更新，才能在移動資源後使用新的資源識別碼」，然後按一下確定。<br> ![移動資源頁面](media/automation-migrate-account-subscription/automation-move-resources-blade.png)<br>   

此動作需要幾分鐘時間才能完成。  在 [通知] 中，您會看到所進行的每個動作 (驗證、移轉) 的狀態，然後最後是完成的時間。     

## <a name="to-move-the-automation-account-using-powershell"></a>使用 PowerShell 移動自動化帳戶
若要將現有的自動化資源移至另一個資源群組或訂用帳戶，請使用 **Get-AzureRmResource** Cmdlet 來取得特定的自動化帳戶，然後使用 **Move-AzureRmResource** Cmdlet 來執行移動。

第一個範例示範如何將自動化帳戶移到新的資源群組。

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup"
   ```

執行上述程式碼範例之後，系統會提示您確認您要執行此動作。  一旦按一下 [是]  並允許繼續執行指令碼，您就不會在執行移轉時收到任何通知。  

若要移動到新的訂用帳戶，請為 *DestinationSubscriptionId* 參數設定某個值。

   ```
    $resource = Get-AzureRmResource -ResourceName "TestAutomationAccount" -ResourceGroupName "ResourceGroup01"
    Move-AzureRmResource -ResourceId $resource.ResourceId -DestinationResourceGroupName "NewResourceGroup" -DestinationSubscriptionId "SubscriptionId"
   ```

如同上述範例，系統會提示您確認移動。  

## <a name="next-steps"></a>後續步驟
* 如需將資源移到新的資源群組或訂用帳戶的詳細資訊，請參閱[將資源移到新的資源群組或訂用帳戶](../azure-resource-manager/resource-group-move-resources.md)
* 如需 Azure 自動化中角色型存取控制的詳細資訊，請參閱 [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)。
* 若要了解用於管理訂用帳戶的 PowerShell Cmdlet，請參閱 [搭配使用 Azure PowerShell 與 Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* 若要了解用於管理訂用帳戶的入口網站功能，請參閱 [使用 Azure 入口網站來管理資源](../azure-resource-manager/resource-group-portal.md)。
