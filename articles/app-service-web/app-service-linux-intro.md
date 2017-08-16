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
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 87b9da134bce20b037e2dce7e1c9f54454fafe9e
ms.contentlocale: zh-tw
ms.lasthandoff: 08/09/2017

---
# <a name="introduction-to-azure-web-app-on-linux"></a>Linux 上的 Azure Web 應用程式簡介

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]

## <a name="overview"></a>概觀
針對支援的應用程式堆疊，客戶可以使用 Linux 上的 Web 應用程式，以原生方式將 Web Apps 裝載於 Linux。 下節列出目前支援的應用程式堆疊。 

## <a name="features"></a>特性
Linux 上的 Web 應用程式目前支援下列應用程式堆疊︰

* Node.js
    * 4.4
    * 4.5
    * 6.2
    * 6.6
    * 6.9
    * 6.10
* PHP
    * 5.6
    * 7.0
* .Net Core
    * 1.0
    * 1.1
* Ruby
    * 2.3

客戶可以使用下列工具來部署應用程式︰

* FTP
* 本機 Git
* GitHub
* Bitbucket

調整應用程式大小︰

* 客戶可以變更 App Service 方案中的階層，即可相應增加和減少 Web 應用程式的規模
* 客戶可以將應用程式相應放大，在其 SKU 的範圍內執行多個應用程式執行個體

針對 Kudu，有一些基本功能︰

* 環境
* 部署
* 基本主控台
* SSH

針對 DevOps：

* 預備環境
* ACR 和 DockerHub CI/CD

## <a name="limitations"></a>限制
Azure 入口網站只會顯示 Linux 上的 Web 應用程式目前可用的功能，並隱藏其餘部分。 隨著我們啟用更多功能，您會在入口網站中看到它們。

某些功能尚無法使用，例如虛擬網路整合、Azure Active Directory/第三方驗證或 Kudu 網站擴充功能。 一旦這些功能提供使用後，我們將會在文件和部落格中更新關於變更的消息。

目前只在下列區域提供此公開預覽版本：

* 美國西部
* 美國東部
* 西歐
* 北歐
* 東南亞
* 澳洲東部
* 日本東部
* 巴西南部

Linux 上的 Web Apps 只在「專用」App Service 方案中才支援，而且沒有「免費」或「共用」層。 此外，一般和 Linux Web 應用程式的 App Service 方案互斥，因此，您無法在非 Linux App Service 方案中建立 Linux Web 應用程式。

只有同一區域沒有非 Linux Web Apps 的資源群組中，才能建立 Linux 上的 Web Apps。

## <a name="next-steps"></a>後續步驟
請參閱下列連結以開始使用 Linux 上的 App Service。 您可以在[我們的論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)張貼問題和疑難。

* [在 Linux 上的 Azure Web 應用程式中建立 Web Apps](app-service-linux-how-to-create-web-app.md)
* [如何針對 Linux 上的 Azure Web 應用程式使用自訂 Docker 映像](app-service-linux-using-custom-docker-image.md)
* [在 Linux 上的 Azure Web 應用程式中使用適用於 Node.js 的 PM2 組態](app-service-linux-using-nodejs-pm2.md)
* [在 Linux 上的 Azure App Service Web 應用程式中使用 .NET Core](app-service-linux-using-dotnetcore.md)
* [在 Linux 上的 Azure App Service Web 應用程式中使用 Ruby](app-service-linux-ruby-get-started.md)
* [Linux 上的 Azure App Service Web 應用程式常見問題集](app-service-linux-faq.md)
* [Linux 上的 Azure Web 應用程式 SSH 支援](./app-service-linux-ssh-support.md)
* [在 Azure App Service 中設定預備環境](./web-sites-staged-publishing.md)
* [在 Linux 上使用 Azure Web 應用程式連續部署 Docker 中樞](./app-service-linux-ci-cd.md)


