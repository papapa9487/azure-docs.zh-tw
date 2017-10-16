---
title: "在 Azure Stack 中建立供應項目 | Microsoft Docs"
description: "身為雲端系統管理員，您應該了解如何在 Azure Stack 中為使用者建立供應項目。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 96b080a4-a9a5-407c-ba54-111de2413d59
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: erikje
ms.openlocfilehash: 269a6106f657536ba74be366f842b2f9cd86c5dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-offer-in-azure-stack"></a>在 Azure Stack 中建立優惠

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

[供應項目](azure-stack-key-features.md)是一組提供者供使用者購買或訂閱的一或多個方案。 此文件將說明如何建立一個包含您在上一個步驟中[所建立方案](azure-stack-create-plan.md)的供應項目。 此供應項目可讓訂閱者佈建虛擬機器。

1. 登入 Azure Stack 系統管理員入口網站 (https://adminportal.local.azurestack.external) > 按一下 [新增] > [租用戶供應項目 + 方案] > [供應項目]。

   ![](media/azure-stack-create-offer/image01.png)
2. 在 [新增供應項目] 刀鋒視窗中，填寫 [顯示名稱] 與 [資源名稱]，然後選取新的或現有的 [資源群組]。 「顯示名稱」是供應項目的易記名稱，而且是使用者訂閱供應項目時唯一會看到的資訊。 因此，請務必使用可協助使用者了解該供應項目附帶內容的直覺式名稱。 只有系統管理員可以看到「資源名稱」。 它是系統管理員用來處理其他供應項目 (以 Azure Resource Manager 資源方式) 的名稱。

   ![](media/azure-stack-create-offer/image01a.png)
3. 按一下 基本方案，在 方案 刀鋒視窗中選取要包含在供應項目中的方案，然後按一下選取。 按一下 [建立]  來建立優惠。

   ![](media/azure-stack-create-offer/image02.png)
4. 按一下 所有資源，搜尋您的新供應項目，按一下該新供應項目、按一下 變更狀態，然後按一下公用。

   ![](media/azure-stack-create-offer/image03.png)

供應項目必須設定為公用，使用者才能在訂閱時取得完整的檢視。 供應項目可以是：

* **公用**：使用者可以看到。
* **私用**：只有雲端系統管理員可以看到。 在草擬方案或供應項目時，或雲端系統管理員想要核准每個訂用帳戶時，會相當實用。
* **已解除**：不開放給新的訂閱者。 雲端系統管理員可以使用「已解除」來避免之後的訂用帳戶使用，但不會影響目前的訂閱者。

對供應項目所做的變更不會立即對使用者顯示。 建立資源/資源群組時，若要查看變更，您可能必須登出/登入，才能在「訂用帳戶選擇器」中看到新的訂用帳戶。

> [!NOTE]
>您也可以如 [Azure Stack 服務管理員讀我檔案](https://github.com/Azure/AzureStack-Tools/tree/master/ServiceAdmin) \(英文\) 所述，使用 PowerShell 來建立預設的供應項目、方案及配額。
>


### <a name="next-steps"></a>後續步驟
[訂閱優惠然後佈建 VM](azure-stack-subscribe-plan-provision-vm.md)
