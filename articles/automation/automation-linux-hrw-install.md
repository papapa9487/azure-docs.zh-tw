---
title: "Azure 自動化 Linux 混合式 Runbook 背景工作角色 | Microsoft Docs"
description: "本文提供有關安裝 Azure 自動化混合式 Runbook 背景工作角色的資訊，它可讓您在本機資料中心或雲端環境內的 Linux 電腦上執行 Runbook。"
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 88c052c3a22611b796559d4dd62c763445aa6210
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>如何部署 Linux 混合式 Runbook 背景工作角色
Azure 自動化中的 Runbook 無法存取其他雲端或內部部署環境中的資源，因為其在 Azure 雲端中執行。  Azure 自動化的混合式 Runbook 背景工作角色功能可讓您直接在裝載角色的電腦上，以及針對環境中的資源執行 Runbook，從而管理這些本機資源。 Runbook 會儲存並在 Azure 自動化中管理，接著傳遞至一或多個指定的電腦。  

下圖說明這項功能：<br>  

![混合式 Runbook 背景工作概觀](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

如需混合式 Runbook 背景工作角色的技術概觀，請參閱[自動化架構概觀](automation-offering-get-started.md#automation-architecture-overview)。 請檢閱下列有關[硬體和軟體需求](automation-offering-get-started.md#hybrid-runbook-worker)和[的資訊，在開始部署混合式 Runbook 背景工作角色之前準備您的網路](automation-offering-get-started.md#network-planning)。  在您成功部署 Runbook 背景工作角色後，請檢閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)，以了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化。     

## <a name="hybrid-runbook-worker-groups"></a>混合式 Runbook 背景工作群組
每一個混合式 Runbook 背景工作是您安裝代理程式時指定的混合式 Runbook 背景工作群組的成員。  群組可包含單一代理程式，但您可以在群組中安裝多個代理程式以獲得高可用性。

在 Hybrid Runbook Worker 上啟動 Runbook 時，您會指定要執行它的群組。  群組的成員決定由哪一個背景工作角色處理要求。  您無法指定特定的背景工作。

## <a name="installing-linux-hybrid-runbook-worker"></a>安裝 Linux 混合式 Runbook 背景工作角色
若要在 Linux 電腦上安裝和設定混合式 Runbook 背景工作角色，您可遵循非常直觀了然的程序手動安裝和設定此角色。   您必須在 OMS 工作區中啟用 [自動化混合式背景工作角色] 解決方案，然後執行一組命令將電腦註冊為背景工作角色，再將它新增至新的或現有的群組。 

繼續之前，您必須記下與自動化帳戶連結的 Log Analytics 工作區，以及自動化帳戶的主索引鍵。  您只要選取您的自動化帳戶，再針對工作區識別碼選取 [工作區]，針對主索引鍵選取 [索引鍵]，即可在入口網站中找到這兩項資訊。  

1.  在 OMS 中啟用 [自動化混合式背景工作角色] 解決方案。 執行方式可以是下列任一項：

   1. 從 [OMS 入口網站](https://mms.microsoft.com)中的 [方案庫]，啟用 [自動化混合式背景工作角色] 解決方案
   2. 執行下列 Cmdlet：

        ```powershell
         $null = Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

2.  在 Linux 電腦上執行以下命令，變更參數 *-w* 和 *-k* 的值。
    
    ```
    sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <OMSworkspaceId> -k <AutomationSharedKey> --groupname <hybridgroupname> -e <automationendpoint>
    ```
3. 命令完成之後，Azure 入口網站中的 [混合式背景工作角色群組] 刀鋒視窗會顯示新的群組和成員數目，或者若是現有的群組，則成員數目會遞增。  您可以在 [Hybrid Worker 群組] 刀鋒視窗從清單中選取群組，然後選取 [Hybrid Worker] 圖格。  在 [Hybrid Worker] 刀鋒視窗上，您會看到列出群組的每個成員。  

## <a name="next-steps"></a>後續步驟

* 請檢閱[在混合式 Runbook 背景工作角色上執行 Runbook](automation-hrw-run-runbooks.md)，以了解如何設定您的 Runbook，將您在內部部署資料中心或其他雲端環境中的程序自動化。
* 如需移除混合式 Runbook 背景工作角色的指示，請參閱[移除 Azure 自動化混合式 Runbook 背景工作角色](automation-remove-hrw.md)