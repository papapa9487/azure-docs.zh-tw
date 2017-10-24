---
title: "使用 CSP 合作夥伴資訊向 Azure 成本管理進行註冊 | Microsoft Docs"
description: "使用您的 CSP 合作夥伴資訊向 Cloudyn 的 Azure 成本管理進行註冊。"
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 09/25/2017
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: a7bd4aed7dbcde803c2106b8c053e8a2412eea99
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>註冊 CSP 合作夥伴計劃並檢視成本資料

身為 CSP 合作夥伴，您可以向 Cloudyn 的 Azure 成本管理進行註冊。 註冊將可提供 Cloudyn 入口網站的存取權。 本快速入門詳細說明建立 Cloudyn 試用訂用帳戶並登入 Cloudyn 入口網站所需的註冊程序。 它也會示範如何立即開始檢視成本資料。

若要完成註冊，您必須是具有合作夥伴中心 API 存取權的合作夥伴計劃管理員。 驗證和資料存取需要合作夥伴中心 API 的設定。 如需詳細資訊，請參閱連線到合作夥伴中心 API。

## <a name="log-in-to-azure"></a>登入 Azure

- 登入 Azure 入口網站，網址是 http://portal.azure.com/。

## <a name="create-a-trial-registration"></a>建立試用註冊

1. 在 Azure 入口網站中，按一下服務清單中的 [成本管理與帳單]。
2. 在 [概觀] 底下，按一下 [成本管理]  
    ![成本管理頁面](./media/quick-register-csp/cost-mgt-billing-service.png)
3. 在 [成本管理] 頁面上，按一下 [移至成本管理] 以在新視窗開啟 Cloudyn 註冊頁面。
4. 在 Cloudyn 入口網站試用註冊頁面上，輸入您的公司名稱，選取 Microsoft CSP 合作夥伴計劃管理員，然後按一下下一步。  
5. 輸入 [應用程式識別碼]、[商務識別碼]、[應用程式祕密金鑰 ]，並選取 [預設定價方案]。 如果您無法立即取得該資訊，請使用您的主要系統管理員帳戶從 [https://partnercenter.microsoft.com](https://partnercenter.microsoft.com) 登入合作夥伴中心入口網站，並執行下列步驟：
  1. 移至 [儀表板] > [帳戶設定] > [應用程式管理]。
  2. 如果您曾經建立過 Web 應用程式，請略過此步驟。 否則，請按一下 [Web 應用程式] 區段中的 [新增 Web 應用程式]。
  3. 從您的 Web 應用程式複製 [應用程式識別碼] GUID。
  4. 從您的 Web 應用程式複製 [商務識別碼] GUID。
  5. 視需要選取一年或兩年的金鑰有效期限。 選取 [新增金鑰]，然後複製並儲存祕密金鑰的值。  
    ![CSP 合作夥伴中心](./media/quick-register-csp/csp-partner-center.png)
  6. 返回註冊頁面並貼上該資訊。  
      ![CSP 帳戶認證](./media/quick-register-csp/csp-reg.png)
6. 同意「使用條款」，然後驗證您的資訊。 按一下 [下一步] 以授權 Cloudyn 收集 Azure 資源資料。 收集的資料包括您訂用帳戶的使用量、效能、計費和標籤資料。  
7. 在 [邀請其他利害關係人] 下，您可以輸入電子郵件地址來新增使用者。 完成時，按一下 [下一步]。 您的所有帳單資料需要大約兩個小時才能新增到 Cloudyn。
8. 按一下 [移至 Cloudyn] 以開啟 Cloudyn 入口網站，然後就會在 [雲端帳戶管理] 頁面上看見已註冊的 CSP 帳戶資訊。

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>後續步驟

在此快速入門中，您使用 CSP 訂用帳戶資訊向「成本管理」進行註冊。 您也登入了 Cloudyn 入口網站，並開始檢視成本資料。 若要深入了解 Cloudyn 的 Azure 成本管理，請繼續進行「成本管理」的教學課程。

> [!div class="nextstepaction"]
> [檢視成本資料](./tutorial-review-usage.md)
