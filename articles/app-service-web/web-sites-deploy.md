---
title: "將您的應用程式部署至 Azure App Service | Microsoft Docs"
description: "了解如何將您的應用程式部署至 Azure App Service。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: f1464f71-2624-400e-86a2-e687e385804f
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 7eff46740cdbc8467c711aada5e6022d82930e31
ms.contentlocale: zh-tw
ms.lasthandoff: 06/30/2017

---
# <a name="deploy-your-app-to-azure-app-service"></a>將您的應用程式部署至 Azure App Service
這篇文章可協助您判斷將 Web 應用程式、行動應用程式後端或 API 應用程式檔案部署至 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)的最佳選項，並針對您的慣用選項，引導您參考含適當指示的資源。

## <a name="overview"></a>Azure App Service 部署概觀
Azure App Service 會維護您的應用程式架構 (ASP.NET、PHP、Node.js 等等)。 有些架構會依預設啟用，而其他架構 (如 Java 和 Python) 則需要簡單的核取記號組態資訊才能啟用。 此外，您可以自訂您的應用程式架構，例如 PHP 版本或執行階段位元。 如需詳細資訊，請參閱 [在 Azure App Service 中設定您的應用程式](web-sites-configure.md)。

由於您不需要擔心 Web 伺服器或應用程式架構，因此若要將您的應用程式部署至 App Service，只要將程式碼、二進位檔、內容檔案和其個別的目錄結構部署至 Azure 的 [**/site/wwwroot** 目錄](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)即可 (或 WebJobs 的 **/site/wwwroot/App_Data/Jobs/** 目錄)。 App Service 支援三種不同的部署程序。 本文中的所有部署方法皆使用下列其中一種程序： 

* [FTP 或 FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol)：使用您最喜愛的 FTP 或 FTPS 啟用工具將您的檔案移至 Azure，從 [FileZilla](https://filezilla-project.org) 到功能完整的整合式開發環境 (IDE) (例如 [NetBeans](https://netbeans.org))。 這完全是檔案上傳程序。 App Service 不會提供其他服務，例如版本控制、檔案結構管理等等。 
* [Kudu (Git/Mercurial 或 OneDrive/Dropbox)](https://github.com/projectkudu/kudu/wiki/Deployment)：Kudu 是 App Service 中的[部署引擎](https://github.com/projectkudu/kudu/wiki)。 將您的程式碼直接從任何儲存機制推送到 Kudu。 Kudu 也會在程式碼推入時提供新增的服務，包括版本控制、封裝還原、MSBuild 和 [Web 勾點](https://github.com/projectkudu/kudu/wiki/Web-hooks) 以連續部署和其他自動化工作。 Kudu 部署引擎支援 3 種不同類型的部署來源：   
  
  * OneDrive 和 Dropbox 的內容同步處理   
  * 以儲存機制為基礎的持續部署，其使用 GitHub、Bitbucket 和 Visual Studio Team Services 的自動同步處理  
  * 以儲存機制為基礎的部署，其使用本機 Git 手動同步處理  
* [Web Deploy](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy)：可讓您使用自動部署至 IIS 伺服器的相同工具，直接從最愛的 Microsoft 工具 (例如 Visual Studio)，將程式碼部署至 App Service。 這項工具支援僅限差異比對的部署、資料庫建立、連接字串等的轉換等等。Web 部署不同於 Kudu 的地方，在於將應用程式二進位檔部署至 Azure 之前，會先進行建置。 類似於 FTP，App Service 不會提供其他服務。

熱門的 Web 開發工具支援一或多個這些部署程序。 雖然您選擇的工具會決定可以利用的部署程序，但可供您使用的實際 DevOps 功能取決於部署程序的組合以及且您選擇的特定工具。 例如，如果您從 [Visual Studio 搭配 Azure SDK](#vspros)執行 Web Deploy，即使您未從 Kudu 獲得自動化，仍可在 Visual Studio 中獲得套件還原和 MSBuild 自動化。 

> [!NOTE]
> 這些部署程序並不會實際 [佈建 Azure 資源](../azure-resource-manager/resource-group-template-deploy-portal.md) ，但這些資源可能是您的應用程式需要的。 然而，大部分連結的作法文章會說明如何以端對端的方式佈建應用程式及將程式碼部署到應用程式。 您也可以在 [使用命令列工具自動化部署](#automate) 一節中找到佈建 Azure 資源的其他選項。
> 
> 

## <a name="ftp"></a>透過 FTP 上傳檔案來手動部署
如果您習慣以手動方式將網站內容複製到 Web 伺服器，則可以使用 [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) 公用程式來複製檔案，例如 Windows 檔案總管或 [FileZilla](https://filezilla-project.org/)。

手動複製檔案的優點如下：

* FTP 工具易於上手且複雜性最小。 
* 可明確掌握檔案的走向。
* FTPS 的高安全性。

手動複製檔案的缺點如下：

* 必須知道如何將檔案部署至 App Service 中正確的目錄。 
* 當發生失敗時沒有回復的版本控制。
* 沒有內建的部署歷程記錄可針對部署問題進行疑難排解。
* 部署時間可能會較長，因為有許多 FTP 工具不提供僅限差異比對的複製，而會複製所有檔案。  

### <a name="howtoftp"></a>如何透過 FTP 上傳檔案
[Azure 入口網站](https://portal.azure.com)會提供您使用 FTP 或 FTPS 來連接到應用程式目錄時所需的一切資訊。

* [使用 FTP 將您的應用程式部署至 Azure App Service](app-service-deploy-ftp.md)

## <a name="dropbox"></a>與雲端資料夾同步處理以進行部署
如果不想 [手動複製檔案](#ftp) ，理想替代方法是從雲端儲存空間 (例如 OneDrive 和 Dropbox)，將檔案和資料夾同步處理到 App Service。 與雲端資料夾同步處理時，會利用 Kudu 程序來進行部署 (請參閱 [部署程序概觀](#overview))。

與雲端資料夾同步處理的優點如下：

* 部署的簡潔性。 部分服務 (例如 OneDrive 和 Dropbox) 提供桌面同步處理用戶端，使您的本機工作目錄也是部署目錄。
* 單鍵部署。
* 可使用 Kudu 部署引擎中的所有功能 (例如封裝還原和自動化)。

與雲端資料夾同步處理的缺點如下：

* 當發生失敗時沒有回復的版本控制。
* 沒有自動化的部署，需要手動同步處理。

### <a name="howtodropbox"></a>如何與雲端資料夾同步處理以進行部署
在 [Azure 入口網站](https://portal.azure.com)中，您可以在 OneDrive 或 Dropbox 雲端儲存空間中指定要同步內容的資料夾、在該資料夾中處理您的應用程式程式碼和內容，並按一下按鈕以同步至 App Service。

* [將雲端資料夾的內容同步處理到 Azure App Service](app-service-deploy-content-sync.md)。 

## <a name="continuousdeployment"></a>從雲端型原始檔控制服務進行持續部署
如果您的開發團隊使用雲端型原始程式碼管理 (SCM) 服務，例如 [Visual Studio Team Services](http://www.visualstudio.com/)[GitHub](https://www.github.com)或 [BitBucket](https://bitbucket.org/)，您可以設定 App Service 與儲存機制整合，並持續部署。 

從雲端型原始檔控制服務進行部署的優點如下：

* 版本控制可進行回復。
* 可設定適用於 Git 儲存機制 (若適用的話也含 Mercurial) 的持續部署。 
* 分支專屬的部署，可以將不同的分支部署至不同的 [位置](web-sites-staged-publishing.md)。
* 可使用 Kudu 部署引擎中的所有功能 (例如部署版本控制、回復、封裝還原和自動化)。

從雲端型原始檔控制服務進行部署的缺點如下：

* 需對個別 SCM 服務有一定程度的了解。

### <a name="vsts"></a>如何從雲端型原始檔控制服務進行持續部署
在 [Azure 入口網站](https://portal.azure.com)中，您可以透過 GitHub、Bitbucket 和 Visual Studio Team Services 設定持續部署。

* [持續部署至 Azure App Service](app-service-continuous-deployment.md)。 

若要了解如何從 Azure 入口網站未列出的雲端存放庫中手動設定連續部署 (例如 [GitLab](https://gitlab.com/))，請參閱[使用手動步驟設定連續部署](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)。

## <a name="localgitdeployment"></a>從本機 Git 進行部署
如果開發團隊使用以 Git 為基礎的內部部署本機原始程式碼管理 (SCM) 服務，則您可將其設為 App Service 的部署來源。 

從本機 Git 進行部署的優點如下：

* 版本控制可進行回復。
* 分支專屬的部署，可以將不同的分支部署至不同的 [位置](web-sites-staged-publishing.md)。
* 可使用 Kudu 部署引擎中的所有功能 (例如部署版本控制、回復、封裝還原和自動化)。

從本機 Git 進行部署的缺點如下：

* 需對個別 SCM 系統有一定程度的了解。
* 沒有任何持續部署的現成解決方案。 

### <a name="vsts"></a>如何從本機 Git 進行部署
在 [Azure 入口網站](https://portal.azure.com)中，您可以設定本機 Git 部署。

* [本機 Git 部署至 Azure App Service](app-service-deploy-local-git.md)。 
* [從任何 git/hg 儲存機制發行至 Web Apps](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html)。  

## <a name="deploy-using-an-ide"></a>使用整合式開發環境 (IDE) 部署
如果您已經將 [Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) 與 [Azure SDK](https://azure.microsoft.com/downloads/) 或其他整合式開發環境 (IDE) 套件 (例如 [Xcode](https://developer.apple.com/xcode/)[Eclipse](https://www.eclipse.org)和 [IntelliJ IDEA](https://www.jetbrains.com/idea/)) 搭配使用，您就可以直接從您的整合式開發環境 (IDE) 內部署到 Azure。 此選項適用於個別的開發人員。

視您的喜好設定而定，Visual Studio 支援所有三種部署程序 (FTP、Git 及 Web Deploy)，而其他 IDE 若有 FTP 或 Git 整合，便可以部署至 App Service (請參閱 [部署程序概觀](#overview))。

使用整合式開發環境 (IDE) 部署的優點如下：

* 可能會減少您端對端應用程式生命週期的工具。 開發、偵錯、追蹤和部署應用程式至 Azure 而不會移動到您的整合式開發環境 (IDE) 之外。 

使用整合式開發環境 (IDE) 部署的缺點如下：

* 工具的複雜度增加。
* 仍然需要團隊專案的原始檔控制系統。

<a name="vspros"></a> 使用 Visual Studio 搭配 Azure SDK 進行部署的其他優點包括：

* Azure SDK 會優先使用 Visual Studio 中的 Azure 資源。 建立、刪除、編輯、啟動和停止應用程式、查詢後端 SQL Database、即時偵錯 Azure 應用程式，以及更多。 
* 即時編輯在 Azure 上的程式碼檔案。
* 即時偵錯在 Azure 上的應用程式。
* 整合式 Azure Explorer。
* 僅限差異比對的部署。 

### <a name="vs"></a>如何直接從 Visual Studio 進行部署
* [開始使用 Azure 和 ASP.NET](app-service-web-get-started-dotnet.md)。 說明如何使用 Visual Studio 和 Web Deploy 建立及部署簡易的 ASP.NET MVC Web 專案。
* [如何使用 Visual Studio 部署 Azure WebJob](websites-dotnet-deploy-webjobs.md)。 如何設定主控台應用程式專案，使其部署為 WebJob。  
* [使用 Visual Studio 的 ASP.NET Web 部署](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction)。 這是分成 12 個單元的教學課程系列，其中討論的部署工作比此處所列的其他資源更為詳盡。 本教學課程撰寫後已新增某些 Azure 部署功能，但稍後的附註會說明遺漏的功能。
* [在 Visual Studio 2012 中直接從 Git 儲存機制將 ASP.NET 網站部署至 Azure](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881)。 說明如何使用 Git 外掛程式將程式碼認可至 Git，以及將 Azure 連接到 Git 儲存機制，以在 Visual Studio 中部署 ASP.NET Web 專案。 自 Visual Studio 2013 起，Git 支援已是內建的功能，不需安裝外掛程式。

### <a name="aztk"></a>如何使用適用於 Eclipse 和 IntelliJ IDEA 的 Azure 工具組來進行部署
Microsoft 可讓您透過[適用於 Eclipse 的 Azure 工具組](../azure-toolkit-for-eclipse.md)和[適用於 IntelliJ 的 Azure 工具組](../azure-toolkit-for-intellij.md)，將 Web Apps 直接從 Eclipse 和 IntelliJ 部署到 Azure。 下列教學課程說明使用上述任一整合式開發環境 (IDE)，將簡單的 "Hello" World Web 應用程式部署到 Azure 所涉及的步驟：

* [Create a Hello World Web App for Azure in Eclipse (在 Eclipse 中建立 Azure Hello World Web 應用程式)](app-service-web-eclipse-create-hello-world-web-app.md)。 本教學課程示範如何使用「適用於 Eclipse 的 Azure 工具組」來建立與部署 Azure 的 Hello World Web 應用程式。
* [在 IntelliJ 中建立 Azure Hello World Web 應用程式](app-service-web-intellij-create-hello-world-web-app.md)。 本教學課程示範如何使用「適用於 IntelliJ 的 Azure 工具組」來建立與部署 Azure 的 Hello World Web 應用程式。

## <a name="automate"></a>使用命令列工具進行自動化部署
如果您偏好使用命令列終端機作為選擇的開發環境，您可以使用命令列工具為 App Service 應用程式編寫部署工作指令碼。 

使用命令列工具進行部署的優點如下：

* 啟用指令碼式部署案例。
* 整合 Azure 資源與程式碼部署的佈建。
* 將 Azure 部署整合至現有的連續整合指令碼。

使用命令列工具進行部署的缺點如下：

* 不適用於偏好 GUI 的開發人員。

### <a name="automatehow"></a>如何使用命令列工具進行自動化部署

如需命令列工具清單和教學課程連結，請參閱[使用命令列工具來自動部署 Azure 應用程式](app-service-deploy-command-line.md)。 

## <a name="nextsteps"></a>後續步驟
在某些情況中，您可能想要輕鬆地在預備版本和生產版本的應用程式之間來回切換。 [如需詳細資訊，請參閱 Web Apps 上的預備部署](web-sites-staged-publishing.md)。

具有備份及還原計劃是部署工作流程中相當重要的環節。 如需有關 App Service 備份和還原功能的資訊，請參閱 [Web Apps 備份](web-sites-backup.md)。  

如需了解如何使用 Azure「角色型存取控制」來管理對 App Service 部署的存取，請參閱 [RBAC 和 Web 應用程式發行](https://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing/)。


