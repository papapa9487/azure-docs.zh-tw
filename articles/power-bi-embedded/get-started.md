---
title: "開始使用 Microsoft Power BI Embedded | Microsoft Docs"
description: "將 Power BI Embedded 加入至您的商業智慧應用程式"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 4787cf44-5d1c-4bc3-b3fd-bf396e5c1176
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: b32b06e9d6cbaacdfbdfe92e2c72cb6763c9eb52
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-microsoft-power-bi-embedded"></a>開始使用 Microsoft Power BI Embedded

**Power BI Embedded** 讓獨立軟體廠商 (ISV) 和開發人員有辦法透過以容量為基礎、依使用時數計量收費的模型，快速地在其應用程式中新增絢麗的視覺效果、報告和儀表板。

![內嵌流程圖](media/get-started/introduction.png)

Power BI Embedded 可讓 ISV、其開發人員及客戶獲得好處。 例如，ISV 可以開始使用 Power BI Desktop 免費地建立視覺效果。 ISV 可以藉由大幅減少視覺分析開發工作來縮短上市時間，並透過差異化的資料體驗從眾多競爭對手之間脫穎而出。 ISV 也可以選擇付費使用進階版，以獲得內嵌分析所創造的額外價值。

開發人員可以將其時間著重運用在為其應用程式打造核心競爭力，而非將時間浪費在開發視覺效果和分析功能。 開發人員可以快速地滿足客戶對於報告和儀表板的需求，並可透過有完整記載的 API 和 SDK 來輕鬆地內嵌。 最後，藉由讓客戶可在其應用程式內輕鬆地瀏覽資料，ISV 可讓其客戶根據情況快速做出以資料為準的決策，並可放心地從任何裝置執行操作。

## <a name="register-an-application-within-azure-active-directory"></a>在 Azure Active Directory 內註冊應用程式

Azure Active Directory (AAD) 內必須有已註冊的應用程式，才能在自訂應用程式中進行內嵌。 已註冊的應用程式會要求您的租用戶必須是 Power BI 租用戶。 Power BI 租用戶代表組織中至少有一位使用者已註冊 Power BI。 讓使用者註冊 Power BI 可讓 Power BI API 出現在已註冊的應用程式中。

如需如何在 AAD 中註冊應用程式的詳細資訊，請參閱[註冊 Azure AD 應用程式以便內嵌 Power BI 內容](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/)。

## <a name="embed-content-in-your-application"></a>在應用程式中內嵌內容

在 AAD 中註冊應用程式之後，請將 Power BI 內容內嵌在應用程式內。 請使用 REST API 以及 JavaScript API 來內嵌內容。

我們有提供範例來協助您快速入門。 如需範例的逐步解說，請參閱[將儀表板、圖格或報告整合到應用程式](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/)。

## <a name="get-capacity-and-move-to-production"></a>取得容量並移至生產環境

請在 Microsoft Azure 內建立 Power BI Embedded 容量，以將應用程式移至生產環境。 如需如何建立容量的相關資訊，請參閱[在 Azure 入口網站中建立 Power BI Embedded 容量](create-capacity.md)。

在 Power BI 管理入口網站中管理您的容量。 指派工作區指派者來協助管理應用程式工作區。 如需詳細資訊，請參閱[管理 Power BI Premium 和 Power BI Embedded 內的容量](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/)。

## <a name="next-steps"></a>後續步驟

如果您已準備好建立 Power BI Embedded 容量，請參閱[在 Azure 入口網站中建立 Power BI Embedded 容量](create-capacity.md)。

如果您需要範例的逐步解說，請參閱[將儀表板、圖格或報告整合到應用程式](https://powerbi.microsoft.com/documentation/powerbi-developer-embed-sample-app-owns-data/)。

有其他疑問？ [試用 Power BI 社群](http://community.powerbi.com/)