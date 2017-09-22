---
title: "啟用或停用 Azure 內容傳遞網路自訂網域上的 HTTPS | Microsoft Docs"
description: "了解如何在具有自訂網域的 Azure CDN 端點上啟用或停用 HTTPS。"
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: casoper
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: c92f1e20acf55b8bd791fad43f17e162a5cb3847
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---
# <a name="enable-or-disable-https-on-an-azure-content-delivery-network-custom-domain"></a>啟用或停用 Azure 內容傳遞網路自訂網域上的 HTTPS

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Microsoft Azure 內容傳遞網路 (CDN) 自訂網域的 HTTPS 支援，可讓您使用自己的網域名稱透過 SSL 傳遞安全內容，以改善資料傳送的安全性。 啟用自訂網域 HTTPS 的端對端工作流程已簡化為一步啟用和完整憑證管理，而且不須額外費用。

確保您所有 Web 應用程式在傳輸資料時的隱私和資料完整性非常重要。 使用 HTTPS 通訊協定可確保您的機密資料在網際網路上傳遞時已經過加密。 它提供信任與認證，並保護您的 Web 應用程式免於攻擊。 目前，Azure CDN 支援 CDN 端點上的 HTTPS。 舉例來說，當您從 Azure CDN (如 https://contoso.azureedge.net) 建立端點時，預設會啟用 HTTPS。 現在透過自訂網域 HTTPS，您也可以針對自訂網域 (如 https://www.contoso.com) 啟用安全傳遞。 

HTTPS 功能的一些重要特色如下：

- 不須額外付費：取得或續約憑證不須付費，且 HTTPS 流量不另外收費。 您只需支付 CDN 的 GB 輸出。

- 容易啟用：從 [Azure 入口網站](https://portal.azure.com)按一下就能佈建。 您也可以使用 REST API 或其他開發工具來啟用此功能。

- 完整憑證管理：為您處理所有憑證採購及管理。 系統會自動佈建憑證並在到期前自動更新。 這樣可以完全避免因為憑證到期而導致的服務中斷風險。

>[!NOTE] 
>啟用 HTTPS 支援之前，您必須先建立 [Azure CDN 自訂網域](./cdn-map-content-to-custom-domain.md)。

## <a name="enabling-https"></a>啟用 HTTPS

若要啟用 HTTPS，請依照下列步驟執行：

### <a name="step-1-enable-the-feature"></a>步驟 1︰啟用功能 

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽到您的 Verizon 標準或進階 CDN 設定檔。

2. 在端點清單中，按一下包含自訂網域的端點。

3. 按一下您要啟用 HTTPS 的自訂網域。

    ![端點刀鋒視窗](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. 按一下 [開啟] 以啟用 HTTPS，然後按一下 [套用]。

    ![自訂 HTTPS 對話方塊](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


### <a name="step-2-validate-domain"></a>步驟 2：驗證網域

>[!IMPORTANT] 
>必須先完成網域驗證才能在您的自訂網域上啟用 HTTPS。 您有六個工作天可以核准網域。 未在六個工作天內核准的要求將會自動遭到取消。 

在您的自訂網域上啟用 HTTPS 之後，我們的 HTTPS 憑證提供者 DigiCert 會根據網域的 [WHOIS](http://whois.domaintools.com/) 註冊資訊連絡網域的註冊人，以驗證網域的所有權。 連絡方式為透過電子郵件地址 (預設) 或列示在 WHOIS 註冊資訊中的電話號碼。 

![WHOIS 記錄](./media/cdn-custom-ssl/whois-record.png)

此外，DigiCert 也會將驗證電子郵件傳送到下列地址。 如果 WHOIS 註冊者資訊為私用，請確認您可以直接從下列其中一個地址核准：

admin@&lt;your-domain-name.com&gt;  
administrator@&lt;your-domain-name.com&gt;  
webmaster@&lt;your-domain-name.com&gt;  
hostmaster@&lt;your-domain-name.com&gt;  
postmaster@&lt;your-domain-name.com&gt;  

您應該會在幾分鐘之內收到邀請您核准要求的電子郵件，如以下範例所示。 如果您使用垃圾郵件篩選器，請將 admin@digicert.com 加入白名單。 如果您未在 24 小時內收到驗證電子郵件，請連絡 Microsoft 支援服務。
    
![自訂 HTTPS 對話方塊](./media/cdn-custom-ssl/domain-validation-email.png)

當您按一下核准連結時，系統會將您導向以下線上核准表單： 
    
![自訂 HTTPS 對話方塊](./media/cdn-custom-ssl/domain-validation-form.png)

遵循表單上的指示；您有兩個驗證選項：

- 您可以核准未來所有經相同帳戶針對同一個根網域 (如 contoso.com) 的所有訂購。如果您計畫未來要針對同一個跟網域新增其他自訂網域，建議您選擇此方法。

- 您可以只核准這次要求使用的特定主機名稱。 後續的要求將需要另外核准。

核准之後，DigiCert 會將您的自訂網域名稱加入主題別名 (SAN) 憑證。 憑證有效期限為一年，並且會在到期之前自動更新。

### <a name="step-3-wait-for-propagation"></a>步驟 3：等待傳播

自訂網域 HTTPS 功能要在網域名稱通過驗證之後 6-8 小時才會啟用。 當流程完成時，Azure 入口網站中的「自訂 HTTPS」狀態將會設定為「已啟用」，而且 [自訂網域 HTTPS] 刀鋒視窗中的四個作業步驟會標示為完成。 您的自訂網域現在已準備好使用 HTTPS。

![[啟用 HTTPS] 對話方塊](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>作業進度

下表指出啟用 HTTPS 時的作業進度。 啟用 HTTPS 之後，四個作業步驟會出現在 [自訂網域 HTTPS] 刀鋒視窗中。 隨著每個步驟變成作用中狀態，其他詳細資料會在步驟進行時顯示於下方。 當步驟成功完成時，旁邊會出現綠色的核取記號。 

| 作業步驟 | 作業步驟詳細資料 | 
| --- | --- |
| 1 提交要求 | 提交要求 |
| | 正在提交您的 HTTPS 要求。 |
| | 已成功提交您的 HTTPS 要求。 |
| 2 網域驗證 | 我們已傳送電子郵件要求您驗證網域擁有權。 正在等候您的確認。 |
| | 已成功驗證您的網域擁有權。 |
| | 網域擁有權驗證要求已過期 (客戶可能未在 6 天內回應)。 您網域上的 HTTPS 將不會啟用。 * |
| | 客戶拒絕網域擁有權驗證要求。 您網域上的 HTTPS 將不會啟用。 * |
| 3 憑證佈建 | 憑證授權單位正在發行在網域上啟用 HTTPS 所需的憑證。 |
| | 憑證已發行，目前正在部署到 CDN 網路。 這可能需要 6 小時的時間。 |
| | 已成功將憑證部署到 CDN 網路。 |
| 4 完成 | 已成功在您的網域上啟用 HTTPS。 |

\* 除非發生錯誤，否則這則訊息將不會出現。 

如果錯誤發生於提交要求之前，您會看到以下錯誤訊息：

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="disabling-https"></a>停用 HTTPS

HTTPS 啟用後，您可以在日後將它停用。 若要停用 HTTPS，請依照下列步驟執行：

### <a name="step-1-disable-the-feature"></a>步驟 1︰停用功能 

1. 在 [Azure 入口網站](https://portal.azure.com)中，瀏覽到您的 Verizon 標準或進階 CDN 設定檔。

2. 在端點清單中，按一下包含自訂網域的端點。

3. 按一下您要停用 HTTPS 的自訂網域。

    ![端點刀鋒視窗](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. 按一下 [關閉] 以停用 HTTPS，然後按一下 [套用]。

    ![自訂 HTTPS 對話方塊](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="step-2-wait-for-propagation"></a>步驟 2：等待傳播

自訂網域 HTTPS 功能停用之後，可能需要 6-8 小時才會生效。 當流程完成時，Azure 入口網站中的「自訂 HTTPS」狀態將會設定為「已停用」，而且 [自訂網域 HTTPS] 刀鋒視窗中的三個作業步驟會標示為完成。 您的自訂網域無法再使用 HTTPS。

![[停用 HTTPS] 對話方塊](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

### <a name="operation-progress"></a>作業進度

下表指出停用 HTTPS 時的作業進度。 停用 HTTPS 之後，三個作業步驟會出現在 [自訂網域 HTTPS] 刀鋒視窗中。 隨著每個步驟變成作用中狀態，其他詳細資料會顯示在步驟下方。 當步驟成功完成時，旁邊會出現綠色的核取記號。 

| 作業進度 | 作業詳細資料 | 
| --- | --- |
| 1 提交要求 | 正在提交您的要求 |
| 2 憑證取消佈建 | 刪除憑證 |
| 3 完成 | 已憑證刪除 |

## <a name="frequently-asked-questions"></a>常見問題集

1. *憑證提供者是誰？使用的是哪一種憑證？*

    我們使用 DigiCert 提供的主題別名 (SAN) 憑證。 SAN 憑證可以使用一個憑證來保護多個完整網域名稱。

2. *是否可以使用自己的憑證？*
    
    目前不行，但此功能已列在藍圖中。

3. *如果沒有收到 DigiCert 的驗證電子郵件該怎麼辦？*

    如果您未在 24 小時內收到電子郵件，請連絡 Microsoft 支援服務。

4. *SAN 憑證的安全性是否比專用憑證來得低？*
    
    SAN 憑證遵循和專用憑證相同的加密與安全性標準。所有核發的 SSL 憑證都使用 SHA-256 來加強伺服器安全性。

5. *是否可以搭配 Akamai 的 Azure CDN 使用自訂網域 HTTPS？*

    此功能目前只提供給 Verizon 的 Azure CDN。 我們正在進行相關工作，讓此功能在未來的幾個月內支援 Akamai 的 Azure CDN。


## <a name="next-steps"></a>後續步驟

- 了解如何[在 Azure CDN 端點上設定自訂網域](./cdn-map-content-to-custom-domain.md)



