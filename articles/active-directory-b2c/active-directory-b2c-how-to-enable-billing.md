---
title: "如何將 Azure 訂用帳戶連結到 Azure AD B2C | Microsoft Docs"
description: "啟用 Azure 訂用帳戶內 Azure AD B2C 租用戶計費的逐步指南。"
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mbaldwin
ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: joroja
ms.openlocfilehash: 5b9955b2af7f20a79981315fa33a0eb5380a5465
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="linking-an-azure-subscription-to-an-azure-b2c-tenant-to-pay-for-usage-charges"></a>將 Azure 訂用帳戶連結至 Azure B2C 租用戶以支付使用費用

Azure Active Directory B2C (或 Azure AD B2C) 的持續使用費用會計入 Azure 訂用帳戶。 建立 B2C 租用戶本身之後，租用戶系統管理員必須明確地將 Azure AD B2C 租用戶連結至 Azure 訂用帳戶。  在目標 Azure 訂用帳戶中建立 Azure AD「B2C 租用戶」資源，即可達成此連結。 許多 B2C 租用戶可以連結至單一 Azure 訂用帳戶以及其他 Azure 資源 (例如，VM、資料儲存體、LogicApps)


> [!IMPORTANT]
> B2C 的最新使用計費和價格資訊位於下列網頁︰[Azure AD B2C 價格](
https://azure.microsoft.com/pricing/details/active-directory-b2c/)

## <a name="step-1---create-an-azure-ad-b2c-tenant"></a>步驟 1 - 建立 Azure AD B2C 租用戶
必須先完成 B2C 租用戶建立。 如果您已經建立目標 B2C 租用戶，請略過此步驟。 [開始使用 Azure AD B2C](active-directory-b2c-get-started.md)

## <a name="step-2---open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>步驟 2 - 在 Azure AD 租用戶中開啟 Azure 入口網站，以顯示您的 Azure 訂用帳戶
瀏覽至 [Azure 入口網站](https://portal.azure.com)。 切換至 Azure AD 租用戶，以顯示您想要使用的 Azure 訂用帳戶。 此 Azure AD 租用戶與 B2C 租用戶不同。 在 Azure 入口網站中，按一下儀表板右上角的帳戶名稱以選取 Azure AD 租用戶。 需要有 Azure 訂用帳戶才能繼續執行。 [取得 Azure 訂用帳戶](https://account.windowsazure.com/signup?showCatalog=True)

![切換至 Azure AD 租用戶](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="step-3---create-a-b2c-tenant-resource-in-azure-marketplace"></a>步驟 3 - 在 Azure Marketplace 中建立 B2C 租用戶資源
按一下 [Marketplace] 圖示開啟 [Marketplace]，或選取儀表板的左上角的綠色 "+"。  搜尋並選取 Azure Active Directory B2C。 選取 [建立]。

![選取 Marketplace](./media/active-directory-b2c-how-to-enable-billing/marketplace.png)

![搜尋 AD B2C](./media/active-directory-b2c-how-to-enable-billing/searchb2c.png)

[Azure AD B2C 資源建立] 對話方塊包含下列參數︰

1. Azure AD B2C 租用戶 – 從下拉式清單選取 Azure AD B2C 租用戶。  只會顯示合格的 Azure AD B2C 租用戶。  合格的 B2C 租用戶符合以下條件︰您是 B2C 租用戶的全域管理員，而且 B2C 租用戶目前並未與 Azure 訂用帳戶相關聯

2. Azure AD B2C 資源名稱 - 已預先選，以符合 B2C 租用戶的網域名稱

3. 訂用帳戶 - 您身為系統管理員或共同管理員的作用中 Azure 訂用帳戶。  可以將多個 Azure AD B2C 租用戶新增至一個 Azure 訂用帳戶

4. 資源群組和資源群組位置 - 此構件可協助您安排多個 Azure 資源。  這個選項對於 B2C 租用戶位置、效能或計費狀態沒有任何影響

5. 釘選到儀表板，即可輕鬆存取您的 B2C 租用戶帳單資訊和 B2C 租用戶設定![建立 B2C 資源](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="step-4---manage-your-b2c-tenant-resources-optional"></a>步驟 4 - 管理 B2C 租用戶資源 (選擇性)
部署完成後，新的「B2C 租用戶」資源便建立於目標資源群組和相關 Azure 訂用帳戶中。  您應該會看到您其他的 Azure 資源旁邊新增「B2C 租用戶」類型的新資源。

![建立 B2C 資源](./media/active-directory-b2c-how-to-enable-billing/b2cresourcedashboard.png)

按一下 B2C 租用戶資源，您就能夠
- 按一下訂用帳戶名稱，以檢閱帳單資訊。 請參閱「計費和使用量」。
- 按一下 [Azure AD B2C 設定]，以在 [B2C 租用戶設定] 刀鋒視窗中直接開啟新的瀏覽器索引標籤
- 提交支援要求
- 將 B2C 租用戶資源移至另一個 Azure 訂用帳戶，或另一個資源群組。  這個選項會變更哪一個 Azure 訂用帳戶會收到使用費用。

![B2C 資源設定](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>已知問題
- B2C 租用戶刪除。 如果在建立「B2C 租用戶」之後，將其刪除再以相同的網域名稱重新建立，請將「連結」資源也一併刪除再以相同的網域名稱重新建立。  您可以透過 Azure 入口網站，在訂用帳戶租用戶中的 [所有資源] 底下找到這個「連結」資源。
- 在區域資源位置自我強加的限制。  在罕見的情況下，使用者可能針對建立 Azure 資源建立了區域限制。  此限制可能會導致無法在 Azure 訂用帳戶與「B2C 租用戶」之間建立「連結」。 若要減輕此問題的影響，請放寬這項限制。

## <a name="next-steps"></a>後續步驟
針對每個 B2C 租用戶完成這些步驟後，您的 Azure 訂用帳戶就會根據 Azure 直接或企業合約詳細資料計費。
- 檢閱您選取的 Azure 訂用帳戶內的使用量和計費
- 使用[使用量報告 API](active-directory-b2c-reference-usage-reporting-api.md) 來檢閱詳細的每天使用量報告
