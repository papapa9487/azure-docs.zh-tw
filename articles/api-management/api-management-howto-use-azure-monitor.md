---
title: "在 Azure API 管理中監視發佈的原則 | Microsoft Docs"
description: "依照此教學課程的步驟，了解如何在 Azure API 管理中監視您的 API。"
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: bdca9d4968e9e68314f350787907f15e417821f7
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2017
---
# <a name="monitor-published-apis"></a>監視發佈的 API

Azure 監視器是一項 Azure 服務，可提供單一來源來讓您監視所有 Azure 資源。 您可以使用 Azure 監視器來視覺化、查詢、路由、封存及針對來自 Azure 資源 (例如 API 管理) 的計量和記錄採取行動。 

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 檢視活動記錄檔
> * 檢視診斷記錄
> * 檢視 API 的計量 
> * 在 API 收到未經授權的呼叫時，設定警示規則

下列影片示範如何使用 Azure 監視器來監視 API 管理。 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>

## <a name="prerequisites"></a>必要條件

+ 完成下列快速入門：[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。
+ 以及完成下列教學課程：[匯入和發佈您的第一個 API](import-and-publish.md)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="diagnostic-logs"></a>檢視活動記錄

活動記錄可讓您深入了解 API 管理服務上所執行的作業。 您可以使用活動記錄來判斷 API 管理服務上所執行之任何寫入作業 (PUT、POST、DELETE) 的「內容、對象和時間」。 

> [!NOTE]
> 活動記錄不會納入讀取 (GET) 作業，也不會納入透過傳統發行者入口網站或原始管理 API 所執行的作業。

您可以在 API 管理服務中存取活動記錄，或在 Azure 監視器中存取所有 Azure 資源的記錄。 

檢視活動記錄：

1. 從您的 **API 管理**執行個體中，按一下 [活動記錄]。

## <a name="view-diagnostic-logs"></a>檢視診斷記錄

診斷記錄可提供豐富的作業與錯誤資訊，這些資訊對於稽核和疑難排解用途來說很重要。 診斷記錄與活動記錄不同。 活動記錄可讓您深入了解 Azure 資源上所執行的作業。 診斷記錄能讓您了解資源自行執行的作業。

存取診斷記錄：

1. 從您的 **API 管理**執行個體中，按一下 [診斷記錄]。

## <a name="view-metrics-of-your-apis"></a>檢視 API 的計量

API 管理會每分鐘發出計量，讓您近乎即時地了解 API 的狀態和健康情況。 以下是一些可用計量的摘要：

* 容量 (預覽)：協助您決定是否升級/降級 APIM 服務。 計量每分鐘發出，並反映提出報告時的閘道容量。 計量的範圍為 0 到 100，是根據 CPU 和記憶體使用率等閘道資源計算而來。
* 閘道要求總數︰該期間內的 API 要求數目。 
* 成功的閘道要求︰收到 HTTP 成功回應碼的 API 要求數目，這些回應碼包括 304、307 和任何小於 301 的代碼 (例如 200)。 
* 失敗的閘道要求︰收到 HTTP 錯誤回應碼的 API 要求數目，這些回應碼包括 400 和任何大於 500 的代碼。
* 未經授權閘道器要求︰收到 401、403 和 429 HTTP 回應碼的 API 要求數目。 
* 其他閘道要求︰所收到的 HTTP 回應碼不屬於上述任何類別 (例如 418) 的 API 要求數目。

存取計量：

1. 從靠近頁面底部的功能表中，選取 [計量]。
2. 從下拉式清單中，選取您想了解的計量 (您可以新增多個度量)。 
    
    例如，從可用的度量清單中選取 [閘道要求總數] 和 [失敗的閘道要求]。
3. 該圖表會顯示 API 呼叫的總數。 也會顯示失敗的 API 呼叫數目。 

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>針對未經授權的要求設定警示規則

您可以進行設定來收到以計量和活動記錄為基礎的警示。 Azure 監視器可讓您將警示設定為在觸發時執行下列動作︰

* 傳送電子郵件通知
* 呼叫 Webhook
* 叫用 Azure 邏輯應用程式

設定警示：

1. 從靠近頁面底部的功能表列中，選取 [警示規則]。
2. 選取 [新增計量警示]。
3. 輸入此警示的**名稱**。
4. 選取 [未經授權的閘道要求] 作為要監視的計量。
5. 選取**電子郵件擁有者、參與者和讀取者**。
6. 按 [確定]。
7. 嘗試呼叫沒有 API 金鑰的會議 API。 身為此 API 管理服務的擁有者，您會收到電子郵件警示。 

    > [!TIP]
    > 警示規則也可以在觸發時呼叫 Web Hook 或 Azure Logic App。

    ![set-up-alert](./media/api-management-azure-monitor/set-up-alert.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 檢視活動記錄檔
> * 檢視診斷記錄
> * 檢視 API 的計量 
> * 在 API 收到未經授權的呼叫時，設定警示規則

前進到下一個教學課程：

> [!div class="nextstepaction"]
> [追蹤呼叫](api-management-howto-api-inspector.md)