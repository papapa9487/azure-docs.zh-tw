---
title: "使用 Application Insights 針對雲端服務進行疑難排解 | Microsoft Docs"
description: "了解如何使用 Application Insights 疑難排解雲端服務問題，以處理 Azure 診斷的資料。"
services: cloud-services
documentationcenter: .net
author: sbtron
manager: timlt
editor: tysonn
ms.assetid: e93f387b-ef29-4731-ae41-0676722accb6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: saurabh
ms.openlocfilehash: 4001ca908ff00b1a40829d687589080e9b07b18a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshoot-cloud-services-using-application-insights"></a>使用 Application Insights 疑難排解雲端服務
使用 [Azure SDK 2.8](https://azure.microsoft.com/downloads/) 和 Azure 診斷延伸模組 1.5，您可以將雲端服務的 Azure 診斷資料直接傳送至 Application Insights。 Azure 診斷所收集的記錄&mdash;包括應用程式記錄、Windows 事件記錄、ETW 記錄，以及效能計數器&mdash;可以傳送至 Application Insights。 然後，您可以在 Application Insights 入口網站 UI 中，將這項資訊視覺化。 接著，您可以使用 Application Insights SDK 深入了解來自您的應用程式和系統的計量和記錄，以及來自 Azure 診斷的基礎結構層級資料。

## <a name="configure-azure-diagnostics-to-send-data-to-application-insights"></a>設定 Azure 診斷將資料傳送至 Application Insights
請遵循下列步驟來設定雲端服務專案，將 Azure 診斷資料傳送至 Application Insights。

1. 在 Visual Studio 方案總管中，以滑鼠右鍵按一下角色，然後選取 [屬性] 以開啟角色設計工具。

    ![方案總管角色屬性][1]

2. 在角色設計的 [診斷] 區段中，選取 [將診斷資料傳送至 Application Insights] 選項。

    ![角色設計工具會將診斷資料傳送至 Application Insights][2]

3. 在快顯的對話方塊中，選取您想要傳送 Azure 診斷資料的 Application Insights 資源。 對話方塊可讓您從訂用帳戶中選取現有的 Application Insights 資源，或是為 Application Insights 資源手動指定檢測金鑰。 如需建立 Application Insights 資源的詳細資訊，請參閱[建立新的 Application Insights 資源](../application-insights/app-insights-create-new-resource.md)。

    ![選取 Application Insights 資源][3]

    新曾 Application Insights 資源後，該資源的檢測金鑰會儲存為名稱是 **APPINSIGHTS_INSTRUMENTATIONKEY** 的服務組態設定。 您可以針對每個服務設定或環境變更此組態設定。 若要這樣做，請從 [服務設定] 清單中選取不同的設定，並為該設定指定新的檢測金鑰。

    ![選取服務組態][4]

    在發佈期間，Visual studio 使用 **APPINSIGHTS_INSTRUMENTATIONKEY** 組態設定來設定診斷延伸模組的適當 Application Insights 資源資訊。 組態設定是為不同的服務組態定義不同檢測金鑰的便利方式。 在發佈過程期間，Visual Studio 會轉譯該設定，並將它插入診斷延伸模組公用組態。 為簡化使用 PowerShell 設定診斷延伸模組的程序，Visual Studio 中的套件輸出也包含了公用組態 XML，並且包含適當的 Application Insights 檢測金鑰。 公用設定檔會在延伸模組資料夾中建立並遵循模式 *PaaSDiagnostics.&lt;RoleName&gt;.PubConfig.xml*。 任何以 PowerShell 為基礎的部署都可以使用此模式，將每個設定對應至角色。

4) 若要設定 Azure 診斷將 Azure 診斷代理程式所收集的所有效能計數器和錯誤層級記錄傳送至 Application Insights，請啟用 [將診斷資料傳送至 Application Insights] 選項。 

    如果您想進一步設定哪些資料要傳送至 Application Insights，就必須手動編輯每個角色的 *diagnostics.wadcfgx* 檔案。 若要深入了解手動更新組，請參閱 [設定 Azure 診斷以將資料傳送至 Application Insights](#configure-azure-diagnostics-to-send-data-to-application-insights) 。

當雲端服務設定為將 Azure 診斷資料傳送到 Application Insights 時，您就可以像平常一樣將它部署至 Azure，確定已啟用 Azure 診斷擴充功能。 如需詳細資訊，請參閱[使用 Visual Studio 發佈雲端服務](../vs-azure-tools-publishing-a-cloud-service.md)。  

## <a name="viewing-azure-diagnostics-data-in-application-insights"></a>在 Application Insights 中檢視 Azure 診斷資料
Azure 診斷遙測會顯示在為您雲端服務設定的 Application Insights 資源中。

Azure 診斷記錄類型會以下列方式對應至 Application Insights 概念：

* 效能計數器在 Application Insights 中會顯示為自訂計量。
* Windows 事件記錄在 Application Insights 中會顯示為追蹤和自訂事件。
* 應用程式記錄、ETW 記錄和任何診斷基礎結構記錄在 Application Insights 中會顯示為追蹤。

若要在 Application Insights 中檢視 Azure 診斷資料，請執行下列其中一項：

* 使用[計量瀏覽器](../application-insights/app-insights-metrics-explorer.md)，以視覺化方式檢視任何自訂的效能計數器，或是不同類型的 Windows 事件記錄事件的計數。

    ![[計量瀏覽器] 中的自訂計量][5]

* 使用 [搜尋](../application-insights/app-insights-diagnostic-search.md)，在 Azure 診斷傳送的追蹤記錄中進行搜尋。 例如，如果未處理的例外狀況造成角色當機和回收，例外狀況的相關資訊就會顯示在 [Windows 事件記錄] 的 [應用程式] 通道中。 您可以使用搜尋來查看 Windows 事件記錄錯誤，並取得例外狀況的完整堆疊追蹤，可協助找出問題的原因。

    ![搜尋追蹤][6]

## <a name="next-steps"></a>後續步驟
* [將 Application Insights SDK 加入您的雲端服務](../application-insights/app-insights-cloudservices.md) ，從您的應用程式傳送有關要求、例外狀況、相依性及任何自訂遙測的資料。 與 Azure 診斷資料結合時，您可以在此資訊相同的 Application Insight 資源中取得應用程式和系統的完整檢視。  

<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png
