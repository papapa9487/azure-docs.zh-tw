---
title: "使用 Azure 網路監看員管理封包擷取 - Azure CLI 1.0 | Microsoft Docs"
description: "此頁面說明如何使用 Azure CLI 1.0 管理網路監看員的封包擷取功能"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 2124dadd1fc6b5d57250c09d31155a4b421fac26
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-cli-10"></a>使用 Azure CLI 1.0，利用 Azure 網路監看員管理封包擷取

> [!div class="op_single_selector"]
> - [Azure 入口網站](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

網路監看員封包擷取可讓您建立擷取工作階段來追蹤虛擬機器的流入和流出流量。 系統會為擷取工作階段提供篩選器，以確保您只會擷取到您想要的流量。 封包擷取有助於被動和主動地診斷網路異常。 其他用途包括收集網路統計資料、取得有關網路入侵的資訊，以及偵錯用戶端與伺服器間的通訊等等。 藉由能夠從遠端觸發封包擷取，這項功能可以減輕在所需機器上手動執行封包擷取的工作負擔，進而省下寶貴的時間。

本文使用跨平台 Azure CLI 1.0，這適用於 Windows、Mac 和 Linux。

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
> 封包擷取需要在虛擬機器上執行代理程式。 代理程式會安裝為擴充功能。 如需 VM 擴充功能的指示，請瀏覽[虛擬機器擴充功能和功能](../virtual-machines/windows/extensions-features.md)。

## <a name="install-vm-extension"></a>安裝 VM 擴充功能

### <a name="step-1"></a>步驟 1

執行 `azure vm extension set` Cmdlet 在客體虛擬機器上安裝封包擷取代理程式。

若為 Windows 虛擬機器：

```azurecli
azure vm extension set -g resourceGroupName -m virtualMachineName -p Microsoft.Azure.NetworkWatcher -r AzureNetworkWatcherExtension -n NetworkWatcherAgentWindows -o 1.4
```

若為 Linux 虛擬機器：

```azurecli
azure vm extension set -g resourceGroupName -m virtualMachineName -p Microsoft.Azure.NetworkWatcher -r AzureNetworkWatcherExtension -n NetworkWatcherAgentLinux -o 1.4
````

### <a name="step-2"></a>步驟 2

為了確定是否已安裝代理程式，請執行 `vm extension get` Cmdlet，並將資源群組和虛擬機器名稱傳遞給它。 檢查結果清單以確定代理程式已安裝。

```azurecli
azure vm extension get -g resourceGroupName -m virtualMachineName
```

下列範例是執行 `azure vm extension get` 所得回應的範例

```
info:    Executing command vm extension get
+ Looking up the VM "virtualMachineName"
data:    Publisher                       Name                        Version  State
data:    ------------------------------  -----------------------     -------  ---------
data:    Microsoft.Azure.NetworkWatcher  NetworkWatcherAgentWindows  1.4      Succeeded
info:    vm extension get command OK
```

## <a name="start-a-packet-capture"></a>啟動封包擷取

完成上述步驟之後，虛擬機器上便已安裝封包擷取代理程式。

### <a name="step-1"></a>步驟 1

下一步是擷取網路監看員執行個體。 此變數會在步驟 4 傳遞至 `network watcher show` Cmdlet。

```azurecli
azure network watcher show -g resourceGroup -n networkWatcherName
```

### <a name="step-2"></a>步驟 2

擷取儲存體帳戶。 此儲存體帳戶會用來儲存封包擷取檔案。

```azurecli
azure storage account list
```

### <a name="step-3"></a>步驟 3

可使用篩選器來限制封包擷取所儲存的資料。 下列範例會設定具有多個篩選器的封包擷取。  前三個篩選器只會收集從本機 IP 10.0.0.3 流往目的地連接埠 20、80 和 443 的連出 TCP 流量。  最後一個篩選器只會收集 UDP 流量。

```azurecli
azure network watcher packet-capture create -g resourceGroupName -w networkWatcherName -n packetCaptureName -t targetResourceId -o storageAccountResourceId -f "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

一個封包擷取可以定義多個篩選器。 如果您使用複雜的篩選器結構，則最好使用 json 檔案形式的篩選器來避免發生語法錯誤。 比方說，使用旗標 "-r" (而不是 "-f") 並傳遞包含下列篩選器之 json 檔案的位置︰

```json
[
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"20"
    },
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"80"
    },
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"443"
    },
    {
        "protocol":"UDP"
    }
]
```


下列範例是執行 `network watcher packet-capture create` Cmdlet 後預期會得到的輸出。

```
data:    Name                            : packetCaptureName
data:    Etag                            : W/"d59bb2d2-dc95-43da-b740-e0ef8fcacecb"
data:    Target                          : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/testVM
data:    Bytes To Capture Per Packet     : 0
data:    Total Bytes Per Session         : 1073741824
data:    Time Limit In Seconds           : 18000
data:    Storage Location:
data:      Storage Id                    : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/testStorage
data:      Storage Path                  : https://testStorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testRG/providers/microsoft.compute/virtualmachines/testVM/2017/02/17/packetcapture_01_21_18_145.cap
data:    Filters                         : []
data:    Provisioning State              : Succeeded
info:    network watcher packet-capture create command OK
```

## <a name="get-a-packet-capture"></a>取得封包擷取

執行 `network watcher packet-capture show` Cmdlet 以擷取目前正在執行或已完成之封包擷取的狀態。

```azurecli
azure network watcher packet-capture show -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

下列範例是 `network watcher packet-capture show` Cmdlet 的輸出。 下列範例是在擷取完成後。 PacketCaptureStatus 值為 Stopped，而 StopReason 為 TimeExceeded。 這個值說明封包擷取已順利完成，並執行了它的時間。

```
data:    Name                            : packetCaptureName
data:    Etag                            : W/"d59bb2d2-dc95-43da-b740-e0ef8fcacecb"
data:    Target                          : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/testVM
data:    Bytes To Capture Per Packet     : 0
data:    Total Bytes Per Session         : 1073741824
data:    Time Limit In Seconds           : 18000
data:    Storage Location:
data:      Storage Id                    : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/testStorage
data:      Storage Path                  : https://testStorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testRG/providers/microsoft.compute/virtualmachines/testVM/2017/02/17/packetcapture_01_21_18_145.cap
data:    Filters                         : []
data:    Provisioning State              : Succeeded
info:    network watcher packet-capture show command OK
```

## <a name="stop-a-packet-capture"></a>停止封包擷取

藉由執行 `network watcher packet-capture stop` Cmdlet，如果擷取工作階段正在進行中，則會加以停止。

```azurecli
azure network watcher packet-capture stop -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

> [!NOTE]
> 此 Cmdlet 若執行於目前正在執行的擷取工作階段或已停止的現有工作階段，則不會傳回任何回應。

## <a name="delete-a-packet-capture"></a>刪除封包擷取

```azurecli
azure network watcher packet-capture delete -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

> [!NOTE]
> 刪除封包擷取不會刪除儲存體帳戶中的檔案。

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
