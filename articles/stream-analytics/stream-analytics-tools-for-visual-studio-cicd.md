---
title: "使用串流分析 Visual Studio 工具設定持續整合和部署程序 | Microsoft Docs"
description: "使用串流分析 Visual Studio 工具設定持續整合和部署程序的教學課程"
keywords: visual studio, NuGet, DevOps, CI/CD
documentationcenter: 
services: stream-analytics
author: su-jie
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/27/2017
ms.author: sujie
ms.openlocfilehash: 13fe5e37424704bd5b948d3a6629c28b320025c4
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2017
---
# <a name="use-stream-analytics-visual-studio-tools-to-set-up-a-continuous-integration-and-deployment-process"></a>使用串流分析 Visual Studio 工具設定持續整合和部署程序
在本教學課程中，您會了解如何使用 Azure 串流分析 Visual Studio 工具設定持續整合和部署程序。

最新版 (2.3.0000.0 或更新版本) 的[適用於 Visual Studio 的串流分析工具](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio)新增 MSBuild 支援。

也有新發行的 NuGet 套件 [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/)。 它提供的本機執行與部署工具 MSBuild，可針對串流分析 Visual Studio 專案，支援連續整合及部署程序。 
> [!NOTE] 
NuGet 套件僅能搭配 2.3.0000.0 或更新版本之適用於 Visual Studio 的串流分析工具使用。 如果您有使用舊版 Visual Studio 工具建立的專案，請直接使用 2.3.0000.0 或更新版本加以開啟並儲存。 接著啟用新功能。 

了解如何使用[適用於 Visual Studio 的串流分析工具](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio)。

## <a name="msbuild"></a>MSBuild
與標準 Visual Studio MSBuild 體驗類似，若要建置專案，您有兩個選項。 您可以使用滑鼠右鍵按一下專案，然後選擇 [建置]。 您也可以從命令列中使用 NuGet 套件中的 **MSBuild**。
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

串流分析 Visual Studio 專案建置成功時，會在 **bin/[Debug/Retail]/Deploy** 資料夾下方產生下列兩個 Azure Resource Manager 範本檔案： 

*  Resource Manager 範本檔案

       [ProjectName].JobTemplate.json 

*  Resource Manager 參數檔案

       [ProjectName].JobTemplate.parameters.json   

parameters.json 檔案中的預設參數來自 Visual Studio 專案中的設定。 如果您想要部署到其他環境，請據以取代參數。

> [!NOTE] 
對於所有認證，預設值都會設為 null。 部署至雲端之前，「必須」設定這些值。

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
深入了解如何[使用 Resource Manager 範本檔案和 Azure PowerShell 部署](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy)。 深入了解如何[在 Resource Manager 範本中使用物件作為參數](https://docs.microsoft.com/en-us/azure/architecture/building-blocks/extending-templates/objects-as-parameters)。


## <a name="command-line-tool"></a>命令列工具

### <a name="build-the-project"></a>建置專案
NuGet 套件有一個稱為 **SA.exe** 的命令列工具。 它支援在任意電腦上進行專案建置和本機測試，讓您可以用於持續整合和持續傳遞程序。 

部署檔案預設會放置在目前的目錄底下。 您可以使用下列 -OutputPath 參數指定輸出路徑：

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>在本機測試指令碼

如果您的專案已經在 Visual Studio 中指定本機輸入檔案，您可以使用 *localrun* 命令來執行自動化指令碼測試。 輸出結果會放在目前的目錄底下。
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>產生可搭配串流分析 PowerShell API 使用的作業定義檔案

*arm* 命令會採用透過組建產生的作業範本和作業範本參數檔案作為輸入。 然後，將它們結合到可搭配串流分析 PowerShell API 使用的作業定義 JSON 檔案中。

```
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
範例：
```
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


