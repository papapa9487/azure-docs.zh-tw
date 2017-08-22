---
title: "使用 Azure 自動化管理 Azure Web 應用程式 | Microsoft Docs"
description: "了解如何使用 Azure 自動化服務管理 Web 應用程式。"
services: app-service\web, automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: c960a44f-f941-401d-afba-a4bc0f0394eb
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: 4edd2696c9a5709ded6e2a3e352090775335f0d2
ms.openlocfilehash: 4fcfa2e7ec2e8257407026ed4cca0e15fd0b5bb6
ms.contentlocale: zh-tw
ms.lasthandoff: 12/07/2016

---
# <a name="managing-azure-web-app-using-azure-automation"></a>使用 Azure 自動化管理 Azure Web 應用程式
本指南將為您介紹 Azure 自動化服務，以及如何使用它來簡化 Web 應用程式管理。

## <a name="what-is-azure-automation"></a>什麼是 Azure 自動化？
[Azure 自動化](../automation/automation-intro.md) 是一項 Azure 服務，可經由程序自動化簡化雲端管理。 使用 Azure 自動化，可以自動執行手動、重複、長時間執行及容易出錯的工作，以提高您的組織的可靠性、效率和時間價值。

Azure 自動化提供高度可靠、高度可用的工作流程執行引擎，可加以調整以符合您的需求。 在 Azure 自動化中，可以手動方式、由協力廠商系統或依排定的間隔開始執行程序，讓工作只發生在必要時刻。

將您的雲端管理工作交由「Azure 自動化」自動執行，以減少營運負擔並釋出 IT 和開發維運人力，使其專注於能夠為企業創造價值的工作上。

## <a name="how-can-azure-automation-help-manage-azure-web-app"></a>Azure 自動化為何有助於管理 Azure Web 應用程式？
Web 應用程式可透過 [Azure PowerShell 模組](/powershell/azureps-cmdlets-docs)中提供的 PowerShell Cmdlet，在 Azure 自動化中受到管理。 您可以 [在 Azure 自動化中安裝這些 Web 應用程式 PowerShell Cmdlet](https://azure.microsoft.com/blog/announcing-azure-resource-manager-support-azure-automation-runbooks/)，以便您能夠在服務內執行所有 Web 應用程式管理工作。 您也可以在 Azure 自動化中將這些 Cmdlet 與其他 Azure 服務的 Cmdlet 配對，將跨 Azure 服務和協力廠商系統的複雜工作自動化。

以下是使用自動化來管理應用程式服務的一些範例 ︰

* [用來管理 Web Apps 的指令碼](https://azure.microsoft.com/documentation/scripts/)

## <a name="next-steps"></a>後續步驟
了解 Azure 自動化的基本概念以及如何用它來管理 Azure Web 應用程式之後，請參考下列連結，以深入了解 Azure 自動化。

* 請參閱 Azure 自動化 [入門指南](../automation/automation-first-runbook-graphical.md)


