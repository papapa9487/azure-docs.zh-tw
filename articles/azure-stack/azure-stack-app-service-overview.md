---
title: "App Service 概觀：Azure Stack | Microsoft Docs"
description: "Azure Stack 上的 App Service 概觀"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/3/2017
ms.author: anwestg
ms.translationtype: HT
ms.sourcegitcommit: 349fe8129b0f98b3ed43da5114b9d8882989c3b2
ms.openlocfilehash: 13928744e7d2fc145662c2a0d5c26d512cf02150
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---

# <a name="app-service-on-azure-stack-overview"></a>Azure Stack 上的 App Service 概觀

Azure Stack 上的 Azure App Service 是 Azure Stack 內含的 Azure 供應項目。 Azure Stack 上的 App Service 安裝程式會建立下列角色執行個體組合：

*  控制器
*  管理 (會建立兩個執行個體)
*  前端
*  發佈者
*  背景工作 (處於共用模式)

此外，Azure Stack 上的 App Service 安裝程式也會建立檔案伺服器。
    
## <a name="whats-new-in-the-first-release-candidate-of-app-service-on-azure-stack"></a>Azure Stack 上之 App Service 的第一個候選版有哪些新功能？
![Azure Stack 上的 App Service 入口網站][1]

Azure Stack 上之 App Service 的第一個候選版建置於第三個預覽上，並引進了新功能與改良功能：

* 以 Active Directory 同盟服務為基礎之 Azure Stack 環境中的 Azure Functions 
* 對於 Functions 入口網站的單一登入支援以及進階開發人員工具 (Kudu)
* 適用於 Web、行動裝置版和 API 應用程式的 Java 支援
* 依虛擬機器擴展集管理背景工作層，以改善服務管理員的相應放大功能
* 將管理員體驗當地語系化
* 提高服務的穩定性
* 租用戶入口網站體驗更新及安裝程序更新

## <a name="limitations-of-the-technical-preview"></a>技術預覽的限制

儘管我們確實監看了 Azure Stack MSDN 論壇，但沒有任何對於 Azure Stack 上之 App Service 預覽版本的支援。 請勿將生產工作負載放在此預覽版本上。 Azure Stack 上的 App Service 預覽版本之間也沒有任何升級。 這些預覽版本的主要目的是顯示我們所提供的內容，並取得意見反應。 

## <a name="what-is-an-app-service-plan"></a>什麼是 App Service 方案？

App Service 資源提供者所使用的程式碼與 Azure App Service 使用的一樣。 因此，有些通用概念值得一提。 在 App Service 中，應用程式的定價容器稱為 App Service 方案。 它代表用來保存您應用程式的專用虛擬機器組。 在指定訂用帳戶內，您可以有多個 App Service 方案。 

在 Azure 中，有共用和專用背景工作。 共用背景工作支援裝載高密度的多租用戶應用程式，而且只有一組共用背景工作。 專用伺服器只可供一個租用戶使用且分成三種大小：小型、中型和大型。 內部部署客戶的需求永遠無法使用這些詞彙來描述。 在 Azure Stack 上的 App Service 中，資源提供者系統管理員可以定義他們想要提供的背景工作層。 系統管理員可以根據其獨特的裝載需求，來定義多組共用背景工作或不同的專用背景工作組。 透過使用這些背景工作層定義，他們接著就能定義自己的定價 SKU。

## <a name="portal-features"></a>入口網站功能

Azure Stack 上的 App Service 所使用的 UI 與 Azure App Service 使用的相同，後端也是如此。 某些功能已停用且無法在 Azure Stack 中運作。 Azure Stack 中尚未提供那些功能所需的 Azure 特有預期或服務。 

## <a name="next-steps"></a>後續步驟

- [開始使用 Azure Stack 上的 App Service 之前](azure-stack-app-service-before-you-get-started.md)
- [安裝 App Service 資源提供者](azure-stack-app-service-deploy.md)

您也可以嘗試其他[平台即服務 (PaaS) 服務](azure-stack-tools-paas-services.md)，例如 [SQL Server 資源提供者](azure-stack-sql-resource-provider-deploy.md)和 [MySQL 資源提供者](azure-stack-mysql-resource-provider-deploy.md)。

<!--Image references-->
[1]: ./media/azure-stack-app-service-overview/AppService_Portal.png

