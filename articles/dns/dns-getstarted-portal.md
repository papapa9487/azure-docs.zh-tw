---
title: "利用 Azure 入口網站開始使用 Azure DNS | Microsoft Docs"
description: "了解如何在 Azure DNS 中建立 DNS 區域和記錄。 這份逐步指南將引導您使用 Azure 入口網站建立和管理第一個 DNS 區域和記錄。"
services: dns
documentationcenter: na
author: jtuliani
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: jonatul
ms.openlocfilehash: 93b24e3d9fbb3fbb3ea995271fd63d1e82eb9c9e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-azure-dns-using-the-azure-portal"></a>利用 Azure 入口網站開始使用 Azure DNS

> [!div class="op_single_selector"]
> * [Azure 入口網站](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 1.0](dns-getstarted-cli-nodejs.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

本文將逐步引導您使用 Azure 入口網站建立第一個 DNS 區域和記錄。 您也可以使用 Azure PowerShell 或跨平台 Azure CLI 執行這些步驟。

DNS 區域用來裝載特定網域的 DNS 記錄。 若要開始將網域裝載到 Azure DNS 中，您必須建立該網域名稱的 DNS 區域。 接著在此 DNS 區域內，建立網域的每筆 DNS 記錄。 最後，若要將 DNS 區域發佈至網際網路，您需要設定網域的名稱伺服器。 下列步驟會說明每個步驟。

## <a name="create-a-dns-zone"></a>建立 DNS 區域

1. 登入 Azure 入口網站
2. 在 [中樞] 功能表上，按一下 [新增] > [網路] > [DNS 區域] 以開啟 [建立 DNS 區域] 刀鋒視窗。

    ![DNS 區域](./media/dns-getstarted-portal/openzone650.png)

4. 在 [建立 DNS 區域] 刀鋒視窗中輸入下列的值，然後按一下 [建立]：


   | **設定** | **值** | **詳細資料** |
   |---|---|---|
   |**名稱**|contoso.com|DNS 區域的名稱|
   |**訂用帳戶**|[您的訂用帳戶]|選取要建立 DNS 區域的訂用帳戶。|
   |**資源群組**|**建立新的︰**contosoDNSRG|建立資源群組。 資源群組名稱在您選取的訂用帳戶中必須是唯一的。 若要深入了解資源群組，請閱讀 [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fdns%2ftoc.json#resource-groups) 概觀。|
   |**位置**|美國西部||

> [!NOTE]
> 資源群組是指資源群組的位置，不會對 DNS 區域有任何影響。 DNS 區域一定是「全域」位置，並不會顯示出來。

## <a name="create-a-dns-record"></a>建立 DNS 記錄

下列範例將逐步引導您完成建立新的 'A' 記錄的程序。 關於其他記錄類型和修改現有的記錄，請參閱[使用 Azure 入口網站管理 DNS 記錄和記錄集](dns-operations-recordsets-portal.md)。 

1. 建立 DNS 區域之後，在 Azure 入口網站的 [我的最愛] 窗格中，按一下 [所有資源]。 在 [所有資源] 刀鋒視窗中，按一下 [contoso.com] DNS 區域。 如果您選取的訂用帳戶已有幾個資源，您可以在 [依名稱篩選...] 方塊中輸入 **contoso.com**， 輕鬆地存取 DNS 區域。

1. 在 [DNS 區域] 刀鋒視窗頂端，選取 [+ 記錄集] 以開啟 [新增記錄集] 刀鋒視窗。

1. 在 [新增記錄集] 刀鋒視窗上，輸入下列的值，然後按一下 [確定]。 在此範例中，我們會建立 A 記錄。

   |**設定** | **值** | **詳細資料** |
   |---|---|---|
   |**名稱**|www|記錄的名稱|
   |**類型**|具有使用 | 要建立的 DNS 記錄類型，可接受的值為 A、AAAA、CNAME、MX、NS、SRV、TXT 和 PTR。  如需記錄類型的詳細資訊，請參閱 [DNS 區域和記錄的概觀](dns-zones-records.md)。|
   |**TTL**|1|DNS 要求的存留時間。|
   |**TTL 單位**|小時|TTL 值的時間測量。|
   |**IP 位址**|ipAddressValue| 這個值是 DNS 記錄解析的 IP 位址。|

## <a name="view-records"></a>檢視記錄

在 [DNS 區域] 刀鋒視窗的下半部，您可以看到 DNS 區域的記錄。 您應該會看到預設 DNS 和 SOA 記錄 (在每個區域中建立)，還有您已建立的任何新記錄。

![區域](./media/dns-getstarted-portal/viewzone500.png)


## <a name="update-name-servers"></a>更新名稱伺服器

當您滿意 DNS 區域且已正確設定記錄之後，您必須設定網域名稱來使用 Azure DNS 名稱伺服器。 這可讓網際網路上的其他使用者找到您的 DNS 記錄。

Azure 入口網站中會提供您區域的名稱伺服器：

![區域](./media/dns-getstarted-portal/viewzonens500.png)

這些名稱伺服器應該向網域名稱註冊機構 (您購買網域名稱的來源) 設定。 您的註冊機構會提供選項來設定網域的名稱伺服器。 如需詳細資訊，請參閱[將網域委派給 Azure DNS](dns-domain-delegation.md)。

## <a name="delete-all-resources"></a>刪除所有資源

若要刪除這篇文章中建立的所有資源，請完成下列步驟︰

1. 在 Azure 入口網站的 [我的最愛] 窗格中，按一下 [所有資源]。 在 [所有資源] 刀鋒視窗中，按一下 [MyResourceGroup] 資源群組。 如果您選取的訂用帳戶已有幾個資源，您可以在 [依名稱篩選...] 方塊中輸入 **MyResourceGroup**， 輕鬆地存取資源群組。
1. 在 [MyResourceGroup] 刀鋒視窗中，按一下 [刪除] 按鈕。
1. 入口網站會要求您輸入資源群組的名稱，以確認您想要刪除它。 按一下 [刪除]，輸入 *MyResourceGroup* 作為資源群組名稱，然後按一下 [刪除]。 刪除資源群組會刪除資源群組內的所有資源，所以務必確認資源群組的內容，然後再刪除它。 入口網站會刪除資源群組內包含的所有資源，然後刪除資源群組本身。 這個程序需要幾分鐘的時間。


## <a name="next-steps"></a>後續步驟

若要深入了解 Azure DNS，請參閱 [Azure DNS 概觀](dns-overview.md)。

若要深入了解在 Azure DNS 中管理 DNS 記錄，請參閱[使用 Azure 入口網站管理 DNS 記錄和記錄集](dns-operations-recordsets-portal.md)。

