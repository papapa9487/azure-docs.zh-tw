---
title: "建立您的第一個 Azure 虛擬網路 | Microsoft Docs"
description: "了解如何建立 Azure 虛擬網路 (VNet)、將兩部虛擬機器 (VM) 連線至 VNet，以及連線至 VM。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2016
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: e653764d7cb514d50b44fadd0cc5963dd404d99e
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---

# <a name="create-your-first-virtual-network"></a>建立您的第一個虛擬網路

了解如何建立具有兩個子網路的虛擬網路 (VNet)、建立兩個虛擬機器 (VM)，並且將各個 VM 連線至其中一個子網路，如下圖所示：

![虛擬網路圖表](./media/virtual-network-get-started-vnet-subnet/vnet-diagram.png)

Azure 虛擬網路 (VNet) 是您的網路在雲端中的身分。 您可以控制 Azure 網路設定，以及定義 DHCP 位址區塊、DNS 設定、安全性原則和路由。 若要深入了解 VNet 的概念，請閱讀[虛擬網路概觀](virtual-networks-overview.md)一文。 完成下列步驟來建立如圖片中所示的資源︰

1. [建立具有兩個子網路的 VNet](#create-vnet)
2. [建立兩個 VM，其分別有一個網路介面 (NIC)](#create-vms)，並且將網路安全性群組 (NSG) 關聯至每個 NIC
3. [連線到 VM 以及從 VM 連線](#connect-to-from-vms)
4. [刪除所有資源](#delete-resources)。 當您在佈建時，您在這個練習中建立的某些資源會產生費用。 若要將費用降至最低，完成練習之後，務必完成此區段中的步驟以刪除您建立的資源。

您必須對於如何在完成這篇文章中的步驟之後使用 VNet 有基本的了解。 接下來的步驟可以讓您進一步了解如何更深層地使用 Vnet。

## <a name="create-vnet"></a>建立具有兩個子網路的虛擬網路

若要建立具有兩個子網路的虛擬網路，請完成下列步驟。 不同的子網路通常用於控制子網路之間的流量。

1. 登入 [Azure 入口網站](<https://portal.azure.com>)。 如果您沒有帳戶，您可以註冊[免費試用一個月](https://azure.microsoft.com/free)。 
2. 在入口網站的 [我的最愛] 窗格中，按一下 [新增]。
3. 在 [新增] 刀鋒視窗中，按一下 [網路]。 在 [網路] 刀鋒視窗中，按一下 [虛擬網路]，如下圖中所示：

    ![虛擬網路圖表](./media/virtual-network-get-started-vnet-subnet/virtual-network.png)

4.  在 [虛擬網路] 刀鋒視窗中，保持 [Resource Manager] 選取做為部署模型，然後按一下 [建立]。
5.  在顯示的 [建立虛擬網路] 刀鋒視窗中，請輸入下列值，然後按一下 [建立]：

    |**設定**|**值**|**詳細資料**|
    |---|---|---|
    |**名稱**|*MyVNet*|名稱必須是資源群組中唯一的。|
    |**位址空間**|*10.0.0.0/16*|您可以使用 CIDR 標記法指定您想要的任何位址空間。|
    |**子網路名稱**|前端|子網路名稱在虛擬網路內必須是唯一的。|
    |**子網路位址範圍**|*10.0.0.0/24*| 指定的範圍必須存在於您為虛擬網路定義的位址空間。|
    |**訂用帳戶**|*[您的訂用帳戶]*|選取要在其中建立 VNet 的訂用帳戶。 VNet 存在於單一訂用帳戶中。|
    |**資源群組**|**建立新的：**MyRG|建立資源群組。 資源群組名稱在您選取的訂用帳戶中必須是唯一的。 若要深入了解資源群組，請閱讀 [Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) 概觀。|
    |**位置**|美國西部| 通常會選取最接近您實體位置的位置。|

    VNet 的建立會耗用幾秒鐘時間。 一旦建立，您會看到 Azure 入口網站儀表板。

6. 建立虛擬網路之後，在 Azure 入口網站 [我的最愛] 窗格中，按一下 [所有資源]。 按一下 [所有資源] 刀鋒視窗中的 [MyVNet] 虛擬網路。 如果您選取的訂用帳戶已有幾個資源，您可以在 [依名稱篩選...] 方塊中輸入 MyVNet， 輕鬆地存取 VNet。
7. [MyVNet] 刀鋒視窗隨即開啟，並顯示 VNet 的相關資訊，如下列圖片所示︰

    ![虛擬網路圖表](./media/virtual-network-get-started-vnet-subnet/myvnet.png)

8. 如上一張圖片所示，按一下 [子網路] 以顯示在 Vnet 內子網路的清單。 唯一存在的子網路是 [前端]，您在步驟 5 中建立的子網路。
9. 在 [MyVNet - 子網路] 刀鋒視窗中，按一下 [+ 子網路] 以建立具有下列資訊的子網路，然後按一下 [確定] 以建立子網路︰

    |**設定**|**值**|**詳細資料**|
    |---|---|---|
    |**名稱**|後端|名稱在虛擬網路內必須是唯一的。|
    |**位址範圍**|*10.0.1.0/24*|指定的範圍必須存在於您為虛擬網路定義的位址空間。|
    |**網路安全性群組**和**路由表**|「無」(預設值)|網路安全性群組 (NSG) 涵蓋於本文章稍後部分。 若要深入了解使用者定義的路由，請閱讀[使用者定義的路由](virtual-networks-udr-overview.md)文章。|

10. 將新的子網路新增至 VNet 之後，您可以關閉 [MyVNet – 子網路] 刀鋒視窗，然後關閉 [所有資源] 刀鋒視窗。

## <a name="create-vms"></a>建立虛擬機器

建立 VNet 和子網路之後，您可以建立 VM。 對於此練習，這兩個 VM 都執行 Windows Server 作業系統，不過它們可以執行 Azure 支援的任何作業系統，包括數個不同的 Linux 散發套件。

### <a name="create-web-server-vm"></a>建立網頁伺服器 VM

若要建立網頁伺服器 VM，請完成下列步驟：

1. 在 Azure 入口網站的 [我的最愛] 窗格中，按一下 [新增]、[計算]，然後按一下 [Windows Server 2016 Datacenter]。
2. 在 [Windows Server 2016 Datacenter] 刀鋒視窗中，按一下 [建立]。
3. 在顯示的 [基本] 刀鋒視窗中，輸入或選取下列值，然後按一下 [確定]：

    |**設定**| **值**|**詳細資料**|
    |---|---|---|
    |**名稱**|*MyWebServer*|此 VM 伺服器做為網際網路資源連線的網頁伺服器。|
    |**VM 磁碟類型**|SSD|
    |**使用者名稱**|您的選擇|
    |**密碼和確認密碼**|您的選擇|
    | **訂用帳戶**|*<Your subscription>*|訂用帳戶必須是您在[建立具有兩個子網路的虛擬網路](#create-vnet)一節中步驟 5 所選取的相同訂用帳戶。 連線至 VM 的 VNet 必須存在於與 VM 相同的訂用帳戶。|
    |**資源群組**|**使用現有︰**選取 MyRG|雖然我們使用針對 Vnet 使用的相同資源群組，資源不需要存在於相同的資源群組。|
    |**位置**|美國西部|位置必須是您在這篇文章的[建立具有兩個子網路的虛擬網路](#create-vnet)一節中步驟 5 所指定的相同位置。 它們所連線的 VM 和 Vnet 必須存在於相同位置。|

4. 在 [選擇大小] 刀鋒視窗中，按一下 [DS1_V2 標準]，然後按一下 [選取]。 閱讀 [Windows VM 大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)文章，以取得 Azure 支援的所有 Windows VM 大小的清單。
5. 在 [設定] 刀鋒視窗中，輸入或選取下列值，然後按一下 [確定]：

    |**設定**|**值**|**詳細資料**|
    |---|---|---|
    |**儲存體：使用受控磁碟**|*是*||
    |**虛擬網路**| 選取 MyVNet|您可以選取存在於與您建立之 VM 相同位置中的任何 VNet。 若要深入了解 VNet 和子網路，請閱讀[虛擬網路](virtual-networks-overview.md)文章。|
    |**子網路**|選取 [前端]|您可以選取存在於 VNet 中的任何子網路。|
    |**公用 IP 位址**|接受預設值|公用 IP 位址可讓您從網際網路連線至 VM。 若要深入了解公用 IP 位址，請閱讀 [IP 位址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)文章。|
    |**網路安全性群組 (防火牆)**|接受預設值|按一下入口網站建立的 [(新的) MyWebServer-nsg] 預設 NSG，以檢視其設定。 在開啟的 [建立網路安全性群組] 刀鋒視窗中，請注意，它具有一個輸入規則以允許來自任何來源 IP 位址的 TCP/3389 (RDP) 流量。|
    |**所有其他值**|接受預設值|若要深入了解其餘設定，請閱讀[關於 VM](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 文章。|

    網路安全性群組 (NSG) 可讓您建立可以流入/流出 VM 之網路流量類型的輸入/輸出規則。 根據預設，會拒絕 VM 的所有輸入流量。 您可以為實際執行網頁伺服器針對 TCP/80 (HTTP) 和 TCP/443 (HTTPS) 新增額外的輸入規則。 因為根據預設沒有輸出流量的規則，所以允許所有輸出流量。 您可以新增/移除規則，以控制每個原則的流量。 若要深入了解 NSG，請閱讀[網路安全性群組](virtual-networks-nsg.md)文章。

6.  在 [摘要] 刀鋒視窗中檢閱設定，然後按一下 [確定] 以建立 VM。 建立 VM 時，狀態圖格會顯示在入口網站儀表板上。 可能需要數分鐘才會建立。 您不需要等候它完成。 建立 VM 時，您可以繼續下一個步驟。

### <a name="create-database-server-vm"></a>建立資料庫伺服器 VM

若要建立資料庫伺服器 VM，請完成下列步驟：

1.  在 [我的最愛] 窗格中，按一下 [新增]、[計算]，然後按一下 [Windows Server 2016 Datacenter]。
2.  在 [Windows Server 2016 Datacenter] 刀鋒視窗中，按一下 [建立]。
3.  在 [基本] 刀鋒視窗中，輸入或選取下列值，然後按一下 [確定]：

    |**設定**|**值**|**詳細資料**|
    |---|---|---|
    |**名稱**|MyDBServer|此 VM 伺服器是做為網頁伺服器所連線的資料庫伺服器，但是網際網路無法連線。|
    |**VM 磁碟類型**|SSD||
    |**使用者名稱**|您的選擇||
    |**密碼和確認密碼**|您的選擇||
    |**訂用帳戶**|<Your subscription>|訂用帳戶必須是您在[建立具有兩個子網路的虛擬網路](#create-vnet)一節中步驟 5 所選取的相同訂用帳戶。|
    |**資源群組**|**使用現有︰**選取 MyRG|雖然我們使用針對 Vnet 使用的相同資源群組，資源不需要存在於相同的資源群組。|
    |**位置**|美國西部|位置必須是您在這篇文章的[建立具有兩個子網路的虛擬網路](#create-vnet)一節中步驟 5 所指定的相同位置。|

4.  在 [選擇大小] 刀鋒視窗中，按一下 [DS1_V2 標準]，然後按一下 [選取]。
5.  在 [設定] 刀鋒視窗中，輸入或選取下列值，然後按一下 [確定]：

    |**設定**|**值**|**詳細資料**|
    |----|----|---|
    |**儲存體：使用受控磁碟**|*是*||
    |**虛擬網路**|選取 MyVNet|您可以選取存在於與您建立之 VM 相同位置中的任何 VNet。|
    |**子網路**|選取 [後端]，方法是按一下 [子網路] 方塊，然後從 [選擇子網路] 刀鋒視窗中選取 [後端]|您可以選取存在於 VNet 中的任何子網路。|
    |**公用 IP 位址**|「無」 – 按一下預設位址，然後從 [選擇公用 IP 位址] 刀鋒視窗中按一下 [無]|如果沒有公用 IP 位址，您只能從另一部連線到相同 VNet 的 VM 連線到 VM。 您無法直接從網際網路連線。|
    |**網路安全性群組 (防火牆)**|接受預設值| 像是為 MyWebServer VM 建立的預設 NSG，這個 NSG 也有相同的預設輸入規則。 您可以為資料庫伺服器針對 TCP 1433 (MS SQL) 新增額外的輸入規則。 因為根據預設沒有輸出流量的規則，所以允許所有輸出流量。 您可以新增/移除規則，以控制每個原則的流量。|
    |**所有其他值**|接受預設值||

6.  在 [摘要] 刀鋒視窗中檢閱設定，然後按一下 [確定] 以建立 VM。 建立 VM 時，狀態圖格會顯示在入口網站儀表板上。 可能需要數分鐘才會建立。 您不需要等候它完成。 建立 VM 時，您可以繼續下一個步驟。

## <a name="review"></a>檢閱資源

雖然您建立一個 VNet 和兩個 VM，Azure 入口網站會為您在 MyRG 資源群組中建立數個其他資源。 藉由完成下列步驟，檢閱 MyRG 資源群組的內容︰

1. 在 [我的最愛] 窗格中，按一下 [更多服務]。
2. 在 [更多服務] 窗格中，在其中具有字詞 [篩選器]  的方塊中輸入 [資源群組]。 當您在篩選清單中看見它時，按一下 [資源群組]。
3. 在 [資源群組] 窗格中，按一下 [MyRG] 資源群組。 如果您在訂用帳戶中有許多現有的資源群組，您可以在包含文字「依名稱篩選...」的方塊中輸入 MyRG 以快速存取 MyRG 資源群組。
4.  在 [MyRG] 刀鋒視窗中，您會看到資源群組包含 12 個資源，如下列圖片所示︰

    ![資源群組內容](./media/virtual-network-get-started-vnet-subnet/resource-group-contents.png)

若要深入了解 VM、磁碟和儲存體帳戶，請閱讀[虛擬機器](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)、[磁碟](../virtual-machines/windows/about-disks-and-vhds.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，和[儲存體帳戶](../storage/common/storage-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)概觀文章。 您可以看到入口網站為您建立的兩個預設 NSG。 您也可以看到入口網站建立兩個網路介面 (NIC) 資源。 NIC 可以讓 VM 透過 VNet 連線至其他資源。 閱讀 [NIC](virtual-network-network-interface.md) 文章，以深入了解 NIC。 入口網站也會建立一個公用 IP 位址資源。 公用 IP 位址是公用 IP 位址資源的一項設定。 若要深入了解公用 IP 位址，請閱讀 [IP 位址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)文章。

## <a name="connect-to-from-vms"></a>連線至 VM

建立您的 VNet 和兩個 VM 之後，您現在可以藉由完成下列章節中的步驟，連線至 VM︰

### <a name="connect-from-internet"></a>從網際網路連線至網頁伺服器 VM

若要從網際網路連線至網頁伺服器 VM，請完成下列步驟︰

1. 在入口網站中，開啟 MyRG 資源群組，方法是完成這篇文章之[檢閱資源](#review)一節中的步驟。
2. 在 [MyRG] 刀鋒視窗中，按一下 [MyWebServer] VM。
3. 在 [MyWebServer] 刀鋒視窗中，按一下 [連線]，如下列圖片所示︰

    ![連線至網頁伺服器 VM](./media/virtual-network-get-started-vnet-subnet/webserver.png)

4. 允許您的瀏覽器下載 MyWebServer.rdp 檔案，然後開啟它。
5. 如果您收到對話方塊，通知您無法驗證遠端連線的發佈者，請按一下 [連線]。
6. 當輸入您的認證時，請確定您使用您在這篇文章之[建立網頁伺服器 VM](#create-web-server-vm) 一節中步驟 3 指定的使用者名稱和密碼進行登入。 如果顯示的 [Windows 安全性] 方塊未列出正確的認證，您可能需要按一下 [更多選擇]，然後按一下 [使用不同的帳戶]，以便能夠指定正確的使用者名稱和密碼)。 按一下 [確定]以連線至 VM。
7. 如果您收到 [遠端桌面連線] 方塊，通知您無法驗證遠端電腦的身分識別，請按一下 [是]。
8. 您現在已經從網際網路連線至 MyWebServer VM。 保持遠端桌面連線開啟來完成下一節中的步驟。

遠端連線是連線至公用 IP 位址，該位址指派給這篇文章之[建立具有兩個子網路的虛擬網路](#create-vnet)一節步驟 5 中入口網站建立的公用 IP 位址資源。 允許連線，因為在 **MyWebServer-nsg** NSG 建立的預設規則允許來自任何來源 IP 位址的 TCP/3389 (RDP) 輸入至 VM。 如果您嘗試透過任何其他連接埠連線至 VM，連線會失敗，除非您將其他輸入規則新增至 NSG，允許使用其他連接埠。

>[!NOTE]
>如果您將其他輸入規則新增至 NSG，請確定在 Windows 防火牆上已開啟相同連接埠，否則連線會失敗。
>

### <a name="connect-to-internet"></a>從網頁伺服器 VM 連線至網際網路

若要從網頁伺服器 VM 連線輸出至網際網路，請完成下列步驟︰

1. 如果您還沒有對 MyWebServerVM 的遠端連線，請進行遠端連線至 VM，方法是完成這篇文章之[從網際網路連線至網頁伺服器 VM](#connect-from-internet) 一節中的步驟。
2. 從 Windows 桌面上，開啟 Internet Explorer。 在 [安裝 Internet Explorer 11] 對話方塊中，按一下 [不使用建議的設定]，然後按一下 [確定]。 建議您接受實際執行伺服器的建議設定。
3. 在 Internet Explorer 網址列中，輸入 [bing.com](http:www.bing.com)。如果您收到 Internet Explorer 對話方塊，按一下 [新增]，然後在 [信任的網站] 對話方塊中 [新增]，然後按一下 [關閉]。 對於任何其他 Internet Explorer 對話方塊重複此程序。
4. 在 Bing 搜尋頁面上，輸入 whatsmyipaddress ，然後按一下 [放大鏡] 按鈕。 Bing 會傳回當您建立 VM 時，指派給入口網站建立的公用 IP 位址資源的公用 IP 位址。 如果您檢查 **MyWebServer-ip**資源的設定，您會看到指派給公用 IP 位址資源的相同 IP 位址，如下圖所示。 不過，指派給 VM 的 IP 位址不同。

    ![連線至網頁伺服器 VM](./media/virtual-network-get-started-vnet-subnet/webserver-pip.png)

5.  保持遠端桌面連線開啟來完成下一節中的步驟。

您也可以從 VM 連線至網際網路，因為預設允許來自 VM 的所有輸出連線。 您可以藉由將新增規則新增至套用到 NIC、NIC 連線的子網路或兩者的 NSG，來限制輸出連線。

如果使用入口網站將 VM 放在已停止 (解除配置) 狀態，可以變更公用 IP 位址。 如果您需要公用 IP 位址永遠不會變更，您可以為 IP 位址使用靜態配置方法，而不是動態配置方法 (這是預設值)。 若要深入了解配置方法之間的差異，請閱讀 [IP 位址類型和配置方法](virtual-network-ip-addresses-overview-arm.md)文章。

### <a name="webserver-to-dbserver"></a>從網頁伺服器 VM 連線至資料庫伺服器 VM

若要從網頁伺服器 VM 連線至資料庫伺服器 VM，請完成下列步驟︰

1. 如果您還沒有對 MyWebServer VM 的遠端連線，請進行遠端連線至 VM，方法是完成這篇文章之[從網際網路連線至網頁伺服器 VM](#connect-from-internet) 一節中的步驟。
2. 按一下 Windows 桌面左下角的 [開始] 按鈕，然後開始輸入 remote desktop。 當 [開始] 功能表清單顯示 [遠端桌面連線] 時，按一下它。
3. 在 [遠端桌面連線] 對話方塊中，針對電腦名稱輸入 MyDBServer，然後按一下 [連線]。
4. 輸入您在這篇文章之[建立資料庫伺服器 VM](#create-database-server-vm) 一節中步驟 3 輸入的使用者名稱和密碼，然後按一下 [確定]。
5. 如果您收到對話方塊，通知您無法驗證遠端電腦的身分識別，請按一下 [是]。
6. 保持對兩部伺服器的遠端桌面連線開啟來完成下一節中的步驟。

您可以從網頁伺服器 VM 連線至資料庫伺服器 VM，原因如下︰

- TCP/3389 輸入連線針對這篇文章之[建立資料庫伺服器 VM](#create-database-server-vm) 一節步驟 5 中建立的預設 NSG 的任何來源 IP 啟用。
- 您從網頁伺服器 VM 起始連線，它連線到與資料庫伺服器 VM 相同的 VNet。 若要連線至沒有公用 IP 位址指派給它的 VM，您必須從另一個 VM 連線至相同的 VNet，即使 VM 連線至不同的子網路。
- 即使 VM 連線至不同的子網路，Azure 會建立預設路由，啟用子網路之間的連線。 但是，您可以建立自己的路由來覆寫預設路由。 若要深入了解 Azure 中的路由，請閱讀[使用者定義的路由](virtual-networks-udr-overview.md)文章。

如果您嘗試起始從網際網路到資料庫伺服器 VM 的遠端連線，如同您在這篇文章之[從網際網路連線至網頁伺服器 VM](#connect-from-internet) 一節中所做的一樣，您會看到 [連線] 選項呈現灰色。連線呈現灰色，因為沒有任何公用 IP 位址指派給 VM，所以從網際網路連線至它的輸入連線是不可能的。

### <a name="connect-to-the-internet-from-the-database-server-vm"></a>從資料庫伺服器 VM 連線至網際網路

從資料庫伺服器 VM 連線輸出至網際網路，方法是完成下列步驟︰

1. 如果您還沒有從 MyWebServer VM 對 MyDBServer VM 的遠端連線，請完成這篇文章之[從網頁伺服器 VM 連線至資料庫伺服器 VM ](#webserver-to-dbserver) 一節中的步驟。
2. 從 MyDBServer VM 的 Windows 桌面上，開啟 Internet Explorer 並且回應對話方塊，如同您在這篇文章之[從網頁伺服器 VM 連線至網際網路](#connect-to-internet)一節的步驟 2 和 3 中所執行的操作一樣。
3. 在網址列中，輸入 [bing.com](http:www.bing.com)。
4. 在顯示的 Internet Explorer 對話方塊中，按一下 [新增]，然後在 [信任的網站] 對話方塊中依序按一下 [新增]、[關閉]。 在顯示的任何其他對話方塊中完成這些步驟。
5. 在 Bing 搜尋頁面上，輸入 whatsmyipaddress ，然後按一下 [放大鏡] 按鈕。 Bing 會傳回目前由 Azure 基礎結構指派給 VM 的公用 IP 位址。 6. 從 MyWebServer VM 關閉到 MyDBServer VM 的遠端桌面，然後關閉對 MyWebServer VM 的遠端連線。

允許到網際網路的輸出連線，因為根據預設，允許所有輸出流量，即使公用 IP 位址資源未指派給 MyDBServer VM。 根據預設，所有 VM 都能夠輸出連線至網際網路，無論有無公用 IP 位址資源指派給 VM。 但是，您不能從網際網路連線至公用 IP 位址，如同具有指派公用 IP 位址資源的 MyWebServer VM 那樣。

## <a name="delete-resources"></a>刪除所有資源

若要刪除這篇文章中建立的所有資源，請完成下列步驟︰

1. 若要檢視這篇文件中建立的 MyRG 資源群組，請完成這篇文章之[檢閱資源](#review)一節中的步驟 1-3。 再次檢閱資源群組中的資源。 如果您建立 MyRG 資源群組，在每個先前步驟中，您會在步驟 4 的圖片中看到 12 個資源顯示。
2. 在 [MyRG] 刀鋒視窗中，按一下 [刪除] 按鈕。
3. 入口網站會要求您輸入資源群組的名稱，以確認您想要刪除它。 如果您看到的資源，有別於這篇文章之[檢閱資源](#review)一節步驟 4 中顯示的資源，請按一下 [取消]。 如果您只看到建立的 12 個資源做為這篇文章的一部分，輸入 MyRG 做為資源群組名稱，然後按一下 [刪除]。 刪除資源群組會刪除資源群組內的所有資源，所以務必確認資源群組的內容，然後再刪除它。 入口網站會刪除資源群組內包含的所有資源，然後刪除資源群組本身。 這個程序需要幾分鐘的時間。

## <a name="next-steps"></a>接續步驟

在本練習中，您會建立 VNet 和兩個 VM。 在建立 VM 期間，指定一些自訂設定，並且接受數個預設設定。 我們建議您先閱讀下列文章，然後再部署生產環境 Vnet 和 VM，以確保您了解所有可用的設定︰

- [虛擬網路](virtual-networks-overview.md)
- [公用 IP 位址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- [網路介面](virtual-network-network-interface.md)
- [網路安全性群組](virtual-networks-nsg.md)
- [虛擬機器](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

