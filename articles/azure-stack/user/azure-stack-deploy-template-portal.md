---
title: "以入口網站部署 Azure Stack 中的範本 | Microsoft Docs"
description: "了解如何使用 Azure Stack 入口網站部署範本。"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 630d86ed7e3558ae2c8a62f4e1a94f10ca11812d
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>使用 Azure Stack 入口網站部署範本

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

使用入口網站將 Azure Resource Manager 範本部署到 Azure Stack 開發套件。

Resource Manager 範本可藉由單一協調作業，來部署和佈建應用程式的所有資源。

1. 登入入口網站，依序按一下 [新增]、[自訂]，然後再按一下 [範本部署]。
2. 按一下 [編輯範本]，然後將您的 JSON 範本程式碼貼入刀鋒視窗中，再按一下 [儲存]。
3. 按一下 [編輯參數]，為所列出參數輸入值，然後再按一下 [確定]。
4. 按一下 訂用帳戶，選擇您需要的訂悅，然後按一下確定。
5. 按一下 [資源群組] ，選擇現有資源群組或建立新群組，然後再按一下 [確定]。
6. 按一下 [建立] 。 在儀表板上的新圖格會追蹤範本部署的進度。

## <a name="next-steps"></a>後續步驟
[使用 PowerShell 部署範本](azure-stack-deploy-template-powershell.md)


