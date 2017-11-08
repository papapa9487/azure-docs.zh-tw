---
title: "Azure IoT Suite 常見問題集 | Microsoft Docs"
description: "IoT 套件的常見問題集"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2017
ms.author: dobett
ms.openlocfilehash: 9dfb0c898e74063719b3fd242d1fa9be2b89246c
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="frequently-asked-questions-for-iot-suite"></a>IoT 套件的常見問題集

另請參閱，連線處理站專用的[常見問題集](iot-suite-faq-cf.md)。

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>哪裡可以找到預先設定之解決方案的原始程式碼？

原始程式碼儲存在下列 GitHub 儲存機制中︰

* [遠端監視預先設定的解決方案 (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [遠端監視預先設定的解決方案 (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [預先設定的預防性維護的解決方案](https://github.com/Azure/azure-iot-predictive-maintenance)
* [連線處理站預先設定的解決方案](https://github.com/Azure/azure-iot-connected-factory)

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>佈建新的遠端監視解決方案需要多少成本？

新的預先設定解決方案會提供兩種部署選項：

* 基本選項，是針對尋求降低開發成本的開發人員，或需要建置示範或概念證明的客戶所設計。
* 標準選項，是針對需要部署備妥基礎結構的企業而設計。

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>如何確定我在開發解決方案的同時保持降低成本？

除了提供兩個不同的部署之外，新的遠端監視解決方案有一項設定，可隨選啟用或停用所有模擬的裝置。 停用模擬會減少解決方案中內嵌的資料，並因而降低整體成本。

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-preconfigured-solutions"></a>新的微服務架構是否適用於這三個預先設定的解決方案？

目前，只有遠端監視解決方案是使用微服務架構，因為它涵蓋最廣泛的情節。

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>以微服務作為基礎的開放原始碼新基礎架構能在更新中提供哪些優點？

在過去兩年中，雲端架構已大幅發展。 微服務問世以來都是絕佳的模式，可達成延展及彈性，而不必犧牲開發速度。 此架構模式運用於多項在內部使用的 Microsoft 服務，都獲得絕佳的可靠性與延展性結果。 我們會將這些學習放入實務，以便客戶能夠從中獲益。

### <a name="is-the-new-preconfigured-solution-available-in-the-same-geographic-region-as-the-existing-solution"></a>是否可在相同的地理區域中使用新的預先設定解決方案作為現有的解決方案？

是，可在相同的地理區域中使用新的遠端監視。

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>基本和標準部署選項之間的差異為何？ 如何在兩個部署選項之間做決定？

每個部署選項都會對應到不同的需求。 基本部署是針對入門及開發 PoC 及小型試驗而設計。 它可以最低需求的資源和較低的成本來提供簡化的架構。 標準部署是針對建置和自訂備妥解決方案而設計，且能提供具有實現所需必要元素的部署。 對於可靠性與調整，應用程式微服務會建置作為 Docker 容器，並使用協調器 (依預設為 Kubernetes) 加以部署。 協調器會負責部署、調整及管理應用程式。 您應該以目前的需求作為基礎選擇一個選項。 您可以根據您的專案階段，使用其中一個、另一個或兩者的組合。

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-suite"></a>可以繼續利用我在 Azure IoT 套件中的現有投資嗎？

是。 目前存在的任何解決方案都會繼續在您的 Azure 訂用帳戶中運作，而且原始碼在 GitHub 中仍保持可用。

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>在 Azure 入口網站中刪除資源群組，與在 azureiotsuite.com 中對預先設定解決方案按一下 [刪除] 之間的差異為何？

* 如果您在 [azureiotsuite.com](https://www.azureiotsuite.com/) 中刪除預先設定的解決方案，就會刪除您在建立該預先設定解決方案時所佈建的一切資源。 將額外資源新增到資源群組時，也會一併刪除這些資源。
* 如果您在 [Azure 入口網站](https://portal.azure.com)中刪除資源群組，則只會刪除該資源群組中的資源。 您還必須刪除與預先設定解決方案關聯的 Azure Active Directory 應用程式。

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>我可以在一個訂用帳戶中佈建多少個 IoT 中樞執行個體？

根據預設，您的佈建方式可以是[每個訂用帳戶 10 個 IoT 中樞](../azure-subscription-service-limits.md#iot-hub-limits)。 您可以建立 [Azure 支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)來提高此限制。 結果就是，由於每個預先設定的解決方案會佈建一個新的「IoT 中樞」，因此在一個指定的訂用帳戶中，您最多只能佈建 10 個預先設定的解決方案。

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>我可以在一個訂用帳戶中佈建多少個 Azure Cosmos DB 執行個體？

50 個。 您可以建立 [Azure 支援票證](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)來提高此限制，但每一個訂用帳戶預設只能佈建 50 個 Cosmos DB 執行個體。

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>我可以在一個訂用帳戶中佈建多少個免費 Bing 地圖服務 API？

2 個。 您只可以在 Azure 訂用帳戶中針對 Enterprise 方案建立兩個內部交易層級 1 Bing 地圖。 根據預設，遠端監視方案會隨著內部交易層級 1 方案一起佈建。 因此，您只可以在一個訂用帳戶中最多佈建 2 個遠端監視方案。

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>如果我有 Microsoft Azure for DreamSpark，是否可以建立預先設定的解決方案？

目前，您無法使用 [Microsoft Azure for DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99) 帳戶來建立預先設定的解決方案。 不過，您只需花幾分鐘即可建立 [Azure 免費試用帳戶](https://azure.microsoft.com/free/)，此帳戶可讓您建立預先設定的解決方案。

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>如果我有雲端方案提供者 (CSP) 訂用帳戶，是否可以建立預先設定的解決方案？

目前您無法透過雲端方案提供者 (CSP) 訂用帳戶建立預先設定的解決方案。 不過，您只需花幾分鐘即可建立 [Azure 免費試用帳戶](https://azure.microsoft.com/free/)，此帳戶可讓您建立預先設定的解決方案。

### <a name="how-do-i-delete-an-aad-tenant"></a>如何刪除 AAD 租用戶？

請參閱 Eric Golpe 的部落格文章：[刪除 Azure AD 租用戶的逐步解說](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx)。

### <a name="next-steps"></a>後續步驟

您也可以瀏覽一些其他功能和預先設定的 IoT 套件解決方案的功能︰

* [預測性維護預先設定的解決方案概觀](iot-suite-predictive-overview.md)
* [連線處理站預先設定的解決方案概觀](iot-suite-connected-factory-overview.md)
* [從頭建立 IoT 安全性](securing-iot-ground-up.md)