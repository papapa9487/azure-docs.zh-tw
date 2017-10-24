---
title: "縮放您的 Power BI Embedded 容量 | Microsoft Docs"
description: "本文將逐步解說如何在 Microsoft Azure 中縮放 Power BI Embedded 容量。"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: e1ab6a2f52fa56f1e04c6c327796587daf43596e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="scale-your-power-bi-embedded-capacity"></a>縮放您的 Power BI Embedded 容量

本文將逐步解說如何在 Microsoft Azure 中縮放 Power BI Embedded 容量。 縮放可讓您增加或減少容量的大小。

這會假設您已建立 Power BI Embedded 容量。 如果您尚未建立，請參閱[在 Azure 入口網站中建立 Power BI Embedded 容量](create-capacity.md)來開始使用。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="scale-a-capacity"></a>縮放容量

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取 [更多服務] > [Power BI Embedded] 以查看您的容量。

    ![Azure 入口網站內的 [更多服務]](media/scale-capacity/azure-portal-more-services.png)

3. 選取您想要縮放的容量。

    ![Azure 入口網站內的 Power BI Embedded 容量清單](media/scale-capacity/azure-portal-capacity-list.png)

4. 在您的容量內，選取 [縮放] 下方的 [定價層]。

    ![位於 [縮放] 下方的 [定價層] 選項](media/scale-capacity/azure-portal-scale-pricing-tier.png)

    您目前的定價層會加上藍色外框。

    ![加上藍色外框的目前定價層](media/scale-capacity/azure-portal-current-tier.png)

5. 若要調升或調降規模，請選取要移至其中的新階層。 選取新的階層，即會在選取項目周圍加上藍色的虛線外框。 選取 [選取] 擴充至新階層。

    ![選取新的階層](media/scale-capacity/azure-portal-select-new-tier.png)

    縮放您的容量可能需要一兩分鐘的時間才能完成。

6. 檢視 [概觀] 索引標籤，以確認您的階層。隨即會列出目前的定價層。

    ![確認目前的階層](media/scale-capacity/azure-portal-confirm-tier.png)

## <a name="next-steps"></a>後續步驟

若要暫停或啟動您的容量，請參閱[在 Azure 入口網站中暫停和啟動您的 Power BI Embedded 容量](pause-start.md)。

若要在您的應用程式內開始內嵌 Power BI 內容，請參閱[如何內嵌 Power BI 儀表板、報表和磚](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/)。

有其他疑問？ [嘗試在 Power BI 社群中提問](http://community.powerbi.com/) \(英文\)