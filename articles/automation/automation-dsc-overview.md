---
title: "Azure Automation DSC 概觀 | Microsoft Docs"
description: "Azure 自動化期望狀態組態 (DSC)、其條款和已知問題的概觀"
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
keywords: "powershell dsc, 需要的狀態組態, powershell dsc azure"
ms.assetid: fd40cb68-c1a6-48c3-bba2-710b607d1555
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 06/15/2017
ms.author: eslesar
ms.openlocfilehash: 468321fa6863d78bc0d179fbe5c2ed6195040d50
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="azure-automation-dsc-overview"></a>Azure 自動化 DSC 概觀

Azure Automation DSC 是一種 Azure 服務，可讓您撰寫、管理和編譯 PowerShell Desired State Configuration (DSC) [設定](https://msdn.microsoft.com/powershell/dsc/configurations)、匯入 [DSC 資源](https://msdn.microsoft.com/powershell/dsc/resources)，以及將設定指派給目標節點，而這些作業都是在雲端中進行。

## <a name="why-use-azure-automation-dsc"></a>為何使用 Azure Automation DSC

Azure Automation DSC 提供在 Azure 外部使用 DSC 的數個優點。

### <a name="built-in-pull-server"></a>內建提取伺服器

Azure 自動化提供 [DSC 提取伺服器](https://msdn.microsoft.com/en-us/powershell/dsc/pullserver)，因此目標節點會自動接收設定、符合期望狀態，並回報其相容性。
Azure 自動化中的內建提取伺服器可讓您不需要設定和維護自己的提取伺服器。
Azure 自動化會以位於雲端或內部部署的虛擬或實體 Windows 或 Linux 機器為目標。

### <a name="management-of-all-your-dsc-artifacts"></a>所有 DSC 構件的管理

Azure Automation DSC 為 [PowerShell Desired State Configuration](https://msdn.microsoft.com/powershell/dsc/overview) 帶來與 Azure 自動化提供的 PowerShell 指令碼相同的管理層。

從 Azure 入口網站或 PowerShell 中，您可以管理所有 DSC 設定、資源和目標節點。

![Azure 自動化刀鋒視窗的螢幕擷取畫面](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>將報告資料匯入至 Log Analytics

使用 Azure Automation DSC 所管理的節點會將詳細報告狀態資料傳送至內建提取伺服器。
您可以設定 Azure Automation DSC，將此資料傳送至 Microsoft Operations Management Suite (OMS) Log Analytics 工作區。
若要了解如何將 DSC 狀態資料傳送至 Log Analytics 工作區，請參閱[將 Azure Automation DSC 報告資料轉送到 OMS Log Analytics](automation-dsc-diagnostics.md)。

## <a name="introduction-video"></a>簡介影片

寧可觀賞也不要閱讀？ 看看下列 2015 年 5 月 Azure 自動化 DSC 推出當時的影片。

>[!NOTE]
>雖然這段影片中所討論的概念和週期都是正確的，但是 Azure Automation DSC 自從錄製這段影片之後已經進步許多。
>它現在已正式發行，在 Azure 入口網站中具有更豐富的 UI，並支援額外的功能。

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>後續步驟

* 若要了解如何將以 Azure Automation DSC 管理的節點上架，請參閱[上架由 Azure Automation DSC 管理的機器](automation-dsc-onboarding.md)。
* 若要開始使用 Azure Automation DSC，請參閱[開始使用 Azure Automation DSC](automation-dsc-getting-started.md)。
* 若要了解如何編譯 DSC 設定，以將它們指派給目標節點，請參閱[編譯 Azure Automation DSC 中的設定](automation-dsc-compile.md)。
* 如需 Azure Automation DSC 的 PowerShell Cmdlet 參考，請參閱 [Azure Automation DSC Cmdlet](/powershell/module/azurerm.automation/#automation)。
* 如需定價資訊，請參閱 [Azure Automation DSC 定價](https://azure.microsoft.com/pricing/details/automation/)。
* 若要查看 Azure 自動化 DSC 使用連續部署管線中的範例，請參閱[IaaS Vm 使用 Azure 自動化 DSC 和 Chocolatey 連續部署](automation-dsc-cd-chocolatey.md)