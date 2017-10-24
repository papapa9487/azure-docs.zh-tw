---
title: "安裝 Durable Functions 擴充和範例 - Azure"
description: "了解如何安裝 Azure Functions 的 Durable Functions 擴充，供入口網站開發或 Visual Studio 開發使用。"
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: debadde78d937bcd4ec1df665aacfd1887fbcd02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>安裝 Durable Functions 擴充和範例 (Azure Functions)

NuGet 套件 [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 中提供 Azure Functions 的 [Durable Functions](durable-functions-overview.md) 擴充。 本文說明如何為下列開發環境安裝套件和一組範例：

* Visual Studio 2017 (建議) 
* Azure 入口網站

## <a name="visual-studio-2017"></a>Visual Studio 2017

使用 Durable Functions 來開發應用程式時，Visual Studio 目前提供最佳體驗。  您的函式可以在本機執行，也可以發佈至 Azure。 您可以從空白專案或一組範例函式開始。

### <a name="prerequisites"></a>必要條件

* 安裝[最新版本的 Visual Studio](https://www.visualstudio.com/downloads/) (15.3 版或更新版本)。 在您的安裝選項中包括 Azure 工具。

### <a name="start-with-sample-functions"></a>從範例函式開始

1. 下載[適用於 Visual Studio 的範例應用程式 .zip 檔案](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip)。 範例專案中已經有 NuGet 參考，您不需要再新增。
2. 安裝並執行 [Azure 儲存體模擬器](https://docs.microsoft.com/azure/storage/storage-use-emulator) 5.2 版或更新版本。 或者，您可以指定實際的 Azure 儲存體連接字串來更新 *local.appsettings.json*檔案。
3. 在 Visual Studio 2017 中開啟專案。 
4. 如需有關如何執行範例的指示，請從[函式鏈結 - Hello 序列範例](durable-functions-sequence.md)開始。 此範例可以在本機執行，或發佈至 Azure。

### <a name="start-with-an-empty-project"></a>從空白專案開始
 
就像從範例開始一樣，遵循相同的指示，但改為執行下列步驟，而不是下載 *.zip* 檔案：

1. 建立函式應用程式專案。
2. 將下列 NuGet 套件參考新增至 *.csproj* 檔案：

   ```xml
   <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.DurableTask" Version="1.0.0-beta" />
   ```

## <a name="azure-portal"></a>Azure 入口網站

想要的話，您可以使用 Azure 入口網站進行 Durable Functions 開發。

### <a name="create-an-orchestrator-function"></a>建立協調器函式

1. 在 [functions.azure.com](https://functions.azure.com/signin) 上建立新的函式應用程式。
2. 將函式應用程式設定成[使用 2.0 執行階段版本](functions-versions.md)。
3. 建立新的函式，並選取 [Durable Functions 協調器 C#] 範本。
4. 在 [擴充未安裝] 底下，按一下 [安裝]，從 NuGet.org 下載擴充。

### <a name="copy-sample-code-to-the-function-app"></a>將範例程式碼複製到函式應用程式

1. 下載 [DFSampleApp.zip](https://github.com/Azure/azure-functions-durable-extension/raw/master/docfx/files/DFSampleApp.zip) 檔案。
2. 使用 Kudu 或 FTP，將範例檔案解壓縮到函式應用程式的 `D:\home\site\wwwroot` 中。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [執行函式鏈結範例](durable-functions-sequence.md)
