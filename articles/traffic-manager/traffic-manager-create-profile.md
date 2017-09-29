---
title: "在 Azure 中建立流量管理員設定檔 | Microsoft Docs"
description: "本文說明如何建立流量管理員設定檔"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: 8ad98f7ef226fa94b75a8fc6b2885e7f0870483c
ms.openlocfilehash: e9ff7947e7801a9f352a7a947b09893b8f615d88
ms.contentlocale: zh-tw
ms.lasthandoff: 09/29/2017

---

# <a name="create-a-traffic-manager-profile"></a>建立流量管理員設定檔

本文說明如何建立具有**優先順序**路由類型的設定檔，用以將使用者路由傳送至兩個 Azure Web Apps 端點。 使用**優先順序**路由類型時，所有流量會路由傳送至第一個端點，而第二個端點會保留做為備份。 因此，如果第一個端點變成狀況不良，就可將使用者路由傳送至第二個端點。

本文中，先前建立的兩個 Azure Web 應用程式端點會與這個新建立的流量管理員設定檔相關聯。 若要深入了解如何建立 Azure Web 應用程式端點，請瀏覽 [Azure Web Apps 文件頁面](https://docs.microsoft.com/azure/app-service/)。 您可以新增具有 DNS 名稱且可透過公用網際網路觸達的任何端點，我們使用 Azure Web Apps 端點做為例子。

### <a name="create-a-traffic-manager-profile"></a>建立流量管理員設定檔
1. 從瀏覽器登入 [Azure 入口網站](http://portal.azure.com)。 如果您沒有帳戶，您可以註冊[免費試用一個月](https://azure.microsoft.com/free/)。 
2. 在 [中樞] 功能表中，按一下 [新增] > [網路] > [查看全部]**，然後按一下 [流量管理員]****設定檔，以開啟 [建立流量管理員設定檔]** 刀鋒視窗。
3. 在 [建立流量管理員設定檔] 刀鋒視窗上，如下所示操作：
    1. 在 [名稱]中，提供設定檔的名稱。 此名稱在 trafficmanager.net 區域內必須是唯一的，而且會產生 DNS 名稱 <name>, trafficmanager.net，用以存取您的流量管理員設定檔。
    2. 在 [路由方法] 中，選取 [優先順序]路由方法。
    3. 在 [訂用帳戶] 中，選取您要用來建立此設定檔的訂用帳戶
    4. 在 [資源群組]中，建立新的資源群組來放置此設定檔。
    5. 在 [資源群組位置] 中，選取資源群組的位置。 這項設定是指資源群組的位置，完全不影響將部署到全球的流量管理員設定檔。
    6. 按一下 [建立] 。
    7. 當流量管理員設定檔的全球部署完成時，它會列為個別資源群組的其中一個資源。

    ![建立流量管理員設定檔](./media/traffic-manager-create-profile/Create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>新增流量管理員端點

1. 在入口網站的搜尋列中，搜尋您在上一節建立的**流量管理員設定檔**名稱，然後在顯示的結果中按一下流量管理員設定檔。
2. 在 [流量管理員設定檔] 刀鋒視窗中，請在 [設定] 區段中按一下 [端點]。
3. 在顯示的 [端點] 刀鋒視窗中，按一下 [新增]。
4. 在 [新增端點] 刀鋒視窗中，如下所示操作︰
    1. 在 [類型] 中，按一下 [Azure 端點]。
    2. 提供您要用來識別這個端點的 [名稱]。
    3. 在 [目標資源類型] 中，按一下 [App Service]。
    4. 在 [目標資源] 中，按一下 [選擇應用程式服務]，顯示相同訂用帳戶下的 Web Apps 清單。 在顯示的 [資源] 刀鋒視窗中，挑選您想要新增為第一個端點的應用程式服務。
    5. 在 [優先順序] 中，選取 [1]。 這會使得所有流量傳送至此端點 (如果狀況良好)。
    6. 維持不勾選 [新增為已停用]。
    7. 按一下 [檔案] &gt; [新增] &gt; [專案] 
5.  針對下一個 Azure Web Apps 端點，重複步驟 3 和 4。 新增它時務必將 [優先順序] 值設為 [2]。
6.  這兩個端點新增完畢後，它們會顯示在 [流量管理員設定檔] 刀鋒視窗中，而且監視狀態是 [線上]。

    ![新增流量管理員端點](./media/traffic-manager-create-profile/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>使用流量管理員設定檔
1.  在入口網站的搜尋列中，搜尋您在上一節建立的**流量管理員設定檔**名稱。 在顯示的結果中，按一下流量管理員設定檔。
2. 在 [流量管理員設定檔] 刀鋒視窗中，按一下 [概觀]。
3. [流量管理員設定檔] 刀鋒視窗會顯示新建立之流量管理員設定檔的 DNS 名稱。 這可由任何用戶端使用 (例如使用網頁瀏覽器進行瀏覽)，以路由傳送至由路由類型所決定的正確端點。 在此情況下，所有的要求都將路由傳送到第一個端點，且如果流量管理員偵測到端點狀況不良，流量會自動容錯移轉到下一個端點。

## <a name="delete-the-traffic-manager-profile"></a>刪除流量管理員設定檔
刪除不再需要的資源群組和您已建立的流量管理員設定檔。 若要這樣做，請從 [流量管理員設定檔] 刀鋒視窗中選取資源群組，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

- 深入了解[路由類型](traffic-manager-routing-methods.md)。
- 深入了解端點類型[端點類型](traffic-manager-endpoint-types.md)。
- 深入了解[端點監視](traffic-manager-monitoring.md)。




