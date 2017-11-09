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
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: 5d795a8aaaa8db5b4b5705b0c6ffd303ea1985c0
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2017
---
# <a name="frequently-asked-questions-for-iot-suite"></a>IoT 套件的常見問題集

另請參閱，連線處理站專用的[常見問題集](iot-suite-faq-cf.md)。

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>哪裡可以找到預先設定之解決方案的原始程式碼？

原始程式碼儲存在下列 GitHub 儲存機制中︰
* [遠端監視預先設定的解決方案][lnk-remote-monitoring-github]
* [預先設定的預防性維護的解決方案][lnk-predictive-maintenance-github]
* [連線處理站預先設定的解決方案](https://github.com/Azure/azure-iot-connected-factory)

### <a name="how-do-i-update-to-the-latest-version-of-the-remote-monitoring-preconfigured-solution-that-uses-the-iot-hub-device-management-features"></a>如何更新為最新版預先設定的遠端監視解決方案，其使用 IoT 中樞裝置管理功能？

* 如果您從 https://www.azureiotsuite.com/ 網站部署預先設定的解決方案，一律會部署最新版解決方案的新執行個體。
* 如果您使用命令列來部署預先設定的解決方案，您可以使用新的程式碼來更新現有的部署。 請參閱 GitHub [儲存機制][lnk-remote-monitoring-github]中的[雲端部署][lnk-cloud-deployment]。

### <a name="how-can-i-add-support-for-a-new-device-method-to-the-remote-monitoring-preconfigured-solution"></a>如何將新裝置方法的支援新增至預先設定的遠端監視解決方案？

請參閱[自訂預先設定的解決方案][lnk-customize]一文中的[將新方法的支援新增至模擬器][lnk-add-method]一節。

### <a name="the-simulated-device-is-ignoring-my-desired-property-changes-why"></a>模擬裝置會忽略我的所需屬性變更，為什麼？
在預先設定的遠端監視解決方案中，模擬裝置程式碼只會使用 **Desired.Config.TemperatureMeanValue** 和 **Desired.Config.TelemetryInterval** 所需屬性來更新報告屬性。 所有其他所需屬性變更要求都會被忽略。

### <a name="my-device-does-not-appear-in-the-list-of-devices-in-the-solution-dashboard-why"></a>我的裝置未顯示於解決方案儀表板的裝置清單中，為什麼？

解決方案儀表板中的裝置清單會使用查詢來傳回裝置清單。 目前，查詢無法傳回超過 10,000 個裝置。 試著讓查詢的搜尋準則更具限制性。

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>在 Azure 入口網站中刪除資源群組，與在 azureiotsuite.com 中對預先設定解決方案按一下 [刪除] 之間的差異為何？

* 如果您在 [azureiotsuite.com][lnk-azureiotsuite] 中刪除預先設定的解決方案，將會刪除您在建立該預先設定解決方案時所佈建的一切資源。 將額外資源新增到資源群組時，也會一併刪除這些資源。 
* 如果您在 [Azure 入口網站][lnk-azure-portal]中刪除資源群組，則只會刪除該資源群組中的資源。 您還必須在 [Azure 傳統入口網站][lnk-classic-portal]中刪除與預先設定的解決方案關聯的 Azure Active Directory 應用程式。

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>我可以在一個訂用帳戶中佈建多少個 IoT 中樞執行個體？

根據預設，您的佈建方式可以是[每個訂用帳戶 10 個 IoT 中樞][link-azuresublimits]。 您可以建立 [Azure 支援票證][link-azuresupportticket]來提高此限制。 結果就是，由於每個預先設定的解決方案會佈建一個新的「IoT 中樞」，因此在一個指定的訂用帳戶中，您最多只能佈建 10 個預先設定的解決方案。 

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>我可以在一個訂用帳戶中佈建多少個 Azure Cosmos DB 執行個體？

50 個。 您可以建立 [Azure 支援票證][link-azuresupportticket]來提高此限制，但每一個訂用帳戶預設只能佈建 50 個 Cosmos DB 執行個體。 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>我可以在一個訂用帳戶中佈建多少個免費 Bing 地圖服務 API？

2 個。 您只可以在 Azure 訂用帳戶中針對 Enterprise 方案建立兩個內部交易層級 1 Bing 地圖。 根據預設，遠端監視方案會隨著內部交易層級 1 方案一起佈建。 因此，您只可以在一個訂用帳戶中最多佈建 2 個遠端監視方案。

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>我有使用靜態地圖的遠端監視解決方案部署，如何加入互動式 Bing 地圖？

1. 從 [Azure 入口網站][lnk-azure-portal]取得 Bing Maps API for Enterprise QueryKey： 
   
   1. 在 [Azure 入口網站][lnk-azure-portal]中瀏覽至 Bing Maps API for Enterprise 所在的資源群組。
   2. 依序按一下 [所有設定] 和 [金鑰管理]。 
   3. 您可以看到兩個金鑰︰**MasterKey** 和 **QueryKey**。 請複製 **QueryKey** 的值。
      
      > [!NOTE]
      > 沒有 Bing Maps API for Enterprise 帳戶嗎？ 請在 [Azure 入口網站][lnk-azure-portal]中按一下 [+ 新增]、搜尋 Bing Maps API for Enterprise，然後依照提示來建立一個帳戶。
      > 
      > 
2. 從 [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github] 下拉最新的程式碼。
3. 依照儲存機制的 /docs/ 資料夾中的命令列部署指引，執行本機或雲端部署。 
4. 執行本機或雲端部署之後，在您的根資料夾中尋找部署期間所建立的 *.user.config。 在文字編輯器中開啟這個檔案。 
5. 變更下列這一行，使其包含從 **QueryKey** 複製的值︰ 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>如果我有 Microsoft Azure for DreamSpark，是否可以建立預先設定的解決方案？

目前，您無法使用 [Microsoft Azure for DreamSpark][lnk-dreamspark] 帳戶來建立預先設定的解決方案。 不過，您只需花幾分鐘即可建立 [Azure 免費試用帳戶][lnk-30daytrial]，此帳戶可讓您建立預先設定的解決方案。

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>如果我有雲端方案提供者 (CSP) 訂用帳戶，是否可以建立預先設定的解決方案？

目前您無法透過雲端方案提供者 (CSP) 訂用帳戶建立預先設定的解決方案。 不過，您只需花幾分鐘即可建立 [Azure 免費試用帳戶][lnk-30daytrial]，此帳戶可讓您建立預先設定的解決方案。

### <a name="how-do-i-delete-an-aad-tenant"></a>如何刪除 AAD 租用戶？

請參閱 Eric Golpe 的部落格文章：[刪除 Azure AD 租用戶的逐步解說 (英文)][lnk-delete-aad-tennant]。

### <a name="next-steps"></a>後續步驟

您也可以瀏覽一些其他功能和預先設定的 IoT 套件解決方案的功能︰

* [預先設定的預防性維護解決方案概觀][lnk-predictive-overview]
* [連線處理站預先設定的解決方案概觀](iot-suite-connected-factory-overview.md)
* [從頭建立 IoT 安全性][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx
[lnk-cloud-deployment]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
[lnk-add-method]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md#add-support-for-a-new-method-to-the-simulator
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-predictive-maintenance-github]: https://github.com/Azure/azure-iot-predictive-maintenance
