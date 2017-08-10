---
title: "藉由新增編碼單元來調整媒體處理的規模 - Azure | Microsoft Docs"
description: "了解如何使用 .NET 新增編碼單元"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;milangada;
ms.translationtype: Human Translation
ms.sourcegitcommit: bdf41edfa6260749a91bc52ec0a2b62fcae99fb0
ms.openlocfilehash: ee40b92c48d91a391fe72582c7ea5e244907747f
ms.contentlocale: zh-tw
ms.lasthandoff: 01/27/2017

---
# <a name="how-to-scale-encoding-with-net-sdk"></a>如何使用 .NET SDK 調整編碼
> [!div class="op_single_selector"]
> * [入口網站](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Overview
> [!IMPORTANT]
> 請務必檢閱 [概觀](media-services-scale-media-processing-overview.md) 主題，以取得調整媒體處理主題的詳細資訊。
> 
> 

若要使用 .NET SDK　變更保留單元類型以及編碼保留單元數目，請執行下列動作：

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>建立支援票證
依預設，每一個媒體服務帳戶可調整為最多 25 個編碼保留單元和 5 個隨選串流保留單元。 您可以建立支援票證來要求更高的限制。

### <a name="open-a-support-ticket"></a>開啟支援票證
若要建立支援票證，請執行下列動作：

1. 按一下 [取得支援](https://manage.windowsazure.com/?getsupport=true)。 如果您未登入，系統會提示您輸入認證。
2. 選取您的訂用帳戶。
3. 在支援類型下，選取 [技術]。
4. 按一下 [建立票證]。
5. 在下一頁顯示的產品清單中，選取 [Azure 媒體服務]。
6. 選取適合您的問題的 [問題類型]。
7. 按一下 [繼續]。
8. 遵循下一頁的指示，然後輸入問題的詳細資訊。
9. 按一下 [提交] 來建立票證。

## <a name="media-services-learning-paths"></a>媒體服務學習路徑
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供意見反應
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


