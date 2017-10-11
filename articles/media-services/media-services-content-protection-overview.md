---
title: "使用 Azure 媒體服務保護您的內容 | Microsoft Docs"
description: "此文章簡介如何利用 Media Services 保護內容。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 81bc00e1-dcda-4d69-b9ab-8768b793422b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 64be4ea104bd11b8e191e2c8d4170a2de88acb47
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="protecting-content-overview"></a>保護內容概觀
Microsoft Azure 媒體服務可讓您保護媒體從離開電腦到進行儲存、處理和傳遞時的安全。 媒體服務可讓您傳遞利用進階加密標準 (AES) (使用 128 位元加密金鑰) 動態加密或任何主要 DRM：Microsoft PlayReady、Google Widevine 和 Apple FairPlay 的即時與隨選內容。 媒體服務也提供服務，可傳遞 AES 金鑰和 DRM (PlayReady、Widevine 和 FairPlay) 授權給授權用戶端。 

下列映像示範 AMS 支援的內容保護工作流程。 

![利用 PlayReady 保護](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

>[!NOTE]
>建立 AMS 帳戶時，**預設**串流端點會新增至 [已停止] 狀態的帳戶。 若要開始串流內容並利用動態封裝和動態加密功能，您想要串流內容的串流端點必須處於 [執行中] 狀態。 

本主題說明關於了解以 AMS 內容保護的 [概念與術語](media-services-content-protection-overview.md) 。 主題也包含說明如何達成內容保護工作的主題 [連結](media-services-content-protection-overview.md#common-scenarios) 。 

## <a name="dynamic-encryption"></a>動態加密
Microsoft Azure 媒體服務可讓您傳遞使用 AES 清除金鑰或 DRM 加密：Microsoft PlayReady、Google Widevine 和 Apple FairPlay 所動態加密的內容。

目前，您可以加密下列串流格式：HLS、MPEG DASH 和 Smooth Streaming。 您無法加密漸進式下載。

如果您想要媒體服務加密資產，則需要建立加密金鑰 (CommonEncryption 或 EnvelopeEncryption) 與資產的關聯，同時設定金鑰的授權原則。

您也需要設定資產的傳遞原則。 如果您想要串流處理儲存體加密的資產，請一定要透過設定資產傳遞原則來指定資產傳遞方式。

播放程式要求串流時，媒體服務便會使用 AES 清除金鑰或 DRM 加密，使用指定的金鑰動態加密您的內容。 為了將串流解密，播放程式將從金鑰傳遞服務要求金鑰。 為了決定使用者是否有權取得金鑰，服務會評估為金鑰指定的授權原則。


## <a name="storage-encryption"></a>儲存體加密
使用儲存體加密，使用 AES-256 位元加密對您的純文字內容進行本機加密，然後將其上傳到已靜止加密儲存的 Azure 儲存體。 使用儲存體加密保護的資產會在編碼之前自動解除加密並放在加密的檔案系統中，並且選擇性地在上傳為新的輸出資產之前重新加密。 儲存體加密的主要使用案例是當您想要使用強式加密保護靜止在磁碟上的高品質輸入媒體檔案時。

若要傳遞儲存體加密資產，您必須設定資產的傳遞原則，讓媒體服務知道您的內容傳遞方式。 串流處理資產之前，串流伺服器會移除儲存體加密，並使用指定的傳遞原則來串流處理您的內容 (例如，AES、一般加密或不加密)。

## <a name="common-encryption-cenc"></a>一般加密 (CENC)
當使用 PlayReady 或/及 Widewine 加密您的內容時會使用一般加密。

## <a name="using-cbcs-aapl-encryption"></a>使用 cbcs-aapl 加密
使用 FairPlay 加密您的內容時會使用 Cbcs-aapl。

## <a name="envelope-encryption"></a>信封加密
如果您想要使用 AES-128 清除金鑰來保護您的內容，請使用此選項。 如果需要更安全的選項，請選擇本主題中所列的其中一個 DRM。 

## <a name="licenses-and-keys-delivery-service"></a>授權和金鑰傳遞服務
媒體服務提供一種服務，可將 DRM (PlayReady、Widevine 與 FairPlay) 授權和 AES 清除金鑰傳遞給授權用戶端。 若要設定您授權和金鑰的授權和驗證原則，才能使用 [Azure 入口網站](media-services-portal-protect-content.md)、REST API 或 Media Services SDK for .NET。

## <a name="token-restriction"></a>權杖限制
內容金鑰授權原則可能會有一個或多個授權限制：open 或 token 限制。 權杖限制原則必須伴隨著安全權杖服務 (STS) 所發出的權杖。 媒體服務支援簡單 Web 權杖 (SWT) 格式和 JSON Web 權杖 (JWT) 格式的權杖。 媒體服務不提供安全權杖服務。 您可以建立自訂 STS，或利用 Microsoft Azure ACS 來發行權杖。 STS 必須設定為建立使用指定的索引鍵和問題宣告您在權杖限制組態中指定簽署的權杖。 如果權杖有效，且權杖中的宣告符合針對金鑰 (或授權) 所設定的宣告，媒體服務金鑰傳遞服務會將所要求的金鑰 (或授權) 傳回給用戶端。

設定 token 限制原則時，您必須指定主要驗證金鑰、簽發者和對象參數。 主要驗證金鑰包含簽署權杖使用的金鑰，簽發者是發行權杖的安全權杖服務。 對象 (有時稱為範圍) 描述權杖或權杖獲授權存取之資源的用途。 媒體服務金鑰傳遞服務會驗證權杖中的這些值符合在範本中的值。

## <a name="streaming-urls"></a>串流 URL
如果使用一個以上 DRM 來加密您的資產，您應該使用串流 URL 中的加密標籤：(format='m3u8-aapl', encryption='xxx')。

您必須考量下列事項：

* 僅可指定零個或一個加密類型。
* 如果只有一個加密套用到資產，則無須在 URL 中指定加密類型。
* 加密類型不區分大小寫。
* 可以指定下列加密類型︰  
  * **cenc**︰一般加密 (Playready 或 Widevine)
  * **cbcs-aapl**：Fairplay
  * **cbc**：AES 信封加密。

## <a name="common-scenarios"></a>常見案例
下列主題說明如何保護儲存體中的內容、提供動態加密串流處理媒體、使用 AMS 金鑰/授權傳遞服務

* [以 AES 保護](media-services-protect-with-aes128.md) 
* [以 PlayReady 及/或 Widevine 保護 ](media-services-protect-with-drm.md)
* [串流以 Apple FairPlay 及/或 PlayReady 保護的 HLS 內容](media-services-protect-hls-with-fairplay.md)

### <a name="additional-scenarios"></a>其他案例
* [如何整合 Azure PlayReady 授權服務與您自己的加密程式/串流伺服器](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server)。
* [使用 castLabs 將 DRM 授權傳遞到 Azure 媒體服務](media-services-castlabs-integration.md)

>[!NOTE]
>目前不支援使用外部 DRM 伺服器 (技術) 並從 AMS 進行串流處理的案例。


## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>相關連結
[利用 Azure 媒體服務宣告 PlayReady 是一個服務和 AES 動態加密](http://mingfeiy.com/playready)

[Azure 媒體服務 PlayReady 授權傳遞定價說明](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[如何偵錯 Azure 媒體服務的 AES 加密串流](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[JWT 權杖驗證](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[整合 Azure 媒體服務 OWIN MVC 型應用程式與 Azure Active Directory 並根據 JWT 宣告限制內容金鑰傳遞](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)。

[使用 Azure ACS 發行權杖](http://mingfeiy.com/acs-with-key-services)。

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png
