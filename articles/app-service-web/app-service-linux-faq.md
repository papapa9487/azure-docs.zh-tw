---
title: "Linux 上的 Azure App Service Web 應用程式常見問題集 | Microsoft Docs"
description: "Linux 上的 Azure App Service Web 應用程式常見問題集。"
keywords: "azure app service, web 應用程式, 常見問題集, linux, oss"
services: app-service
documentationCenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: aelnably;wesmc
ms.translationtype: HT
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 6122f28b35d143ec26a379ae9aa8aee9bdaaff9e
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---

# <a name="azure-app-service-web-app-on-linux-faq"></a>Linux 上的 Azure App Service Web 應用程式常見問題集

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


隨著 Linux 上的 Web 應用程式推出，我們正著手在我們的平台上新增功能和進行改善。 以下是過去幾個月以來，我們的客戶一直詢問的一些常見問題 (FAQ)。
如果您有問題，請對文章發表評論，我們會盡速回答。

## <a name="built-in-images"></a>內建映像

**問︰**我想要將平台所提供的內建 Docker 容器進行分岔。 哪裡可以找到這些檔案？

**答：**您可以在 [GitHub](https://github.com/azure-app-service) 上找到所有 Docker 檔案。 您可以在 [Docker Hub](https://hub.docker.com/u/appsvc/) 上找到所有 Docker 容器。

**問︰**設定執行階段堆疊時，在 [啟動檔案] 區段應該使用哪些值？

**答︰**針對 Node.Js，您需指定 PM2 組態檔或指令碼檔案。 針對 .Net Core，請指定已編譯的 DLL 名稱。 針對 Ruby，您可以指定要用來將應用程式初始化的 Ruby 指令碼。

## <a name="management"></a>管理

**問︰**當我按下 Azure 入口網站中的 [重新啟動] 按鈕時，會發生什麼事？

**答︰**這相當於 Docker 的重新啟動。

**問：**我是否可以使用「安全殼層」(SSH) 來連線到應用程式容器虛擬機器 (VM)？

**答︰**是，您可以透過 SCM 網站來這樣做，如需詳細資訊，請查看下列文件：[Linux 上的 Web 應用程式 SSH 支援](./app-service-linux-ssh-support.md)

**問：**要如何才能夠透過 SDK 或 ARM 範本來建立 Linux App Service 方案？

**答：**您必須將 App Service 的 `reserved` 欄位設定為 `true`。

## <a name="continuous-integrationdeployment"></a>連續整合/部署

**問︰**我的 Web 應用程式在我已更新 Docker Hub 上的映像之後，仍然使用舊的 Docker 容器映像。 您是否支援自訂容器的連續整合/部署？

**答︰**如需設定 Azure Container Registry 或 DockerHub 映像的持續整合/部署，請查看下列文件：[在 Linux 上使用 Azure Web 應用程式持續部署](./app-service-linux-ci-cd.md)。 針對私人登錄，您可以將 Web 應用程式先停止再啟動，來重新整理容器。 您也可以變更或新增虛擬應用程式設定，來強制重新整理您的容器。

**問︰**是否支援預備環境？

**答：** 是。

**問：**我可以使用 **Web 部署**來部署我的 Web 應用程式嗎？

**答：**是，您需要將稱為 `WEBSITE_WEBDEPLOY_USE_SCM` 的應用程式設定設定為 `false`。

## <a name="language-support"></a>語言支援

**問︰**是否支援未編譯的 .NET Core 應用程式？

**答：** 是。

**問︰**您是否支援以 Composer 做為 PHP 應用程式的相依性管理程式？

**答：** 是。 在 Git 部署期間，Kudu 應該會偵測到您要部署 PHP 應用程式 (這點受惠於 composer.json 檔案)，並且會為您觸發編輯器安裝。

## <a name="custom-containers"></a>自訂容器

**問︰**我使用自己的自訂容器。 我的應用程式位於 `\home\` 目錄中，但是我使用 [SCM 網站](https://github.com/projectkudu/kudu)或 FTP 用戶端來瀏覽內容時卻找不到檔案。 我的檔案在哪裡？

**答︰**我們在 `\home\` 目錄掛接了 SMB 共用。 這會覆寫該處的所有內容。

**問︰**我使用自己的自訂容器。 我不需要平台將 SMB 共用掛接至 `\home\`。

**答：**您可以將 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` 應用程式設定設為 `false` 來執行此動作。

**問：**我的自訂容器需要很長時間才能啟動，而平台會在它完成啟動之前將容器重新啟動。

**答：**您可以設定在重新啟動容器之前，平台所要等待的時間。 可藉由將 `WEBSITES_CONTAINER_START_TIME_LIMIT` 應用程式設定設為所需的值 (以秒為單位) 來執行此動作。 預設為 230 秒，最大值為 600 秒。

**問︰**私人登錄伺服器 URL 的格式為何？

**答︰**您需要提供包括 `http://` 或 `https://` 的完整登錄 URL。

**問︰**私人登錄選項中的映像名稱格式為何？

**答︰**您需要新增包括私人登錄 URL 的完整映像名稱 (例如 myacr.azurecr.io/dotnet:latest)

**問︰**我想要在我的自訂容器映像上公開多個連接埠。 是否可行？

**答：**目前不支援此做法。

**問︰**我可以攜帶自己的儲存體嗎？

**答：**目前不支援此做法。

**問︰**我無法從 SCM 網站瀏覽自訂容器的檔案系統或執行中處理序。 這是為什麼？

**答︰**SCM 網站是在個別的容器中執行。 您無法檢查應用程式容器的檔案系統或執行中的處理程序。

**問︰**我的自訂容器接聽連接埠 80 以外的連接埠。 如何設定我的應用程式將要求路由至該連接埠？

**答︰**我們會自動偵測連接埠，您也可以指定一個名為 [WEBSITES_PORT] 的應用程式設定，為它提供一個預期的連接埠號碼值。 先前平台使用 `PORT` 應用程式設定，我們計劃取代此應用程式設定的使用，並改為專門使用 `WEBSITES_PORT`。

**問︰**我是否需要在我的自訂容器中實作 HTTPS。

**答︰**不需要，平台會在共用的前端處理 HTTPS 終止。

## <a name="pricing-and-sla"></a>價格和 SLA

**問︰**使用公開預覽版時是價格是多少？

**答︰**我們會以標準 Azure App Service 定價，向您收取應用程式執行時數一半的費用。 這表示您會獲得標準 Azure App Service 定價的五折優惠。

## <a name="other"></a>其他

**問︰**應用程式設定名稱所支援的字元為何？

**答︰**針對應用程式設定，您只能使用 A-Z、a-z、0-9 以及底線。

**問︰**我可以在何處提出新功能的要求？

**答︰**您可以在 [Web Apps 意見反應論壇 (英文)](https://aka.ms/webapps-uservoice) 提交您的想法。 將 "[Linux]" 新增至您想法的標題。

## <a name="next-steps"></a>後續步驟
* [什麼是 Linux 上的 Azure Web 應用程式？](app-service-linux-intro.md)
* [Linux 上的 Azure Web 應用程式 SSH 支援](./app-service-linux-ssh-support.md)
* [在 Azure App Service 中設定預備環境](./web-sites-staged-publishing.md)
* [在 Linux 上使用 Azure Web 應用程式持續部署](./app-service-linux-ci-cd.md)

