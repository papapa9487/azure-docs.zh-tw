---
title: "如何設定應用程式 Proxy 應用程式以使用 Kerberos 限制委派 | Microsoft Docs"
description: "如何針對 Azure AD 應用程式 Proxy 應用程式設定 Kerberos 限制委派。"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 3a768c30cb874d42d7b4fbd2eeaa6c0e23904e10
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-configure-an-application-proxy-application-to-use-kerberos-constrained-delegation"></a>如何設定應用程式 Proxy 應用程式以使用 Kerberos 限制委派

可用來針對已發佈應用程式達成 SSO 的方法會因應用程式而異，而 Azure 應用程式 Proxy 提供的其中一個現成選項，便是 Kerberos 限制委派 (KCD)。 我們會在此設定連接器主機，來代表使用者對後端應用程式執行受限制 Kerberos 驗證。

啟用 KCD 的實際程序相當簡單，使用者通常只需要大致了解達成 SSO 的各種元件和驗證流程。 有關 KCD SSO 無法正常運作的案例，可能較不易找到良好的疑難排解資訊來源。

因此，本文嘗試提供統整的參考內容，這應該有助於針對一些常見的問題進行疑難排解和自我修復。 同時，本文也提供其他指導方針，以診斷更複雜及困難的實作。

請注意，本文會做出以下假設：

-   Azure 應用程式 Proxy 已經根據我們的[文件](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable)進行部署，且針對非 KCD 應用程式的一般存取都能夠正常運作。

-   已發佈的目標應用程式是以 IIS 和 Microsoft 的 Kerberos 實作為基礎。

-   伺服器和應用程式主機位於單一的 Active Directory 網域中。 如需有關跨網域和樹系案例的詳細資訊，請參閱 [KCD 白皮書 (英文)](http://aka.ms/KCDPaper)。

-   對象應用程式是在已啟用預先驗證的 Azure 租用戶中發佈，而且使用者必須透過表單式驗證向 Azure 驗證。 本文並未涵蓋各種豐富型用戶端驗證案例，但會於未來新增。

## <a name="prerequisites"></a>必要條件

Azure 應用程式 Proxy 可以部署到幾乎所有類型的基礎結構或環境，且架構當然會隨組織而有所不同。 造成 KCD 相關問題最常見的一個原因並不是環境本身，而僅僅是設定錯誤或一般性的疏忽。

基於這個理由，建議您在開始進行疑難排解之前，先確定您已滿足我們主要的[使用 KCD SSO 搭配應用程式 Proxy 文章](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd)中所述的所有必要條件。

特別是在 2012R2 上設定 KCD 的小節，因為這會採用與在舊版 Windows 上設定 KCD 完全不同的方式。但同時您也應留意其他數個考量：

-   由於網域成員伺服器開啟與特定網域控制站的安全通道對話， 然後隨時變更至另一個對話的情況並不罕見，因此連接器主機通常並不應該限制為僅能與特定本機網站 DC 通訊。

-   類似於上一點，跨網域案例依賴將連接器主機導向至 DC 的轉介，且該 DC 可能位於本機網路周邊之外。 在此案例中，請同樣務必確定您也允許流向代表其他個別網域之 DC 的流量，否則委派會失敗。

-   您應該盡可能避免在連接器主機和 DC 之間放置任何作用中的 IPS/IDS 裝置，因為這些裝置有時候會侵入及干擾核心 RPC 流量

雖然我們都想要盡快且有效地解決問題，但整體的工作可能會相當耗時，因此您應該盡可能在最簡單的案例中嘗試並測試委派。 您加入越多的變數，就需要應付更多的問題。 例如，將您的測試限制為單一連接器可以節省寶貴的時間，其他的連接器則可以在問題解決後再加入。

有些環境因素也可能造成問題，因此我們在此再次提醒您，請盡可能嘗試將架構縮減為最低限度來進行測試。 例如，由於內部防火牆 ACL 設定錯誤的情況並不罕見，因此可能的話，請允許來自連接器的所有流量直接通往 DC 和後端應用程式。 

事實上，放置連接器的最佳位置便是讓它盡可能接近其目標。 在測試同時設置內嵌的防火牆，只會增加不必要的複雜性，而且可能會延長您的調查時間。

所以，KCD 問題到底是由什麼構成？ 有數個常見的徵兆會指出 KCD SSO 失敗，而問題的第一個徵兆通常會在瀏覽器中出現。

   ![不正確的 KCD 組態錯誤](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

   ![因為缺少權限而導致授權失敗](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

這些都有無法執行 SSO 的相同徵狀，並因此拒絕使用者存取應用程式。

## <a name="troubleshooting"></a>疑難排解

接著，您必須根據問題與觀察到的徵狀來進行疑難排解。 在進一步之前，建議您先參閱下列連結，其中可能包含您尚未了解的有用資訊：

-   [針對應用程式 Proxy 問題和錯誤訊息進行疑難排解](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)

-   [Kerberos 錯誤與徵狀](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#kerberos-errors)

-   [在內部部署和雲端身分識別不相同時使用 SSO](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd#working-with-sso-when-on-premises-and-cloud-identities-are-not-identical)

如果您已經進行到這裡，則必然存在主要問題。 您必須更深入地鑽研，因此我們會先將流程分為可以進行疑難排解的三個不同階段。

**用戶端預先驗證**：外部使用者透過瀏覽器向 Azure 進行驗證。

KCD SSO 若要運作，必須要能夠向 Azure 進行預先驗證。 如果有發生任何問題，便應該先測試並解決這項問題。 請注意，預先驗證階段與 KCD 或已發佈的應用程式無關。 透過例行性檢查 Azure 中是否存在對象帳戶，且該帳戶是否被停用/封鎖，應該就能輕鬆地修正任何不一致。 瀏覽器中的錯誤回應，通常便能提供足夠的資訊以了解原因。 如果您無法確定，也可以查看我們其他的疑難排解文件。

**委派服務**：代表使用者從 KDC (Kerberos 發佈中心) 取得 Kerberos 服務票證的 Azure Proxy 連接器。

用戶端與 Azure 前端之間的外部通訊，除了確保 KCD 正常運作外，應該和 KCD 沒有任何關係。 這是為了使 Azure Proxy 服務可以獲得用來取得 Kerberos 票證的有效使用者識別碼。 如果不這麼做，就無法使用 KCD 並且會失敗。

如先前所述，瀏覽器錯誤訊息通常會針對失敗的原因提供一些很好的線索。 請務必記下回應中的活動識別碼和時間戳記，因為這可讓您將行為與 Azure Proxy 事件記錄檔中的實際事件相互關聯。

   ![不正確的 KCD 組態錯誤](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

而事件記錄檔中看到的對應項目，會被視為事件 13019 或 12027。 您可以在 [應用程式及服務記錄檔] &gt; [Microsoft] &gt; [AadApplicationProxy] &gt; [連接器] &gt; [管理] 中找到連接器事件記錄檔。

   ![應用程式 Proxy 事件記錄檔中的事件 13019](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

   ![應用程式 Proxy 事件記錄檔中的事件 12027](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

-   在您的內部 DNS 中針對應用程式位址使用 A 記錄，而非 CName

-   重新確認連接器主機已授與權限以委派至指定目標帳戶的 SPN，而且已選取 [使用任何驗證通訊協定]。 這部分涵蓋於我們主要的 [SSO 組態文章](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd)

-   確定 AD 中只存在單一 SPN 執行個體，方法是從任何網域成員主機上的命令提示字元發出 **setspn -x**

-   檢查是否強制執行網域原則來限制[發出的 Kerberos 權杖大小上限 (英文)](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/)，因為這會在超過上限的情況下防止連接器取得權杖

擷取連接器主機和網域 KDC 之間交換的網路追蹤，將會是針對問題取得更低層級詳細資料的下一個最佳步驟。 您可以在[深入探討疑難排解文件 (英文)](https://aka.ms/proxytshootpaper) 中找到此資訊。

如果票證看起來沒問題，則您應該會在記錄檔中看到事件，指出因應用程式傳回 401 而導致驗證失敗。 這通常表示目標應用程式拒絕您的票證，因此請繼續進行接下來的下一個階段。

**目標應用程式**：連接器所提供之 Kerberos 票證的取用者

在這個階段，連接器應該已經以第一個應用程式要求內之標頭的形式，將 Kerberos 服務票證傳送到後端。

-   使用應用程式在入口網站中定義的內部 URL，驗證應用程式可從連接器主機上的瀏覽器直接存取。 然後您就可以成功登入。 執行此操作的詳細資料，可在 [連接器疑難排解] 頁面上找到。

-   在連接器主機上繼續確認瀏覽器與應用程式之間的驗證使用 Kerberos，方法是執行下列動作：

1.  在 Internet Explorer 中執行開發工具 (**F12**)，或從連接器主機使用 [Fiddler (英文)](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/)。 使用內部 URL 移至應用程式，然後檢查應用程式回應中傳回的 WWW 授權標頭，以確保存在交涉或 Kerberos。 在瀏覽器至應用程式的回應中傳回的後續 Kerberos Blob，通常會以 **YII** 做為開頭，因此這是一個判斷 Kerberos 可以使用的最佳指標。 另一方面，NTLM 一律會以 **TlRMTVNTUAAB** 做為開頭，它會在從 Base64 解碼的情況下讀取 NTLMSSP。 如果您在 Blob 的開頭看到 **TlRMTVNTUAAB**，便表示「無法」使用 Kerberos。 如果您沒有看到它，則應該可以使用 Kerberos。

  * 請注意，如果使用 Fiddler，這個方法需要在 IIS 中的應用程式設定上將延伸保護暫時停用。

     ![瀏覽器網路檢查視窗](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

    *圖：*由於這個開頭不是 TIRMTVNTUAAB，所以 Kerberos 在此範例中可以使用。 這是 Kerberos Blob 開頭不是 YII 的範例。

2.  暫時從 IIS 網站上的提供者清單移除 NTLM，然後直接從連接器主機上的 IE 存取應用程式。 當提供者清單中不存在 NTLM 時，您應該便只能使用 Kerberos 存取應用程式。 如果失敗，則表示應用程式的設定有問題，且 Kerberos 驗證無法正常運作。

如果 Kerberos 無法使用，請接著檢查 IIS 中的應用程式驗證設定，以確定交涉已列於最上層，而 NTLM 則位於它的下方。 (不是 Negotiate:kerberos 或 Negotiate:PKU2U)。 只有在 Kerberos 能正常運作的情況下才繼續。

   ![Windows 驗證提供者](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)
   
-   Kerberos 和 NTLM 就位之後，現在請暫時在入口網站中停用應用程式的預先驗證。 查看您是否能使用外部 URL 從網際網路存取它。 系統應該會提示您進行驗證，而您應該可以使用上一個步驟中的相同帳戶進行驗證。 如果不行，這表示問題是在後端應用程式，而與 KCD 完全無關。

-   現在，請重新在入口網站中啟用預先驗證，並嘗試透過應用程式的外部 URL 連線到應用程式，來透過 Azure 驗證。 如果 SSO 失敗，則您應該會在瀏覽器中看到禁止錯誤訊息，並在記錄檔中看見事件 13022：

    「因為後端伺服器以 HTTP 401 錯誤回應 Kerberos 驗證嘗試，Microsoft AAD 應用程式 Proxy 連接器便無法驗證使用者。」

    ![HTTP 401 禁止錯誤](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

-   檢查 IIS 應用程式，確定設定的應用程式集區已設定為使用 SPN 針對 AD 所設定的相同帳戶，方法是以下列方法瀏覽至 IIS 中

    ![IIS 應用程式組態視窗](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

    一旦您知道身分識別後，請從命令提示字元中發出下列命令，以確定此帳戶確實是搭配目標 SPN 進行設定。 例如，**setspn – q http/spn.wacketywack.com**

    ![SetSPN 命令視窗](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

-   檢查入口網站中針對應用程式的設定所定義的 SPN，是否與針對應用程式之應用集區所使用的目標 AD 帳戶所設定的 SPN 相同

   ![Azure 入口網站中的 SPN 組態](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)
   
-   移至 IIS 並選取應用程式的 [設定編輯器] 選項，然後瀏覽至 **system.webServer/security/authentication/windowsAuthentication** 以確定 **UseAppPoolCredentials** 已設定為 true

   ![IIS 設定應用程式集區認證選項](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

雖然保留核心模式啟用對於改善 Kerberos 作業的效能很有用，但這也會導致要求之服務的票證可以使用電腦帳戶解密。 這也稱為本機系統，因此當應用程式裝載於伺服器陣列中的多部伺服器上時，將此設定設為 true 將會中斷 KCD。

-   做為額外的檢查，您也可以停用「延伸」保護。 曾經有發生過案例，證明在非常特定的組態 (其中應用程式被發佈為預設網站的子資料夾) 啟用此保護時會中斷 KCD。 此保護本身僅會針對匿名驗證進行設定，並使整個對話方塊呈現灰色，表示子物件不會繼承任何作用中的設定。 但可能的話，我們還是會建議一律啟用此保護，因此在測試完畢之後，請記得將它還原為啟用狀態。

這些額外的測試應該可以讓您開始使用已發佈的應用程式。 您可以開始啟動其他已設定進行委派的連接器，但如果狀況沒有進展，建議您閱讀更多深入的技術逐步解說：[針對 Azure AD 應用程式 Proxy 進行疑難排解的完整指南 (英文)](https://aka.ms/proxytshootpaper)

如果您仍無法處理問題，支援人員會很樂意接手並提供協助。 請直接在入口網站內建立支援票證，我們的工程師將會連絡您。

## <a name="other-scenarios"></a>其他案例

-   Azure 應用程式 Proxy 會在將要求傳送至應用程式之前要求 Kerberos 票證。 某些協力廠商應用程式 (例如 Tableau Server) 不會使用此驗證方法，而會預期更加傳統的交涉方式。 第一個要求是匿名的，可讓應用程式透過 401 回應它所支援的驗證類型。

-   雙躍點驗證：常用於應用程式已分層並具有後端與前端，且兩者皆需要驗證的案例 (例如 SQL Reporting Services)。

## <a name="next-steps"></a>後續步驟
[在受管理的網域上設定 Kerberos 限制委派 (KCD)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-enable-kcd)
