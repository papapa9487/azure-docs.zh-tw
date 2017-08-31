---
title: "使用 Azure 自動化管理 Azure API 管理"
description: "了解如何使用 Azure 自動化服務來管理 Azure API 管理。"
services: api-management, automation
documentationcenter: 
author: vladvino
manager: eamono
editor: 
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 3e0e91cf3652b71af49242206057854500f78b2e
ms.contentlocale: zh-tw
ms.lasthandoff: 11/17/2016

---
# <a name="managing-azure-api-management-using-azure-automation"></a>使用 Azure 自動化管理 Azure API 管理
本指南將為您介紹 Azure 自動化服務，以及如何使用它來簡化 Azure API 管理。

## <a name="what-is-azure-automation"></a>什麼是 Azure 自動化？
[Azure 自動化](https://azure.microsoft.com/services/automation/) 是一項 Azure 服務，可經由程序自動化簡化雲端管理。 使用 Azure 自動化，可以自動執行手動、重複、長時間執行及容易出錯的工作，以提高您的組織的可靠性、效率和時間價值。

Azure 自動化提供高度可靠、高度可用的工作流程執行引擎，可加以調整以符合您的需求。 在 Azure 自動化中，可以手動方式、由協力廠商系統或依排定的間隔開始執行程序，讓工作只發生在必要時刻。

將您的雲端管理工作交由「Azure 自動化」自動執行，以減少營運負擔並釋出 IT 和開發維運人力，使其專注於能夠為企業創造價值的工作上。

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Azure 自動化如何協助管理 Azure API 管理？
您可以在 Azure 自動化中利用 [適用於 API 管理 API 的 Windows PowerShell Cmdlet](https://azure.microsoft.com/updates/full-set-of-windows-powershell-cmdlets-for-azure-api-management-api/)來管理「API 管理」。 在 Azure 自動化內，您可以利用 Cmdlet 撰寫 PowerShell 工作流程指令碼，以執行許多 API 管理工作。 您也可以將 Azure 自動化中的這些 Cmdlet 與其他 Azure 服務的 Cmdlet 搭配，以透過 Azure 服務和協力廠商系統自動執行複雜的工作。

以下是幾個使用 API 管理搭配自動化的範例︰

* [Azure API 管理 – 使用 PowerShell 進行備份和還原](https://blogs.msdn.microsoft.com/katriend/2015/10/02/azure-api-management-using-powershell-for-backup-and-restore/)

## <a name="next-steps"></a>後續步驟
了解 Azure 自動化的基本概念以及如何用它來管理 Azure API 管理之後，請參考下列連結以深入了解。

* 請參閱 Azure 自動化 [快速入門教學課程](../automation/automation-first-runbook-graphical.md)。


