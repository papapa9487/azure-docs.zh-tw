---
title: "內部部署資料閘道 | Microsoft Docs"
description: "如果 Azure 中的 Analysis Services 伺服器會連接到內部部署資料來源，則需要一個內部部署閘道。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: cd596155-b608-4a34-935e-e45c95d884a9
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/21/2017
ms.author: owend
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 514b5404e8cbfa0baa657eb41736e20cad502638
ms.contentlocale: zh-tw
ms.lasthandoff: 08/25/2017

---
# <a name="connecting-to-on-premises-data-sources-with-azure-on-premises-data-gateway"></a>透過 Azure 內部部署資料閘道連線至內部部署資料來源
內部部署資料閘道的角色如同橋接器，在內部部署資料來源和雲端中的 Azure Analysis Services 伺服器之間提供安全的資料傳輸。 除了搭配相同區域中的多部 Azure Analysis Services 伺服器運作，最新版的閘道也可以搭配 Azure Logic Apps、Power BI、Power Apps 和 Microsoft Flow運作。 您可以讓相同區域中的多項服務與單一閘道建立關聯。 

 Azure Analysis Services 需要相同區域中的閘道資源。 例如，如果您在美國東部 2 區域中有 Azure Analysis Services 伺服器，您就需要美國東部 2 區域中的閘道資源。 美國東部 2 中的多部伺服器可以使用相同的閘道。

第一次設定閘道的程序有四部分：

- **下載並執行安裝程式** - 這個步驟會在您組織中的電腦上安裝閘道服務。

- **註冊您的閘道** - 在此步驟中，您會為您的閘道指定名稱和復原金鑰，然後選取區域，並且向閘道雲端服務註冊您的閘道。

- **在 Azure 中建立閘道資源** - 在此步驟中，您會在您的 Azure 訂用帳戶中建立閘道資源。

- **將您的伺服器連線到閘道資源** - 您的訂用帳戶中一旦有閘道資源，您就可以開始將您的伺服器連線到它。

為您的訂用帳戶設定閘道資源後，您就可以將多部伺服器和其他服務連線到它。 如果您在不同的區域中有伺服器或其他服務，您只需要安裝不同的閘道，並建立其他閘道資源。

若要立即開始，請參閱[安裝及設定內部部署資料閘道](analysis-services-gateway-install.md)。

## <a name="how-it-works"> </a>運作方式
您在組織的電腦上安裝的閘道會以 Windows 服務 (**內部部署資料閘道**) 的形式執行。 此本機服務已透過 Azure 服務匯流排向閘道雲端服務註冊。 您接著可為您的 Azure 訂用帳戶建立閘道資源 (閘道雲端服務)。 Azure Analysis Services 伺服器會接著連線至閘道資源。 當您伺服器上的模型需要連線到內部部署資料來源進行查詢或處理時，查詢和資料流程會周遊閘道資源、Azure 服務匯流排、本機內部部署資料閘道服務以及您的資料來源。 

![運作方式](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

查詢和資料流程：

1. 雲端服務使用內部部署資料來源的加密認證建立查詢。 查詢接著傳送至佇列供閘道處理。
2. 閘道雲端服務會分析該查詢，並將要求推送至 [Azure 服務匯流排](https://azure.microsoft.com/documentation/services/service-bus/)。
3. 內部部署資料閘道會輪詢 Azure 服務匯流排是否有待處理的要求。
4. 閘道收到查詢、解密認證，並使用這些認證連接至資料來源。
5. 閘道將查詢傳送至資料來源執行。
6. 結果會從資料來源傳送回閘道，然後再到雲端服務和您的伺服器。

## <a name="windows-service-account"> </a>Windows 服務帳戶
內部部署資料閘道設定為使用 *NT SERVICE\PBIEgwService* 做為 Windows 服務的登入認證。 根據預設，它具有「以服務方式登入」的權限 (在您安裝閘道所在機器的環境中)。 此認證不同於連接到內部部署資料來源使用的帳戶或您的 Azure 帳戶。  

如果您因為身分驗證面臨與 Proxy 服務器相關的問題，您可能需要將 Windows 服務帳戶變更為網域使用者或受管理的服務帳戶。

## <a name="ports"></a>連接埠
閘道會建立 Azure 服務匯流排的輸出連接。 閘道會與下列輸出連接埠進行通訊︰TCP 443 (預設)、5671、5672、9350 到 9354。  閘道不需要輸入連接埠。

建議您在防火牆中將您的資料區域 IP 位址列入白名單。 您可以下載 [Microsoft Azure Datacenter IP 清單](https://www.microsoft.com/download/details.aspx?id=41653)。 此清單每週更新。

> [!NOTE]
> Azure Datacenter IP 清單中列出的 IP 位址採用 CIDR 標記法。 例如，10.0.0.0/24 並非 10.0.0.0 到 10.0.0.24。 深入了解 [CIDR 標記法](http://whatismyipaddress.com/cidr)。
>
>

以下是閘道使用的完整網域名稱。

| 網域名稱 | 輸出連接埠 | 說明 |
| --- | --- | --- |
| *.powerbi.com |80 |用於下載安裝程式的 HTTP。 |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |進階訊息佇列通訊協定 (AMQP) |
| *.servicebus.windows.net |443、9350-9354 |透過 TCP 之服務匯流排轉送上的接聽程式 (需要 443 才能取得「存取控制」權杖) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |如果 Power BI 服務無法連接至閘道，則用來測試網際網路連線能力。 |
| *.microsoftonline-p.com |443 |用於驗證 (視設定而定)。 |

### <a name="force-https"></a>強制使用 Azure 服務匯流排進行 HTTPS 通訊
您可以強制閘道使用 HTTPS 取代直接 TCP 來與 Azure 服務匯流排通訊；但這樣會大幅降低效能。 您可以修改 *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* 檔案，方法是將值從 `AutoDetect` 變更為 `Https`。 這個檔案通常位於 *C:\Program Files\On-premises data gateway*。

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="faq"></a>常見問題集

### <a name="general"></a>一般

**問**︰我在雲端中的資料來源 (例如 Azure SQL Database) 是否需要閘道？ <br/>
**答**：否。 閘道只連接至內部部署資料來源。

**問**︰閘道必須安裝在與資料來源相同的電腦上嗎？ <br/>
**答**：否。 閘道會使用所提供的連線資訊連線至資料來源。 在此請將閘道視為用戶端應用程式。 閘道只需要能夠連線到所提供的伺服器名稱，通常是在相同網路上。

<a name="why-azure-work-school-account"></a>

**問︰**︰為何必須使用公司或學校帳戶進行登入？ <br/>
**答**︰安裝內部部署資料閘道時，您只能使用公司或學校的 Azure 帳戶。 登入帳戶會儲存在由 Azure Active Directory (Azure AD) 所管理的租用戶中。 Azure AD 帳戶的使用者主體名稱 (UPN) 通常會與電子郵件地址相符。

**問**︰我的認證儲存在哪裡？ <br/>
**答**：您針對資料來源輸入的認證會以加密方式儲存在閘道雲端服務中。 認證會在內部部署資料閘道進行解密。

**問**︰網路頻寬是否有任何需求？ <br/>
**答**︰建議您的網路連線要有良好的輸送量。 每個環境都是不同的，而且所傳送的資料量會影響結果。 使用 ExpressRoute 可以協助您確保在內部部署與 Azure 資料中心之間有一定的輸送量等級。
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
**答**：在「服務」中，閘道稱為「內部部署資料閘道服務」。

**問**︰閘道 Windows 服務可以使用 Azure Active Directory 帳戶執行嗎？ <br/>
**答**：否。 Windows 服務必須要有有效的 Windows 帳戶。 根據預設，此服務會使用服務 SID，NT SERVICE\PBIEgwService 執行。

### <a name="high-availability"></a>高可用性和災害復原

**問**︰災害復原有哪些選項？ <br/>
**答**：您可以使用修復金鑰還原或移動閘道。 當您安裝閘道時，請指定修復金鑰。

**問**︰修復金鑰有什麼好處？ <br/>
**答**：修復金鑰可在災害發生後讓您有辦法移轉或復原閘道設定。

## <a name="troubleshooting"></a>疑難排解

**問**︰如何查看有哪些查詢正要傳送至內部部署資料來源？ <br/>
**答**：您可以啟用查詢追蹤，其中包含要傳送的查詢。 完成疑難排解時，請務必將查詢追蹤變更回原始值。 讓查詢追蹤保持開啟會產生較大的記錄。

您也可以查看資料來源具備的工具是否有追蹤查詢。 例如，您可以使用 SQL Server 和 Analysis Services 的擴充事件或 SQL Profiler。

**問**︰閘道記錄在哪裡？ <br/>
**答**：請參閱本主題稍後的「記錄」。

### <a name="update"></a>更新為最新版本

閘道版本過期時，可能會出現很多問題。 良好的一般作法是確實使用最新版本。 如果有一個月以上未更新閘道，您可以考慮安裝最新版的閘道，並看看是否能重現問題。

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>錯誤︰無法將使用者新增到群組。 (-2147463168 PBIEgwService Performance Log Users)

如果您嘗試在不支援的網域控制站上安裝閘道，可能會收到這個錯誤。 請確定您是在非網域控制站的電腦上部署閘道。

## <a name="logs"></a>記錄

進行疑難排解時，記錄檔是重要的資源。

#### <a name="enterprise-gateway-service-logs"></a>企業閘道服務記錄檔

`C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\<yyyyymmdd>.<Number>.log`

#### <a name="configuration-logs"></a>組態記錄檔

`C:\Users\<username>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator.log`




#### <a name="event-logs"></a>事件記錄檔

您可以在 [應用程式及服務記錄] 底下找到資料管理閘道和 PowerBIGateway 記錄。


## <a name="telemetry"></a>遙測
遙測可應用在監視和疑難排解上。 依照預設

**開啟遙測**

1.  檢查電腦上的內部部署資料閘道用戶端目錄。 此目錄通常是 **%systemdrive%\Program Files\內部部署資料閘道**。 或者，您可以開啟 [服務] 主控台並查看可執行檔的路徑：內部部署資料閘道服務的一個屬性。
2.  在來自用戶端目錄的 Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config 檔案中， 將 SendTelemetry 設定變更為 true。
        
    ```
        <setting name="SendTelemetry" serializeAs="String">
                    <value>true</value>
        </setting>
    ```

3.  儲存您的變更，然後重新啟動 Windows 服務：內部部署資料閘道服務。




## <a name="next-steps"></a>後續步驟
* [ Analysis Services](analysis-services-manage.md)
* [從 Azure Analysis Services 取得資料](analysis-services-connect.md)

