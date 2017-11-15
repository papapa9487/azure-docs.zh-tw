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
ms.date: 10/20/2017
ms.author: ccompy
ms.openlocfilehash: f0b148cb9c304c54b47be9601740e7634462d59b
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Azure App Service 混合式連線 #

## <a name="overview"></a>概觀 ##

混合式連線既是 Azure 服務，也是 Azure App Service 功能。  作為服務時，它具有 Azure App Service 所利用之用途和功能以外的用途和功能。  若要深入了解混合式連線以及其在 Azure App Service 之外的使用方式，您可以從 [Azure 轉送混合式連線][HCService]來開始

在 Azure App Service 中，「混合式連線」可用來存取其他網路中的應用程式資源。 它可讓您從應用程式存取應用程式端點。  它不會啟用替代功能來存取應用程式。  和在 App Service 中使用時相同，每個「混合式連線」都會與單一 TCP 主機和連接埠的組合相互關聯。  這意謂著「混合式連線」端點可以位於任何作業系統和任何應用程式上，只要您有命中 TCP 接聽連接埠即可。 「混合式連線」不知道 (或不在意) 應用程式通訊協定是哪一種，或您要存取什麼資源。  它們只負責提供網路存取。  


## <a name="how-it-works"></a>運作方式 ##
「混合式連線」功能是由對「服務匯流排轉送」發出的兩個輸出呼叫所組成。  有一個連線是來自應用程式在 App Service 中執行所在主機上的程式庫，還有一個連線是從「混合式連線管理員」(HCM) 到「服務匯流排轉送」。  HCM 是您在網路內部署的轉送服務，該網路裝載您要嘗試存取的資源。 

透過這個兩個聯結的連線，應用程式便會有一個可通往 HCM 另一端固定之「主機:連接埠」組合的 TCP 通道。  連線會使用 TLS 1.2 來獲得安全性，並使用 SAS 金鑰來進行驗證/授權。    

![混合式連線概要流程][1]

當應用程式提出的 DNS 要求與所設定的「混合式連線」端點相符時，系統就會透過「混合式連線」將輸出 TCP 流量重新導向。  

> [!NOTE]
> 這表示您應該嘗試一律對混合式連線使用 DNS 名稱。  如果端點改為使用 IP 位址，某些用戶端軟體不會執行 DNS 查閱。
>
>

「混合式連線」有兩種：分別是在「Azure 轉送」底下以服務形式提供的新式「混合式連線」，以及舊式的「BizTalk 混合式連線」。  在入口網站中，舊式的 BizTalk 混合式連線稱為傳統混合式連線。  本文稍後會提供這種連線的詳細資訊。

### <a name="app-service-hybrid-connection-benefits"></a>App Service 混合式連線優點 ###

「混合式連線」功能有一些優點，包括：

- 應用程式可以安全地存取內部部署系統和服務。
- 此功能不需要可從網際網路存取的端點。
- 安裝速度快且過程簡單。 
- 每個「混合式連線」都會與單一的「主機:連接埠」組合對應，因此具有良好的安全性。
- 它通常不需要防火牆洞，因為所有連線都是透過標準 Web 連接埠來輸出。
- 由於這是網路層級的功能，因此不會因為應用程式所使用的語言及端點所使用的技術而受到影響。
- 它可用來讓您從單一應用程式存取多個網路。 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>混合式連線無法執行的作業 ###

有一些作業您無法透過「混合式連線」來執行，包括：

- 掛接磁碟機
- 使用 UDP
- 存取使用動態連接埠的 TCP 型服務 (例如「FTP 被動模式」或「延伸被動模式」)
- LDAP 支援，因為它有時需要 UDP
- Active Directory 支援

## <a name="adding-and-creating-a-hybrid-connection-in-your-app"></a>在應用程式中新增和建立混合式連線 ##

您可以透過 Azure 入口網站中的 App Service 應用程式，或從 Azure 入口網站中的「Azure 轉送」，來建立「混合式連線」。  強烈建議您透過要與「混合式連線」搭配使用的 App Service 應用程式來建立「混合式連線」。  若要建立「混合式連線」，請移至 [Azure 入口網站][portal]，然後選取您的應用程式。  選取 [網路] > [設定您的混合式連接端點]。  您可以在這裡看到為您應用程式設定的「混合式連線」。  

![混合式連線清單][2]

若要新增混合式連線，請按一下 [新增混合式連線]。  所開啟的 UI 會列出您已建立的混合式連線。  若要在應用程式中新增其中的一或多個混合式連線，請按一下您想要的混合式連線，然後按下 [新增選取的混合式連線]。  

![混合式連線入口網站][3]

如果您想要建立新的「混合式連線」，請按一下 [建立新的混合式連線]。  您可以在這裡指定： 

- 端點名稱
- 端點主機名稱
- 端點連接埠
- 您想要使用的「服務匯流排」命名空間

![Create a hybrid connection][4]

每個「混合式連線」都會繫結至一個「服務匯流排」命名空間，而每個「服務匯流排」命名空間都位於 Azure 區域中。  請務必嘗試並使用與應用程式位於相同區域的「服務匯流排」命名空間，以避免網路所引發的延遲。

如果您想要移除應用程式中的「混合式連線」，請對它按一下滑鼠右鍵，然後選取 [中斷連線]。  

將「混合式連線」新增至應用程式之後，您只要對它按一下就可以查看其詳細資料。 

![混合式連線詳細資料][5]

### <a name="creating-a-hybrid-connection-in-the-azure-relay-portal"></a>在 Azure 轉送入口網站中建立混合式連線 ###

除了透過入口網站從您應用程式內進行的方式之外，您也可以從「Azure 轉送」入口網站內建立「混合式連線」。 為了讓「混合式連線」能夠供 Azure App Service 使用，它必須滿足兩個準則。 它必須：

* 要求用戶端授權
* 擁有一個以「主機:連接埠」組合作為值的中繼資料項目具名端點

## <a name="hybrid-connections-and-app-service-plans"></a>混合式連線和 App Service 方案 ##

只有在「基本」、「標準」、「進階」及「隔離」定價 SKU 中，才有提供「混合式連線」。  定價方案會有相關聯的限制。  

> [!NOTE] 
> 您只能根據「Azure 轉送」來建立新的「混合式連線」。 您無法建立新的「BizTalk 混合式連線」。
>

| 定價方案 | 方案中可用的混合式連線數目 |
|----|----|
| 基本 | 5 |
| 標準 | 25 |
| 進階 | 200 |
| 隔離 | 200 |

由於「App Service 方案」有相關限制，因此「App Service 方案」中也會提供 UI 來顯示有多少使用中的「混合式連線」，以及是哪些應用程式在使用這些連線。  

![App Servie 方案層級屬性][6]

您可以按一下「混合式連線」來查看其詳細資料。  在這裡所顯示的屬性中，您可以查看您在應用程式檢視中所看到的所有資訊，而且也可以查看相同「App Service 方案」中有多少其他應用程式正在使用該「混合式連線」。

雖然「App Service 方案」中可以使用的「混合式連線」端點數目有所限制，但所使用的每個「混合式連線」都可以用於該「App Service 方案」中任意數目的應用程式。  換句話說，一個在「App Service 方案」之 5 個個別應用程式中使用的「混合式連線」只算 1 個「混合式連線」。

使用「混合式連線」需額外付費。  如需有關「混合式連線」定價的詳細資料，請前往這裡︰[服務匯流排定價][sbpricing]。

## <a name="hybrid-connection-manager"></a>混合式連線管理員 ##

為了讓「混合式連線」運作，在裝載「混合式連線」端點的網路中必須有轉送代理程式。  該轉送代理程式稱為混合式連線管理員 (HCM)。  您可以在 [Azure 入口網站][portal]中，從應用程式所提供的 UI ([網路] > [設定您的混合式連接端點]) 下載此工具。  

此工具可在 Windows Server 2012 和更新版的 Windows 上執行。  安裝完成後，HCM 會以服務形式來執行。  此服務會根據所設定的端點來連線到「Azure 服務匯流排轉送」。  來自 HCM 的連線會透過連接埠 443 輸出到 Azure。    

HCM 有提供 UI 來供您進行設定。  HCM 安裝完成後，您可以執行「混合式連線管理員」安裝目錄中的 HybridConnectionManagerUi.exe 來顯示 UI。  在 Windows 10 上，您也可以在搜尋方塊中輸入「混合式連線管理員 UI」來輕鬆取得該 UI。  

![混合式連線入口網站][7]

當 HCM UI 啟動時，您會先看到一個表格，當中會列出與這個 HCM 執行個體搭配設定的所有「混合式連線」。  如果您想要進行任何變更，就必須向 Azure 進行驗證。 

若要在 HCM 中新增一或多個混合式連線︰

1. 啟動 HCM UI
1. 按一下 [設定另一個混合式連線] ![在 HCM 中新增 HC][8]

1. 使用 Azure 帳戶進行登入
1. 選擇訂用帳戶
1. 按一下您要讓 HCM 轉送的「混合式連線」 ![選取一個 HC][9]

1. 按一下 [Save] \(儲存)。

此時，您會看到您新增的「混合式連線」。  您也可以按一下所設定的「混合式連線」，然後查看其相關詳細資料。

![HC 詳細資料][10]

若要讓 HCM 能夠支援所設定與其搭配的「混合式連線」，它需要︰

- 能夠透過連接 80 和 443 對 Azure 進行 TCP 存取
- 能夠對「混合式連線」端點進行 TCP 存取
- 能夠對端點主機和「Azure 服務匯流排」命名空間執行 DNS 查閱

HCM 同時支援新式「混合式連線」和舊式「BizTalk 混合式連線」。

> [!NOTE]
> 「Azure 轉送」需倚賴「Web 通訊端」來取得連線能力。 此功能只有在 Windows Server 2012 或更新版本上才有提供。 因此，Windows Server 2012 以前的所有版本皆不支援「混合式連線管理員」。
>

### <a name="redundancy"></a>備援性 ###

每個 HCM 都可以支援多個「混合式連線」。  此外，任何指定的「混合式連線」也都可以受到多個 HCM 支援。  預設行為是在給定端點所設定的 HCM 之間循環配置流量。  如果您想要讓來自您網路的「混合式連線」具有高可用性，只要在個別電腦上將多個 HCM 具現化即可。 

### <a name="manually-adding-a-hybrid-connection"></a>手動新增混合式連線 ###

如果您想讓訂用帳戶外的人員裝載指定「混合式連線」的 HCM 執行個體，您可以與他們共用「混合式連線」的閘道連接字串。  您可以在 [Azure 入口網站][portal]中，於「混合式連線」的屬性中看到此字串。 若要使用該字串，請在 HCM 中按一下 [手動輸入] 按鈕，然後貼上閘道連接字串。


## <a name="troubleshooting"></a>疑難排解 ##

「混合式連線」的連線狀態意謂著該「混合式連線」至少有一個已設定的 HCM，並且能夠連線至 Azure。  如果「混合式連線」的狀態並未顯示 [已連線]，則表示在任何可存取 Azure 的 HCM 上皆未設定該「混合式連線」。

用戶端無法連線至其端點的主要原因是，端點在指定時所使用的是 IP 位址而非 DNS 名稱。  如果您的應用程式無法連線到所要的端點，且您使用的是 IP 位址，請改為使用對 HCM 執行所在之主機有效的 DNS 名稱。  另外還需要檢查 DNS 名稱在 HCM 執行所在的主機上是否解析正確，以及 HCM 執行所在的主機是否可以連線到「混合式連線」端點。  

App Service 中有一個可從 [進階工具 (Kudu)] 主控台中叫用的工具，稱為 tcpping。  這個工具可以指出您是否能夠存取 TCP 端點，但不會指出您是否能夠存取「混合式連線」端點。  在主控台中針對「混合式連線」端點使用此工具時，成功的 ping 只表示您已為應用程式設定一個使用該「主機:連接埠」組合的「混合式連線」。  

## <a name="biztalk-hybrid-connections"></a>BizTalk 混合式連線 ##

舊式「BizTalk 混合式連線」功能已關閉，以利發展新式「BizTalk 混合式連線」。  您可以繼續搭配應用程式使用現有的「BizTalk 混合式連線」，但應該將其移轉成使用「Azure 轉送」的新式「混合式連線」。  相較於 BizTalk 版本，新服務的好處包括︰

- 不需要額外的 BizTalk 帳戶。
- TLS 是 1.2，而非「BizTalk 混合式連線」所使用的 1.0。
- 通訊是使用 DNS 名稱透過連接埠 80 和 443 來連線到 Azure，而非使用 IP 位址和其他連接埠範圍。  

若要在您的應用程式中新增現有的「BizTalk 混合式連線」，請在 [Azure 入口網站][portal] 中移至您的應用程式，然後按一下 [網路] > [設定您的混合式連接端點]。  在 [傳統混合式連線] 表格中，按一下 [新增傳統混合式連線]。  您可以在這裡看到「BizTalk 混合式連線」的清單。  


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
