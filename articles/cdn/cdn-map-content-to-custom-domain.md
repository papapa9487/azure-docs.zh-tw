---
title: "將自訂網域新增至 CDN 端點 | Microsoft Docs"
description: "了解如何將 Azure CDN 內容對應至自訂網域。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 289f8d9e-8839-4e21-b248-bef320f9dbfc
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: mazha
ms.openlocfilehash: fd36b94c64ad31064dbb2e0badceaee5e5bc400f
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2017
---
# <a name="add-a-custom-domain-to-your-cdn-endpoint"></a>將自訂網域新增至 CDN 端點
建立設定檔之後，通常也會建立一或多個 CDN [端點](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint) (`azureedge.net` 的子網域)，用來透過 HTTP 和 HTTPS 傳遞內容。 根據預設，此端點包含在您的所有 URL 內 (例如，`https://contoso.azureedge.net/photo.png`)。 為了方便起見，Azure CDN 可讓您在自訂網域 (例如，`www.contoso.com`) 與您的端點之間建立關聯。 使用此選項，您可以使用自訂網域 (而不是端點) 來傳遞內容。 這個選項非常有用，例如，基於品牌目的，您想讓客戶看到您自己的網域名稱。

如果您還沒有自訂網域，必須先向網域提供者購買。 取得自訂網域後，請遵循下列步驟：
1. [存取網域提供者的 DNS 記錄](#step-1-access-dns-records-by-using-your-domain-provider)
2. [建立 CNAME DNS 記錄](#step-2-create-the-cname-dns-records)
    - 選項 1：直接將自訂網域對應到 CDN 端點
    - 選項 2：使用 cdnverify 將自訂網域對應到 CDN 端點 
3. [在 Azure 中啟用 CNAME 記錄對應](#step-3-enable-the-cname-record-mapping-in-azure)
4. [確認自訂子網域參照您的 CDN 端點](#step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint)
5. [(相依步驟) 將永久自訂網域對應到 CDN 端點](#step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint)

## <a name="step-1-access-dns-records-by-using-your-domain-provider"></a>步驟1：使用網域提供者存取 DNS 記錄

如果您使用 Azure 來裝載您的 [DNS 網域](https://docs.microsoft.com/en-us/azure/dns/dns-overview)，必須將網域提供者的 DNS 委派給 Azure DNS。 如需詳細資訊，請參閱[將網域委派給 Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)。

反之，如果您是交由網域提供者來處理您的 DNS 網域，請登入網域提供者的網站。 找到管理 DNS 記錄的頁面 (可查閱提供者的文件或，尋找網站上標示**網域名稱**、**DNS** 或**名稱伺服器管理**的區域)。 通常可透過檢視您的帳戶資訊，尋找 [我的網域] 之類的連結，來找到 DNS 記錄分頁。 某些提供者會使用不同的連結來新增不同類型的記錄。

> [!NOTE]
> 對於某些提供者 (例如 GoDaddy)，您必須選取另外的 [儲存變更] 連結，才會讓 DNS 記錄的變更生效。 


## <a name="step-2-create-the-cname-dns-records"></a>步驟 2：建立 CNAME DNS 記錄

您必須先透過網域提供者建立正式名稱 (CNAME) 記錄，才可以在 Azure CDN 端點使用自訂網域。 CNAME 記錄是一種在網域名稱系統 (DNS) 裡的記錄，可藉由指定「正式」或真實網域名稱的網域名稱別名，將來源網域對應至目的地網域。 在 Azure CDN 中，來源網域是您的自訂網域 (和子網域)，目的地網域是您的 CDN 端點。 當您從入口網站或 API 將自訂網域新增至端點時，Azure CDN 會去確認 DNS CNAME 記錄。 

CNAME 記錄會對應到特定網域和子網域 (例如 `www.contoso.com` 或 `cdn.contoso.com`)；但 CNAME 記錄無法對應至根網域 (例如 `contoso.com`)。 子網域只可以和一個 CDN 端點關聯，您所建立的 CNAME 記錄會將所有定址至子網域的流量路由傳送至指定的端點。 例如，如果您將 `www.contoso.com` 與您的 CDN 端點產生關聯，則無法將它與其他 Azure 端點 (例如儲存體帳戶端點、雲端服務端點) 產生關聯。 不過，針對不同的服務端點，您可以使用來自相同網域的不同子網域。 您也可以將不同的子網域對應至相同的 CDN 端點。

使用下列選項之一將自訂網域對應至 CDN 端點：

- 選項 1：直接對應。 如果自訂網域上沒有執行生產流量，您可以直接將自訂網域對應到 CDN 端點。 在將自訂網域對應至 CDN 端點的過程中，可能會由於您在 Azure 入口網站中註冊網域而導致網域短暫停擺。 CNAME 對應項目的檔案格式應該如下： 
 
  | 名稱             | 類型  | 值                  |
  |------------------|-------|------------------------|
  | www\.contoso.com | CNAME | contoso\.azureedge.net |


- 選項 2：與 **cdnverify** 子網域對應。 如果不可中斷的生產流量在自訂網域上執行，您可以建立暫時 CNAME 來對應至 CDN 端點。 使用此選項，您可以使用 Azure **cdnverify** 子網域來提供中繼註冊步驟，讓使用者可以存取您的網域，而不會在 DNS 對應進行時中斷。

   1. 建立新的 CNAME 記錄，並提供包含 **cdnverify** 子網域的子網域別名。 例如，**cdnverify.www** 或 **cdnverify.cdn**。 
   2. 以下列格式提供主機名稱 (也就是您的 CDN 端點)：`cdnverify.<EndpointName>.azureedge.net`。 CNAME 對應項目的檔案格式應該如下： 

   | 名稱                       | 類型  | 值                            |
   |----------------------------|-------|----------------------------------|
   | cdnverify.www\.contoso.com | CNAME | cdnverify.contoso\.azureedge.net | 


## <a name="step-3-enable-the-cname-record-mapping-in-azure"></a>步驟 3：在 Azure 中啟用 CNAME 記錄對應

使用先前的程序之一註冊您的自訂網域之後，您便可以在 Azure CDN 中啟用自訂網域功能。 

1. 登入 [Azure 入口網站](https://portal.azure.com/)，瀏覽至您要對應到自訂網域之端點的 CDN 設定檔。  
2. 在 [CDN 設定檔] 刀鋒視窗中，選取要與子網域產生關聯的 CDN 端點。
3. 在端點刀鋒視窗左上角，按一下 [自訂網域]。 

   ![自訂網域按鈕](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

4. 在 [自訂網域] 文字方塊中，輸入自訂網域 (包括子網域)。 例如，`www.contoso.com` 或 `cdn.contoso.com`。

   ![新增自訂網域對話方塊](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain-dialog.png)

5. 按一下 [新增] 。

   Azure 會確認您所輸入的網域名稱存在 CNAME 記錄。 如果 CNAME 正確，您的自訂網域就會驗證。 CNAME 記錄傳播到名稱伺服器需要一些時間。 如果您的網域未立即驗證，但您確定 CNAME 記錄正確，請等待數分鐘的時間，然後再試一次。 若是**來自 Verizon 的 Azure CDN** (標準與進階) 端點，最多可能需要 90 分鐘的時間，自訂網域設定才能傳播至所有 CDN 邊緣節點。  


## <a name="step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>步驟 4：確認自訂子網域參照您的 CDN 端點

完成註冊自訂網域之後，請確認自訂子網域參照您的 CDN 端點。
 
1. 確定您有在端點上快取的公用內容。 例如，如果您的 CDN 端點與儲存體帳戶相關聯，則 CDN 會快取公用 Blob 容器中的內容。 若要測試自訂網域，確認您的容器設定為允許公用存取，而且它包含至少一個 blob。

2. 在瀏覽器中，使用自訂網域瀏覽至 blob 位址。 例如，如果您的自訂網域是 `cdn.contoso.com`，快取 blob 的 URL 應和以下 URL 類似：`http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg`。


## <a name="step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint"></a>步驟 5 (相依步驟)：將永久自訂網域對應到 CDN 端點

這個步驟取決於步驟 2 的選項 2 (與 **cdnverify** 子網域對應)。 如果您使用暫時 **cdnverify** 子網域且確認它能運作，您便可以將永久自訂網域對應到 CDN 端點。

1. 在您的網域提供者網站上建立 CNAME DNS 記錄，來將永久自訂網域對應至 CDN 端點。 CNAME 對應項目的檔案格式應該如下： 
 
   | 名稱             | 類型  | 值                  |
   |------------------|-------|------------------------|
   | www\.contoso.com | CNAME | contoso\.azureedge.net |
2. 刪除具有您先前建立之 **cdnverify** 子網域的 CNAME 記錄。

## <a name="see-also"></a>另請參閱
[如何啟用 Azure 內容傳遞網路 (CDN)](cdn-create-new-endpoint.md)  
[將網域委派給 Azure DNS](../dns/dns-domain-delegation.md)
