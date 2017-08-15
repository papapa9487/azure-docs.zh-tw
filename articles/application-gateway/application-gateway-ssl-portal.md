---
title: "設定 SSL 卸載 - Azure 應用程式閘道 - Azure 入口網站 | Microsoft Docs"
description: "本頁面提供使用入口網站來建立具有 SSL 卸載之應用程式閘道的指示"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 8373379a-a26a-45d2-aa62-dd282298eff3
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: f61be0cc4c9274c9914f7c468ce48a2a3d0a4f4a
ms.contentlocale: zh-tw
ms.lasthandoff: 08/04/2017

---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-portal"></a>使用入口網站設定適用於 SSL 卸載的應用程式閘道

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Azure 傳統 PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Azure 應用程式閘道可以設定為在閘道終止安全通訊端層 (SSL) 工作階段，以避免 Web 伺服陣列發生高成本的 SSL 解密工作。 SSL 卸載也可以簡化 Web 應用程式的前端伺服器設定和管理。

## <a name="scenario"></a>案例

下列案例會完成在現有應用程式閘道上設定 SSL 卸載的程序。 此案例假設您已經依照步驟 [建立應用程式閘道](application-gateway-create-gateway-portal.md)。

## <a name="before-you-begin"></a>開始之前

若要設定與應用程式閘道搭配運作的 SSL 卸載，必須要有憑證。 系統會在應用程式閘道上載入此憑證，然後用來對透過 SSL 傳送的流量進行加密和解密。 此憑證必須採用「個人資訊交換」(pfx) 格式。 此檔案格式可允許將私密金鑰匯出，而應用程式閘道需要這個匯出的金鑰來執行流量的加密和解密。

## <a name="add-an-https-listener"></a>新增 HTTPS 接聽程式

HTTPS 接聽程式會根據其組態來尋找流量，並協助將流量路由傳送到後端集區。

### <a name="step-1"></a>步驟 1

瀏覽到 Azure 入口網站，然後選取現有的應用程式閘道

### <a name="step-2"></a>步驟 2

按一下 [接聽程式]，然後按一下 [加入] 按鈕來加入接聽程式。

![[應用程式閘道概觀] 刀鋒視窗][1]

### <a name="step-3"></a>步驟 3

填入必要的接聽程式資訊，然後上傳 .pfx 憑證，完成時，按一下 [確定]。

**名稱** - 此值是接聽程式的易記名稱。

**前端 IP 組態** - 此值是用於接聽程式的前端 IP 組態。

**前端連接埠 (名稱/連接埠)** - 應用程式閘道前端上所使用之連接埠的易記名稱，以及實際使用的連接埠。

**通訊協定** - 用以判斷前端使用的是 https 還是 http 的參數。

**憑證 (名稱/密碼)** - 如果使用 SSL 卸載，此設定就需要 .pfx 憑證，並且需要易記名稱和密碼。

![[加入接聽程式] 刀鋒視窗][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>建立規則並將它與接聽程式建立關聯

現在已建立接聽程式。 此時即可建立規則來處理來自接聽程式的流量。 規則會定義如何根據多個組態設定將流量路由傳送到後端集區，包括是否使用以 Cookie 為基礎的工作階段同質性、通訊協定、連接埠和健康狀態探查。

### <a name="step-1"></a>步驟 1

按一下應用程式閘道的 [規則]  ，然後按一下 [新增]。

![[應用程式閘道規則] 刀鋒視窗][3]

### <a name="step-2"></a>步驟 2

在 [新增基本規則]  刀鋒視窗中，輸入規則的易記名稱，然後選擇在上一個步驟中建立的接聽程式。 選擇適當的後端集區和 http 設定，然後按一下 [確定] 

![[https 設定] 視窗][4]

這些設定現在已儲存至應用程式閘道。 這些設定可能需要一些時間進行儲存，然後才能透過入口網站或 PowerShell 檢視。 儲存之後，應用程式閘道就會處理流量的加密和解密。 應用程式閘道與後端 Web 伺服器之間的所有流量都會透過 http 進行處理。 任何回到用戶端的通訊只要是透過 https 起始，就會以加密的方式傳回用戶端。

## <a name="next-steps"></a>後續步驟

若要了解如何設定與「Azure 應用程式閘道」搭配運作的自訂健康狀態探查，請參閱 [建立自訂健康狀態探查](application-gateway-create-gateway-portal.md)。

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png

