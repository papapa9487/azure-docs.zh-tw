---
title: "針對 Azure Data Lake Analytics 作業開發 U-SQL 組件 | Microsoft Docs"
description: "了解如何開發要用於和重複用於 Data Lake Analytics 作業中的組件。 "
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/30/2016
ms.author: jejiang
ms.openlocfilehash: c49f80f8dcd330d7f46726241e7178351b9cc28f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="develop-u-sql-assemblies-for-azure-data-lake-analytics-jobs"></a>針對 Azure Data Lake Analytics 作業開發 U-SQL 組件
了解如何將程式碼後置轉換成可用於和重複用於 Data Lake Analytics 作業中的組件。 

U-SQL 可讓您輕鬆地以 .Net 語言來新增您自己的程式碼，例如 C#、VB.Net 或 F #。 您甚至可以部署自己的執行階段以支援其他語言。

使用自訂程式碼最簡單的方法是使用 Data Lake Tools for Visual Studio 的程式碼後置功能。 如需詳細資訊，請參閱 [教學課程：使用適用於 Visual Studio 的 Data Lake 工具開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)。 使用程式碼後置有幾個缺點︰

- 每次提交指令碼時都會上傳原始程式碼。
- 程式碼後置無法與其他作業共用。

若要解決這些缺點，您可以將程式碼後置轉換成組件，再將組件註冊到 Data Lake Analytics 目錄。

## <a name="prerequisites"></a>必要條件
* 已安裝 Visual Studio 2017、Visual Studio 2015、Visual Studio 2013 Update 4，或具有 Visual C++ 的 Visual Studio 2012。
* Microsoft Azure SDK for .NET 2.5 版或更新版本。  使用 Web 平台安裝程式或 Visual Studio 安裝程式來安裝它。
* Data Lake Analytics 帳戶。  請參閱[使用 Azure 入口網站開始使用 Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)。
* 請參閱 [開始使用 Azure Data Lake Analytics U-SQL Studio](data-lake-analytics-u-sql-get-started.md) 教學課程。
* 連接到 Azure。
* 上傳來源資料，請參閱[開始使用 Azure Data Lake Analytics U-SQL Studio](data-lake-analytics-u-sql-get-started.md)。 

## <a name="develop-assemblies-for-u-sql"></a>開發 U-SQL 的組件

**建立和提交 U-SQL 工作**

1. 從 [檔案] 功能表中，按一下 [新增]，再按 [專案]。
2. 展開 已安裝、範本、Azure Data Lake、U-SQL(ADLA)，選取 類別庫 (適用於 U-SQL 應用程式) 範本，然後按一下確定。
3. 在 Class1.cs 中撰寫程式碼。  以下是程式碼範例。

        using Microsoft.Analytics.Interfaces;

        namespace USQLApplication_codebehind
        {
            [SqlUserDefinedProcessor]
            public class MyProcessor : IProcessor
            {
                public override IRow Process(IRow input, IUpdatableRow output)
                {
                    output.Set(0, input.Get<string>(0));
                    output.Set(0, input.Get<string>(0));
                    return output.AsReadOnly();
                }
            }
        }
4. 按一下 建置 功能表，然後按一下建置方案 來建立 dll。

## <a name="register-assemblies"></a>註冊組件

請參閱[使用 Data Lake Analytics (U-SQL) 目錄](data-lake-analytics-use-u-sql-catalog.md)。


## <a name="use-the-assemblies"></a>使用組件

請參閱[使用 Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)。

## <a name="see-also"></a>另請參閱
* [使用 PowerShell 開始使用 Data Lake Analytics](data-lake-analytics-get-started-powershell.md)
* [使用 Azure 入口網站開始使用 Data Lake Analytics](data-lake-analytics-get-started-portal.md)
* [使用適用於 Visual Studio 的 Data Lake 工具來開發 U-SQL 應用程式](data-lake-analytics-data-lake-tools-get-started.md)
* [使用 Data Lake Analytics (U-SQL) 目錄](data-lake-analytics-use-u-sql-catalog.md)
* [使用 Azure Data Lake Tools for Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)