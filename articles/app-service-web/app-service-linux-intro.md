---
title: "Linux 上的 Azure Web 應用程式簡介 | Microsoft Docs"
description: "深入了解 Linux 上的 Azure Web 應用程式。"
keywords: azure app service, linux, oss
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: bbe2e0bafe48b39f12c5a8a46511e9275926d4b6
ms.contentlocale: zh-tw
ms.lasthandoff: 08/30/2017

---
# <a name="introduction-to-azure-web-app-on-linux"></a>Linux 上的 Azure Web 應用程式簡介

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>概觀
[Web 應用程式](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-overview)是受到完整管理的計算平台，非常適合用來裝載網站和 Web 應用程式。 針對支援的應用程式堆疊，客戶可以使用 Linux 上的 Web 應用程式，以原生方式將 Web Apps 裝載於 Linux。 以下章節列出目前支援的應用程式堆疊。 

### <a name="languages"></a>語言

|Node.js|PHP|.NET Core|Ruby|
|:------------------:|:---:|:---------:|:----:|
|4.4, 4.5|5.6|1.0-1.1|2.3|
|6.2, 6.6, 6.9-6.11|7.0|||
|8.0-8.1||||

### <a name="deployments"></a>部署

* FTP
* 本機 Git
* GitHub
* Bitbucket

### <a name="devops"></a>DevOps

* 預備環境
* [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-intro) 和 DockerHub CI/CD

### <a name="console-publishing-and-debugging"></a>主控台、發佈和偵錯

* 環境
* 部署
* 基本主控台
* SSH

### <a name="scaling"></a>調整大小

* 客戶可以變更 [App Service 方案](https://docs.microsoft.com/en-us/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json)中的階層，即可相應增加和減少 Web Apps 的規模

## <a name="limitations"></a>限制
Azure 入口網站只會顯示 Linux 上的 Web 應用程式目前可用的功能。 隨著我們啟用更多功能，您會在入口網站中看到它們。

某些功能尚無法使用，例如虛擬網路整合、Azure Active Directory/第三方驗證或 Kudu 網站擴充功能。 一旦這些功能提供使用後，我們將會在文件和部落格中更新關於變更的消息。

目前只在下列區域提供此公開預覽版本：

|美洲|亞太地區|歐洲|
|:----------------:|:--------------:|:------------:|
|巴西南部|澳洲東部|北歐|
|美國東部|東亞|西歐|
|美國中北部|日本東部||
|美國中南部|印度南部||
|美國西部|東南亞||

若要查看其他地區何時可以使用，請檢查 [Azure 狀態儀表板](https://azure.microsoft.com/status)。

Linux 上的 Web Apps 只在[「基本」、「標準」和「進階」](https://azure.microsoft.com/en-us/pricing/details/app-service/plans/)App Service 方案中才支援，而且沒有[「免費」或「共用」](https://azure.microsoft.com/en-us/pricing/details/app-service/plans/)層。 以下也是 Linux 上的 Web 應用程式重要限制：

* 在已裝載非 Linux Web Apps 的 App Service 方案中，無法在 Linux 上建立 Web Apps。
* 在包含非 Linux Web Apps 的資源群組中建立 Linux 的 Web 應用程式時，在 Linux 上建立 Web 應用程式，您必須在與現有 App Service 方案不同的區域中建立 App Service 方案。

## <a name="troubleshooting"></a>疑難排解 ##

當您的應用程式無法啟動或您想要檢查應用程式的記錄時，請檢查 LogFiles 目錄中的 Docker 記錄。 您可以透過 SCM 網站或 FTP 來存取此目錄。
若要從您的容器記錄 `stdout` 和 `stderr`，您必須啟用 [診斷記錄] 下的 [Docker 容器記錄]。

![啟用記錄][2]

![Using Kudu to view Docker logs][1]

您可以在 [開發工具] 功能表中從 [進階工具] 存取 SCM 網站。

## <a name="next-steps"></a>後續步驟
請參閱下列連結以開始使用 Linux 上的 App Service。 您可以在[我們的論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)張貼問題和疑難。

* [如何針對 Linux 上的 Azure Web 應用程式使用自訂 Docker 映像](app-service-linux-using-custom-docker-image.md)
* [在 Linux 上的 Azure Web 應用程式中使用適用於 Node.js 的 PM2 組態](app-service-linux-using-nodejs-pm2.md)
* [在 Linux 上的 Azure App Service Web 應用程式中使用 .NET Core](app-service-linux-using-dotnetcore.md)
* [在 Linux 上的 Azure App Service Web 應用程式中使用 Ruby](app-service-linux-ruby-get-started.md)
* [Linux 上的 Azure App Service Web 應用程式常見問題集](app-service-linux-faq.md)
* [Linux 上的 Azure Web 應用程式 SSH 支援](./app-service-linux-ssh-support.md)
* [在 Azure App Service 中設定預備環境](./web-sites-staged-publishing.md)
* [在 Linux 上使用 Azure Web 應用程式連續部署 Docker 中樞](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png

