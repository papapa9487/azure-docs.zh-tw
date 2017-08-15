---
title: "使用現有的內部部署 Proxy 伺服器與 Azure AD | Microsoft Docs"
description: "涵蓋如何使用現有的內部部署 Proxy 伺服器。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: kgremban
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: bdca442755507c4ffe8d43692c5b7f2aa3a746f3
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---

# <a name="work-with-existing-on-premises-proxy-servers"></a>使用現有的內部部署 Proxy 伺服器

本文說明如何設定 Azure Active Directory (Azure AD) 應用程式 Proxy 連接器以使用輸出 Proxy 伺服器。 它適用於具有現有 Proxy 之網路環境的客戶。

我們先來看看這些主要部署案例︰
* 設定連接器以略過您的內部部署輸出 Proxy。
* 設定要使用輸出 Proxy 來存取 Azure AD 應用程式 Proxy 的連接器。

如需連接器運作方式的詳細資訊，請參閱[了解 Azure AD 應用程式 Proxy 連接器](application-proxy-understand-connectors.md)。

## <a name="configure-the-outbound-proxy"></a>設定輸出 Proxy

如果您的環境中有輸出 Proxy，請使用具有適當權限的帳戶來設定輸出 Proxy。 安裝程式會在進行安裝的使用者內容中執行，因此您可以使用 Microsoft Edge 或其他網際網路瀏覽器來檢查組態。

若要在 Microsoft Edge 中設定 Proxy 的設定︰

1. 移至 [設定] > [檢視進階設定] > [開啟 Proxy 設定] > [手動 Proxy 設定]。
2. 將 [使用 Proxy 伺服器] 設定為 [開啟]，選取 [不要為近端 (內部網路) 位址使用 Proxy 伺服器] 核取方塊，然後變更位址和連接埠以反映您的本機 Proxy 伺服器。
3. 填寫必要的 Proxy 設定。

   ![Proxy 設定的對話方塊](./media/application-proxy-working-with-proxy-servers/proxy-bypass-local-addresses.png)

## <a name="bypass-outbound-proxies"></a>略過輸出 Proxy

連接器有會發出輸出要求的基礎作業系統元件。 這些元件會自動嘗試在網路上找出 Proxy 伺服器。 然後使用 Web Proxy 自動探索 (WPAD) (如果已在環境中啟用)。

OS 元件會嘗試藉由對 wpad.domainsuffix 執行 DNS 查閱來尋找 Proxy 伺服器。 如果這在 DNS 中解決，則會對 wpad.dat 的 IP 位址提出 HTTP 要求。 此要求會成為您環境中的 Proxy 組態指令碼。 連接器會使用這個指令碼來選取輸出 Proxy 伺服器。 不過，連接器流量可能會因為 Proxy 上所需的其他組態設定而仍然不會通過。

您可以將連接器設定為略過內部部署 Proxy，以確保它會使用與 Azure 服務的直接連線。 我們之所以建議這種方式 (若您的網路原則允許)，是因為這表示您有一個要維護的較少組態。

若要停用連接器的輸出 Proxy 使用，請編輯 C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config 檔案，並新增此程式碼範例中所示的 system.net 區段︰

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```
若要確保連接器更新程式服務也會略過 Proxy，對位於 C:\Program Files\Microsoft AAD App Proxy Connector Updater 的 ApplicationProxyConnectorUpdaterService.exe.config 檔案進行類似的變更。

請務必複製原始檔案，以免您需要還原為預設 .config 檔案。

## <a name="use-the-outbound-proxy-server"></a>使用輸出 Proxy 伺服器

某些環境會要求所有輸出流量通過輸出 Proxy，無一例外。 如此一來，略過 Proxy 不是選項。

您可以設定連接器流量通過輸出 Proxy，如下圖所示：

 ![設定讓連接器流量通過輸出 Proxy 來到達 Azure AD 應用程式 Proxy](./media/application-proxy-working-with-proxy-servers/configure-proxy-settings.png)

由於僅有輸出流量，因此您不需要設定透過防火牆來進行的輸入存取。

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>步驟 1：設定連接器和相關服務以通過輸出 Proxy

如先前所述，若 WPAD 在環境中啟用並正確地設定，連接器會自動探索輸出 Proxy 伺服器，並嘗試使用它。 不過，您可以明確地設定連接器以通過輸出 Proxy。

若要這樣做，請編輯 C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config 檔案，並新增此程式碼範例中所示的 system.net 區段。 變更 proxyserver:8080 以反映您的本機 Proxy 伺服器名稱或 IP 位址與其正在接聽的連接埠。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

接著，設定連接器更新程式服務來使用 Proxy，方法為對位於 C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config 的檔案進行類似變更。

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>步驟 2︰設定 Proxy 以允許來自連接器與相關服務的流量通過

在輸出 Proxy 有 4 個層面需要考量：
* Proxy 輸出規則
* Proxy 驗證
* Proxy 連接埠
* SSL 審查

#### <a name="proxy-outbound-rules"></a>Proxy 輸出規則
允許存取下列端點以取得連接器服務的存取權︰

* *.msappproxy.net
* *.servicebus.windows.net

針對初始註冊，允許存取下列端點︰

* login.windows.net
* login.microsoftonline.com

如果您不允許 FQDN 連線且需要改為指定 IP 範圍，請使用下列選項：

* 允許連接器輸出存取所有目的地。
* 允許連接器輸出存取 [Azure 資料中心 IP 範圍](https://www.microsoft.com/en-gb/download/details.aspx?id=41653)。 Azure 資料中心 IP 範圍清單在使用上的麻煩在於此清單是每週更新。 您必須制定程序，以確保存取規則會跟著更新。

#### <a name="proxy-authentication"></a>Proxy 驗證

目前不支援 Proxy 驗證。 目前的建議是允許連接器匿名存取網際網路目的地。

#### <a name="proxy-ports"></a>Proxy 連接埠

連接器可使用 CONNECT 方法進行輸出 SSL 型連線。 此方法基本上會透過輸出 Proxy 設定通道。 設定 Proxy 伺服器以允許連接埠 443 和 80 通道。

>[!NOTE]
>當服務匯流排在 HTTPS 上執行時，它會使用連接埠 443。 不過，根據預設，服務匯流排會嘗試導向 TCP 連線，且僅在直接連線失敗時，才會改為使用 HTTPS。

若要確認服務匯流排流量也會傳送輸出 Proxy 伺服器，請確定連接器無法直接連接到連接埠 9350、9352 和 5671 的 Azure 服務。

#### <a name="ssl-inspection"></a>SSL 審查
請勿對連接器流量使用 SSL 審查，因為這樣會導致連接器流量發生問題。

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>針對連接器 Proxy 問題和服務連線問題進行疑難排解
現在您應該會看到所有的流量通過 Proxy。 如果您有問題，下列疑難排解資訊應該會有幫助。

找出連接器連線問題並進行疑難排解的最佳方式，是在啟動連接器服務時，在連接器服務上進行網路擷取。 這可能是令人怯步的工作，因此讓我們看看關於擷取及篩選網路追蹤的快速提示。

您可以使用您選擇的監視工具。 基於本文的目的，我們使用了 Microsoft Network Monitor 3.4。 您可以[從 Microsoft 下載](https://www.microsoft.com/download/details.aspx?id=4865)。

我們在後續章節所使用的範例與篩選器僅限網路監視器，但這些原則可以套用到任何分析工具。

### <a name="take-a-capture-by-using-network-monitor"></a>使用網路監視器進行擷取

若要開始擷取︰

1. 開啟網路監視器，並按一下 [新增擷取]。
2. 按一下 [啟動] 按鈕。

   ![網路監視器視窗](./media/application-proxy-working-with-proxy-servers/network-capture.png)

完成擷取之後，按一下 [停止] 按鈕予以結束。

### <a name="take-a-capture-of-connector-traffic"></a>擷取連接器流量

如需初始的疑難排解，執行下列步驟︰

1. 從 services.msc 停止 Azure AD 應用程式 Proxy 連接器服務。
2. 啟動網路擷取。
3. 啟動 Azure AD 應用程式 Proxy 連接器服務。
4. 停止網路擷取。

   ![services.msc 中的 Azure AD 應用程式 Proxy 連接器服務](./media/application-proxy-working-with-proxy-servers/services-local.png)

### <a name="look-at-the-requests-from-the-connector-to-the-proxy-server"></a>查看連接器對 Proxy 伺服器的要求

現在，您有網路擷取，準備好要進行篩選。 查看追蹤的關鍵在於了解如何篩選擷取。

其中一個篩選條件如下所示 (其中 8080 是 Proxy 服務連接埠)︰

**(http.Request or http.Response) and tcp.port==8080**

如果您在 [顯示篩選器] 視窗中輸入此篩選器並選取 [套用]，它會根據篩選條件篩選擷取的流量。

上述篩選條件只會顯示 HTTP 要求和往返 Proxy 連接埠的回應。 針對連接器設定為使用 Proxy 伺服器的連接器啟動，篩選條件會顯示如下︰

 ![篩選之 HTTP 要求和回應的範例清單](./media/application-proxy-working-with-proxy-servers/http-requests.png)

您現在要特別尋找顯示與 Proxy 伺服器通訊的 CONNECT 要求。 成功時，您會收到 HTTP OK (200) 回應。

如果您看到其他回應碼，例如 407 或 502，則表示 Proxy 需要驗證或由於某些其他原因不允許流量。 此時您會與您的 Proxy 伺服器支援團隊合作。

### <a name="identify-failed-tcp-connection-attempts"></a>識別失敗的 TCP 連線嘗試

您可能感興趣的其他常見案例是當連接器嘗試直接連線但是失敗。

另一個可協助您更容易找出這個問題的網路監視器篩選條件是︰

**property.TCPSynRetransmit**

SYN 封包是傳送到建立 TCP 連線的第一個封包。 如果此封包未傳回回應，則會重新嘗試 SYN。 您可以使用上述篩選條件來查看任何重新傳輸的 SYN。 然後，您可以檢查這些 SYN 是否對應至任何與連接器相關的流量。

下列範例會示範失敗的服務匯流排連接埠 9352 連線嘗試︰

 ![失敗連線嘗試的範例回應](./media/application-proxy-working-with-proxy-servers/failed-connection-attempt.png)

如果您看到類似上述的回應，則表示連接器嘗試直接與 Azure 服務匯流排服務通訊。 如果您希望該連接器直接連線至 Azure 服務，則此回應清楚表示您有網路或防火牆問題。

>[!NOTE]
>如果您已設定為使用 Proxy 伺服器，此回應可能表示服務匯流排在改為嘗試透過 HTTPS 連線之前，會先嘗試直接 TCP 連線。
>

網路追蹤分析並非適合每個人。 但它會是可用來快速取得您網路運作情況相關資訊的寶貴工具。

如果您繼續遭遇連接器連線問題，請向我們的支援小組建立票證。 該小組能協助您進行進一步疑難排解。

如需有關解決應用程式 Proxy 連接器錯誤的相關資訊，請參閱[疑難排解應用程式 Proxy](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-troubleshoot)。

## <a name="next-steps"></a>後續步驟

[了解 Azure AD 應用程式 Proxy 連接器](application-proxy-understand-connectors.md)<br>
[如何以無訊息方式安裝 Azure AD 應用程式 Proxy 連接器](active-directory-application-proxy-silent-installation.md)

