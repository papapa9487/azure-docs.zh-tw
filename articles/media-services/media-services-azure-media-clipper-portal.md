---
title: "在入口網站中使用 Azure Media Clipper | Microsoft Docs"
description: "從 Azure 入口網站使用 Azure Media Clipper 建立剪輯"
services: media-services
keywords: "clip;subclip;encoding;media;剪輯;子剪輯;編碼;媒體"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: faaae7edbc2fb62ae219dd963f405e7246c982d4
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>在入口網站使用 Azure Media Clipper 建立剪輯
您可以在入口網站使用 Azure Media Clipper，從媒體服務帳戶中的資產建立剪輯。 若要開始，請在入口網站中瀏覽至您的媒體服務帳戶。 接下來，選取 [子剪輯] 索引標籤。

在 [子剪輯] 索引標籤上，您可以開始撰寫剪輯。 Clipper 會在入口網站中載入單一位元速率 mp4、多位元速率 mp4，以及使用有效串流定位器發佈的即時封存。 不會載入未發佈的資產。

## <a name="producing-clips"></a>產生剪輯
若要建立剪輯，將資產拖放到剪輯介面。 如果已知標記時間，可以手動將它們輸入至介面。 否則，播放資產或拖曳播放點來尋找所需的開始標記和結束標記時間。 如果未提供開始標記和結束標記時間，剪輯分別會從頭開始或繼續到輸入資產結尾。

若要以畫面精確度/GOP 精確度瀏覽，請使用畫面向前/GOP 向前或畫面向後/GOP 向後按鈕。 針對多個資產剪輯時，請從資產選取面板將多個資產拖放到剪輯介面。 您可以在介面中選取並且將資產重新排列為所需的順序。 資產選取面板提供每個資產的資產持續時間、類型和解析度中繼資料。 將多個資產串連在一起時，請考慮每個輸入檔案的來源解析度。 如果來源解析度不同，較低解析度的輸入會被放大以符合最高解析度資產的解析度。 若要預覽剪輯作業的輸出，請選取預覽按鈕，剪輯就會從選取的標記時間開始播放。

## <a name="producing-dynamic-manifest-filters"></a>產生動態資訊清單篩選
[動態資訊清單篩選](https://azure.microsoft.com/blog/dynamic-manifest/)描述一組規則，該規則是根據資訊清單屬性和資產時間軸。 這些規則會決定您的串流端點如何操作輸出播放清單 (資訊清單)。 篩選條件可以用於變更哪些區段要串流播放。 Clipper 所產生的篩選條件是本機篩選條件，並且專屬於來源資產。 與轉譯的剪輯不同的是，篩選條件不是新的資產，不需要產生編碼作業。 您可以透過 [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) 或 [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest) 快速建立，不過，它們只有 GOP 精確度。 一般而言，針對串流編碼的資產有兩秒的 GOP 大小。

若要建立動態資訊清單篩選條件，從進階設定功能表選取動態資訊清單篩選條件作為剪輯模式。 您可以遵循相同的程序，以產生剪輯來建立篩選條件。 篩選條件只能針對單一資產產生。

## <a name="submitting-clipping-jobs"></a>提交剪輯作業
當您完成撰寫剪輯時，選取提交作業按鈕以起始對應的剪輯作業或動態資訊清單呼叫。