---
title: "移動 Azure 中的 Linux VM | Microsoft Docs"
description: "在 Resource Manager 部署模型中將 Linux VM 移至另一個 Azure 訂用帳戶或資源群組。"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d635f0a5-4458-4b95-a5f8-eed4f41eb4d4
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
ms.openlocfilehash: 4695a9c934f97f2b2d448c4990e7ad5533e38e9f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>將 Linux VM 移至另一個訂用帳戶或資源群組
本文將逐步引導您了解如何在資源群組或訂用帳戶之間移動 Linux VM。 如果您在個人訂用帳戶中建立了 VM，而現在想要將它移至您的公司訂用帳戶以繼續工作，在訂用帳戶之間移動 VM 會很方便。

> [!IMPORTANT]
>此時，您無法移動受控磁碟。 
>
>移動過程中會建立新的資源識別碼。 移動 VM 之後，您必須更新工具和指令碼以使用新的資源識別碼。 
> 
> 

## <a name="use-the-azure-cli-to-move-a-vm"></a>使用 Azure CLI 移動 VM
若要成功移動 VM，您需要移動 VM 及其所有支援的資源。 使用 **azure group show** 命令來列出資源群組中的所有資源及其識別碼。 它有助於將此命令的輸出透過管線送至檔案，以便您將識別碼複製並貼到稍後的命令中。

    azure group show <resourceGroupName>

若要將 VM 與其資源移到另一個資源群組，請使用 **azure resource move** CLI 命令。 下列範例說明如何移動 VM 與其所需的大多數常見資源。 我們使用 **-i** 參數，並針對要移動的資源傳入以逗號分隔的識別碼清單 (不含空格)。

    vm=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Compute/virtualMachines/<vmName>
    nic=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkInterfaces/<nicName>
    nsg=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkSecurityGroups/<nsgName>
    pip=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/publicIPAddresses/<publicIPName>
    vnet=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/virtualNetworks/<vnetName>
    diag=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<diagnosticStorageAccountName>
    storage=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<storageAcountName>      

    azure resource move --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag -d "<destinationResourceGroup>"

如果您想要將 VM 及其資源移至不同的訂用帳戶，請加入 **--destination-subscriptionId &#60;destinationSubscriptionID&#62;** 參數以指定目的地訂用帳戶。

如果您從 Windows 電腦的命令提示字元作業，您必須於宣告變數名稱時在其前面加上 **$** 。 在 Linux 中不需要這麼做。

系統會要求您確認您想要移動指定的資源。 輸入 **Y** 確認您要移除資源。

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>後續步驟
您可以在資源群組和訂用帳戶之間移動許多不同類型的資源。 如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](../../resource-group-move-resources.md)。    

