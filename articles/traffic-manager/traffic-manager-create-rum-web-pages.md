---
title: "使用網頁將實際使用者度量傳送給 Azure 流量管理員 | Microsoft Docs"
description: "設定您的網頁將實際使用者度量傳送給流量管理員"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: ce5d333ad5d339b64d1fc1d081651052f71a8b67
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>如何使用網頁將實際使用者度量傳送給 Azure 流量管理員

>[!NOTE]
>「流量管理員」中的「實際使用者度量」功能目前為「公開預覽版」，可能沒有與正式發行版本功能相同層級的可用性和可靠性。 不支援該功能、可能已經限制功能，以及可能無法在所有 Azure 位置提供使用。 如需此功能可用性和狀態的最新通知，請查看 [Azure 流量管理員](https://azure.microsoft.com/updates/?product=traffic-manager)分頁。

您可以設定網頁將實際使用者度量傳送給流量管理員，方法是取得實際使用者度量 (RUM) 金鑰，然後將產生的程式碼內嵌至網頁。

## <a name="obtain-a-real-user-measurements-key"></a>取得實際使用者度量金鑰

您從用戶端應用程式取得並且傳送至流量管理員的度量，是由使用獨特字串的服務來識別，該字串稱為**實際使用者度量 (RUM) 金鑰**。 您可以使用 Azure 入口網站、REST API 或使用 PowerShell 或 Azure CLI，來取得 RUM 金鑰。

若要使用 Azure 入口網站取得 RUM 金鑰：
1. 從瀏覽器登入 Azure 入口網站。 如果您沒有帳戶，可以註冊免費試用一個月。
2. 在入口網站的搜尋列中，搜尋您想要修改的流量管理員設定檔名稱，然後按一下結果中顯示的流量管理員設定檔。
3. 在流量管理員設定檔刀鋒視窗上，按一下 [設定] 底下的 [實際使用者度量]。
4. 按一下 [產生金鑰] 以建立新的 RUM 金鑰。
 
  ![產生實際使用者度量金鑰](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **圖 1：實際使用者度量金鑰產生**

5. 刀鋒視窗會顯示產生的 RUM 金鑰，以及必須內嵌至 HTML 分頁的 JavaScript 程式碼片段。
 
    ![實際使用者度量金鑰的 Javascript 程式碼](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **圖 2：實際使用者度量金鑰和度量 JavaScript**
 
6.  按一下 [複製] 按鈕以複製 JavaScript 程式碼。 

>[!IMPORTANT]
> 針對「實際使用者度量」功能使用產生的 JavaScript，讓功能正常運作。 對這個指令碼或實際使用者度量使用的指令碼所做的任何變更，都可能會導致無法預期的行為。

## <a name="embed-the-code-to-an-html-web-page"></a>將程式碼內嵌至 HTML 網頁

取得 RUM 金鑰之後，下一個步驟是將這個複製的 JavaScript 內嵌至您的使用者造訪的 HTML 網頁。 編輯 HTML 可以透過許多方式，以及使用不同的工具和工作流程來完成。 這個範例示範如何更新 HTML 網頁以新增此指令碼。 您可以使用本指南，將其調整為您的 HTML 來源管理工作流程。

1.  在文字編輯器中開啟 HTML 網頁
2.  將您在稍早步驟中複製的 JavaScript 程式碼貼至 HTML 的本文區段 (複製的程式碼在第 8 行和第 9 行，請參閱圖 3)。
 
    ![將 Javascript 程式碼內嵌至實際使用者度量](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)  

    **圖 3：具有內嵌實際使用者度量 JavaScript 的簡單 HTML**

3.  儲存 HTML 檔案
4. 下一次此網頁在網頁瀏覽器上呈現時，會下載 JavaScript 參考，且指令碼會執行測量並報告作業。


## <a name="next-steps"></a>後續步驟
- 深入了解[實際使用者度量](traffic-manager-rum-overview.md)
- 了解 [流量管理員的運作方式](traffic-manager-overview.md)
- 深入了解流量管理員支援的 [流量路由方法](traffic-manager-routing-methods.md)
- 了解如何 [建立流量管理員設定檔](traffic-manager-create-profile.md)

