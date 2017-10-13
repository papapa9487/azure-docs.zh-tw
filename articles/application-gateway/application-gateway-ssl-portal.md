---
title: "設定 SSL 卸載 - Azure 應用程式閘道 - Azure 入口網站 | Microsoft Docs"
description: "本文提供的指示，說明如何使用 Azure 入口網站來建立具有 SSL 卸載的應用程式閘道"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 8373379a-a26a-45d2-aa62-dd282298eff3
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 2f7f5d4132e28c8c192d90d5f4bfb2a9034f8b8c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-azure-portal"></a>使用 Azure 入口網站設定適用於 SSL 卸載的應用程式閘道

> [!div class="op_single_selector"]
> * [Azure 入口網站](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Azure 傳統 PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Azure 應用程式閘道可以設定為在閘道終止安全通訊端層 (SSL) 工作階段，以避免 Web 伺服陣列發生高成本的 SSL 解密工作。 SSL 卸載也可以簡化 Web 應用程式的前端伺服器設定和管理。

## <a name="scenario"></a>案例

下列情節會完成在現有應用程式閘道上設定 SSL 卸載的程序。 此情節假設您已經依照步驟[建立應用程式閘道](application-gateway-create-gateway-portal.md)。

## <a name="before-you-begin"></a>開始之前

若要設定與應用程式閘道搭配運作的 SSL 卸載，必須要有憑證。 系統會在應用程式閘道上載入此憑證，然後用來對透過 SSL 傳送的流量進行加密和解密。 此憑證必須採用「個人資訊交換」(.pfx) 格式。 此檔案格式可允許將私密金鑰匯出，而應用程式閘道需要這個匯出的金鑰來執行流量的加密和解密。

## <a name="add-an-https-listener"></a>新增 HTTPS 接聽程式

HTTPS 接聽程式會以其組態作為基礎來尋找流量，並協助將流量路由傳送到後端集區。 若要新增 HTTPS 接聽程式，請遵循下列步驟：

   1. 瀏覽至 Azure 入口網站，然後選取現有的應用程式閘道。

   2. 選取 [接聽程式]，然後選取 [新增] 按鈕來新增接聽程式。

   ![[應用程式閘道概觀] 窗格][1]


   3. 填入下列必要的接聽程式資訊，然後上傳 .pfx 憑證：
      - **名稱**：接聽程式的易記名稱。

      - **前端 IP 組態**：用於接聽程式的前端 IP 組態。

      - **前端連接埠 (名稱/連接埠)**：應用程式閘道前端上所使用之連接埠的易記名稱，以及實際使用的連接埠。

      - **通訊協定**：用以判斷前端使用的是 HTTPS 還是 HTTP 的參數。

      - **憑證 (名稱/密碼)**：如果使用 SSL 卸載，此設定就需要 .pfx 憑證。 易記名稱和密碼也是必要項目。

   4. 選取 [確定] 。

![[新增接聽程式] 窗格][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>建立規則並將它與接聽程式建立關聯

現在已建立接聽程式。 接下來，建立規則來處理來自接聽程式的流量。 規則會以多個組態設定作為基礎，定義流量路由傳送到後端集區的方式。 這些設定包括通訊協定、連接埠和健康情況探查，以及是否使用以 cookie 作為基礎的工作階段親和性。 若要建立規則並加以關聯至接聽程式，請遵循下列步驟：


   1. 選取應用程式閘道的 [規則]，然後選取 [新增]。

   ![[應用程式閘道規則] 窗格][3]


   2. 在 [新增基本規則] 下的 [名稱]欄位中輸入規則的易記名稱，然後選取上一個步驟中建立的 [接聽程式]。 選取適當的**後端集區**和 **HTTP 設定**，然後選取 [確定]。

   ![[HTTPS 設定] 視窗][4]

這些設定現在已儲存至應用程式閘道。 這些設定可能需要一些時間進行儲存，然後才能透過入口網站或 PowerShell 檢視。 儲存之後，應用程式閘道就會處理流量的加密和解密。 應用程式閘道與後端 Web 伺服器之間的所有流量，都會透過 HTTP 進行處理。 任何回到用戶端的通訊，只要是透過 HTTPS 起始，就會以加密的方式傳回用戶端。

## <a name="next-steps"></a>後續步驟

若要了解如何設定與「Azure 應用程式閘道」搭配運作的自訂健康狀態探查，請參閱 [建立自訂健康狀態探查](application-gateway-create-gateway-portal.md)。

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png

