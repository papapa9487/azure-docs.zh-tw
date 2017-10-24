---
title: "在 Azure 入口網站中暫停並啟動您的 Power BI Embedded 容量 | Microsoft Docs"
description: "本文將逐步解說如何在 Microsoft Azure 中暫停並啟動 Power BI Embedded 容量。"
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
ms.openlocfilehash: ab5d184fde2cbcb517b325624c8405954b4d0972
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="pause-and-start-your-power-bi-embedded-capacity-in-the-azure-portal"></a>在 Azure 入口網站中暫停並啟動您的 Power BI Embedded 容量

本文將逐步解說如何在 Microsoft Azure 中暫停並啟動 Power BI Embedded 容量。 這會假設您已建立 Power BI Embedded 容量。 如果您尚未建立，請參閱[在 Azure 入口網站中建立 Power BI Embedded 容量](create-capacity.md)來開始使用。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/) 。

## <a name="pause-your-capacity"></a>暫停您的容量

暫停您的容量可防止您支付費用。 如果您有一段時間不需要使用容量，暫停容量會很棒。 您可以使用下列步驟來暫停容量。

> [!NOTE]
> 暫停容量可以避免 Power BI 中的內容受到使用。 暫停以避免中斷之前，請務必從您的容量中取消指派工作區。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取 [更多服務] > [Power BI Embedded] 以查看您的容量。

    ![Azure 入口網站內的 [更多服務]](media/pause-start/azure-portal-more-services.png)

3. 選取您需要暫停的容量。

    ![Azure 入口網站內的 Power BI Embedded 容量清單](media/pause-start/azure-portal-capacity-list.png)

4. 選取容量詳細資料內的 [暫停]。

    ![暫停您的容量](media/pause-start/azure-portal-pause-capacity.png)

5. 選取 [是]，以確認您需要暫停容量。

    ![確認暫停](media/pause-start/azure-portal-confirm-pause.png)

## <a name="start-your-capacity"></a>啟動您的容量

透過啟動容量來繼續使用。 啟動您的容量時，也會繼續計費。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取 [更多服務] > [Power BI Embedded] 以查看您的容量。

    ![Azure 入口網站內的 [更多服務]](media/pause-start/azure-portal-more-services.png)

3. 選取您需要啟動的容量。

    ![Azure 入口網站內的 Power BI Embedded 容量清單](media/pause-start/azure-portal-capacity-list.png)

4. 選取容量詳細資料內的 [啟動]。

    ![啟動您的容量](media/pause-start/azure-portal-start-capacity.png)

5. 選取 [是]，以確認您需要啟動容量。

    ![確認啟動](media/pause-start/azure-portal-confirm-start.png)

如果將任何內容指派給這個容量，就可在啟動後提供使用。

## <a name="next-steps"></a>後續步驟

如果您需要將您的容量相應增加或相應減少，請參閱[縮放您的 Power BI Embedded 容量](scale-capacity.md)。

若要在您的應用程式內開始內嵌 Power BI 內容，請參閱[如何內嵌 Power BI 儀表板、報告和圖格](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/)。

有其他疑問？ [嘗試在 Power BI 社群中提問](http://community.powerbi.com/)