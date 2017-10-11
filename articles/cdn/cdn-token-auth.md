---
title: "使用權杖驗證保護 Azure CDN 資產 | Microsoft Docs"
description: "使用權杖驗證來保護對 Azure CDN 資產的存取。"
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: 
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/11/2016
ms.author: mezha
ms.openlocfilehash: 42b182c314795b1ebf69639ec7ac5583208dc7c1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>使用權杖驗證保護 Azure CDN 資產

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

##<a name="overview"></a>概觀

權杖驗證是一種機制，可讓您防止 Azure CDN 將資產提供給未經授權的用戶端。  這一般是藉由防止對內容進行熱門連結來完成，這類案例中的不同網站 (通常是留言板) 不需權限即可使用您的網站。  這會對您的內容傳遞成本產生影響。 藉由在 CDN 上啟用這個功能，CDN 邊緣 POP 會在傳遞內容之前驗證要求。 

## <a name="how-it-works"></a>運作方式

權杖驗證會藉由提出要求需包含權杖值 (其中包含關於要求者的編碼資訊) 的要求，來確定要求是由受信任的網站所產生。 只有在編碼資訊符合需求時才會將內容提供給要求者，否則要求將會遭到拒絕。 您可以使用下列一或多個參數來設定需求。

- 國家/地區︰允許或拒絕來自指定國家/地區的要求。  [有效的國碼/地區碼清單。](https://msdn.microsoft.com/library/mt761717.aspx) 
- URL︰只允許要求指定的資產或路徑。  
- 主機︰允許或拒絕在要求標頭中使用指定主機的要求。
- 訪客來源︰允許或拒絕要求指定的訪客來源。
- IP 位址︰只允許來自特定 IP 位址或 IP 子網路的要求。
- 通訊協定︰根據用來要求內容的通訊協定來允許或封鎖要求。
- 到期時間︰指派日期和時間週期，以確保連結只能維持一段有效的有限期間。

請參閱每個參數的詳細組態範例。

## <a name="reference-architecture"></a>參考架構

請參閱下圖，其中為在 CDN 上設定權杖驗證以便與您的 Web 應用程式一同運作的參考架構。

![[CDN 設定檔] 刀鋒視窗的 [管理] 按鈕](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>CDN 端點上的權杖驗證邏輯
    
此圖說明若權杖驗證設定於 CDN 端點上，Azure CDN 如何驗證用戶端要求。

![[CDN 設定檔] 刀鋒視窗的 [管理] 按鈕](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>設定權杖驗證

1. 從 [Azure 入口網站](https://portal.azure.com)，瀏覽至您的 CDN 設定檔，然後按一下 [管理] 按鈕來啟動補充入口網站。

    ![[CDN 設定檔] 刀鋒視窗的 [管理] 按鈕](./media/cdn-rules-engine/cdn-manage-btn.png)

2. 將滑鼠移至 [HTTP 大型] 上，然後按一下飛出視窗上的 [權杖驗證]。 您將在此索引標籤中設定加密金鑰和加密參數。

    1. 針對**主要金鑰**輸入唯一的加密金鑰。  針對**備份金鑰**輸入另一個加密金鑰

        ![CDN 權杖驗證安裝識別碼](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    2. 使用加密工具設定加密參數 (根據到期時間、國家/地區、訪客來源、通訊協定、用戶端 IP 來允許或拒絕要求。 您可以使用任何組合。)

        ![[CDN 設定檔] 刀鋒視窗的 [管理] 按鈕](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

        - ec-expire︰指定權杖在指定時間週期之後到期的時間。 在到期時間之後提交的要求將會遭到拒絕。 這個參數會使用 Unix 時間戳記 (以自從 1/1/1970 00:00:00 GMT 的標準 epoch 以來的秒數為根據。 您可以使用線上工具來提供標準時間與 Unix 時間之間的轉換。)例如，如果您想要將權杖設定為在 12/31/2016 12:00:00 GMT 過期，可使用 Unix 時間：1483185600，如下所示：
    
        ![[CDN 設定檔] 刀鋒視窗的 [管理] 按鈕](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
        - ec-url-allow︰允許您量身打造特定資產或路徑的權杖。 若要求的 URL 是以特定的相對路徑開頭，它會限制對該要求的存取。 您可以輸入多個路徑，並以逗號分隔每個路徑。 URL 會區分大小寫。 根據需求，您可以設定不同的值來提供不同層級的存取。 下面是數個案例：
        
            如果您有一個 URL：http://www.mydomain.com/pictures/city/strasbourg.png。 據以查看輸入值 "" 及其存取層級

            1. 輸入值 "/"：將允許所有的要求
            2. 輸入值 "/pictures"：將允許下列的所有要求
            
                - http://www.mydomain.com/pictures.png
                - http://www.mydomain.com/pictures/city/strasbourg.png
                - http://www.mydomain.com/picturesnew/city/strasbourgh.png
            3. 輸入值 "/pictures/"：只允許 /pictures/ 的要求
            4. 輸入值 "/ pictures/city/strasbourg.png"：只允許此資產的要求
    
        ![[CDN 設定檔] 刀鋒視窗的 [管理] 按鈕](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
    
        - ec-country-allow︰只允許來自一或多個指定國家/地區的要求。 來自其他所有國家/地區的要求將會遭到拒絕。 使用國碼/地區碼設定參數，並以逗號分隔每個國碼/地區碼。 例如，如果您想要允許來自美國和法國的存取，請在資料行中輸入 US, FR，如下所示。  
        
        ![[CDN 設定檔] 刀鋒視窗的 [管理] 按鈕](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

        - ec-country-deny︰拒絕來自一或多個指定國家/地區的要求。 將允許來自其他所有國家/地區的要求。 使用國碼/地區碼設定參數，並以逗號分隔每個國碼/地區碼。 例如，如果您想要拒絕來自美國和法國的存取，請在資料行中輸入 US, FR。
    
        - ec-ref-allow︰只允許來自指定訪客來源的要求。 訪客來源會識別連結到所要求之資源的網頁 URL。 訪客來源參數值不應該包含通訊協定。 您可以輸入主機名稱和 (或) 該主機名稱上的特定路徑。 您也可以在單一參數內加入多個訪客來源，以逗號區隔每個訪客來源。 如果您指定了訪客來源值，但由於某些瀏覽器設定導致要求中不會傳送訪客來源資訊，預設將會拒絕這些要求。 您可以在參數中指派 "Missing" 或空白值，以允許這些要求遺漏訪客來源資訊。 您也可以使用 "*.consoto.com" 來允許 consoto.com 的所有子網域。  例如，如果您想要允許存取來自 www.consoto.com、consoto2.com 下的所有子網域的要求，以及具有空白或遺漏訪客來源的要求，請輸入下列值：
        
        ![[CDN 設定檔] 刀鋒視窗的 [管理] 按鈕](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
        - ec-ref-deny︰拒絕來自指定訪客來源的要求。 請參閱 "ec-ref-allow" 參數中的詳細資料和範例。
         
        - ec-proto-allow︰只允許來自指定通訊協定的要求。 例如，http 或 https。
        
        ![[CDN 設定檔] 刀鋒視窗的 [管理] 按鈕](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
            
        - ec-proto-deny︰拒絕來自指定通訊協定的要求。 例如，http 或 https。
    
        - ec-clientip︰限制存取指定要求者的 IP 位址。 支援 IPV4 和 IPV6。 您可以指定單一要求 IP 位址或 IP 子網路。
            
        ![[CDN 設定檔] 刀鋒視窗的 [管理] 按鈕](./media/cdn-token-auth/cdn-token-auth-clientip.png)
        
    3. 您可以使用描述工具測試您的權杖。

    4. 您也可以自訂當要求遭到拒絕時要傳回給使用者的回應類型。 根據預設，我們會使用 403。

3. 現在按一下 [HTTP 大型] 下方的 [規則引擎] 索引標籤。 您將使用此索引標籤來定義路徑，以套用功能、啟用權杖驗證功能，以及啟用其他與權杖驗證相關的功能。

    - 使用 "IF" 資料行，來定義您想要套用權杖驗證的資產或路徑。 
    - 從功能下拉式清單中按一下以新增「權杖驗證」，來啟用權杖驗證。
        
    ![[CDN 設定檔] 刀鋒視窗的 [管理] 按鈕](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. 在 [規則引擎] 索引標籤上，有數個您可以啟用的其他功能。
    
    - 權杖驗證拒絕代碼：判斷在要求遭到拒絕時將傳回給使用者的回應類型。 此處設定的規則將會覆寫權杖驗證索引標籤中的拒絕程式碼設定。
    - 權杖驗證忽略︰判斷用來驗證權杖的 URL 是否將會區分大小寫。
    - 權杖驗證參數︰重新命名所要求 URL 中所顯示的權杖驗證查詢字串參數。 
        
    ![[CDN 設定檔] 刀鋒視窗的 [管理] 按鈕](./media/cdn-token-auth/cdn-rules-engine2.png)

5. 您可以自訂權杖，這是可針對權杖型驗證功能產生權杖的應用程式。 原始程式碼可以在 [GitHub (英文)](https://github.com/VerizonDigital/ectoken) 上存取。
可用的語言包括：
    
    - C
    - C#
    - PHP
    - Perl
    - Java
    - Python    


## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN 功能和提供者定價

請參閱 [CDN 概觀](cdn-overview.md)主題。
