---
title: "使用合作夥伴將 Widevine 授權傳遞到 Azure 媒體服務 | Microsoft Docs"
description: "本文說明如何使用 Azure 媒體服務 (AMS) 來傳遞 AMS 使用 PlayReady 與 Widevine DRM 動態加密的資料流。 PlayReady 授權來自媒體服務 PlayReady 授權伺服器，Widevine 授權由 castLabs 授權伺服器傳遞。"
services: media-services
documentationcenter: 
author: Juliako
manager: SyntaxC4
editor: 
ms.assetid: 5bcad5a4-c0bb-4871-9cce-808a913c53e6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: b4211cef3b4f3ffa2c0c97fd7650606f3eef7008
ms.contentlocale: zh-tw
ms.lasthandoff: 11/17/2016

---
# <a name="using-partners-to-deliver-widevine-licenses-to-azure-media-services"></a>使用合作夥伴將 Widevine 授權傳遞到 Azure 媒體服務
## <a name="overview"></a>Overview
Microsoft Azure 媒體服務可讓您提供受 Widevine DRM 保護的 MPEG-DASH，其會依據「一般加密 (Common Encryption，CENC)」規格進行加密。

從媒體服務 .NET SDK 版本 3.5.2 開始，媒體服務讓您可設定 Widevine 授權範本並取得 Widevine 授權。 您也可以使用下列 AMS 合作夥伴來協助您傳遞 Widevine 授權：[Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/)、[EZDRM](http://ezdrm.com/)、[castLabs](http://castlabs.com/company/partners/azure/)。

## <a name="castlabs"></a>castLabs
您可以使用 [castLabs](http://castlabs.com/company/partners/azure/) 傳遞 Widevine 授權。 如需詳細資訊，請參閱 [使用 castLabs 將 DRM 授權傳遞到 Azure 媒體服務](media-services-castlabs-integration.md)

## <a name="axinom"></a>Axinom
您可以使用 [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) 傳遞 Widevine 授權。 如需詳細資訊，請參閱 [使用 Axinom 將 DRM 授權傳遞到 Azure 媒體服務](media-services-axinom-integration.md)

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>另請參閱
[使用 PlayReady 和/或 Widevine 動態一般加密](media-services-protect-with-drm.md)

[Mingfei 的部落格](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)


