---
title: "使用命令列工具來自動部署 Azure 應用程式 | Microsoft Docs"
description: "探索有關如何從命令列部署 Azure 應用程式的資訊"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 8b65980c-eb75-44a2-8e0f-f9eb9e617d16
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: cephalin
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: 2cc0c3a1afd42d1c04e8a220b66bcc6179d7ff33
ms.contentlocale: zh-tw
ms.lasthandoff: 09/07/2017

---
# <a name="automate-deployment-of-your-azure-app-with-command-line-tools"></a>使用命令列工具來自動部署 Azure 應用程式
您可以使用命令列工具來自動部署 Azure 應用程式。 本文除了列出可用的工具之外，也列出說明如何在部署工作流程中使用這些工具的實用連結。 

## <a name="msbuild"></a>使用 MSBuild 自動化部署
如果您使用 [Visual Studio IDE](#vs) 進行開發，您將可使用 [MSBuild](http://msbuildbook.com/) 將任何您可在 IDE 中執行的工作自動化。 您可以設定 MSBuild，以使用 [Web Deploy](#webdeploy) 或 [FTP/FTPS](#ftp) 來複製檔案。 Web Deploy 也可自動化其他多種部署相關工作，例如部署資料庫。

如需使用 MSBuild 進行命令列部署的詳細資訊，請參閱下列資源：

* [使用 Visual Studio 的 ASP.NET Web 部署：命令列部署](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)。 這是系列中的第十個教學課程，討論如何使用 Visual Studio 部署至 Azure。 此課程說明在 Visual Studio 中設定發佈設定檔後，如何使用命令列進行部署。
* [深入瞭解 Microsoft Build Engine： 使用 MSBuild 和 Team Foundation Build](http://msbuildbook.com/)。 這是實體書籍，其中有幾章會說明如何使用 MSBuild 進行部署。

## <a name="powershell"></a>使用 Windows PowerShell 自動化部署
您可以從 [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx)(英文) 執行 MSBuild 或 FTP 部署功能。 如果您這麼做，則您也可以使用 Windows PowerShell Cmdlet 的集合，使 Azure REST 管理 API 更容易呼叫。

如需詳細資訊，請參閱下列資源：

* [透過可預測方式在 Azure 中佈建和部署微服務](app-service-deploy-complex-application-predictably.md)
* [使用 Azure 建置真實世界的雲端應用程式 - 自動化各個項目](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything)。 這是電子書的其中一章，說明電子書中的範例應用程式如何使用 Windows PowerShell 指令碼建立 Azure 測試環境，並加以部署。 如需其他 Azure PowerShell 文件的連結，請參閱 [資源](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources) 一節。
* [使用 Windows PowerShell 指令碼來發行至開發和測試環境](../vs-azure-tools-publishing-using-powershell-scripts.md)。 如何使用 Visual Studio 產生的 Windows PowerShell 部署指令碼。

## <a name="api"></a>使用 .NET 管理 API 自動化部署
您可以撰寫 C# 程式碼，以執行部署所需的 MSBuild 或 FTP 功能。 如果您這麼做，您將可存取 Azure 管理 REST API，以執行網站管理功能。

如需詳細資訊，請參閱下列資源：

* [使用 Azure 管理庫和 .NET 將一切自動化](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx)。 這是 .NET 管理 API 的簡介，並提供相關文件的連結。

## <a name="cli"></a>從 Azure 命令列介面 (Azure CLI) 部署
您可以透過 FTP，使用 Windows、Mac 或 Linux 機器中的命令列進行部署。 如果您這麼做，也可以使用 Azure CLI 存取 Azure REST 管理 API。

如需詳細資訊，請參閱下列資源：

* [Azure 命令列工具](https://azure.microsoft.com/downloads/)。 Azure.com 中提供命令列工具資訊的入口網站頁面。

## <a name="webdeploy"></a>從 Web Deploy 命令列部署
[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) 是一項用於 IIS 部署的 Microsoft 軟體，不僅提供智慧型檔案同步功能，也可執行或協調其他許多無法在您使用 FTP 時自動化的部署相關工作。 例如，Web Deploy 可對您的 Web 應用程式部署新的資料庫或資料庫更新。 Web Deploy 也可儘量縮短更新現有網站所需的時間，因為它具有智慧功能，可僅複製有所變更的檔案。 Microsoft Visual Studio 和 Team Foundation Server 皆內建支援 Web Deploy ，但您也可直接從命令列使用 Web Deploy，將部署自動化。 Web Deploy 命令的功能十分強大，但學習起來較為困難。

## <a name="more-resources"></a>其他資源
另一個命令列自動化部署選項是使用雲端型服務，例如 [Octopus Deploy](http://en.wikipedia.org/wiki/Octopus_Deploy)。 如需詳細資訊，請參閱 [將 ASP.NET Web 應用程式部署至 Azure 網站](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites)。

如需有關命令列工具的詳細資訊，請參閱下列資源：

* [簡單的 Web 應用程式：部署](https://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/)。 David Ebbo 所撰寫使 Web Deploy 更容易使用之工具的部落格。
* [Web 部署工具](http://technet.microsoft.com/library/dd568996)。 這是 Microsoft TechNet 網站上的正式文件。 雖已過時，但仍適合入門使用。
* [使用 Web Deploy](http://www.iis.net/learn/publish/using-web-deploy)。 這是 Microsoft IIS.NET 網站上的正式文件。 同樣也已過時，但仍適合入門使用。
* [使用 Visual Studio 的 ASP.NET Web 部署：命令列部署](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment)。 MSBuild 是 Visual Studio 所使用的建置引擎，您也可以從命令列加以使用，來將 Web 應用程式部署至 Web Apps。 這是系列中的教學課程之一，主要與 Visual Studio 部署有關。


