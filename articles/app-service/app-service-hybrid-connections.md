---
title: "Azure App Service 混合式連線 | Microsoft Docs"
description: "如何建立和使用混合式連線以存取不同網路的資源"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2017
ms.author: ccompy
ms.openlocfilehash: fef9e7b280387934cb093f51b4c8aa134a3b87e7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Azure App Service 混合式連線 #

## <a name="overview"></a>概觀 ##

混合式連線既是 Azure 服務，也是 Azure App Service 功能。  作為服務時，它具有 Azure App Service 所利用之用途和功能以外的用途和功能。  若要深入了解混合式連線以及其在 Azure App Service 之外的使用方式，您可以從 [Azure 轉送混合式連線][HCService]來開始

在 Azure App Service 中，混合式連線可用來存取其他網路的應用程式資源。 它可讓您從應用程式存取應用程式端點。  它無法讓您以其他功能來存取應用程式。  和在 App Service 中使用時相同，每個混合式連線都會與單一 TCP 主機和連接埠的組合相互關聯。  這表示混合式連線端點可以位於任何作業系統和任何應用程式上，只要您有叫用 TCP 接聽連接埠就行。 混合式連線不知道 (或不在意) 應用程式通訊協定為何或您要存取什麼資源。  它只負責提供網路存取。  


## <a name="how-it-works"></a>運作方式 ##
混合式連線功能是由服務匯流排轉送的兩個輸出呼叫所組成。  系統會從應用程式在應用程式服務中執行所在之主機上的程式庫進行連線，然後再從混合式連線管理員 (HCM) 連線到服務匯流排轉送。  HCM 是轉送服務，您會將它部署在網路裝載內 

透過兩個聯結的連線，應用程式會對 HCM 另一端的固定「主機:連接埠」組合建立 TCP 通道。  連線會使用 TLS 1.2 來獲得安全性，並使用 SAS 金鑰來進行驗證/授權。    

![][1]

當應用程式所提出的 DNS 要求符合所設定的混合式連線端點時，系統就會將輸出 TCP 流量往下朝混合式連線進行重新導向。  

> [!NOTE]
> 這表示您應該嘗試一律對混合式連線使用 DNS 名稱。  如果端點改為使用 IP 位址，某些用戶端軟體不會執行 DNS 查閱。
>
>

混合式連線有兩種，分別是在 Azure 轉送底下以服務形式來提供的新式混合式連線，以及舊式的 BizTalk 混合式連線。  在入口網站中，舊式的 BizTalk 混合式連線稱為傳統混合式連線。  本文稍後會提供這種連線的詳細資訊。

### <a name="app-service-hybrid-connection-benefits"></a>App Service 混合式連線的好處 ###

混合式連線功能的諸多好處如下

- 應用程式可以安全地存取內部部署系統和服務
- 此功能不需要可從網際網路存取的端點
- 安裝速度快且過程簡單  
- 每個混合式連線都會與單一的「主機:連接埠」組合匹配，因此具有良好的安全性
- 它通常不需要防火牆漏洞，因為所有連線都是透過標準的 Web 連接埠來輸出
- 因為這是網路層級的功能，因此也表示此功能不會因為應用程式所使用的語言以及端點所使用的技術而受到影響
- 它可用來讓您從單一應用程式存取多個網路 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>混合式連線無法執行的作業 ###

您無法透過混合式連線來執行的一些作業如下：

- 掛接磁碟機
- 使用 UDP
- 存取使用動態連接埠的 TCP 型服務 (例如，FTP 被動模式或延伸被動模式)
- LDAP 支援，因為它有時需要 UDP
- Active Directory 支援

## <a name="adding-and-creating-a-hybrid-connection-in-your-app"></a>在應用程式中新增和建立混合式連線 ##

您可以透過應用程式入口網站或從混合式連線服務入口網站，來建立混合式連線。  強烈建議您使用應用程式入口網站來建立要用於應用程式的混合式連線。  若要建立混合式連線，請移至 [Azure 入口網站][portal]，然後進到應用程式的 UI。  選取 [網路] > [設定混合式連線端點]。  您可以在這裡看到應用程式所設定的混合式連線  

![][2]

若要新增混合式連線，請按一下 [新增混合式連線]。  所開啟的 UI 會列出您已建立的混合式連線。  若要在應用程式中新增其中的一或多個混合式連線，請按一下您想要的混合式連線，並按下 [新增選取的混合式連線]。  

![][3]

如果您想要建立新的混合式連線，請按一下 [建立新的混合式連線]。  您可以在這裡指定： 

- 端點名稱
- 端點主機名稱
- 端點連接埠
- 您想要使用的服務匯流排命名空間

![][4]

每個混合式連線都會繫結至一個服務匯流排命名空間，而且每個服務匯流排命名空間都位於 Azure 區域中。  請務必嘗試並使用和應用程式位於相同區域的服務匯流排命名空間，以避免網路所引發的延遲。

如果您想要移除應用程式中的混合式連線，請對它按一下滑鼠右鍵，然後選取 [中斷連線]。  

在將混合式連線新增至 Web 應用程式後，您只要對它按一下就可以查看其詳細資料。  

![][5]

## <a name="hybrid-connections-and-app-service-plans"></a>混合式連線和 App Service 方案 ##

您現在只能建立新的混合式連線。  基本、標準、進階和隔離的定價 SKU 才能使用混合式連線。  定價方案會有相關聯的限制。  

| 定價方案 | 方案中可用的混合式連線數目 |
|----|----|
| 基本 | 5 |
| 標準 | 25 |
| 進階 | 200 |
| 隔離 | 200 |

因為 App Service 方案有相關限制，因此 App Service 方案會提供 UI，來顯示應用程式所使用的混合式連線數目，以及是哪些應用程式在使用。  

![][6]

和應用程式檢視一樣，您也可以按一下混合式連線來查看其詳細資料。  在這裡所顯示的屬性中，您可以查看您在應用程式檢視中擁有的所有資訊，但也可以查看相同的 App Service 方案內有多少其他應用程式正在使用該混合式連線。

![][7]

雖然 App Service 方案中可以使用的混合式連線端點數目有所限制，但所使用的每個混合式連線都可以用於該 App Service 方案中任意數目的應用程式中。  也就是說，如果我將 1 個混合式連線用在 App Service 方案的 5 個不同應用程式中，仍只算 1 個混合式連線。

除了只能用於基本、標準、進階或隔離的 SKU 外，混合式連線還有額外成本。  如需混合式連線定價的詳細資訊，請前往這裡︰[服務匯流排定價][sbpricing]。

## <a name="hybrid-connection-manager"></a>混合式連線管理員 ##

為了讓混合式連線運作，裝載混合式連線端點的網路中必須有轉送代理程式。  該轉送代理程式稱為混合式連線管理員 (HCM)。  您可以在 [Azure 入口網站][portal]中，從應用程式所提供的 UI ([網路] > [設定混合式連線端點]) 下載這項工具。  

此工具會在 Windows Server 2008 R2 和更新版本的 Windows 上執行。  安裝完成後，HCM 會以服務形式來執行。  此服務會根據所設定的端點來連線到 Azure 服務匯流排轉送。  來自 HCM 的連線會輸出到連接埠 80 和 443。    

HCM 有提供 UI 來供您進行設定。  HCM 安裝完成後，您可以執行混合式連線管理員安裝目錄中的 HybridConnectionManagerUi.exe 來顯示 UI。  在 Windows 10 上，您也可以在搜尋方塊中輸入「混合式連線管理員 UI」來輕鬆取得該 UI。  

當 HCM UI 啟動時，您會先看到一個資料表，裡面會列出這個 HCM 執行個體所設定的所有混合式連線。  如果您想要進行變更，您必須對 Azure 進行驗證。 

若要在 HCM 中新增一或多個混合式連線︰

1. 啟動 HCM UI
1. 按一下 [設定另一個混合式連線] ![][8]

1. 使用 Azure 帳戶進行登入
1. 選擇訂用帳戶
1. 按一下您要讓這個 HCM 轉送的混合式連線 ![][9]

1. 按一下 [Save] \(儲存)。

此時，您會看到您新增的混合式連線。  您也可以按一下所設定的混合式連線，然後查看該連線的詳細資訊。

![][10]

若要讓 HCM 能夠支援其所設定要使用的混合式連線，它需要︰

- 對 Azure 的 TCP 存取權 (透過連接埠 80 和 443)
- 對混合式連線端點的 TCP 存取權
- 可以對端點主機和 Azure 服務匯流排命名空間執行 DNS 查閱

HCM 同時支援新式混合式連線以及舊式的 BizTalk 混合式連線。

### <a name="redundancy"></a>備援性 ###

每個 HCM 都可以支援多個混合式連線。  此外，任何給定的混合式連線也可由多個 HCM 來支援。  預設行為是在給定端點所設定的 HCM 之間循環配置流量。  如果您想讓網路的混合式連線具有高可用性，只需在不同機器上具現化多個 HCM。 

### <a name="manually-adding-a-hybrid-connection"></a>手動新增混合式連線 ###

如果您想讓訂用帳戶以外的人員裝載給定混合式連線的 HCM 執行個體，您可以與他們共用混合式連線的閘道連接字串。  在 [Azure 入口網站][portal]中，您可以在混合式連線的屬性中看到此字串。 若要使用該字串，請在 HCM 中按一下 [手動設定] 按鈕，然後貼入閘道連接字串。


## <a name="troubleshooting"></a>疑難排解 ##

當您的混合式連線已設定用於執行中的應用程式，且至少有一個 HCM 已設定混合式連線時，入口網站中的狀態會顯示為**已連線**。  如果狀態未顯示為**已連線**，則表示您的應用程式已關閉，或是您的 HCM 無法在連接埠 80 或 443 上向外連線到 Azure。  

用戶端無法連線至其端點的主要原因是，端點在指定時所使用的是 IP 位址而非 DNS 名稱。  如果您的應用程式無法連線到所要的端點，且您使用的是 IP 位址，請改為使用對 HCM 執行所在之主機有效的 DNS 名稱。  另外則要檢查 HCM 執行所在的主機上是否已正確解析 DNS 名稱，以及 HCM 執行所在的主機是否可以連線到混合式連線端點。  

App Service 中有一個稱為 tcpping 的工具，您可以從主控台叫用這個工具。  這個工具可以指出您是否有 TCP 端點的存取權，但不會指出您是否有混合式連線端點的存取權。  當您在主控台中對混合式連線端點使用此工具時，成功的 ping 只表示您已為應用程式設定了混合式連線，且此連線使用「主機:連接埠」組合。  

## <a name="biztalk-hybrid-connections"></a>BizTalk 混合式連線 ##

舊式的 BizTalk 混合式連線功能已關閉，以促進 BizTalk 混合式連線的建立。  您可以繼續對應用程式使用既有的 BizTalk 混合式連線，但請將其移轉至新服務。  相較於 BizTalk 版本，新服務的好處包括︰

- 不需要其他 BizTalk 帳戶
- TLS 是 1.2，而非 BizTalk 混合式連線所使用的 1.0
- 通訊是使用 DNS 名稱透過連接埠 80 和 443 來連線到 Azure，而非使用 IP 位址和其他連接埠範圍。  

若要在您的應用程式中新增 BizTalk 混合式連線，請在 [Azure 入口網站][portal] 中移至您的應用程式，然後按一下 [網路] > [設定混合式連線端點]。  在傳統的混合式連線資料表中，按一下 [新增傳統混合式連線]。  您可以在這裡看到 BizTalk 混合式連線的清單。  


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png

<!--Links-->
[HCService]: http://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: http://portal.azure.com/
[oldhc]: http://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: http://azure.microsoft.com/pricing/details/service-bus/

