---
title: "安裝內部部署資料閘道 - Azure Logic Apps | Microsoft Docs"
description: "在存取內部部署資料來源之前，安裝內部部署資料閘道以便在內部部署資料來源和邏輯應用程式之間快速地傳輸和加密資料"
keywords: "存取資料, 內部部署, 資料傳輸, 加密, 資料來源"
services: logic-apps
documentationcenter: 
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/14/2017
ms.author: LADocs; millopis; estfan
ms.openlocfilehash: b3c1e2afadea91f010c3e4b43206b6d30a75ec38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="install-the-on-premises-data-gateway-for-azure-logic-apps"></a>安裝 Azure Logic Apps 的內部部署資料閘道

您必須先安裝和設定內部部署資料閘道，邏輯應用程式才能存取內部部署資料來源。 此閘道可作為橋樑，讓內部部署系統與邏輯應用程式之間快速地傳輸和加密資料。 閘道會在加密通道上經過 Azure 服務匯流排轉送來自內部部署來源的資料。 源自閘道代理程式的所有流量都是安全輸出流量。 深入了解[資料閘道的運作方式](#gateway-cloud-service)。

閘道可連線到下列內部部署資料來源︰

*   BizTalk Server 2016
*   DB2  
*   檔案系統
*   Informix
*   MQ
*   MySQL
*   Oracle 資料庫
*   PostgreSQL
*   SAP 應用程式伺服器 
*   SAP 訊息伺服器
*   SharePoint
*   SQL Server
*   Teradata

下列步驟會說明如何先安裝內部部署資料閘道，再[設定閘道與邏輯應用程式之間的連線](./logic-apps-gateway-connection.md)。 如需所支援連接器的詳細資訊，請參閱 [Azure Logic Apps 的連接器](https://docs.microsoft.com/azure/connectors/apis-list)。 

如需如何使用閘道與其他服務的資訊，請參閱下列文章：

*   [Microsoft Power BI 內部部署資料閘道](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Azure Analysis Services 內部部署資料閘道](../analysis-services/analysis-services-gateway.md)
*   [Microsoft Flow 內部部署資料閘道](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Microsoft PowerApps 內部部署資料閘道](https://powerapps.microsoft.com/tutorials/gateway-management/)

<a name="requirements"></a>

## <a name="requirements"></a>需求

**最低**：

* .NET 4.5 Framework
* 64 位元版本的 Windows 7 或 Windows Server 2008 R2 (或更新版本)

**建議**：

* 8 核心 CPU
* 8 GB 記憶體
* 64 位元版本的 Windows 2012 R2 (或更新版本)

**重要考量**︰

* 請只在本機電腦上安裝內部部署資料閘道。
您不能在網域控制站上安裝閘道。

   > [!TIP]
   > 閘道不必安裝在和資料來源相同的電腦上。 若要盡量減少延遲，您可以將閘道安裝在最靠近資料來源的位置或同一部電腦上，但前提是您有相關權限。

* 請勿將閘道安裝在關閉、進入睡眠狀態，或未連線到網際網路的電腦上，因為閘道無法在這些情況下執行。 此外，透過無線網路的閘道效能可能會受到影響。

* 在安裝期間，您必須登入[公司或學校帳戶](https://docs.microsoft.com/azure/active-directory/sign-up-organization)，該帳戶是由 Azure Active Directory (Azure AD) 所管理，而非 Microsoft 帳戶。

  > [!TIP]
  > 如果您想要將擁有 Visual Studio 的 Microsoft 帳戶與 MSDN 訂用帳戶搭配使用，請先使用您的 Microsoft 帳戶[在 Azure Active Directory 中建立一個目錄 (租用戶)](../active-directory/develop/active-directory-howto-tenant.md) 或使用預設目錄。 請將使用者和密碼新增至目錄，然後將您訂用帳戶的存取權授與該使用者。 接著，您就可以在安裝閘道時，使用此使用者名稱和密碼來進行登入。

  稍後在 Azure 入口網站中，當您建立閘道資源並將其與閘道安裝建立關聯時，必須使用此相同的公司或學校帳戶。 接著，在建立邏輯應用程式與內部部署資料來源間的連線時，選取該閘道資源。 [為何必須使用公司或學校的 Azure AD 帳戶？](#why-azure-work-school-account)

  > [!TIP]
  > 如果您註冊 Office 365 供應項目，但是未提供實際的公司電子郵件，您的登入位址看起來可能會像 jeff@contoso.onmicrosoft.com。 

* 如果您現有的閘道在設定時使用的是 14.16.6317.4 版之前的安裝程式，您就無法藉由執行最新版的安裝程式來變更閘道位置。 不過，您可以使用最新版的安裝程式，來對新的閘道設定您想要的位置。
  
  如果您的閘道安裝程式版本比 14.16.6317.4 還舊，但您尚未安裝閘道，則可以下載並使用最新版的安裝程式。

<a name="install-gateway"></a>

## <a name="install-the-data-gateway"></a>安裝資料閘道

1.  [在本機電腦上下載並執行閘道安裝程式](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409)。

2. 檢閱並接受使用規定和隱私權聲明。

3. 指定您要用來安裝閘道的本機電腦路徑。

4. 在系統提示時，使用公司或學校的 Azure 帳戶而非 Microsoft 帳戶來登入。

   ![使用 Azure 公司或學校帳戶登入](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. 現在向[閘道雲端服務](#gateway-cloud-service)註冊您已安裝的閘道。 選擇**在這部電腦上註冊新的閘道**。

   閘道雲端服務會將資料來源認證和閘道詳細資料予以加密並儲存。 
   此服務也會在邏輯應用程式、內部部署資料閘道和內部部署資料來源之間路由傳送查詢和其結果。

6. 為閘道安裝提供名稱。 建立復原金鑰，然後確認復原金鑰。 

   > [!IMPORTANT] 
   > 修復金鑰必須至少包含 8 個字元。 請務必將金鑰儲存並保管在安全的地方。 當您想要移轉、還原或取代現有閘道時，也需要此金鑰。

   1. 若要變更閘道安裝所使用之閘道雲端服務和 Azure 服務匯流排的預設區域，請選擇 [變更區域]。

      ![變更區域](./media/logic-apps-gateway-install/change-region-gateway-install.png)

      預設區域是與 Azure AD 租用戶相關聯的區域。

   2. 在下一個窗格中，開啟**選取區域**以選擇不同的區域。

      ![選取其他區域](./media/logic-apps-gateway-install/select-region-gateway-install.png)

      例如，您可以選取和邏輯應用程式相同的區域，也可以選取最靠近內部部署資料來源的區域，以便減少延遲。 閘道資源和邏輯應用程式可位於不同位置。

      > [!IMPORTANT]
      > 安裝完成後就無法變更此區域。 此區域也會決定並限制您可以用來為閘道建立 Azure 資源的位置。 因此當您在 Azure 中建立閘道資源時，請確定資源位置符合您在閘道安裝期間所選取的區域。
      > 
      > 如果您之後想要為閘道使用不同區域，就必須設定新的閘道。

   3. 準備就緒時，請選擇 [完成]。

7. 現在，在 Azure 入口網站中遵循下列步驟，以便[為閘道建立 Azure 資源](../logic-apps/logic-apps-gateway-connection.md)。 

深入了解[資料閘道的運作方式](#gateway-cloud-service)。

## <a name="migrate-restore-or-take-over-an-existing-gateway"></a>移轉、還原或取代現有閘道

若要執行這些工作，您必須擁有安裝閘道時所指定的修復金鑰。

1. 在電腦的 [開始] 功能表中，選擇 [內部部署資料閘道]。

2. 安裝程式開啟後，登入先前用來安裝閘道的同一個公司或學校的 Azure 帳戶。

3. 選擇**移轉、還原或取代現有閘道**。

4. 提供您想要移轉、還原或取代之閘道的名稱和復原金鑰。

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Windows 服務帳戶

內部部署資料閘道會以 Windows 服務身分執行，並且設定成使用 `NT SERVICE\PBIEgwService` 作為 Windows 服務登入認證。 根據預設，閘道會擁有其本身安裝所在電腦的「以服務方式登入」權限。 若要在 Azure 入口網站中建立和維護閘道，Windows 服務帳戶必須至少具備**參與者**權限。 

> [!NOTE]
> Windows 服務帳戶與用來連線至內部部署資料來源的帳戶不同，也與用來登入雲端服務的公司或學校 Azure 帳戶不同。

<a name="restart-gateway"></a>

## <a name="restart-the-gateway"></a>重新啟動閘道

和其他任何 Windows 服務一樣，您可以透過多種方式來啟動和停止服務。 例如，您可以在閘道執行所在的電腦上以提高的權限開啟命令提示字元，並執行下列其中一個命令︰

* 若要停止服務，請執行此命令：
  
    `net stop PBIEgwService`

* 若要啟動服務，請執行此命令：
  
    `net start PBIEgwService`

## <a name="configure-a-firewall-or-proxy"></a>設定防火牆或 Proxy

閘道會對 [Azure 服務匯流排](https://azure.microsoft.com/services/service-bus/)建立輸出連線。 若要為閘道提供 Proxy 資訊，請參閱[設定 Proxy 設定](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/)。

若要檢查防火牆或 Proxy 是否會封鎖連線，請確認電腦確實可以連線到網際網路和 [Azure 服務匯流排](https://azure.microsoft.com/services/service-bus/)。 從 PowerShell 命令提示字元中，執行此命令︰

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> 此命令只會測試網路連線和對 Azure 服務匯流排的連線。 因此，這個命令和閘道或是閘道雲端服務 (會加密並儲存認證和閘道詳細資料) 沒有任何關係。 
>
> 此外，這個命令僅適用於 Windows Server 2012 R2 或更新版本，以及 Windows 8.1 或更新版本。 對於較舊的作業系統版本，您可以使用 Telnet 來測試連線能力。 深入了解 [Azure 服務匯流排和混合式解決方案](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)。

結果應該類似此範例：

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

如果 **TcpTestSucceeded** 未設為 **True**，防火牆可能會封鎖連線。 如果您想要更詳細的資訊，請將 **ComputerName** 和 **Port** 值取代為本主題的[設定連接埠](#configure-ports)之下所列的值。

防火牆也可能會封鎖 Azure 服務匯流排到 Azure 資料中心的連線。 若發生此情況，請核准 (解除封鎖) 您所在區域之資料中心的所有 IP 位址。 如需這些 IP 位址，您可以[在此取得 Azure IP 位址清單](https://www.microsoft.com/download/details.aspx?id=41653)。

## <a name="configure-ports"></a>設定連接埠

閘道會建立對 [Azure 服務匯流排](https://azure.microsoft.com/services/service-bus/)的輸出連線，並與下列輸出連接埠進行通訊︰TCP 443 (預設)、5671、5672 和 9350 到 9354。 閘道不需要輸入連接埠。 深入了解 [Azure 服務匯流排和混合式解決方案](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)。

| 網域名稱 | 輸出連接埠 | 描述 |
| --- | --- | --- |
| *.analysis.windows.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.servicebus.windows.net | 5671-5672 | 進階訊息佇列通訊協定 (AMQP) | 
| *.servicebus.windows.net | 443、9350-9354 | 透過 TCP 之服務匯流排轉送上的接聽程式 (需要 443 才能取得「存取控制」權杖) | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| login.microsoftonline.com | 443 | HTTPS | 
| *.msftncsi.com | 443 | 當 Power BI 服務無法連線至閘道時，用來測試網際網路連線能力。 | 

如果您必須核准 IP 位址而非網域，您可以下載並使用 [Microsoft Azure 資料中心 IP 範圍清單](https://www.microsoft.com/download/details.aspx?id=41653)。 在某些情況下，Azure 服務匯流排連線會使用 IP 位址而非完整網域名稱來建立。

<a name="gateway-cloud-service"></a>
## <a name="how-does-the-data-gateway-work"></a>資料閘道如何運作？

資料閘道可讓邏輯應用程式、閘道雲端服務和內部部署資料來源之間，快速且安全地進行通訊。 

![diagram-for-on-premises-data-gateway-flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

因此，當雲端使用者與連線到內部部署資料來源的項目互動時︰

1. 閘道雲端服務會建立查詢，以及資料來源的加密認證，並將查詢傳送至閘道的佇列處理。

2. 閘道雲端服務會分析該查詢，並將要求推送至 Azure 服務匯流排。

3. 內部部署資料閘道會輪詢 Azure 服務匯流排是否有待處理的要求。

4. 閘道收到查詢、解密認證，並使用這些認證連線至資料來源。

5. 閘道將查詢傳送至資料來源執行。

6. 結果會從資料來源傳送回閘道，然後再到閘道雲端服務。 閘道雲端服務接著就會使用結果。

<a name="faq"></a>
## <a name="frequently-asked-questions"></a>常見問題集

### <a name="general"></a>一般

**問**︰我在雲端中的資料來源 (例如 SQL Azure) 是否需要閘道？ <br/>
**答**：否。 閘道只連接至內部部署資料來源。

**問**︰閘道必須安裝在與資料來源相同的電腦上嗎？ <br/>
**答**：否。 閘道會使用所提供的連線資訊連線至資料來源。 在此請將閘道視為用戶端應用程式。 閘道只需要能夠連線到所提供的伺服器名稱。

<a name="why-azure-work-school-account"></a>

**問︰**︰為何必須使用公司或學校的 Azure 帳戶來登入？ <br/>
**答**︰安裝內部部署資料閘道時，您只能使用公司或學校的 Azure 帳戶。 登入帳戶會儲存在由 Azure Active Directory (Azure AD) 所管理的租用戶中。 Azure AD 帳戶的使用者主體名稱 (UPN) 通常會與電子郵件地址相符。

**問**︰我的認證儲存在哪裡？ <br/>
**答**：您針對資料來源輸入的認證會以加密方式儲存在閘道雲端服務中。 認證會在內部部署資料閘道進行解密。

**問**︰網路頻寬是否有任何需求？ <br/>
**答**︰我們的建議是，您的網路連線要有良好的輸送量。 每個環境都是不同的，而且所傳送的資料量會影響結果。 使用 ExpressRoute 可以協助您確保在內部部署與 Azure 資料中心之間有一定的輸送量等級。
您可以使用第三方工具 Azure 速度測試應用程式，協助您量測輸送量。

**問**︰從閘道向資料來源執行查詢時的延遲為何？ 最佳的架構為何？ <br/>
**答**：若要減少網路延遲，請盡可能靠近資料來源安裝閘道。 如果您可以在實際的資料來源上安裝閘道，此鄰近程度就能將所產生的延遲降到最低。 請一併考量資料中心。 例如，如果您的服務使用美國西部的資料中心，而您在 Azure VM 中裝載 SQL Server，Azure VM 也應該位於美國西部。 此鄰近程度能將延遲降到最低，並避免 Azure VM 的輸出費用。

**問**︰結果如何傳送回雲端？ <br/>
**答**︰結果會透過 Azure 服務匯流排傳送。

**問**︰是否有任何從雲端至閘道的輸入連線？ <br/>
**答**：否。 閘道使用連至 Azure 服務匯流排的輸出連接。

**問**︰如果我封鎖輸出連線會如何？ 我需要開啟什麼嗎？ <br/>
**答**：查看閘道使用的連接埠和主機。

**問**：實際的 Windows 服務名稱為何？<br/>
**答**：在「服務」中，閘道稱為 Power BI Enterprise Gateway Service。

**問**︰閘道 Windows 服務可以使用 Azure Active Directory 帳戶執行嗎？ <br/>
**答**：否。 Windows 服務必須要有有效的 Windows 帳戶。 根據預設，此服務會使用服務 SID，NT SERVICE\PBIEgwService 執行。

### <a name="high-availability-and-disaster-recovery"></a>高可用性和災害復原

**問**︰災害復原有哪些選項？ <br/>
**答**：您可以使用修復金鑰還原或移動閘道。 當您安裝閘道時，請指定修復金鑰。

**問**︰修復金鑰有什麼好處？ <br/>
**答**：修復金鑰可在災害發生後讓您有辦法移轉或復原閘道設定。

**問**︰有計劃針對閘道啟用高可用性案例嗎？ <br/>
**答**：這些案例正在規劃中，但是還沒有時間表。

## <a name="troubleshooting"></a>疑難排解

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**問**︰如何查看有哪些查詢正要傳送至內部部署資料來源？ <br/>
**答**：您可以啟用查詢追蹤，其中包含要傳送的查詢。 完成疑難排解時，請務必將查詢追蹤變更回原始值。 讓查詢追蹤保持開啟會產生較大的記錄。

您也可以查看資料來源具備的工具是否有追蹤查詢。 例如，您可以使用 SQL Server 和 Analysis Services 的擴充事件或 SQL Profiler。

**問**︰閘道記錄在哪裡？ <br/>
**答**：請參閱本主題稍後的「工具」。

### <a name="update-to-the-latest-version"></a>更新為最新版本

閘道版本過期時，可能會出現很多問題。 良好的一般作法是確實使用最新版本。 如果有一個月以上未更新閘道，您可以考慮安裝最新版的閘道，並看看是否能重現問題。

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>錯誤︰無法將使用者新增到群組。 (-2147463168 PBIEgwService Performance Log Users)

如果您嘗試在不支援的網域控制站上安裝閘道，可能會收到這個錯誤。 請確定您是在非網域控制站的電腦上部署閘道。

## <a name="tools"></a>工具

### <a name="collect-logs-from-the-gateway-configurer"></a>從閘道設定程式收集記錄

您可以收集閘道的數個記錄檔。 一律先從記錄檔開始！

#### <a name="installer-logs"></a>安裝程式記錄檔

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>組態記錄檔

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>企業閘道服務記錄檔

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>事件記錄檔

您可以在 [應用程式及服務記錄] 底下找到資料管理閘道和 PowerBIGateway 記錄。

### <a name="fiddler-trace"></a>Fiddler 追蹤

[Fiddler](http://www.telerik.com/fiddler) 是一個 Telerik 公司開發，用來監視 HTTP 流量的免費工具。 您可以從用戶端電腦使用 Power BI 服務來查看此流量。 此服務可能會顯示錯誤和其他相關資訊。

## <a name="next-steps"></a>後續步驟
    
* [從邏輯應用程式連線至內部部署資料](../logic-apps/logic-apps-gateway-connection.md)
* [企業整合功能](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [適用於 Azure Logic Apps 的連接器](../connectors/apis-list.md)
