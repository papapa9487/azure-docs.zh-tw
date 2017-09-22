---
title: "部署適用於 Cloud Foundry 監控的 Azure Log Analytics Nozzle | Microsoft Docs"
description: "說明如何部署適用於 Azure Log Analytics 的 Cloud Foundry Loggregator Nozzle、設定 Azure Log Analytics 和 OMS 主控台，以及使用這些工具來監控 Cloud Foundry 系統健康情況和效能計量的逐步指引。"
services: virtual-machines-linux
documentationcenter: 
author: ningk
manager: timlt
editor: 
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: ningk
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: f704a2638a4b6b57c014d502dd87303a55334672
ms.contentlocale: zh-tw
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-azure-log-analytics-nozzle-for-cloud-foundry-system-monitoring"></a>部署適用於 Cloud Foundry 系統監控的 Azure Log Analytics Nozzle

## <a name="background"></a>背景

[Azure Log Analytics](https://azure.microsoft.com/services/log-analytics/) 是 Microsoft [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/) (OMS) 中的一項服務。 它能協助您收集和分析從雲端及內部部署環境產生的資料。

Microsoft Azure Log Analytics Nozzle (下文簡稱為 Nozzle) 是一項 Cloud Foundry 元件，它能將計量從 [Cloud Foundry Loggregator](https://docs.cloudfoundry.org/loggregator/architecture.html) Firehose 轉送至 Azure Log Analytics。 在 Nozzle 的協助之下，您可以收集、檢視、分析多個部署的 Cloud Foundry 系統健康情況和效能計量。

在本文件中，您將學習如何將 Nozzle 部署到 Cloud Foundry 環境，然後從 Azure Log Analytics OMS 主控台存取資料。

## <a name="prerequisites"></a>必要條件

### <a name="1-deploy-a-cf-or-pcf-environment-in-azure"></a>1.在 Azure 中部署 CF 或 PCF 環境

您可以使用 Nozzle 搭配開放原始碼 Cloud Foundry (CF) 部署或 Pivotal Cloud Foundry (PCF) 部署。

* [在 Azure 上部署 Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md)

* [在 Azure 上部署 Pivotal Cloud Foundry](https://docs.pivotal.io/pivotalcf/1-11/customizing/azure.html)

### <a name="2-install-the-cf-command-line-tools-for-deploying-the-nozzle"></a>2.安裝部署 Nozzle 所需的 CF 命令列工具

Nozzle 會以應用程式的形式在 CF 環境中執行，因此您需要 CF CLI 來部署應用程式。 它也需要 Loggregator Firehose 和 Cloud Controller 的存取權限，因此您需要 UAA 命令列用戶端來建立及設定使用者。

* [安裝 Cloud Foundry CLI](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html)

* [安裝 Cloud Foundry UAA 命令列用戶端](https://github.com/cloudfoundry/cf-uaac/blob/master/README.md)

設定 UAA 命令列用戶端之前，請確認 Rubygems 已安裝。

### <a name="3-create-an-oms-workspace-in-azure"></a>3.在 Azure 中建立 OMS 工作區

#### <a name="create-the-oms-workspace-manually"></a>手動建立 OMS 工作區

您可以手動建立 OMS 工作區，然後在完成 Nozzle 部署之後載入預先設定的 OMS 檢視和警示。

1. 在 Azure 入口網站中，搜尋 Marketplace 服務清單以找到 Log Analytics，然後選取 [Log Analytics]。
2. 按一下 [建立]，然後選取下列項目的選項：

* OMS 工作區：輸入工作區的名稱。
* 訂用帳戶：如果您擁有多個訂用帳戶，請選擇與 CF 部署相同的訂用帳戶。
* 資源群組：您可以建立新的資源群組，或使用與 CF 部署相同的資源群組。
* 位置
* 定價層：按一下 [確定] 以完成。
> 如需詳細資訊，請參閱[開始使用 Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)

#### <a name="create-the-oms-workspace-through-the-oms-template"></a>透過 OMS 範本建立 OMS 工作區

您可以建立 OMS 工作區、載入預先設定的 OMS 檢視和警示，全部都可以透過[適用於 Cloud Foundry 的 Azure OMS Log Analytics 解決方案](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-cloudfoundry-solution)來完成

## <a name="deploy-the-nozzle"></a>部署 Nozzle

### <a name="deploy-the-nozzle-as-a-pcf-ops-manager-tile"></a>將 Nozzle 部署為 PCF Ops Manager 圖格

如果您已透過 Ops Manager 部署 PCF，請按照這些步驟來[安裝及設定 PCF 的 Nozzle](http://docs.pivotal.io/partners/azure-log-analytics-nozzle/installing.html)，Nozzle 即會安裝為 Ops Manager 的圖格。

### <a name="deploy-the-nozzle-as-an-application-to-cloud-foundry"></a>以應用程式的形式將 Nozzle 部署到 Cloud Foundry

如果您未使用 PCF Ops Manager，便需要以應用程式的形式推送 Nozzle。

#### <a name="log-in-to-your-cf-deployment-as-an-admin-through-cf-cli"></a>透過 CF CLI 以管理員身分登入 CF 部署

在您的 Dev 方塊上執行以下命令：
```
cf login -a https://api.${SYSTEM_DOMAIN} -u ${CF_USER} --skip-ssl-validation
```

> "SYSTEM_DOMAIN" 是您的 CF 網域名稱。 您可以在 CF 部署資訊清單檔案中搜尋 "SYSTEM_DOMAIN" 來取出。 
> "CF_User" 是 CF 管理員名稱。 您可以在 CF 部署資訊清單檔案中搜尋 "scim" 區段，並找尋名稱和 "cf_admin_password" 來取出名稱和密碼。

#### <a name="create-a-cf-user-and-grant-required-privileges"></a>建立 CF 使用者及授與必要權限

在您的 Dev 方塊上執行以下命令：
```
uaac target https://uaa.${SYSTEM_DOMAIN} --skip-ssl-validation
uaac token client get admin
cf create-user ${FIREHOSE_USER} ${FIREHOSE_USER_PASSWORD}
uaac member add cloud_controller.admin ${FIREHOSE_USER}
uaac member add doppler.firehose ${FIREHOSE_USER}
```

> "SYSTEM_DOMAIN" 是您的 CF 網域名稱。 您可以在 CF 部署資訊清單檔案中搜尋 "SYSTEM_DOMAIN" 來取出。

#### <a name="download-the-latest-azure-log-analytics-nozzle-release"></a>下載最新版本的 Azure Log Analytics Nozzle

在您的 Dev 方塊上執行以下命令：
```
git clone https://github.com/Azure/oms-log-analytics-firehose-nozzle.git
cd oms-log-analytics-firehose-nozzle
```

#### <a name="set-environment-variables-in-manifestyml-in-your-current-directory"></a>在目前的目錄中，於 "manifest.yml" 中設定環境變數

這是 Nozzle 的應用程式資訊清單，您需要將值取代為特定的 OMS 工作區資訊。

```
OMS_WORKSPACE             : OMS workspace ID: open OMS portal from your OMS workspace, click "Settings", click "connected sources"
OMS_KEY                   : OMS key: open OMS portal from your OMS workspace, click "Settings", click "connected sources"
OMS_POST_TIMEOUT          : HTTP post timeout for sending events to OMS Log Analytics, default is 10s.
OMS_BATCH_TIME            : Interval for posting a batch to OMS Log Analytics, default is 10s.
OMS_MAX_MSG_NUM_PER_BATCH : The max number of messages in a batch to OMS Log Analytics, default is 1000.
API_ADDR                  : The api URL of the CF environment, refer to "Push the Nozzle as an App to Cloud Foundry" section step 1 on how to retrive your <CF_SYSTEM_DOMAIN>
DOPPLER_ADDR              : Loggregator's traffic controller URL, refer to "Deploy the Nozzle as an App to Cloud Foundry" section step 1 on how to retrive your <CF_SYSTEM_DOMAIN>
FIREHOSE_USER             : CF user you created in "Push the Nozzle as an App to Cloud Foundry" section, who has firehose and Cloud Controller admin access.
FIREHOSE_USER_PASSWORD    : Password of the CF user above.
EVENT_FILTER              : Event types to be filtered out. The format is a comma separated list, valid event types are METRIC,LOG,HTTP
SKIP_SSL_VALIDATION       : If true, allows insecure connections to the UAA and the Trafficcontroller
CF_ENVIRONMENT            : Enter any string value for identifying logs and metrics from different CF environments
IDLE_TIMEOUT              : Keep Alive duration for the firehose consumer, default is 60s.
LOG_LEVEL                 : Logging level of the nozzle, valid levels: DEBUG, INFO, ERROR
LOG_EVENT_COUNT           : If true, the total count of events that the nozzle has received and sent will be logged to OMS Log Analytics as CounterEvents
LOG_EVENT_COUNT_INTERVAL  : The time interval of logging event count to OMS Log Analytics, default is 60s.
```

### <a name="push-the-application-from-your-dev-box"></a>從 Dev 方塊推送應用程式

確認您在 "oms-log-analytics-firehose-nozzle" 資料夾下，執行：
```
cf push
```

## <a name="validate-the-nozzle-installation"></a>驗證 Nozzle 安裝

### <a name="from-apps-manager-for-pcf"></a>從 Apps Manager (適用於 PCF)

1. 登入 Ops Manager，確認圖格顯示於安裝儀表板上。
2. 登入 Apps Manager，確認您為 Nozzle 建立的空間列示在使用情況報告中，並且處於正常狀態。

### <a name="from-dev-box"></a>從 Dev 方塊

在 Dev 方塊 CF CLI 視窗中，輸入：
```
cf apps
```
確認 OMS Nozzle 應用程式正在執行。

## <a name="view-the-data-in-oms-portal"></a>在 OMS 入口網站中檢視資料

### <a name="1-import-oms-view"></a>1.匯入 OMS 檢視

在 OMS 入口網站中，瀏覽至 [檢視設計工具]  ->  [匯入]  ->  [瀏覽]，選取任一個 omsview 檔案 (如 *Cloud Foundry.omsview*)，然後儲存檢視。 **圖格**隨即會顯示在主要的 OMS [概觀] 頁面上。 按一下**圖格**，它會顯示以視覺效果呈現的計量。

操作員可以透過**檢視設計工具**，來自訂這些檢視或建立新檢視。

*"Cloud Foundry.omsview"* 是預覽版本的 Cloud Foundry OMS 檢視範本，我們正在著手開發完整設定的預設範本，如果您有任何建議和意見反應，請傳送到[議題區段](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues)。

### <a name="2-create-alert-rules"></a>2.建立警示規則

操作員可以視需要[建立警示](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)，自訂查詢和閾值。 以下是一組建議的警示。

| 搜尋查詢                                                                  | 產生警示的依據 | 說明                                                                       |
| ----------------------------------------------------------------------------- | ----------------------- | --------------------------------------------------------------------------------- |
| Type=CF_ValueMetric_CL Origin_s=bbs Name_s="Domain.cf-apps"                   | 結果數目 < 1   | **bbs.Domain.cf-apps** 代表如果 cf-apps 網域處於最新狀態，表示來自 Cloud Controller 的 CF 應用程式要求已同步至 bbs.LRPsDesired (Diego 所需 AI) 以供執行。 未接收到資料表示 cf-apps 網域在指定時間範圍內不是最新狀態。 |
| Type=CF_ValueMetric_CL Origin_s=rep Name_s=UnhealthyCell Value_d>1            | 結果數目 > 0   | 對於 Diego 資料格來說，0 表示健康情況良好，1 表示健康情況不佳。 請設定警示，使其在指定時間範圍內偵測到多個**健康情況不佳的 Diego 資料格**時發出。 |
| Type=CF_ValueMetric_CL Origin_s="bosh-hm-forwarder" Name_s="system.healthy" Value_d=0 | 結果數目 > 0 | 1 表示系統健康情況良好，0 表示系統健康情況不佳。 |
| Type=CF_ValueMetric_CL Origin_s=route_emitter Name_s=ConsulDownMode Value_d>0 | 結果數目 > 0   | Consul 會定期發出它的健康情況狀態。 0 表示系統健康情況良好，1 表示路由發射器偵測到 **Consul 已關閉**。 |
| Type=CF_CounterEvent_CL Origin_s=DopplerServer (Name_s="TruncatingBuffer.DroppedMessages" or Name_s="doppler.shedEnvelopes") Delta_d>0 | 結果數目 > 0 | Doppler 因背部壓力而刻意**捨棄**的訊息差異數目。 |
| Type=CF_LogMessage_CL SourceType_s=LGR MessageType_s=ERR                      | 結果數目 > 0   | Loggregator 發出 **LGR** 來指出記錄流程發生問題 (例如，記錄訊息輸出太高)。 |
| Type=CF_ValueMetric_CL Name_s=slowConsumerAlert                               | 結果數目 > 0   | 當 Nozzle 自 Loggregator 收到緩慢取用者警示時，它會將 **slowConsumerAlert** ValueMetric 傳送給 OMS。 |
| Type=CF_CounterEvent_CL Job_s=nozzle Name_s=eventsLost Delta_d>0              | 結果數目 > 0   | 如果**遺失事件**的差異數目達到閾值，代表 Nozzle 可能發生一些執行上的問題。 |

## <a name="scale"></a>調整

### <a name="scale-the-nozzle"></a>調整 Nozzle

我們建議操作員從至少兩個 Nozzle 執行個體開始。 Firehose 會將工作負載平均分配給所有 Nozzle 執行個體。
為了確保 Nozzle 能跟上 Firehose 傳出的資料流量，操作員應設定「建立警示規則」一節列示的 **slowConsumerAlert** 警示。當警示發出時，請遵循 [Nozzle 過於緩慢時的指引](https://docs.pivotal.io/pivotalcf/1-11/loggregator/log-ops-guide.html#slow-noz)來判斷是否需要調整。
若要相應增加 Nozzle，請使用 Apps Manager 或 CF CLI 來增加 Nozzle 的執行個體數目或記憶體/磁碟資源。

### <a name="scale-the-loggregator"></a>調整 Loggregator

Loggregator 能傳送 **LGR** 記錄訊息來指出記錄流程發生的問題。 操作員能監控警示來判斷 Loggregator 是否需要相應增加。
若要相應增加 Loggregator，操作員可在 CF 資訊清單中增加 Doppler 緩衝區大小或新增額外的 Doppler 伺服器執行個體。如需詳細資訊，請參閱 [Loggregator 調整指引](https://docs.cloudfoundry.org/running/managing-cf/logging-config.html#scaling)。

## <a name="update"></a>更新

若要將 Nozzle 更新為新版本，請下載新版本的 Nozzle，再遵循「部署」一節中的步驟重新推送一次應用程式。

若要移除 Nozzle，請遵循下列步驟：

### <a name="from-the-ops-manager"></a>從 Ops Manager

1. 登入 Ops Manager
2. 找出「適用於 PCF 的 Microsoft Azure Log Analytics Nozzle」圖格
3. 按一下回收圖示並確認刪除動作。

### <a name="from-the-dev-box"></a>從 Dev 方塊

在 CF CLI 視窗中，輸入：
```
cf delete <App Name> -r
```

如果您移除 Nozzle，OMS 入口網站中的資料並不會自動移除，而是會按照您的 OMS 記錄分析保留期設定到期。

## <a name="support-and-feedback"></a>支援與意見反應

Azure Log Analytics Nozzle 係為開放原始碼，請將您的問題和意見反應傳送到 [github 區段](https://github.com/Azure/oms-log-analytics-firehose-nozzle/issues)。 若要開啟 Azure 支援要求，請使用「執行 Cloud Foundry 的虛擬機器」服務類別。 

## <a name="next-step"></a>後續步驟

除了 Nozzle 涵蓋的 Cloud Foundry 計量之外，您還可以利用 OMS 代理程式來取得 VM 層級作業資料 (Syslog、效能、警示、清查) 的深入解析。您可以將它視為 CF VM 的 Bosh 附加元件加以安裝。
> 如需詳細資訊，請參閱[將 OMS 代理程式部署到 Cloud Foundry 部署](https://github.com/Azure/oms-agent-for-linux-boshrelease)。
