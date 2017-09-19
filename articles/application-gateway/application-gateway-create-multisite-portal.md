---
title: "使用 Azure 應用程式閘道裝載多個站台 | Microsoft Docs"
description: "本頁面提供設定現有 Azure 應用程式閘道以在具有 Azure 入口網站的相同閘道上裝載多個 Web 應用程式的指示。"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 95f892f6-fa27-47ee-b980-7abf4f2c66a9
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 84bd62ae17b7f7ba4cd815ef1f9880679607ebce
ms.contentlocale: zh-tw
ms.lasthandoff: 04/20/2017

---
# <a name="configure-an-existing-application-gateway-for-hosting-multiple-web-applications"></a>設定現有應用程式閘道來裝載多個 Web 應用程式

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-create-multisite-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-multisite-azureresourcemanager-powershell.md)
> 
> 

多站台裝載可讓您在相同的應用程式閘道上部署多個 Web 應用程式。 它需倚賴在連入的 HTTP 要求中有主機標頭存在，以判斷哪一個接聽程式要接收流量。 然後再由接聽程式將流量導向閘道的規則定義中所設定的適當後端集區。 在已啟用 SSL 功能的 Web 應用程式中，則應用程式閘道會依賴「伺服器名稱指示」(SNI) 擴充功能來選擇正確的 Web 流量接聽程式。 多站台裝載的常見用法是將不同 Web 網域的要求負載平衡至不同的後端伺服器集區。 同樣地，相同根網域的多個子網域也可以裝載在相同的應用程式閘道上。

## <a name="scenario"></a>案例

在下列範例中，應用程式閘道會利用兩個後端伺服器集區來為 contoso.com 和 fabrikam.com 的流量提供服務：contoso 伺服器集區和 fabrikam 伺服器集區。 類似的設定也可用來裝載子網域，例如 app.contoso.com 和 blog.contoso.com。

![多網站案例][multisite]

## <a name="before-you-begin"></a>開始之前

這個案例會將多網站支援新增至現有應用程式閘道。 若要完成此案例，現有的應用程式閘道必須可供設定。 請造訪[使用入口網站建立應用程式閘道](application-gateway-create-gateway-portal.md)，以了解如何在入口網站中建立基本應用程式閘道。

以下是更新應用程式閘道所需的步驟：

1. 建立要用於每個網站的後端集區。
2. 建立每個網站應用程式閘道都支援的接聽程式。
3. 建立規則，將每個接聽程式與適當的後端對應。

## <a name="requirements"></a>需求

* **後端伺服器集區：** 後端伺服器的 IP 位址清單。 列出的 IP 位址應屬於虛擬網路子網路或是公用 IP/VIP。 您也可以使用 FQDN。
* **後端伺服器集區設定：** 每個集區都包括一些設定，例如連接埠、通訊協定和以 Cookie 為基礎的同質性。 這些設定會繫結至集區，並套用至集區內所有伺服器。
* **前端連接埠：** 此連接埠是在應用程式閘道上開啟的公用連接埠。 流量會到達此連接埠，然後重新導向至其中一個後端伺服器。
* **接聽程式：** 接聽程式具有前端連接埠、通訊協定 (Http 或 Https，這些值都區分大小寫) 和 SSL 憑證名稱 (如果已設定 SSL 卸載)。 針對啟用多站台功能的應用程式閘道，會一併新增主機名稱和 SNI 指示器。
* **規則：**規則會繫結接聽程式和後端伺服器集區，並定義流量達到特定接聽程式時應該導向至哪個後端伺服器集區。 會以規則列出的順序進行處理，且不論精確性，都會透過相符的第一個規則將流量進行導向。 例如，如果您在相同的連接埠上同時使用基本接聽程式的規則和多站台接聽程式的規則，則必須將多站台接聽程式的規則列於基本接聽程式的規則之前，多站台規則才能如預期般運作。 
* **憑證︰**每個接聽程式需要唯一的憑證，在此範例中針對多網站建立 2 個接聽程式。 必須建立兩個 .pfx 憑證和它們的密碼。

## <a name="create-back-end-pools-for-each-site"></a>建立每個網站的後端集區

需要應用程式閘道支援之每個網站的後端集區，在此案例中會建立 2 個，一個用於 contoso11.com，另一個用於 fabrikam11.com。

### <a name="step-1"></a>步驟 1

瀏覽至 Azure 入口網站中現有的應用程式閘道 ( https://portal.azure.com ) 。 選取 [後端集區]，按一下 [新增]

![新增後端集區][7]

### <a name="step-2"></a>步驟 2

針對後端集區 **pool1** 填入資訊，新增後端伺服器的 ip 位址或 FQDN，然後按一下 [確定]

![後端集區 pool1 設定][8]

### <a name="step-3"></a>步驟 3

在後端集區刀鋒視窗上按一下 [新增] 以新增額外的後端集區 **pool2**，新增後端伺服器的 ip 位址或 FQDN，然後按一下 **[確定]**

![後端集區 pool2 設定][9]

## <a name="create-listeners-for-each-back-end"></a>建立每個後端的接聽程式

「應用程式閘道」需依賴 HTTP 1.1 主機標頭，才能在相同的公用 IP 位址和連接埠上裝載多個網站。 在入口網站中建立的基本接聽程式不包含這個屬性。

### <a name="step-1"></a>步驟 1

按一下現有應用程式閘道上的 [接聽程式]，然後按一下 [多網站] 以新增第一個接聽程式。

![接聽程式概觀刀鋒視窗][1]

### <a name="step-2"></a>步驟 2

填入接聽程式的資訊。 在此範例中，會設定 SSL 終止，建立新的前端連接埠。 上傳 .pfx 憑證，用於進行 SSL 終止。 此刀鋒視窗上相較於標準基本接聽程式刀鋒視窗的唯一差別是主機名稱。

![接聽程式屬性刀鋒視窗][2]

### <a name="step-3"></a>步驟 3

按一下 [多網站]，並且針對第二個網站建立另一個接聽程式，如同上一個步驟所述。 請確定針對第二個接聽程式使用不同的憑證。 此刀鋒視窗上相較於標準基本接聽程式刀鋒視窗的唯一差別是主機名稱。 填入接聽程式的資訊，然後按一下 [確定]。

![接聽程式屬性刀鋒視窗][3]

> [!NOTE]
> 在 Azure 入口網站中針對應用程式閘道建立接聽程式是長時間執行的工作，在此案例中可能需要一段時間來建立兩個接聽程式。 完成時，入口網站中的接聽程式如下圖所示：

![接聽程式概觀][4]

## <a name="create-rules-to-map-listeners-to-backend-pools"></a>建立規則，將接聽程式對應至後端集區

### <a name="step-1"></a>步驟 1

瀏覽至 Azure 入口網站中現有的應用程式閘道 ( https://portal.azure.com ) 。 選取 [規則]，選擇現有的預設規則 **rule1**，然後按一下 [編輯]。

### <a name="step-2"></a>步驟 2

填寫 [規則] 刀鋒視窗，如下圖所示。 選擇第一個接聽程式和第一個集區，完成時按一下 [儲存]。

![編輯現有的規則][6]

### <a name="step-3"></a>步驟 3

按一下 [基本規則] 以建立第 2 個規則。 以第二個接聽程式和第二個後端集區填寫表單，按一下 [確定] 以儲存。

![新增基本規則刀鋒視窗][10]

此案例會完成透過 Azure 入口網站設定具有多網站支援的現有應用程式閘道。

## <a name="next-steps"></a>後續步驟

了解如何使用[應用程式閘道 - Web 應用程式防火牆](application-gateway-webapplicationfirewall-overview.md)保護您的網站

<!--Image references-->
[1]: ./media/application-gateway-create-multisite-portal/figure1.png
[2]: ./media/application-gateway-create-multisite-portal/figure2.png
[3]: ./media/application-gateway-create-multisite-portal/figure3.png
[4]: ./media/application-gateway-create-multisite-portal/figure4.png
[5]: ./media/application-gateway-create-multisite-portal/figure5.png
[6]: ./media/application-gateway-create-multisite-portal/figure6.png
[7]: ./media/application-gateway-create-multisite-portal/figure7.png
[8]: ./media/application-gateway-create-multisite-portal/figure8.png
[9]: ./media/application-gateway-create-multisite-portal/figure9.png
[10]: ./media/application-gateway-create-multisite-portal/figure10.png
[multisite]: ./media/application-gateway-create-multisite-portal/multisite.png

