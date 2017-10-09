---
title: "在 Azure Stack 中建立測試 VM | Microsoft Docs"
description: "了解如何在 Azure Stack 中，以雲端操作員的身分佈建測試 VM。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: c86646e1-a12e-493f-b396-f17bfacd60c2
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: erikje
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 98770945d5cccc27bfce971491e6ea30fee2460a
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="create-a-test-virtual-machine-in-azure-stack"></a>在 Azure Stack 中建立測試虛擬機器

「適用於：Azure Stack 開發套件」

身為雲端操作員，您可以建立測試虛擬機器來驗證 [Azure Stack](azure-stack-poc.md) 部署。

> [!NOTE]
> 佈建虛擬機器之前，您必須[將 Windows Server 2016 評估版映像新增至 Azure Stack 市集](azure-stack-add-default-image.md)。
> 
> 

## <a name="create-a-virtual-machine"></a>建立虛擬機器
1. 在 Azure Stack 開發套件主機上，[登入](azure-stack-connect-azure-stack.md)系統管理員入口網站 (`https://adminportal.local.azurestack.external`)，然後按一下新增 > [計算] > [Windows Server 2016 Datacenter 評估版] > [建立]。  
2. 在 [基本] 刀鋒視窗中，輸入 [名稱]、[使用者名稱] 與 [密碼]。 選擇 [訂用帳戶] 。 建立 資源群組，或選取現有的資源群組，然後按一下確定。  
3. 在 [選擇大小] 刀鋒視窗中，按一下 [A1 標準]，再按一下 [選取]。  
4. 在 [設定] 刀鋒視窗中，接受預設值並按一下 [確定]
5. 在 [摘要] 刀鋒視窗中，按一下 [確定]，以建立虛擬機器。  
6. 若要查看您的新虛擬機器，請按一下 [所有資源]，然後搜尋虛擬機器並按一下其名稱。
    ![](media/azure-stack-provision-vm/image06.png)


## <a name="next-steps"></a>後續步驟
[使用 Azure Stack 中系統管理員和使用者的入口網站](azure-stack-manage-portals.md)

