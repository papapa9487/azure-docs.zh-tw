---
title: "如何針對 Linux 上的 Azure Web 應用程式使用自訂 Docker 映像 | Microsoft Docs"
description: "如何針對 Linux 上的 Azure Web 應用程式使用自訂 Docker 映像。"
keywords: "azure app service, web 應用程式, linux, docker, 容器"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: f51cacb33251d479f48a39014cc2db60a23358d5
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---

# <a name="using-a-custom-docker-image-for-azure-web-app-on-linux"></a>針對 Linux 上的 Azure Web 應用程式使用自訂 Docker 映像 #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


App Service 在 Linux 上提供預先定義的應用程式堆疊，且支援特定的版本，例如 PHP 7.0 和 Node.js 4.5。 Linux 上的 App Service 使用 Docker 容器來裝載這些預先建置的應用程式堆疊。 您也可以使用自訂 Docker 映像，將您的 Web 應用程式部署到尚未定義在 Azure 中的應用程式堆疊。 自訂 Docker 映像可裝載於公用或私人 Docker 儲存機制。


## <a name="how-to-set-a-custom-docker-image-for-a-web-app"></a>如何︰設定 Web 應用程式的自訂 Docker 映像
您可以為新的和現有的 Web 應用程式設定自訂 Docker 映像。 當您在 [Azure 入口網站](https://portal.azure.com/#create/Microsoft.AppSvcLinux)中建立 Linux 上的 Web 應用程式時，請按一下 [設定容器] 來設定自訂 Docker 映像︰

![Linux 上新 Web 應用程式的自訂 Docker 映像][1]


## <a name="how-to-use-a-custom-docker-image-from-docker-hub"></a>作法︰從 Docker 中樞使用自訂 Docker 映像 ##
若要從 Docker 中樞使用自訂 Docker 映像：

1. 在 [Azure 入口網站](https://portal.azure.com)中，找出您的 Linux 上的 Web 應用程式，然後在 [設定] 中按一下 [Docker 容器]。

2.  選取 [Docker 中樞] 作為 [映像來源]，然後按一下 [公用] 或 [私人]，輸入 [映像和選擇性標記名稱]，例如 `node:4.5`。 **啟動命令**會根據 Docker 映像檔案中的定義而自動設定，但是您可以設定自己的命令。  

    ![Configure Docker Hub public repository image][2]

    當您的映像是來自私人儲存機制時，您也必須輸入關於私人 Docker 中樞儲存機制的 Docker 中樞認證 ([登入使用者名稱] 和 [密碼])。

    ![Configure Docker Hub private repository image][3]

3. 設定容器之後，按一下 [儲存]。

## <a name="how-to-use-a-docker-image-from-a-private-image-registry"></a>如何從私人映像登錄使用 Docker 映像 ##
若要從私人映像登錄使用 Docker 映像：

1. 在 [Azure 入口網站](https://portal.azure.com)中，找出您的 Linux 上的 Web 應用程式，然後在 [設定] 中按一下 [Docker 容器]。

2.  按一下 [私人登錄] 作為 [映像來源]。 輸入私人登錄的 [映像和選擇性標記名稱]、[伺服器 URL] 以及認證 ([登入使用者名稱] 和 [密碼])。 按一下 [儲存] 。

    ![Configure Docker image from private registry][4]


## <a name="how-to-set-the-port-used-by-your-docker-image"></a>作法︰設定 Docker 映像使用的連接埠 ##

當您對 Web 應用程式使用自訂 Docker 映像時，您可以在 Dockerfile 中使用 `WEBSITES_PORT` 環境變數，它會新增至產生的容器。 請看下列 Ruby 應用程式的 docker 檔案範例︰

    FROM ruby:2.2.0
    RUN mkdir /app
    WORKDIR /app
    ADD . /app
    RUN bundle install
    CMD bundle exec puma config.ru -p WEBSITES_PORT -e production

在最後一行命令中，您可以看到執行階段會傳遞 WEBSITES_PORT 環境變數。 請記住，命令中區分大小寫。

先前平台使用 `PORT` 應用程式設定，我們計劃取代此應用程式設定的使用，並改為專門使用 `WEBSITES_PORT`。

當您使用其他人所建置的現有 Docker 映像時，您可能需要指定連接埠 80 以外的連接埠給應用程式。 若要設定連接埠，將數值加入稱為 `WEBSITES_PORT` 的應用程式設定，如下所示：

![Configure PORT app setting for custom Docker image][6]


## <a name="how-to-switch-back-to-using-a-built-in-image"></a>作法︰切換回使用內建映像 ##

若要從使用自訂映像切換回使用內建映像︰

1. 在 [Azure 入口網站](https://portal.azure.com)中，找出您的 Linux 上的 Web 應用程式，然後在 [設定] 中按一下 [App Service]。

2. 選取要用於內建映像的 [執行階段堆疊]，然後按一下 [儲存]。 

![Configure Built-In Docker image][5]


## <a name="troubleshooting"></a>疑難排解 ##

當您的應用程式無法使用自訂 Docker 映像來啟動時，請檢查 LogFiles 目錄中的 Docker 記錄檔。 您可以透過 SCM 網站或 FTP 來存取此目錄。
若要從您的容器記錄 `stdout` 和 `stderr`，您必須啟用 [診斷記錄] 下的 [Docker 容器記錄]。

![啟用記錄][8]

![Using Kudu to view Docker logs][7]

您可以在 [開發工具] 功能表中從 [進階工具] 存取 SCM 網站。

## <a name="next-steps"></a>後續步驟 ##

請前往下列連結以開始使用 Linux 上的 Web 應用程式。   

* [Linux 上的 Azure Web 應用程式簡介](./app-service-linux-intro.md)
* [在 Linux 上的 Azure Web 應用程式中建立 Web Apps](./app-service-linux-how-to-create-web-app.md)
* [在 Linux 上的 Azure Web 應用程式中使用適用於 Node.js 的 PM2 組態](./app-service-linux-using-nodejs-pm2.md)
* [Linux 上的 Azure App Service Web 應用程式常見問題集](app-service-linux-faq.md)

在[我們的論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)張貼問題和關切事項。


<!--Image references-->
[1]: ./media/app-service-linux-using-custom-docker-image/new-configure-container.png
[2]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-public.png
[3]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-private.png
[4]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-privateregistry.png
[5]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-builtin.png
[6]: ./media/app-service-linux-using-custom-docker-image/setting-port.png
[7]: ./media/app-service-linux-using-custom-docker-image/kudu-docker-logs.png
[8]: ./media/app-service-linux-using-custom-docker-image/logging.png

