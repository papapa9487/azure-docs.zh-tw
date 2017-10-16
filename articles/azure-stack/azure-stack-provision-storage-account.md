---
title: "Azure Stack 中的儲存體帳戶 | Microsoft Docs"
description: "了解如何建立 Azure Stack 儲存體帳戶。"
services: azure-stack
documentationcenter: 
author: vhorne
manager: byronr
editor: 
ms.assetid: e1152110-b756-4c1a-9fa2-73fe3ab0ad8e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/1/2017
ms.author: victorh
ms.openlocfilehash: 41c9ee37c43d4ad41c51ea2ed023d3b47d460dd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="storage-accounts-in-azure-stack"></a>Azure Stack 中的儲存體帳戶
儲存體帳戶包含 Blob 與資料表服務，且儲存體資料物件的命名空間不得重複。 根據預設，您帳戶中的資料只有身為儲存體帳戶擁有者的您可以使用。

1. 在 Azure Stack POC 電腦上，以[系統管理員](azure-stack-connect-azure-stack.md)身分登入 `https://adminportal.local.azurestack.external`，然後按一下新增] > [資料 + 儲存體] > [儲存體帳戶]。

   ![](media/azure-stack-provision-storage-account/image01.png)
2. 在 [建立儲存體帳戶] 刀鋒視窗中，輸入儲存體帳戶的名稱。 建立新的 [資源群組]，或選取現有的資源群組，然後按一下 [建立]，以建立儲存體帳戶。

   ![](media/azure-stack-provision-storage-account/image02.png)
3. 若要查看您的新儲存體帳戶，請按一下 [所有資源]，然後搜尋儲存體帳戶並按一下其名稱。

    ![](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>後續步驟
[使用 Azure Resource Manager 範本](user/azure-stack-arm-templates.md)

[深入了解 Azure 儲存體帳戶](../storage/common/storage-create-storage-account.md)

[下載 Azure Stack 與 Azure 一致的儲存體驗證指南](http://aka.ms/azurestacktp1doc) \(英文\)
