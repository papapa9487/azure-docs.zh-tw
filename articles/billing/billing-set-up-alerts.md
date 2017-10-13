---
title: "為 Azure 訂用帳戶設定計費或信用額度警示 | Microsoft Docs"
description: "描述如何設定您的 Azure 帳單上的警示，以避免計費出現意外的狀況。"
keywords: "信用額度警示, 計費警示"
services: 
documentationcenter: 
author: vikdesai
manager: tonguyen
editor: 
tags: billing
ms.assetid: 9b7b3eeb-cd9d-4690-86a3-51b1e2a8974f
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/9/2017
ms.author: vikdesai
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1fc0cb2b036e835450ee0fc404cce12439fabc77
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-billing-or-credit-alerts-for-your-microsoft-azure-subscriptions"></a>為您的 Microsoft Azure 訂用帳戶設定計費或信用額度警示
如果您是 Azure 訂用帳戶的帳戶管理員，您可以使用「Azure 計費警示服務」來建立自訂計費警示，以協助您監視和管理您 Azure 帳戶的計費活動。

此服務為預覽狀態，因此您必須先在 [預覽功能] 頁面中加以啟用。

## <a name="set-the-alert-threshold-and-email-recipients"></a>設定警示閾值與電子郵件收件者
1. 請瀏覽[預覽功能頁面](https://account.windowsazure.com/PreviewFeatures)並啟用**計費警示服務**。

1. 在收到已為您的訂用帳戶開啟計費服務的電子郵件確認之後，請瀏覽帳戶入口網站中的 [訂用帳戶頁面](https://account.windowsazure.com/Subscriptions) 。 按一下您想要監視的訂用帳戶，然後按一下警示 。

    ![Azure 帳戶中心的訂用帳戶檢視螢幕擷取畫面，已醒目提示警示][Image1]

2. 接著，按一下 [新增警示] 來建立您的第一個警示。 每一訂用帳戶總計可以設定 5 個具有不同臨界值的警示，每個警示最多可以設定兩個電子郵件收件者。

    ![可供新增警示的警示檢視螢幕擷取畫面][Image2]

3. 新增警示時，您將為它提供一個唯一的名稱、選擇消費臨界值，以及選擇要接收警示的電子郵件地址。 設定臨界值時，您可以從 [警示對象] 清單中選擇 [計費總計] 或 [貨幣信用額度]。 如果是計費總計，當訂用帳戶消費超過臨界值時，就會傳送警示。 如果是貨幣信用額度，當貨幣信用額度低於限制時，就會傳送警示。 貨幣信用額度通常適用於免費試用和 Visual Studio 的訂用帳戶。

    ![可供設定收件者的警示新增檢視螢幕擷取畫面][Image3]

Azure 支援任何電子郵件地址，但不會驗證電子郵件地址是否有效，所以請仔細檢查是否有錯字。

## <a name="check-on-your-alerts"></a>檢查您的通知
設定警示之後，[帳戶中心] 會列出這些警示，並顯示您還可以設定多少個警示。 針對每個警示，您會看到其傳送日期和時間 (不論是 [計費總計] 還是 [貨幣信用額度] 警示)，以及您所設定的限制。 日期和時間格式是 24 小時制的世界標準時間 (UTC)，而日期為 yyyy-mm-dd 格式。 您可以按一下清單中某個警示的加號來編輯該警示，或按一下資源回收筒圖示來將它刪除。

## <a name="billing-alerts-for-enterprise-agreement-ea-customers"></a>Enterprise 合約 (EA) 客戶適用的計費警示
透過設定消費配額，EA 客戶可以取得註冊底下每個部門的警示。 請參閱 EA 入口網站中的[部門消費配額](https://ea.azure.com/helpdocs/departmentSpendingQuotas)以便開始使用。

## <a name="learn-more-about-azure-cost-management"></a>深入了解 Azure 成本管理
- 使用[價格計算機](https://azure.microsoft.com/pricing/calculator/)、[擁有權總成本計算機](https://aka.ms/azure-tco-calculator)，以及新增服務的時間預估成本。
- [定期在 Azure 入口網站檢閱您的使用量和成本](billing-getting-started.md#costs)。
- 開啟 [Azure Advisor 成本建議](../advisor/advisor-cost-recommendations.md)。

若要深入了解，請參閱 [Azure 成本管理指導](billing-getting-started.md)。

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png 
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png 
