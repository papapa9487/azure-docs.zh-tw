---
title: "利用 Azure 成本管理預測花費 | Microsoft Docs"
description: "使用歷程記錄的使用方式及花費資料來預測花費。"
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 09/29/2017
ms.topic: article
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: decae695544e8b9e0ff48cca32a38f533a86a563
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="forecast-future-spending"></a>預測未來的花費

Cloudyn 提供的 Azure 成本管理使用歷程記錄的使用方式及花費資料，協助您預測未來的花費。 您可以使用 Cloudyn 報告來檢視所有成本預估的資料。 本教學課程中的範例會引導您使用報告來檢閱成本預估。 在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 預測未來的花費

## <a name="forecast-future-spending"></a>預測未來的花費

Cloudyn 包含成本預估報告，可協助您以一段時間的使用量作為基礎來預測花費。 其主要目的是協助您確保成本趨勢不會超過組織的期望。 您使用的報告是「當月預估成本」和「年度預估成本」。 如果您的使用方式維持在與過去 30 天內的使用方式一致，兩個報告就都會顯示預估的未來花費。

「當月預估成本」報告會顯示您服務的成本。 它會使用月份開頭和前一個月的成本來顯示預估的成本。 在入口網站頂端的 [報告] 功能表上，按一下 [成本] > [預估和預算] > [當月預估成本]。 下圖說明一個範例。

![當月預估成本](./media/tutorial-forecast-spending/project-month01.png)

在範例中，您可以查看哪些服務的花費最大。 Azure 的成本低於 AWS 成本。 如果您需要查看 Azure VM 的成本預估詳細資料，請在 [篩選] 清單中選取 [Azure/VM]。

![Azure VM 當月預估成本](./media/tutorial-forecast-spending/project-month02.png)

請遵循相同的基本上述步驟，看看您感興趣的其他服務每月成本預估。

「年度預估成本」報告會顯示您的服務接下來 12 個月的推斷成本。

在入口網站頂端的 [報告] 功能表上，按一下 [成本] > [預估和預算] > [年度預估成本]。 下圖說明一個範例。

![年度預估成本報告](./media/tutorial-forecast-spending/project-annual01.png)

在範例中，您可以查看哪些服務的花費最大。 如同每月範例，Azure 成本低於 AWS 成本。 如果您需要查看 Azure VM 的成本預估詳細資料，請在 [篩選] 清單中選取 [Azure/VM]。

![VM 的年度預估成本](./media/tutorial-forecast-spending/project-annual02.png)

在上圖中，Azure VM 的年度預估成本為 $28,374。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 預測未來的花費


前往 Cloudyn 文件，深入了解如何開始使用 Cloudyn 及使用其功能。

> [!div class="nextstepaction"]
> [Cloudyn 文件](https://support.cloudyn.com/hc/)
