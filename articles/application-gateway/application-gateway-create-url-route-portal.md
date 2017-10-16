---
title: "為應用程式閘道建立路徑型規則 - Azure 入口網站 | Microsoft Docs"
description: "了解如何使用 Azure 入口網站為應用程式閘道建立路徑型規則。"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 87bd93bc-e1a6-45db-a226-555948f1feb7
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: davidmu
ms.openlocfilehash: b207e7e7bd83e56db68288190c7bedafa8b5b7fa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-path-based-rule-for-an-application-gateway-by-using-the-azure-portal"></a>使用 Azure 入口網站為應用程式閘道建立路徑型規則

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

使用 URL 路徑型路由，您可以根據 HTTP 要求的 URL 路徑來關聯路由。 它會檢查是否有路由連至針對「應用程式閘道」中的 URL 清單設定的後端伺服器集區，並將網路流量傳送至定義的集區。 URL 路徑型路由的常見用法是將不同內容類型的要求負載平衡至不同的後端伺服器集區。

應用程式閘道有兩種規則類型：基本型規則和 URL 路徑型規則。 基本規則類型會為後端集區提供循環配置資源服務。 路徑型規則，除了循環配置資源發佈以外，還可以在選擇適當的後端集區時使用要求 URL 的路徑模式。

## <a name="scenario"></a>案例

下列案例會在現有應用程式閘道中建立路徑型規則。
此情節假設您已經依照[使用入口網站建立應用程式閘道](application-gateway-create-gateway-portal.md)中的步驟。

![URL 路由][scenario]

## <a name="createrule"></a>建立路徑型規則

路徑型規則需要它自己的接聽程式。 建立規則之前，務必確認您擁有可用的接聽程式。

### <a name="step-1"></a>步驟 1

移至 [Azure 入口網站](http://portal.azure.com)，然後選取現有的應用程式閘道。 按一下 [規則]。

![應用程式閘道概觀][1]

### <a name="step-2"></a>步驟 2

按一下 [路徑型] 按鈕，以新增路徑型規則。

### <a name="step-3"></a>步驟 3

[新增路徑型規則]  刀鋒視窗有兩個區段。 您會在第一個區段中，定義接聽程式、規則的名稱和預設路徑設定。 預設路徑設定適用於未落在自訂路徑型路由之下的路由。 您會在 [新增路徑型規則]  刀鋒視窗的第二個區段中，定義路徑型規則本身。

**基本設定**

* **名稱**：入口網站中可存取規則的易記名稱。
* **接聽程式**：用於規則的接聽程式。
* **預設後端集區**：要用於預設規則的後端。
* **預設 HTTP 設定**：要用於預設規則的 HTTP 設定。

**路徑型規則設定**

* **名稱**：路徑型規則的易記名稱。
* **路徑**：規則在轉送流量時尋找的路徑。
* **後端集區**：要用於規則的後端。
* **HTTP 設定**：要用於規則的 HTTP 設定。

> [!IMPORTANT]
> **路徑**設定是要比對的路徑模式清單。 每個模式必須以正斜線開始，結尾只允許使用星號。 有效範例：/xyz、/xyz*，以及 /xyz/*。  

![已填入資訊的新增路徑型規則刀鋒視窗][2]

將路徑型規則新增至現有應用程式閘道是在 Azure 入口網站中進行的簡單程序。 建立路徑型規則之後，您可以加以編輯以加入其他規則。 

![新增其他路徑型規則][3]

此步驟會設定路徑型路由。 請務必了解系統不會重寫要求。 應用程式閘道會在收到要求時檢查要求，並根據 URL 模式將要求傳送到適當的後端集區。

## <a name="next-steps"></a>後續步驟

若要了解如何使用「Azure 應用程式閘道」設定「SSL 卸載」，請參閱[使用 Azure 入口網站設定適用於 SSL 卸載的應用程式閘道](application-gateway-ssl-portal.md)。

[1]: ./media/application-gateway-create-url-route-portal/figure1.png
[2]: ./media/application-gateway-create-url-route-portal/figure2.png
[3]: ./media/application-gateway-create-url-route-portal/figure3.png
[scenario]: ./media/application-gateway-create-url-route-portal/scenario.png
