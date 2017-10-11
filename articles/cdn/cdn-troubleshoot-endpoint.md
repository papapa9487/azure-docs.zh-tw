---
title: "針對傳回 404 狀態的 Azure CDN 端點進行疑難排解 | Microsoft Docs"
description: "針對 Azure CDN 端點的 404 回應碼進行疑難排解。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f59fbd18413fb44026d8c92b7f6940ed2f8a00a8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshooting-cdn-endpoints-returning-404-statuses"></a>針對傳回 404 狀態的 CDN 端點進行疑難排解
這篇文章可協助您針對 [CDN 端點](cdn-create-new-endpoint.md) 傳回 404 錯誤的問題進行疑難排解。

若您對本文中的任何步驟有需要進一步協助的地方，請連絡 [MSDN Azure 和堆疊溢位論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您也可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/) ，然後按一下 **取得支援**。

## <a name="symptom"></a>徵狀
您已建立 CDN 設定檔和端點，但您的內容似乎無法在 CDN 上使用。  嘗試透過 CDN URL 存取內容的使用者收到 HTTP 404 狀態碼。 

## <a name="cause"></a>原因
有幾個可能的原因，包括︰

* CDN 看不到檔案的來源
* 端點設定錯誤，導致 CDN 找錯位置
* 主機正在拒絕來自 CDN 的主機標頭
* 端點沒有足夠的時間散佈到整個 CDN

## <a name="troubleshooting-steps"></a>疑難排解步驟
> [!IMPORTANT]
> 建立 CDN 端點之後，無法立即使用，因為註冊需要時間透過 CDN 進行散佈。  若為 <b>來自 Akamai 的 Azure CDN</b> 設定檔，通常會在一分鐘之內完成傳播。  若為<b>來自 Verizon 的 Azure CDN</b> 設定檔，則通常會在 90 分鐘之內完成傳播，但在某些情況下可能會更久。  如果您完成這份文件中的步驟，而仍然收到 404 回應；請考慮在開啟支援票證之前，等候數小時後再次進行檢查。
> 
> 

### <a name="check-the-origin-file"></a>請檢查來源檔案
首先，我們應確認要快取的檔案在來源上可使用，且可供公開存取。  要做到這一點，最快的做法是在 In-Private 或 Incognito 工作階段中開啟瀏覽器，並直接瀏覽至檔案。  只需輸入 URL，或將此 URL 貼入網址方塊，並查看是否會產生您所預期的檔案。  此範例中，我將使用在 Azure 儲存體帳戶中的檔案，可在 `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`中存取。  如您所見，它已成功通過測試。

![成功！](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> 雖然這是驗證檔案已公開可用最快且最簡單的方式；在您組織中的某些網路設定會給您一種假象，認為這個檔案是公開可用；但事實上，只有您網路內的使用者才看得見 (即便其已裝載在 Azure 中)。  如果您有外部瀏覽器，便可進行測試，例如行動裝置並未連線到您組織的網路，或是在 Azure 的虛擬機器上驗證，這會是最佳的作法。
> 
> 

### <a name="check-the-origin-settings"></a>請檢查來源設定
現在我們已確認檔案在網際網路上已公開可用，接著應該驗證來源設定。  在 [Azure 入口網站](https://portal.azure.com)，瀏覽至您的 CDN 設定檔，並按一下您要進行疑難排解的端點。  在產生的 [端點]  刀鋒視窗中，按一下來源。  

![來源反白的端點刀鋒視窗](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

[來源]  刀鋒視窗隨即出現。 

![原始刀鋒視窗](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>來源類型和主機名稱
確認**來源類型**正確，並確認**來源主機名稱**。  在我的範例中，`https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`，URL 的主機名稱部分是 `cdndocdemo.blob.core.windows.net`。  您可以在螢幕擷取畫面中看到，這部分是正確的。  對於 Azure 儲存體、Web 應用程式，及雲端服務的來源，[來源主機名稱]  欄位為下拉式清單，所以我們不需要擔心拼寫正確的問題。  不過，如果您使用的是自訂來源，則您的主機名稱的拼字正確便「十分重要」  ！

#### <a name="http-and-https-ports"></a>HTTP 和 HTTPS 連接埠
其他需要在此檢查的是 **HTTP** 和 **HTTPS 連接埠**。  在大部分情況下，80 和 443 皆正確，而且您不需要進行任何變更。  不過，如果原始伺服器是透過不同連接埠連接，那麼就必須在這裡表示。  如果您不確定，不妨看一下原始檔案的 URL。  HTTP 和 HTTPS 規格指定連接埠 80 和 443 做為預設值。 在我的 URL 中， `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`，未指定連接埠，因此會假設預設值為 443，而且我的設定值正確無誤。  

不過，假如您在稍早測試的原始檔案 URL 為 `http://www.contoso.com:8080/file.txt`。  請注意主機名稱區段結尾的 `:8080` 。  這會告知瀏覽器使用連接埠 `8080` 連接到位於 `www.contoso.com` 的 Web 伺服器，因此您必須在 [HTTP 連接埠] 欄位輸入 8080。  請務必注意，這些連接埠設定只會影響端點用來從來源擷取資訊的連接埠。

> [!NOTE]
> **來自 Akamai 的 Azure CDN** 端點不允許原始來源的完整 TCP 連接埠範圍。  如需不允許的原始連接埠清單，請參閱 [來自 Akamai 的 Azure CDN 允許的原始連接埠](https://msdn.microsoft.com/library/mt757337.aspx)。  
> 
> 

### <a name="check-the-endpoint-settings"></a>請檢查端點設定
回到 [端點] 刀鋒視窗，按一下 [設定] 按鈕。

![以反白顯示設定按鈕的端點刀鋒視窗](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

端點的 [設定]  刀鋒視窗隨即出現。

![設定刀鋒視窗](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>通訊協定
針對 **通訊協定**，請確認已選取用戶端所使用的通訊協定。  用戶端使用的通訊協定和用來存取來源的協定相同，因此正確設定來源連接埠 (如上一節所示) 相當重要。  不論來源連接埠為何，只會透過預設的 HTTP 和 HTTPS 連接埠 (80 和 443) 連接端點。

讓我們回到 `http://www.contoso.com:8080/file.txt`的假設範例。  您應該還記得之前 Contoso 指定 `44300` 做為其 HTTP 連接埠，但我們也假設其指定 `8080` 做為 HTTPS 連接埠。  如果他們建立名為 `contoso` 的端點，其 CDN 端點的主機名稱會是 `contoso.azureedge.net`。  `http://contoso.azureedge.net/file.txt` 的要求為 HTTP 要求，因此端點會使用連接埠 8080 上的 HTTP 從來源擷取該要求。  對 HTTPS 的安全要求 `https://contoso.azureedge.net/file.txt`，會導致當從來源擷取檔案時，端點會使用在連接埠 44300 上的 HTTPS。

#### <a name="origin-host-header"></a>原始主機標頭
**原始主機標頭** 為隨著每個要求傳送至來源的主機標頭值。  在大部分情況下，這應該和我們稍早驗證的是相同的 **原始主機名稱** 。  此欄位中不正確的值通常不會造成 404 狀態；但根據來源預期的結果，可能會導致其他 4xx 狀態。

#### <a name="origin-path"></a>原始路徑
最後，我們應該確認 **原始路徑**。  依預設這會是空白。  如果您想要縮小在 CDN 上使用原始裝載資源的範圍，應該只使用此欄位。  

例如，在我的端點中，我想要能夠使用儲存體帳戶上的所有資源，因此我會將 **來源路徑** 保留空白。  這表示，對於 `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` 的要求，結果會讓我的端點連接至要求 `/publicblob/lorem.txt` 的 `cdndocdemo.core.windows.net`。  同樣地，對於 `https://cdndocdemo.azureedge.net/donotcache/status.png` 的要求會導致端點向來源要求 `/donotcache/status.png`。

但如果我不想在我的來源上的每個路徑使用 CDN 呢？  假設我只想要公開 `publicblob` 路徑。  如果我在 [原始路徑] 欄位中輸入 */publicblob*，將導致端點在對來源提出每個要求之前，都要插入 */publicblob*。  這表示，對於 `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` 的要求，現在實際上會取得 URL 的要求部分 `/publicblob/lorem.txt`，並將 `/publicblob` 附加至開頭。 這會造成從來源對 `/publicblob/publicblob/lorem.txt` 進行要求。  如果該路徑未解析為實際檔案，來源會傳回 404 狀態。  實際上，在此範例中擷取 lorem.txt 的正確 URL 會是 `https://cdndocdemo.azureedge.net/lorem.txt`。  請注意，我們完全不會納入*/publicblob* 路徑，因為 URL 的要求部分是 `/lorem.txt`，且端點新增 `/publicblob` 會造成 `/publicblob/lorem.txt` 將要求傳遞至來源。

