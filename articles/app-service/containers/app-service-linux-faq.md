---
title: "Azure App Service Web App for Containers 常見問題集 | Microsoft Docs"
description: "Azure App Service Web App for Containers 常見問題集。"
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
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: b783f7e8cfef991e7028ba4b4c7b1d0935397580
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---
# <a name="azure-app-service-web-app-for-containers-faq"></a>Azure App Service Web App for Containers 常見問題集

隨著 Web App for Containers 的推出，我們正著手在我們的平台上新增功能和進行改善。 本文章提供我們的客戶最近向我們詢問之問題的回答。

如果您有問題，請對文章發表評論，我們會盡速回答。

## <a name="built-in-images"></a>內建映像

**我想要將平台所提供的內建 Docker 容器進行分岔。哪裡可以找到這些檔案？**

您可以在 [GitHub](https://github.com/azure-app-service) 上找到所有 Docker 檔案。 您可以在 [Docker Hub](https://hub.docker.com/u/appsvc/) 上找到所有 Docker 容器。

**設定執行階段堆疊時，在 [啟動檔案] 區段應該使用哪些值？**

針對 Node.js，您需指定 PM2 組態檔或指令碼檔案。 針對 .Net Core，請指定已編譯的 DLL 名稱。 針對 Ruby，您可以指定要用來將應用程式初始化的 Ruby 指令碼。

## <a name="management"></a>管理

**當我按下 Azure 入口網站中的 [重新啟動] 按鈕時，會發生什麼事？**

這個動作與 Docker 重新啟動相同。

**我是否可以使用「安全殼層」(SSH) 來連線到應用程式容器虛擬機器 (VM)？**

是的，您可以透過原始檔控制管理 (SCM) 網站執行該動作。

**我如何透過 SDK 或 Azure Resource Manager 範本建立 Linux App Service 方案？**

您必須將 App Service 的 **reserved** 欄位設定為 *true*。

## <a name="continuous-integration-and-deployment"></a>持續整合及部署

**我的 Web 應用程式在我已更新 Docker Hub 上的映像之後，仍然使用舊的 Docker 容器映像。您是否支援自訂容器的連續整合和部署？**

**問：**如需設定 Azure Container Registry 或 DockerHub 映像的持續整合/部署，請查看下列文章：[使用 Azure Web App for Containers 進行持續部署](./app-service-linux-ci-cd.md)。 針對私人登錄，您可以將 Web 應用程式先停止再啟動，來重新整理容器。 您也可以變更或新增虛擬應用程式設定，來強制重新整理您的容器。

**是否支援預備環境？**

是。

**我可以使用 *Web 部署*來部署我的 Web 應用程式嗎？**

是，您需要將稱為 `WEBSITE_WEBDEPLOY_USE_SCM` 的應用程式設定設定為 *false*。

## <a name="language-support"></a>語言支援

**是否支援未編譯的 .NET Core 應用程式？**

是。

**您是否支援以 Composer 做為 PHP 應用程式的相依性管理程式？**

是。 在 Git 部署期間，Kudu 應該會偵測到您要部署 PHP 應用程式 (這點受惠於 composer.lock 檔案)，然後 Kudu 會為您觸發編輯器安裝。

## <a name="custom-containers"></a>自訂容器

**我使用自己的自訂容器。我希望平台將 SMB 共用掛接至 `/home/` 目錄。**

您可以將 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` 應用程式設定設為 *true*，或完全移除應用程式設定來達成。 請記住，當平台儲存體進行變更時，如此會導致容器重新啟動。 

>[!NOTE]
>如果 `WEBSITES_ENABLE_APP_SERVICE_STORAGE` 設定為 *false*，`/home/` 目錄不會由級別執行個體共用，而且寫入其中的檔案不會保存到重新啟動之後。

**我的自訂容器需要很長時間才能啟動，而平台會在它完成啟動之前將容器重新啟動。**

您可以設定在重新啟動容器之前，平台所要等待的時間量。 若要這樣做，請將 `WEBSITES_CONTAINER_START_TIME_LIMIT` 應用程式設定設定成您要的值。 預設值為 230 秒，而最大值為 600 秒。

**私人登錄伺服器 URL 的格式為何？**

提供包括 `http://` 或 `https://` 的完整登錄 URL。

**私人登錄選項中的映像名稱格式為何？**

新增包括私人登錄 URL 的完整映像名稱 (例如 myacr.azurecr.io/dotnet:latest)。 使用自訂連接埠的映像名稱[無法透過入口網站輸入](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650)。 若要設定 `docker-custom-image-name`，請使用 [`az` 命令列工具](https://docs.microsoft.com/en-us/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set)。

**我可以在我的自訂容器映像上公開多個連接埠嗎？**

我們目前不支援公開多個連接埠。

**我可以攜帶自己的儲存體嗎？**

我們目前不支援攜帶您自己的儲存體。

**為什麼我無法從 SCM 網站瀏覽自訂容器的檔案系統或執行中處理序？**

SCM 網站是在個別的容器中執行。 您無法檢查應用程式容器的檔案系統或執行中的處理程序。

**我的自訂容器接聽連接埠 80 以外的連接埠。如何設定我的應用程式將要求路由至該連接埠？**

我們有自動連接埠偵測。 您也可以指定一個叫做 *WEBSITES_PORT* 的應用程式設定，為它提供一個預期的連接埠號碼值。 之前，平台是使用 *PORT* 應用程式設定。 我們計劃淘汰此應用程式設定，並專門使用 *WEBSITES_PORT*。

**我是否需要在我的自訂容器中實作 HTTPS？**

不需要，平台會在共用的前端處理 HTTPS 終止。

## <a name="pricing-and-sla"></a>價格和 SLA

**既然服務已正式推出，服務的定價為何？**

我們會以標準 Azure App Service 定價，向您收取應用程式執行時數的費用。

## <a name="other-questions"></a>其他問題

**應用程式設定名稱所支援的字元為何？**

對於應用程式設定，您僅可以使用字母 (A-Z、a-z)、數字 (0-9) 和底線字元 (_)。

**我可以在何處提出新功能的要求？**

您可以在 [Web Apps 意見反應論壇](https://aka.ms/webapps-uservoice) (英文) 提交您的想法。 將 "[Linux]" 新增至您想法的標題。

## <a name="next-steps"></a>後續步驟

* [什麼是 Azure Web App for Containers？](app-service-linux-intro.md)
* [在 Azure App Service 中設定預備環境](../../app-service/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [使用 Azure Web App for Containers 進行持續部署](./app-service-linux-ci-cd.md)

