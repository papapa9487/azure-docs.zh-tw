---
title: "檢視及使用虛擬機器的 Azure Resource Manager 範本 | Microsoft Docs"
description: "了解如何從虛擬機器使用 Azure Resource Manager 範本來建立其他 VM"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: tarcher
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 0807ab367b91be5acd261f2b58ca2112b2c9e380
ms.contentlocale: zh-tw
ms.lasthandoff: 09/09/2017

---

# <a name="use-a-virtual-machines-azure-resource-manager-template"></a>使用虛擬機器的 Azure Resource Manager 範本

透過 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)在 DevTest Labs 中建立虛擬機器 (VM) 時，您可以在儲存 VM 之前檢視 Azure Resource Manager 範本。 然後可以使用範本作為基礎來建立具有相同設定的多個實驗室 VM。

本文說明如何在建立 VM 時檢視 Resource Manager 範本，以及稍後如何部署它以自動化建立相同 VM。

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>多部 VM 與單一 VM Resource Manager 範本
有兩種方式可以在使用Resource Manager 範本，在 DevTest Labs 中建立 VM：佈建 Microsoft.DevTestLab/labs/virtualmachines 資源或佈建 Microsoft.Commpute/virtualmachines 資源。 每種方式在不同案例中使用，而且需要不同的權限。

- 使用 Microsoft.DevTestLab/labs/virtualmachines 資源類型 (在範本的 “resource” 屬性中宣告) 的 Resource Manager 範本可以佈建個別實驗室 VM。 然後每個 VM 會顯示為 DevTest Labs 虛擬機器清單中的單一項目：

   ![DevTest Labs 虛擬機器清單中單一項目之 VM 的清單](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   這個類型的 Resource Manager 範本可以透過 Azure PowerShell 命令 **New-AzureRmResourceGroupDeployment**，或透過 Azure CLI 命令 **az group deployment create** 進行佈建。 它需要系統管理員權限，所以獲得 DevTest Labs 使用者角色指派的使用者無法執行部署。 

- 使用 Microsoft.Compute/virtualmachines 資源類型的 Resource Manager 範本可以將多個 VM 佈建為 DevTest Labs 虛擬機器清單中的單一環境：

   ![DevTest Labs 虛擬機器清單中單一項目之 VM 的清單](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   在相同環境中的 VM 可以一起管理，並且共用相同生命週期。 只要系統管理員已對實驗室進行相關設定，獲得 DevTest Labs 使用者角色指派的使用者就可以使用這些範本來建立環境。

本文的其餘部分討論使用 Mirosoft.DevTestLab/labs/virtualmachines 的 Resource Manager 範本。 這些範本是由實驗室管理員用來自動化實驗室 VM 建立 (例如，可宣告 VM) 或黃金映像產生 (例如，映像工廠)。

[建立 Azure Resource Manager 範本的最佳做法](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)提供許多指導方針和建議，可以協助您建立可靠又容易使用的 Azure Resource Manager 範本。

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>檢視及儲存虛擬機器的 Resource Manager 範本
1. 請依照[在實驗室中建立您的第一個 VM](devtest-lab-create-first-vm.md) 中的步驟，開始建立虛擬機器。
1. 輸入虛擬機器的必要資訊，並且新增您想要用於此 VM 的任何成品。
1. 在 [設定] 設定視窗底部，選擇 [檢視 ARM 範本]。

   ![檢視 ARM 範本按鈕](./media/devtest-lab-use-arm-template/devtestlab-lab-view-rm-template.png)
1. 複製及儲存 Resource Manager 範本，以便稍後用來建立另一個虛擬機器。

   ![儲存以供稍後使用的 Resource Manager 範本](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

儲存 Resource Manager 範本之後，您必須更新範本的參數區段，才能使用它。 您可以建立 parameter.json，它只會自訂實際 Resource Manager 範本外部的參數。 

![使用 JSON 檔案自訂參數](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

## <a name="deploy-a-resource-manager-template-to-create-a-vm"></a>部署 Resource Manager 範本以建立 VM
儲存 Resource Manager 範本並且針對需求自訂之後，您可以使用它來自動化 VM 建立。 [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)說明如何使用 Azure PowerShell 與 Resource Manager 範本以將資源部署至 Azure。 [使用 Resource Manager 範本與 Azure CLI 來部署資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)說明如何使用 Azure CLI 與 Resource Manager 範本以將資源部署至 Azure。

> [!NOTE]
> 只有具備實驗室擁有者權限的使用者才可以使用 Azure PowerShell 從 Resource Manager 範本建立 VM。 如果您想要使用 Resource Manager 範本自動化 VM 建立，而且您只有使用者權限，您可以使用 [CLI 中的 **az lab vm create** 命令](https://docs.microsoft.com/cli/azure/lab/vm#az_lab_vm_create)。

### <a name="next-steps"></a>後續步驟
* 了解如何[使用 Resource Manager 範本建立多個 VM 環境](devtest-lab-create-environment-from-arm.md)。
* 從[公用 DevTest Labs GitHub 存放庫](https://github.com/Azure/azure-quickstart-templates)探索 DevTest Labs 的快速入門 Resource Manager 範本。

