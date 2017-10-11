---
title: "在 Azure DevTest Labs 中對實驗室新增 VM | Microsoft Docs"
description: "了解如何在 Azure DevTest Labs 中對實驗室新增虛擬機器"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: tarcher
ms.openlocfilehash: 449bffb040dafc8edd0b8b0afd80dbea35cd28ac
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="add-a-vm-to-a-lab-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中對實驗室新增 VM
如果您已經[建立您的第一個 VM](devtest-lab-create-first-vm.md)，您很有可能是透過預先載入的 [Marketplace 映像](devtest-lab-configure-marketplace-images.md)來完成的。 現在，如果您想要將後續的 VM 新增至您的實驗室，您也可以選擇一個「基底」，它可以是[自訂映像](devtest-lab-create-template.md)或[公式](devtest-lab-manage-formulas.md)。 本教學課程會逐步引導您使用 Azure 入口網站，在 DevTest Labs 中對實驗室新增 VM。

本文同時說明如何在實驗室中管理 VM 的構件。

## <a name="steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中對實驗室新增 VM 的步驟
1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。
1. 從實驗室清單中，選取您想要在其中建立 VM 的實驗室。  
1. 在實驗室的 [概觀] 刀鋒視窗中，選取 [+ 新增]。  

    ![加入 VM 按鈕](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)

1. 在 [選擇基底]  刀鋒視窗中，選取 VM 的基底。
1. 在 [虛擬機器] 刀鋒視窗的 [虛擬機器名稱] 文字方塊中，輸入新虛擬機器的名稱。

    ![實驗室 VM 刀鋒視窗](./media/devtest-lab-add-vm/devtestlab-lab-vm-blade.png)

1. 輸入**使用者名稱**，此名稱會被授與虛擬機器上的系統管理員權限。  
1. 如果您想使用儲存在[密碼存放區](https://azure.microsoft.com/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store)中的密碼，請選取 [使用儲存的密碼]，並指定與密碼對應的金鑰值。 否則，請在標示為 [輸入值] 的文字欄位中輸入密碼。
1. [虛擬機器磁碟類型] 會決定實驗室中的虛擬機器所允許的儲存磁碟類型。
1. 選取 [虛擬機器大小]  ，然後選取其中一個預先定義的項目，這些項目可以指定處理器核心、RAM 大小，以及要建立的 VM 的硬碟大小。
1. 選取 [構件]，然後從構件清單中，選取並設定您想要新增到基本映像中的構件。
    **附註：**如果您對 DevTest Labs 或設定構件並不熟悉，請參閱[將現有的構件加入至 VM](#add-an-existing-artifact-to-a-vm) 一節，完成該節之後再返回此處。
1. 選取 [進階設定] 以設定 VM 的網路選項和到期日選項。 

   若要設定到期選項，選擇行事曆圖示，以指定 VM 將會自動刪除的日期。  根據預設，VM 永遠不會到期。 
1. 如果您想要檢視或複製 Azure Resource Manager 範本，請參閱[儲存 Azure Resource Manager 範本](#save-azure-resource-manager-template)一節，然後在完成時回到這裡。
1. 選取 [建立]  ，將指定的 VM 加入實驗室。
1. 實驗室刀鋒視窗會顯示 VM 的建立狀態，其狀態會先是 [正在建立]，在啟動該 VM 之後才會變成 [正在執行]。

> [!NOTE]
> [新增可宣告 VM](devtest-lab-add-claimable-vm.md) 會示範如何允許宣告 VM，使它可供實驗室中的所有使用者使用。
>
>

## <a name="add-an-existing-artifact-to-a-vm"></a>將現有的構件加入至 VM
建立 VM 時，您可以加入現有的構件。 每個實驗室都會包括來自公用研發/測試實驗室構件儲存機制的構件，以及您已建立並加入至您自己之構件儲存機制的構件。

* Azure DevTest Labs「構件」可讓您指定會在 VM 佈建時執行的「動作」，例如執行 Windows PowerShell 指令碼、執行 Bash 命令，以及安裝軟體。
* 構件「參數」可讓您自訂適用於特定案例的構件

若要了解如何建立構件，請參閱 [了解如何撰寫您自己的構件以用於研發/測試實驗室](devtest-lab-artifact-author.md)文章。

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。
1. 從實驗室清單中，選取您想要使用之 VM 所在的實驗室。  
1. 選取 [我的虛擬機器]。
1. 選取所需的 VM。
1. 選取 [構件]。 
1. 選取 [套用構件]。
1. 在 [套用構件] 刀鋒視窗中，選取您要新增到 VM 的構件。
1. 在 [新增構件] 刀鋒視窗中，輸入必要的參數值以及任何您所需的選用參數。  
1. 選取 [新增] 以新增構件，然後返回 [套用構件] 刀鋒視窗。
1. 視需要繼續為您的 VM 加入構件。
1. 加入構件之後，您可以 [變更構件的執行順序](#change-the-order-in-which-artifacts-are-run)。 您也可以返回來 [檢視或修改構件](#view-or-modify-an-artifact)。
1. 當您新增好構件時，選取 [套用]

## <a name="change-the-order-in-which-artifacts-are-run"></a>變更構件的執行順序
根據預設，構件的動作是依照它們加入 VM 的順序來執行。 下列步驟說明如何變更構件的執行順序。

1. 在 [套用構件] 刀鋒視窗頂端，選取會指出已新增至 VM 之構件數目的連結。
   
    ![新增至 VM 的構件數目](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. 在 [選取的構件] 刀鋒視窗中，將構件拖放到所需的順序。 **附註︰**如果您在拖曳構件時發生問題，請確定您是從構件左側進行拖曳。 
1. 完成時選取 [確定]  。  

## <a name="view-or-modify-an-artifact"></a>檢視或修改構件
下列步驟說明如何檢視或修改構件的參數︰

1. 在 [套用構件] 刀鋒視窗頂端，選取會指出已新增至 VM 之構件數目的連結。
   
    ![新增至 VM 的構件數目](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. 在 [選取的構件] 刀鋒視窗中，選取您想要檢視或編輯的構件。  
1. 在 [新增構件] 刀鋒視窗中，進行任何所需的變更，然後選取 [確定] 以關閉 [新增構件] 刀鋒視窗 。
1. 選取 [確定] 以關閉 [選取的構件] 刀鋒視窗。

## <a name="save-azure-resource-manager-template"></a>儲存 Azure Resource Manager 範本
Azure Resource Manager 範本提供宣告式方法來定義可重複的部署。 下列步驟說明如何為建立的 VM 儲存 Azure Resource Manager 範本。
儲存之後，您便可以使用 Azure Resource Manager 範本[透過 Azure PowerShell 部署新的 VM](../azure-resource-manager/resource-group-overview.md#template-deployment)。

1. 在 [虛擬機器] 刀鋒視窗上，選取 [檢視 ARM 範本]。
2. 在 [檢視 Azure Resource Manager 範本] 刀鋒視窗中，選取範本文字。
3. 將選取的文字複製到剪貼簿。
4. 選取 [確定] 以關閉 [檢視 Azure Resource Manager 範本] 刀鋒視窗。
5. 開啟文字編輯器。
6. 從剪貼簿貼上範本文字。
7. 儲存檔案供稍後使用。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>後續步驟
* 一旦建立 VM 之後，您可以選取 VM 刀鋒視窗上的 [連接]  來連接至 VM。
* 了解如何 [為您的 DevTest Labs VM 建立自訂構件](devtest-lab-artifact-author.md)。
* 瀏覽 [DevTest Labs Azure Resource Manager 快速入門範本資源庫 (英文)](https://github.com/Azure/azure-devtestlab/tree/master/Samples)。
