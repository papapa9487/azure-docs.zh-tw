---
title: "在 Azure Stack 中建立方案 | Microsoft Docs"
description: "身為雲端系統管理員，您可以建立可供訂閱者佈建虛擬機器的方案。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: erikje
ms.openlocfilehash: 30759dca746fd7fd02653556cb105f419f5bf854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-plan-in-azure-stack"></a>在 Azure Stack 中建立方案

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

[方案](azure-stack-key-features.md)結合一或多項服務。 身為提供者的您可以為使用者製作方案。 使用者接著即可訂閱您的供應項目，以使用其中的方案與服務。 此範例說明如何建立一個包含計算、網路及儲存體資源提供者的方案。 此方案可讓訂閱者佈建虛擬機器。

1. 登入 Azure Stack 系統管理員入口網站 ( https://adminportal.local.azurestack.external )。 輸入您在[執行 PowerShell 指令碼](azure-stack-run-powershell-script.md)一節步驟 5 中所建立帳戶的認證。

2. 若要建立使用者可訂閱的方案和供應項目，請按一下 [新增] > [租用戶供應項目 + 方案] > [方案]。

   ![](media/azure-stack-create-plan/image01.png)
3. 在 [新的方案] 刀鋒視窗中，填寫 [顯示名稱] 與 [資源名稱]。 [顯示名稱] 是使用者看到的方案易記名稱。 只有系統管理員可以看到「資源名稱」。 它是系統管理員用來將方案當作 Azure Resource Manager 資源來使用時的名稱。

   ![](media/azure-stack-create-plan/image02.png)
4. 建立新的 [資源群組]或選取現有的資源群組，以作為方案的容器。

   ![](media/azure-stack-create-plan/image02a.png)
5. 按一下 [服務]，選取 [Microsoft.Compute]、[Microsoft.Network] 及 [Microsoft.Storage]，然後按一下 [選取]。

   ![](media/azure-stack-create-plan/image03.png)
6. 依序按一下 [配額]、[Microsoft.Storage (本機)]，然後選取預設配額，或按一下 [建立新的配額] 來自訂配額。

   ![](media/azure-stack-create-plan/image04.png)
7. 如果您要建立新配額，請輸入配額名稱 > 設定配額值 > 按一下 [確定] > 按一下新配額的名稱。

   ![](media/azure-stack-create-plan/image06.png)
8. 按一下 [Microsoft.Network (本機)]，然後選取預設配額，或按一下 [建立新的配額] 來自訂配額。

    ![](media/azure-stack-create-plan/image07.png)
9. 如果您要建立新配額，請輸入配額名稱 > 設定配額值 > 按一下 [確定] > 按一下新配額的名稱。

    ![](media/azure-stack-create-plan/image08.png)
10. 按一下 [Microsoft.Compute (本機)]，然後選取預設配額，或按一下 [建立新的配額] 來自訂配額。

    ![](media/azure-stack-create-plan/image09.png)
11. 如果您要建立新配額，請輸入配額名稱 > 設定配額值 > 按一下 [確定] > 按一下新配額的名稱。

    ![](media/azure-stack-create-plan/image10.png)
12. 在 [配額] 刀鋒視窗中，按一下 [確定]，然後在 [新的方案] 刀鋒視窗中，按一下 [建立] 來建立方案。

    ![](media/azure-stack-create-plan/image11.png)
13. 若要查看您的新方案，請按一下 [所有資源]，然後搜尋該方案並按一下其名稱。

    ![](media/azure-stack-create-plan/image12.png)

### <a name="next-steps"></a>後續步驟
[建立優惠](azure-stack-create-offer.md)
