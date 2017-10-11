---
title: "使用 Azure 入口網站設定內容保護原則 | Microsoft Docs"
description: "本文章示範如何使用 Azure 入口網站設定內容保護原則。 文章也會說明如何啟用資產的動態加密。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 270b3272-7411-40a9-ad42-5acdbba31154
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: 67b3fa9936daebeafb7e87fe3a7b0c7e0105b3b3
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="configuring-content-protection-policies-using-the-azure-portal"></a>使用 Azure 入口網站設定內容保護原則
> [!NOTE]
> 若要完成此教學課程，您需要 Azure 帳戶。 如需詳細資訊，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
> 
> 

## <a name="overview"></a>Overview
Microsoft Azure 媒體服務 (AMS) 可讓您保護媒體從離開電腦到進行儲存、處理和傳遞時的安全。 媒體服務可讓您傳遞利用進階加密標準 (AES) (使用 128 位元加密金鑰) 動態加密、使用 PlayReady 和/或 Widevine DRM 的一般加密 (CENC) 和 Apple FairPlay 的內容。 

AMS 提供一種服務，將 DRM 授權和 AES 純文字金鑰傳遞給授權用戶端。 Azure 入口網站可讓您針對所有加密類型建立一個 **金鑰/授權的授權原則** 。

本文章示範如何使用 Azure 入口網站設定內容保護原則。 文章也會說明如何將動態加密套用到資產。


> [!NOTE]
> 如果您是使用 Azure 傳統入口網站建立保護原則，原則可能不會出現在 [Azure 入口網站](https://portal.azure.com/)。 不過，所有舊的原則仍然存在。 您可以使用 Azure 媒體服務 .NET SDK 或 [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer/releases) 工具來檢查這些原則 (若要參閱原則，請以滑鼠右鍵按一下 [資產] -> [顯示資訊] \(F4) -> 按一下 [內容金鑰] 索引標籤 -> 按一下金鑰)。 
> 
> 如果您想要使用新的原則加密資產，請使用 Azure 入口網站設定它們、按一下 [儲存]，然後重新套用動態加密。 
> 
> 

## <a name="start-configuring-content-protection"></a>開始設定內容保護
若要使用入口網站開始設定內容保護，對 AMS 帳戶設為全域，執行下列動作：

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您的 Azure 媒體服務帳戶。
2. 選取 [設定] > [內容保護]。

![保護內容](./media/media-services-portal-content-protection/media-services-content-protection001.png)

## <a name="keylicense-authorization-policy"></a>金鑰/授權的授權原則
AMS 支援多種方式來驗證提出金鑰或授權要求的使用者。 內容金鑰授權原則必須由您設定，而且您的用戶端必須符合條件，才能將金鑰/授權傳遞給用戶端。 內容金鑰授權原則可能會有一個或多個授權限制：**open** 或 **token** 限制。

Azure 入口網站可讓您針對所有加密類型建立一個 **金鑰/授權的授權原則** 。

### <a name="open"></a>開啟
Open 限制表示系統將會傳送金鑰給提出金鑰要求的任何人。 這項限制可用於測試用途。 

### <a name="token"></a>token
token 限制原則必須伴隨著安全權杖服務 (STS) 所發出的權杖。 媒體服務支援簡單 Web 權杖 (SWT) 格式和 JSON Web 權杖 (JWT) 格式的權杖。 媒體服務不提供安全權杖服務。 您可以建立自訂 STS，或利用 Microsoft Azure ACS 來發行權杖。 STS 必須設定為建立使用指定的索引鍵和問題宣告您在權杖限制組態中指定簽署的權杖。 如果權杖有效，且權杖中的宣告符合針對金鑰 (或授權) 所設定的宣告，媒體服務金鑰傳遞服務會將所要求的金鑰 (或授權) 傳回給用戶端。

設定 token 限制原則時，您必須指定主要驗證金鑰、簽發者和對象參數。 主要驗證金鑰包含簽署權杖使用的金鑰，簽發者是發行權杖的安全權杖服務。 對象 (有時稱為範圍) 描述權杖或權杖獲授權存取之資源的用途。 媒體服務金鑰傳遞服務會驗證權杖中的這些值符合在範本中的值。

![保護內容](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## <a name="playready-rights-template"></a>PlayReady 權限範本
如需 PlayReady 權限範本的詳細資訊，請參閱 [媒體服務 PlayReady 授權範本概觀](media-services-playready-license-template-overview.md)。

### <a name="non-persistent"></a>非持續性
如果您將授權設定為非持續性，則當播放程式使用授權時，授權只會保留在記憶體中。  

![保護內容](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### <a name="persistent"></a>持續性
如果您將授權設定為持續性，它會儲存在用戶端上的永續性儲存體中。

![保護內容](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## <a name="widevine-rights-template"></a>Widevine 權限範本
如需 Widevine 權限範本的詳細資訊，請參閱 [Widevine 授權範本概觀](media-services-widevine-license-template-overview.md)。

### <a name="basic"></a>基本
當您選取 [基本] 時，會使用所有預設值建立範本。

### <a name="advanced"></a>進階
如需有關 Widevine 組態進階選項的詳細說明，請參閱 [這個](media-services-widevine-license-template-overview.md) 主題。

![保護內容](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## <a name="fairplay-configuration"></a>FairPlay 組態
若要啟用 FairPlay 加密，您必須透過 FairPlay 組態選項提供應用程式憑證和應用程式秘密金鑰 (ASK)。 如需 FairPlay 組態和需求的詳細資訊，請參閱 [這個](media-services-protect-hls-with-fairplay.md) 文章。

![保護內容](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## <a name="apply-dynamic-encryption-to-your-asset"></a>將動態加密套用到您的資產
若要利用動態封裝功能，您必須將您的來源檔案編碼成一組調適性位元速率 MP4 檔案。

### <a name="select-an-asset-that-you-want-to-encrypt"></a>選取您要加密的資產
若要查看您所有的資產，請選取 [設定] > [資產]。

![保護內容](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### <a name="encrypt-with-aes-or-drm"></a>使用 AES 或 DRM 加密
一旦您在資產上按下 [加密] 後，會看到兩個選擇︰[AES] 或 [DRM]。 

#### <a name="aes"></a>AES
AES 清除金鑰加密將會在所有串流通訊協定上啟用︰Smooth Streaming、HLS 和 MPEG DASH。

![保護內容](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### <a name="drm"></a>DRM
當您選取 DRM 索引標籤時，會看到不同內容保護原則的選擇 (您現在必須已設定) + 一組串流通訊協定。

* **PlayReady 和 Widevine 與 MPEG-DASH** - 會使用 PlayReady 與 Widevine DRM 動態加密您的 MPEG DASH 串流。
* **PlayReady 和 Widevine 與 MPEG-DASH + FairPlay 與 HLS** - 會使用 PlayReady 與 Widevine DRM 動態加密您的 MPEG DASH 串流。 並且會使用 FairPlay 加密您的 HLS 串流。
* **PlayReady 僅與 Smooth Streaming、HLS 和 MPEG-DASH** - 會使用 PlayReady DRM 動態加密 Smooth Streaming、HLS、MPEG-DASH 串流。
* **Widevine 僅與 MPEG-DASH** - 會使用 Widevine DRM 動態加密您的 MPEG-DASH。
* **FairPlay 僅與 HLS** - 會使用 FairPlay 動態加密您的 HLS 串流。

若要啟用 FairPlay 加密，您必須透過內容保護設定刀鋒視窗的 FairPlay 組態選項提供應用程式憑證和應用程式秘密金鑰 (ASK)。

![保護內容](./media/media-services-portal-content-protection/media-services-content-protection009.png)

一旦您進行加密選取後，按下 [套用] 。

>[!NOTE] 
>如果您想要在 Safari 中播放 AES 加密的 HLS，請參閱[這篇部落格](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)。

## <a name="next-steps"></a>後續步驟
檢閱媒體服務學習路徑。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

