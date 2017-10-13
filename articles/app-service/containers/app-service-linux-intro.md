---
title: "Azure Web App for Containers 簡介 | Microsoft Docs"
description: "了解 Azure Web App for Containers。"
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
ms.topic: overview
ms.date: 02/16/2017
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: bdfaf0cd7d4bb44b877a9f14891e540fd789b6c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-web-app-for-containers"></a>Azure Web App for Containers 簡介

[Web 應用程式](../app-service-web-overview.md)是受到完整管理的計算平台，非常適合用來裝載網站和 Web 應用程式。 針對支援的應用程式堆疊，客戶可以使用 Web App for Containers，以原生方式將 Web 應用程式裝載於 Linux 上。 以下章節列出目前支援的應用程式堆疊。

## <a name="languages"></a>語言

|Node.js|PHP|.NET Core|Ruby|
|:------------------:|:---:|:---------:|:----:|
|4.4, 4.5|5.6|1.0-1.1|2.3|
|6.2, 6.6, 6.9-6.11|7.0|||
|8.0-8.1||||

## <a name="deployments"></a>部署

* FTP
* 本機 Git
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* 預備環境
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) 和 DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>主控台、發佈和偵錯

* 環境
* 部署
* 基本主控台
* SSH

## <a name="scaling"></a>調整大小

* 客戶可以變更 [App Service 方案](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview?toc=%2fazure%2fapp-service-web%2ftoc.json)中的階層，即可相應增加和減少 Web Apps 的規模

## <a name="locations"></a>位置

查看 [Azure 狀態儀表板](https://azure.microsoft.com/status)。

## <a name="limitations"></a>限制

Azure 入口網站只會顯示 Web App for Containers 目前可用的功能。 隨著我們啟用更多功能，您會在入口網站中看到它們。

某些功能尚無法使用，例如虛擬網路整合、Azure Active Directory/第三方驗證或 Kudu 網站擴充功能。 一旦這些功能提供使用後，我們將會在文件和部落格中更新關於變更的消息。

Web App for Containers 僅支援[基本和標準](https://azure.microsoft.com/pricing/details/app-service/plans/)應用程式服務方案，而且沒有[免費或共用](https://azure.microsoft.com/pricing/details/app-service/plans/)層。 以下也是 Web App for Containers 的重要限制：

* 在已裝載非 Linux Web Apps 的 App Service 方案中，您無法建立 Web App for Containers。
* 在包含非 Linux Web Apps 的資源群組中建立 Web App for Containers 時，您必須在與現有 App Service 方案不同的區域中建立 App Service 方案。

## <a name="troubleshooting"></a>疑難排解

當您的應用程式無法啟動或您想要檢查應用程式的記錄時，請檢查 LogFiles 目錄中的 Docker 記錄。 您可以透過 SCM 網站或 FTP 來存取此目錄。
若要從您的容器記錄 `stdout` 和 `stderr`，您必須啟用 [診斷記錄] 下的 [Docker 容器記錄]。

![啟用記錄][2]

![Using Kudu to view Docker logs][1]

您可以在 [開發工具] 功能表中從 [進階工具] 存取 SCM 網站。

## <a name="next-steps"></a>後續步驟

請參閱下列連結以開始使用 Linux 上的 App Service。 您可以在[我們的論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)張貼問題和疑難。

* [如何針對 Azure Web App for Containers 使用自訂 Docker 映像](quickstart-custom-docker-image.md)
* [在 Azure App Service Web App for Containers 中使用 .NET Core](quickstart-dotnetcore.md)
* [在 Azure App Service Web App for Containers 中使用 Ruby](quickstart-ruby.md)
* [Azure App Service Web App for Containers 常見問題集](app-service-linux-faq.md)
* [Azure Web App for Containers 的 SSH 支援](app-service-linux-ssh-support.md)
* [在 Azure App Service 中設定預備環境](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [使用 Azure Web App for Containers 進行 Docker Hub 持續部署](./app-service-linux-ci-cd.md)

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
