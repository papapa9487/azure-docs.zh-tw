---
title: "移除 Azure 自動化混合式 Runbook 背景工作 | Microsoft Docs"
description: "本文提供有關管理已部署之 Azure 自動化混合式 Runbook 背景工作角色的資訊，它可讓您在本機資料中心或雲端環境內的電腦上執行 Runbook。"
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: bf4f7befa4a66b739e72d0ea3d632b95f9b31c47
ms.contentlocale: zh-tw
ms.lasthandoff: 09/13/2017

---

# <a name="remove-azure-automation-hybrid-runbook-workers"></a>移除 Azure 自動化混合式 Runbook 背景工作角色

您可以 

## <a name="removing-hybrid-runbook-worker"></a>移除 Hybrid Runbook Worker

您可以移除群組中的一或多個 Hybrid Runbook 背景工作角色，或移除該群組，視您的需求而定。  若要從內部部署電腦中移除 Hybrid Runbook Worker，請執行下列步驟。

1. 在 Azure 入口網站中，瀏覽至您的自動化帳戶。  
2. 從 [設定] 刀鋒視窗中，選取 [金鑰]，記下欄位 [URL] 和 [主要存取金鑰] 的值。  下一個步驟需要此資訊。
3. 在系統管理員模式中開啟 PowerShell 工作階段，並執行下列命令 - `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`。  使用 **-Verbose** 參數可取得移除程序的詳細記錄。

> [!NOTE]
> 這不會移除電腦上的 Microsoft Monitoring Agent，只會移除 Hybrid Runbook 背景工作角色的功能和組態。  

## <a name="remove-hybrid-worker-groups"></a>移除混合式背景工作角色群組

若要移除群組，您必須先使用先前所示的程序，從群組的每一部成員電腦中移除 Hybrid Runbook Worker，然後執行下列步驟移除群組。  

1. 在 Azure 入口網站中，開啟自動化帳戶。
1. 選取 [Hybrid 背景工作角色] 刀鋒視窗中的 [Hybrid 背景工作角色群組] 圖格，然後選取要刪除的群組。  選取特定的群組之後，[Hybrid 背景工作角色群組] 屬性刀鋒視窗隨即出現。<br> ![Hybrid Runbook 背景工作角色群組刀鋒視窗](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
1. 在所選群組的屬性刀鋒視窗中，按一下 [刪除]。  將會出現一則訊息要求您確認此動作，如果您確定要繼續，請選取 [是]。<br> ![建立群組確認對話方塊](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> 此程序需要幾秒鐘才能完成，您可以在功能表的 [通知] 底下追蹤其進度。 

## <a name="next-steps"></a>後續步驟

* 請檢閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)，以了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化。
* 如需安裝 Windows 混合式 Runbook 背景工作角色的相關資訊，請參閱 [Azure 自動化 Windows 混合式 Runbook 背景工作角色](automation-windows-hrw-install.md)。
* 如需安裝 Linux 混合式 Runbook 背景工作角色的相關資訊，請參閱 [Azure 自動化 Linux 混合式 Runbook 背景工作角色](automation-linux-hrw-install.md)。
