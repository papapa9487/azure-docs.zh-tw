---
title: "訂閱供應項目 | Microsoft Docs"
description: "以租用戶身分了解如何訂閱供應項目。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/03/2017
ms.author: erikje
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 3cd87ebe9827249d32f15b5de0ad8521d0282c47
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---
# <a name="subscribe-to-an-offer"></a>訂閱供應項目
既然您已經[建立供應項目](azure-stack-create-offer.md)，接著應測試您的租用戶是否可以建立訂用帳戶。

1. [登入](azure-stack-connect-azure-stack.md) Azure Stack 租用戶入口網站 (https://portal.local.azurestack.external)，然後按一下 [取得訂用帳戶]。

   ![](media/azure-stack-subscribe-plan-provision-vm/image01.png)
2. 在 [顯示名稱] 欄位中，輸入您的訂用帳戶名稱、按一下 [供應項目]、按一下 [選擇供應項目] 刀鋒視窗中的其中一個供應項目，然後按一下 [建立]。

   ![](media/azure-stack-subscribe-plan-provision-vm/image02.png)
3. 若要檢視您建立的訂用帳戶，請按一下 [更多服務]、按一下 [訂用帳戶]，然後按一下您的新訂用帳戶。  

訂閱供應項目之後，請重新整理入口網站以查看哪些服務是新訂用帳戶的一部分。

## <a name="subscribe-to-an-add-on-plan"></a>訂閱附加方案
如果供應項目有附加方案，租用戶可以隨時將附加方案加入至其訂用帳戶。  

1. 在租用戶入口網站中，選取 [更多服務] > [訂用帳戶]。

2. 按一下訂用帳戶 > [新增方案] 按鈕，然後選取附加方案。



## <a name="next-steps"></a>後續步驟
[佈建虛擬機器](azure-stack-provision-vm.md)

