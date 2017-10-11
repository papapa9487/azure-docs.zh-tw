---
title: "在 Visual Studio 中建置無伺服器應用程式 | Microsoft Docs"
description: "根據本指南，在 Visual Studio 中開始打造您的第一個無伺服器應用程式，包括建立、部署和管理應用程式。"
keywords: 
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 3672beda8a502e5fe2c8182076a8edef7ee9ebf6
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="build-a-serverless-app-in-visual-studio-with-logic-apps-and-functions"></a>在 Visual Studio 中使用 Logic Apps 和 Functions 建置邏輯應用程式

Azure 中的無伺服器工具和功能可讓您快速開發和部署雲端應用程式。  本文件著重於如何開始在 Visual Studio 中建置無伺服器應用程式。  如需 Azure 中的無伺服器概觀，請參閱[這篇文章](logic-apps-serverless-overview.md)。

## <a name="getting-everything-ready"></a>備妥一切

以下是從 Visual Studio 建立無伺服器應用程式所需的必要條件︰

* [Visual Studio 2017](https://www.visualstudio.com/vs/) 或 Visual Studio 2015 - Community、Professional 或 Enterprise
* [Logic Apps Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551)
* [最新的 Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 或更新版本)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* [Azure Functions 核心工具](https://www.npmjs.com/package/azure-functions-core-tools)，在本機對函式進行偵錯
* 使用內嵌的邏輯應用程式設計工具時能夠存取 Web

## <a name="getting-started-with-a-deployment-template"></a>開始使用部署範本

Azure 中是在資源群組內管理資源。  資源群組是資源的邏輯群組。  資源群組可用來部署和管理資源集合。  對於 Azure 中的無伺服器應用程式，我們的資源群組包含 Azure Logic Apps 和 Azure Functions。  我們在 Visual Studio 內使用資源群組專案，能夠將整個應用程式當作單一資產來開發、管理和部署。

### <a name="create-a-resource-group-project-in-visual-studio"></a>在 Visual Studio 中建立資源群組

1. 在 Visual Studio 中，按一下以新增 [新專案]。
1. 在 [雲端] 分類中，選擇建立 Azure [資源群組] 專案  
 * 如果您沒看到分類或專案列出，請確定您已經為 Visual Studio 安裝 Azure SDK
1. 指定專案的名稱和位置，然後選取 [確定] 開始建立。Visual Studio 會提示您選取範本。  您可以選擇從 [空白]、邏輯應用程式或其他資源開始。  但在此案例中，我們使用 [Azure 快速入門範本] 開始打造無伺服器應用程式。
1. 選取即可顯示從範本**Azure 快速入門**![選取 Azure 快速入門範本][1]
1. 選取無伺服器快速入門範本︰**101-logic-app-and-function-app**，然後按一下 [確定]

快速入門範本會在您的資源群組專案中建立部署範本。  此範本包含一個簡單的邏輯應用程式，它會呼叫 Azure Functions，然後傳回結果。  如果您在 [方案總管] 中開啟 `azuredeploy.json` 檔案，您可以看到無伺服器應用程式的資源。

## <a name="deploying-the-serverless-application"></a>部署無伺服器應用程式

需要有預先部署的 Azure 資源群組，您才能在 Visual Studio 中開啟邏輯應用程式視覺化設計工具。  這樣可讓設計師在邏輯應用程式中建立和使用資源和服務的連線。  首先，我們只需要部署已建立的方案。

1. 以滑鼠右鍵按一下 Visual Studio 專案中，選取**部署**，並建立**新增**部署![選取新的資源部署][2]
1. 選取有效的 Azure 訂用帳戶和資源群組
1. 選擇**部署**方案
1. 輸入邏輯應用程式和 Azure 函式應用程式的名稱。  Azure 函式名稱不必是全域唯一

無伺服器方案會部署到指定的資源群組。  如果您在 Visual Studio 中查看 [輸出]，您可以看到部署的狀態。

## <a name="editing-the-logic-app-in-visual-studio"></a>在 Visual Studio 中編輯邏輯應用程式

當方案部署到任何資源群組之後，就可以使用視覺化設計工具來編輯和變更邏輯應用程式。

1. 以滑鼠右鍵按一下 [方案總管] 中的 `azuredeploy.json` 檔案，然後選取 [使用 Logic Apps 設計工具開啟]
1. 選取方案已部署到的 [資源群組] 和 [位置]，然後選取 [確定]

現在應該可以在 Visual Studio 中看到邏輯應用程式視覺化設計工具。  您可以繼續新增步驟、修改工作流程，然後儲存變更。  您也可以從 Visual Studio 建立邏輯應用程式。  如果您以滑鼠右鍵按一下範本巡覽器中的 [資源]，您可以選擇將 [邏輯應用程式] 資源至專案。  空的邏輯應用程式會載入視覺化設計工具中，而不會預先部署至資源群組。

### <a name="managing-and-viewing-run-history-for-a-deployed-logic-app"></a>管理和檢視已部署之邏輯應用程式的執行歷程記錄

您也可以管理和檢視已部署在 Azure 中之邏輯應用程式的執行歷程記錄。  如果您在 Visual Studio 中開啟 [Cloud Explorer] 工具，您可以用滑鼠右鍵按一下任何邏輯應用程式，然後選擇編輯、停用、檢視屬性，或檢視執行歷程記錄。  按一下編輯也可讓您將已發佈的邏輯應用程式下載到 Visual Studio 資源群組專案。  這表示即使是在 Azure 入口網站中開始建置邏輯應用程式，您仍可在 Visual Studio 中匯入和管理它。

## <a name="developing-an-azure-function-in-visual-studio"></a>在 Visual Studio 中開發 Azure 函式

部署範本會將方案中包含的任何 Azure Functions，部署至 `azuredeploy.json` 變數中指定的 git 存放庫。  如果您在方案內撰寫函式專案、將它簽入原始檔控制 (GitHub、Visual Studio Team Services 等)，然後更新 `repo` 變數，此範本將會部署 Azure 函式。

### <a name="creating-an-azure-function-project"></a>建立 Azure 函式專案

如果使用 JavaScript、Python、F#、Bash、Batch 或 PowerShell，請遵循 [Functions CLI 中的步驟](../azure-functions/functions-run-local.md)建立專案。  如果以 C# 開發函式，您可以在 Azure 函式的目前方案中使用 [C# 類別庫](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/)。

## <a name="next-steps"></a>後續步驟

* [了解如何建置無伺服器社交儀表板](logic-apps-scenario-social-serverless.md)
* [從 Visual Studio Cloud Explorer 管理邏輯應用程式](logic-apps-manage-from-vs.md)
* [邏輯應用程式工作流程定義語言](logic-apps-workflow-definition-language.md)

<!-- Image references -->
[1]: ./media/logic-apps-serverless-get-started-vs/select-template.png
[2]: ./media/logic-apps-serverless-get-started-vs/deploy.png
