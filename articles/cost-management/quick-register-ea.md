---
title: "使用 Azure 成本管理註冊 Azure Enterprise 合約 | Microsoft Docs"
description: "使用您的 Enterprise 合約註冊 Cloudyn 的 Azure 成本管理。"
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: 41a9df712b07253d9f5f9db8542fb9917592320f
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2017
---
# <a name="register-an-azure-enterprise-agreement-and-view-cost-data"></a>註冊 Azure Enterprise 合約並檢視成本資料

使用您的 Azure Enterprise 合約註冊 Cloudyn 的 Azure 成本管理。 您的註冊提供 Cloudyn 入口網站的存取權。 本快速入門詳細說明建立 Cloudyn 試用訂用帳戶並登入 Cloudyn 入口網站所需的註冊程序。 它也會示範如何立即開始檢視成本資料。

## <a name="log-in-to-azure"></a>登入 Azure

- 登入 Azure 入口網站，網址是 http://portal.azure.com/。

## <a name="create-a-trial-registration"></a>建立試用註冊

1. 在 Azure 入口網站中，按一下服務清單中的 [成本管理與帳單]。
2. 在 [概觀] 底下，按一下 [成本管理]  
    ![成本管理頁面](./media/quick-register-ea/cost-mgt-billing-service.png)
3. 在 [成本管理] 頁面上，[移至成本管理] 可在新視窗開啟 Cloudyn 註冊頁面。
4. 在 Cloudyn 入口網站試用版註冊頁面上，輸入您的公司名稱，然後選取 [Azure 企業註冊系統管理員]。  
    ![試用版註冊](./media/quick-register-ea/trial-reg.png)
5. 輸入您的企業版入口網站註冊 API 金鑰。 如果您手邊沒有金鑰，請按一下[企業版入口網站](https://ea.azure.com)連結，並執行下列步驟：
  1. 登入 Azure 企業版網站，然後依序按一下 [報告]和 [API 存取金鑰]，然後複製您的主索引鍵。  
    ![EA API 金鑰](./media/quick-register-ea/ea-key.png)
  3. 返回註冊頁面，並貼上您的 API 金鑰。
6. 同意「使用條款」，然後驗證您的金鑰。 按一下 [下一步] 以授權 Cloudyn 收集 Azure 資源資料。 收集的資料包括您訂用帳戶的使用量、效能、計費和標籤資料。  
    ![金鑰驗證](./media/quick-register-ea/ea-key-validated.png)
7. 在 [邀請其他專案關係人] 下，您可以輸入他們的電子郵件地址以新增使用者。 完成時，按一下 [下一步]。 您的所有帳單資料需要大約兩個小時才能新增至 Cloudyn。
8. 按一下 [移至 Cloudyn] 開啟 Cloudyn 入口網站，然後您應該會在 [雲端帳戶管理] 頁面上看見您已註冊的 EA 帳戶資訊。

若要觀看註冊 Enterprise 合約的教學課程影片，請參閱[如何尋找您的 EA 註冊識別碼和 API 金鑰，以用於 Cloudyn 的 Azure 成本管理](https://youtu.be/u_phLs_udig)。

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>後續步驟

在此快速入門中，您使用您的 Azure Enterprise 合約資訊來註冊「成本管理」。 您也登入了 Cloudyn 入口網站並開始檢視成本資料。 若要深入了解 Cloudyn 的 Azure 成本管理，請繼續進行「成本管理」的教學課程。

> [!div class="nextstepaction"]
> [檢閱使用量和成本](./tutorial-review-usage.md)
