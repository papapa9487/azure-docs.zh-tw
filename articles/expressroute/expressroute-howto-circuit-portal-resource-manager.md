---
title: "建立與修改 ExpressRoute 線路：Azure 入口網站 | Microsoft Docs"
description: "本文說明如何建立、佈建、驗證、更新、刪除和取消佈建 ExpressRoute 線路。"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 68d59d59-ed4d-482f-9cbc-534ebb090613
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/07/2017
ms.author: cherylmc;ganesr
ms.openlocfilehash: e3721cd3c031622788f553e71a6555b844f3f7dc
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="create-and-modify-an-expressroute-circuit"></a>建立和修改 ExpressRoute 線路
> [!div class="op_single_selector"]
> * [Azure 入口網站](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [影片 - Azure 入口網站](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (傳統)](expressroute-howto-circuit-classic.md)
>

此文章說明如何使用 Azure 入口網站和 Azure Resource Manager 部署模型建立 Azure ExpressRoute 線路。 下列步驟也會示範如何檢查線路的狀態、對它進行更新，或是對它進行刪除及取消佈建。


## <a name="before-you-begin"></a>開始之前
* 開始設定之前，請檢閱[必要條件](expressroute-prerequisites.md)和[工作流程](expressroute-workflows.md)。
* 確定您可以存取 [Azure 入口網站](https://portal.azure.com)。
* 確定您具有建立新網路資源的權限。 如果您沒有正確的權限，請連絡您的帳戶管理員。
* 您可以在開始前先[觀賞影片](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)，以便更加了解步驟。

## <a name="create-and-provision-an-expressroute-circuit"></a>建立和佈建 ExpressRoute 線路
### <a name="1-sign-in-to-the-azure-portal"></a>1.登入 Azure 入口網站
透過瀏覽器瀏覽至 [Azure 入口網站](http://portal.azure.com) ，並使用您的 Azure 帳戶登入。

### <a name="2-create-a-new-expressroute-circuit"></a>2.建立新的 ExpressRoute 線路
> [!IMPORTANT]
> ExpressRoute 線路將會從發出服務金鑰時開始收費。 請確定在連線提供者準備好佈建線路之後，再執行此作業。
> 
> 

1. 您可以選取建立新資源的選項來建立 ExpressRoute 線路。 按一下 [新增] > [網路] > [ExpressRoute]，如下圖所示：
   
    ![建立 ExpressRoute 線路](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. 按一下 [ExpressRoute] 之後，將會看到 [建立 ExpressRoute 線路] 刀鋒視窗。 填寫此刀鋒視窗上的值時，請確定您指定正確的 SKU 層和資料計量。
   
   *  決定要啟用 ExpressRoute 標準或 ExpressRoute 進階附加元件。 您可以指定 [標準] 來取得標準 SKU，或指定 [進階] 來取得進階附加元件。
   *  決定計費類型。 您可以指定 [計量付費] 以採用計量付費數據傳輸方案，選取 [無限制] 以採用無限制的資料方案。 請注意，您可以將計費類型從 [計量付費] 變更為 [無限制]，但無法從 [無限制] 變更為 [計量付費]。
     
     ![設定 SKU 層和資料計量](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit2.png)

> [!IMPORTANT]
> 請留意「對等位置」表示您與 Microsoft 對等互連的[實體位置](expressroute-locations.md) 。 這**不會**連結到「位置」屬性，這是指 Azure 網路資源提供者所在的地理位置。 儘管它們並無關聯，但最好還是選擇地理位置靠近線路對等位置的網路資源提供者。 
> 
> 

### <a name="3-view-the-circuits-and-properties"></a>3.檢視線路和屬性
**檢視所有線路**

您可以選取左側功能表上的 [所有資源] 來檢視您建立的所有線路。

![檢視線路](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**檢視屬性**

    You can view the properties of the circuit by selecting it. On this blade, note the service key for the circuit. You must copy the circuit key for your circuit and pass it down to the service provider to complete the provisioning process. The circuit key is specific to your circuit.

![檢視屬性](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4.將服務金鑰傳送給連線提供者以進行佈建
在此刀鋒視窗上，[提供者狀態] 提供服務提供者端目前的佈建狀態相關資訊。 [線路狀態] 提供 Microsoft 端的狀態。 如需線路佈建狀態的詳細資訊，請參閱[工作流程](expressroute-workflows.md#expressroute-circuit-provisioning-states)文章。

當您建立新的 ExpressRoute 線路時，線路會是下列狀態：

提供者狀態︰未佈建<BR>
線路狀態：已啟用

![起始佈建程序](./media/expressroute-howto-circuit-portal-resource-manager/viewstatus.png)

當連線提供者正在為您啟用線路時，線路會變更為下列狀態：

提供者狀態︰正在佈建<BR>
線路狀態：已啟用

若要能夠使用 ExpressRoute 線路，它必須處於下列狀態：

提供者狀態︰已佈建<BR>
線路狀態：已啟用

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5.定期檢查線路金鑰的狀態與狀況
選取感興趣的線路，即可檢視該線路的屬性。 檢查 [提供者狀態]，確定它已變成 [已佈建] 再繼續。

![線路和提供者狀態](./media/expressroute-howto-circuit-portal-resource-manager/viewstatusprovisioned.png)

### <a name="6-create-your-routing-configuration"></a>6.建立路由組態
如需逐步指示，請參閱 [ExpressRoute 線路路由組態](expressroute-howto-routing-portal-resource-manager.md)一文以建立和修改線路對等。

> [!IMPORTANT]
> 這些指示只適用於由提供第 2 層連線服務的服務提供者所建立的線路。 如果您使用的服務提供者是提供受管理的第 3 層服務 (通常是 IP VPN，如 MPLS)，您的連線提供者會為您設定和管理路由。
> 
> 

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7.將虛擬網路連結到 ExpressRoute 線路
接下來，將虛擬網路連結到 ExpressRoute 線路。 當使用 Resource Manager 部署模型時，使用[將虛擬網路連結到 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)文章。

## <a name="getting-the-status-of-an-expressroute-circuit"></a>取得 ExpressRoute 線路的狀態
選取線路，即可檢視該線路的狀態。 

![ExpressRoute 線路的狀態](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)

## <a name="modifying-an-expressroute-circuit"></a>修改 ExpressRoute 線路
您可以修改 ExpressRoute 線路的某些屬性，而不會影響連線。

您可以執行下列工作，而無需中途停機：

* 啟用或停用 ExpressRoute 線路的 ExpressRoute 進階附加元件。
* 只要連接埠有可用的容量，就增加 ExpressRoute 線路的頻寬。 請注意，不支援將線路的頻寬降級。 
* 將計量方案從 [計量付費] 變更為 [無限制]。 請注意，不支援將計量方案從 [無限制] 變更為 [計量付費]。
* 您可以啟用和停用 [允許傳統作業]。

如需限制的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。

若要修改 ExpressRoute 線路，請按一下 [設定]，如下圖所示。

![修改線路](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)

您可以在 [設定] 刀鋒視窗中修改頻寬、SKU、計費模型並允許傳統作業。

> [!IMPORTANT]
> 如果現有的連接埠上沒有足夠的容量，您可能必須重新建立 ExpressRoute 線路。 如果該位置已無額外的容量，您無法升級線路。
>
> 降低 ExpressRoute 線路的頻寬時必須中斷運作。 頻寬降級需要取消佈建 ExpressRoute 線路，然後重新佈建新的 ExpressRoute 線路。
> 
> 如果您使用的資源超出標準線路所允許的數量，停用進階附加元件作業可能會失敗。
> 
> 

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>取消佈建和刪除 ExpressRoute 線路
您可以選取**刪除**圖示，刪除 ExpressRoute 線路。 請注意：

* 您必須取消連結 ExpressRoute 線路的所有虛擬網路。 如果此操作失敗，請檢查您是否有任何虛擬網路連結至線路。
* 如果 ExpressRoute 線路服務提供者佈建狀態為「正在佈建」或「已佈建」，您就必須與服務提供者一起合作，取消佈建他們那邊的線路。 我們將繼續保留資源並向您收取費用，直到線路服務提供者完成取消佈建並通知我們。
* 若服務提供者已取消佈建線路 (服務提供者佈建狀態設定為 [NotProvisioned] )，則您可以刪除線路。 這樣將會停止針對線路計費

## <a name="next-steps"></a>後續步驟
建立好線路後，請務必執行下列作業：

* [建立和修改 ExpressRoute 線路的路由](expressroute-howto-routing-portal-resource-manager.md)
* [將虛擬網路連結至 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)

