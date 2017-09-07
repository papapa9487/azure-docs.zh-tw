---
title: "使用 Azure 網路監看員管理封包擷取 - Azure 入口網站 | Microsoft Docs"
description: "此頁面說明如何使用 Azure 入口網站管理網路監看員的封包擷取功能"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 33390532cc4fc1129a4f960d589f41bc95e5a1ff
ms.contentlocale: zh-tw
ms.lasthandoff: 06/15/2017

---

# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>使用入口網站以 Azure 網路監看員管理封包擷取

> [!div class="op_single_selector"]
> - [Azure 入口網站](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

網路監看員封包擷取可讓您建立擷取工作階段來追蹤虛擬機器的流入和流出流量。 系統會為擷取工作階段提供篩選器，以確保您只會擷取到您想要的流量。 封包擷取有助於被動和主動地診斷網路異常。 其他用途包括收集網路統計資料、取得有關網路入侵的資訊，以及偵錯用戶端與伺服器間的通訊等等。 藉由能夠從遠端觸發封包擷取，這項功能可以減輕在所需機器上手動執行封包擷取的工作負擔，進而省下寶貴的時間。

本文會帶領您逐步完成封包擷取目前可用的不同管理工作。

- [**啟動封包擷取**](#start-a-packet-capture)
- [**停止封包擷取**](#stop-a-packet-capture)
- [**刪除封包擷取**](#delete-a-packet-capture)
- [**下載封包擷取**](#download-a-packet-capture)

## <a name="before-you-begin"></a>開始之前

本文假設您具有下列資源：

- 您想要用來建立封包擷取之區域中的網路監看員執行個體
- 已啟用封包擷取擴充功能的虛擬機器。

> [!IMPORTANT]
> 封包擷取需要虛擬機器擴充功能 `AzureNetworkWatcherExtension`。 若要在 Windows VM 上安裝擴充功能，請瀏覽[適用於 Windows 的 Azure 網路監看員代理程式虛擬機器擴充功能](../virtual-machines/windows/extensions-nwa.md)，若要在 Linux VM 上安裝，則請瀏覽[適用於 Linux 的 Azure 網路監看員代理程式虛擬機器擴充功能](../virtual-machines/linux/extensions-nwa.md)。

### <a name="packet-capture-agent-extension-through-the-portal"></a>透過入口網站封包擷取代理程式延伸模組

若要透過入口網站安裝封包擷取 VM 代理程式，瀏覽至您的虛擬機器，然後按一下 [延伸模組] > [新增]，並搜尋 **Windows 網路監看員代理程式**

![代理程式檢視][agent]

## <a name="packet-capture-overview"></a>封包擷取概觀

瀏覽至 [Azure 入口網站](https://portal.azure.com)然後按一下 [網路] > [網路監看員] > [封包擷取]

[概觀] 頁面會顯示所有存在的封包擷取清單，無論狀態為何。

> [!NOTE]
> 封包擷取需要透過連接埠 443 的儲存體帳戶連線。

![封包擷取概觀畫面][1]

## <a name="start-a-packet-capture"></a>啟動封包擷取

按一下 [新增] 來建立封包擷取。

您可以在封包擷取上定義的屬性如下︰

**主要設定**

- **訂閱** - 這個值是所使用的訂用帳戶，每個訂用帳戶需要網路監看員的執行個體。
- **資源群組** - 目標的虛擬機器資源群組。
- **目標虛擬機器** - 執行封包擷取的虛擬機器
- **封包擷取名稱** - 這個值是封包擷取的名稱。

**擷取設定**

- **儲存體帳戶** - 決定封包擷取是否會儲存在儲存體帳戶中。
- **檔案** - 決定封包擷取是否會本機儲存在虛擬機器上。
- **儲存體帳戶** - 要在其中儲存封包擷取的指定儲存體帳戶。 預設位置是 https://{儲存體帳戶名稱}.blob.core.windows.net/network-watcher-logs/subscriptions/{訂用帳戶識別碼}/resourcegroups/{資源群組名稱}/providers/microsoft.compute/virtualmachines/{虛擬機器名稱}/{YY}/{MM}/{DD}/packetcapture_{HH}_{MM}_{SS}_{XXX}.cap。 (只在選取**儲存體**時才會啟用)
- **本機檔案路徑** - 要儲存封包擷取的虛擬機器上之本機路徑。 (只在選取**檔案**時才會啟用)。 必須提供有效的路徑
- **每個封包的最大位元組** - 來自每個封包所擷取的位元組，如果保留空白，會擷取所有位元組。
- **每個工作階段的位元組數上限** - 已擷取的位元組總數，一旦達到此值，封包擷取便會停止。
- **時間限制 (秒)** - 設定要停止擷取封包的時間限制。 預設值為 18000 秒。

> [!NOTE]
> 儲存封包擷取目前不支援進階儲存體帳戶。

**篩選 (選用)**

- **通訊協定** - 用來篩選封包擷取的通訊協定。 可用的值為 TCP、UDP 和 Any。
- **本機 IP 位址** - 這個值會將封包擷取篩選為其中本機 IP 位址符合此篩選值的封包。
- **本機連接埠** - 這個值會將封包擷取篩選為其中本機連接埠符合此篩選值的封包。
- **遠端 IP 位址** - 這個值會將封包擷取篩選為其中遠端 IP 符合此篩選值的封包。
- **遠端連接埠** - 這個值會將封包擷取篩選為其中遠端連接埠符合此篩選值的封包。

> [!NOTE]
> 連接埠和 IP 位址的值可以是單一值、值的範圍或一組。 (也就是連接埠的 80-1024) 您可以定義所有您想要的篩選。

這些值都已填寫之後，按一下 [確定] 以建立封包擷取。

![建立封包擷取][2]

封包擷取上設定的時間限制到期之後，封包擷取會停止且可以檢視。 您也可以手動停止封包擷取工作階段。

## <a name="delete-a-packet-capture"></a>刪除封包擷取

在封包擷取檢視中，按一下 **[內容功能表]** \(...) 或按一下滑鼠右鍵，然後按一下 **[刪除]** 來停止封包擷取

![刪除封包擷取][3]

> [!NOTE]
> 刪除封包擷取不會刪除儲存體帳戶中的檔案。

系統會要求您確認您想要刪除封包擷取，按一下 [是]

![確認][4]

## <a name="stop-a-packet-capture"></a>停止封包擷取

在封包擷取檢視中，按一下 **[內容功能表]** \(...) 或按一下滑鼠右鍵，然後按一下 **[停止]** 來停止封包擷取

## <a name="download-a-packet-capture"></a>下載封包擷取

封包擷取工作階段完成後，即可將擷取檔案上傳到 Blob 儲存體或 VM 上的本機檔案。 封包擷取的儲存位置會在建立工作階段時定義。 若要存取這些儲存至儲存體帳戶的擷取檔案，Microsoft Azure 儲存體總管是很便利的工具，您可以在這裡下載︰http://storageexplorer.com/

如果指定了儲存體帳戶，封包擷取檔案便會儲存到儲存體帳戶的下列位置︰
```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>後續步驟

檢視[建立由警示觸發的封包擷取](network-watcher-alert-triggered-packet-capture.md)來了解如何透過虛擬機器警示自動化封包擷取

造訪[檢查 IP 流量驗證](network-watcher-check-ip-flow-verify-portal.md)來得知 VM 是否允許特定流量流入或流出

<!-- Image references -->
[1]: ./media/network-watcher-packet-capture-manage-portal/figure1.png
[2]: ./media/network-watcher-packet-capture-manage-portal/figure2.png
[3]: ./media/network-watcher-packet-capture-manage-portal/figure3.png
[4]: ./media/network-watcher-packet-capture-manage-portal/figure4.png
[agent]: ./media/network-watcher-packet-capture-manage-portal/agent.png














