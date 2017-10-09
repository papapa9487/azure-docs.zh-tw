---
title: "使用 Visual Studio 部署 Azure Stack 中的範本 | Microsoft Docs"
description: "了解如何以 Visual Studio 部署 Azure Stack 中的範本。"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 8fc32dc50d96d202dfc982cbdc52d8e479c3a3eb
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>使用 Visual Studio 部署 Azure Stack 中的範本

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

使用 Visual Studio 將 Azure Resource Manager 範本部署到 Azure Stack 開發套件。

1. 使用 Visual Studio [安裝並連接](azure-stack-install-visual-studio.md)至 Azure Stack。
2. 開啟 Visual Studio。
3. 依序按一下 [檔案]、[新增]，在 [新增專案] 對話方塊中，按一下 [Azure 資源群組]。
4. 輸入新專案的**名稱**，然後按一下確定。
5. 在 [選取 Azure 範本] 對話方塊中，將 [顯示此位置的範本] 下拉式清單變更為 [Azure Stack 快速入門]
6. 按一下 [101-建立-儲存體-帳戶]，然後再按一下 [確定]。  
7. 在您的新專案中，展開 [方案總管] 窗格中的 [範本] 節點，可以看到可用的範本清單。
8. 在 [方案總管] 窗格中，以滑鼠右鍵按一下專案名稱，按一下 [部署]，然後按一下 [新增部署]。
9. 在 [部署至資源群組] 對話方塊中，於 [訂用帳戶] 下拉式清單中，選取您的 Microsoft Azure Stack 訂用帳戶。
10. 在 [資源群組] 清單中，選擇現有資源群組或建立新群組。
11. 在 資源群組位置 清單中，選擇某個位置，然後按一下部署。
12. 在 編輯參數 對話方塊中，輸入參數值 (依範本而異)，然後按一下儲存。

## <a name="next-steps"></a>後續步驟
[使用命令列部署範本](azure-stack-deploy-template-command-line.md)

[開發適用於 Azure Stack 的範本](azure-stack-develop-templates.md)


