---
title: "Azure 中 Linux VM 的已排定事件 | Microsoft Docs"
description: "針對您的 Linux 虛擬機器，使用 Azure 中繼資料服務排定事件。"
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2017
ms.author: zivr
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 75e509a7e39f5b268ce550d0c4dea2261d4fe56f
ms.contentlocale: zh-tw
ms.lasthandoff: 08/19/2017

---

# <a name="azure-metadata-service-scheduled-events-preview-for-linux-vms"></a>Azure 中繼資料服務：Linux VM 的已排定事件 (預覽)

> [!NOTE] 
> 若您同意使用規定即可取得預覽。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

排定的事件是 Azure 中繼資料服務下的其中一項子服務， 其會呈現即將發生的事件 (例如，重新開機) 相關資訊，以便應用程式做好準備並限制中斷造成的影響。 它適用於所有 Azure 虛擬機器類型 (包括 PaaS 和 IaaS)。 排定的事件讓虛擬機器有時間執行預防性工作，以將事件的影響降至最低。 

排定的事件適用於 Windows 和 Linux VM。 如需 Windows 上已排定事件的資訊，請參閱 [Windows VM 的已排定事件](../windows/scheduled-events.md)。

## <a name="why-scheduled-events"></a>為什麼要使用排定的事件？

使用排定的事件時，您可以採取措施，限制平台起始的維護及使用者起始的動作對服務造成的影響。 

當多個執行個體間發生中斷時，多重執行個體的工作負載 (其使用複寫技術來維護狀態) 可能非常容易受到牽連。 例如中斷可能會導致耗費資源的工作 (例如，重建索引) 或甚至是複本遺失。 

在其他許多情況下，使用正常關機順序可改善整體服務可用性，例如完成 (或取消) 進行中的交易、重新指派工作給叢集中的其他 VM (手動容錯移轉)，或從網路負載平衡器集區移除虛擬機器。 

而在某些情況下，通知系統管理員有關即將發生的事件，或記錄這類事件，都有助改善裝載於雲端之應用程式的服務性。

Azure 中繼資料服務會在下列使用案例中呈現排定的事件︰
-   平台起始的維護 (例如，主機作業系統推出)
-   使用者起始的呼叫 (例如，使用者重新啟動或重新部署 VM)


## <a name="the-basics"></a>基本概念  

如果您是使用可由 VM 內存取的 REST 端點來執行虛擬機器，Azure 中繼資料服務會公開這類相關資訊。 這項資訊是透過無法路由傳送的 IP 取得，因此不會在 VM 之外公開。

### <a name="scope"></a>Scope
排定的事件會對雲端服務中的所有虛擬機器或可用性設定組中的所有虛擬機器呈現。 因此，您應該檢查事件中的 `Resources` 欄位以找出哪些 VM 即將受到影響。 

### <a name="discovering-the-endpoint"></a>探索端點
如果虛擬機器是在虛擬網路 (VNet) 中建立的情況下，可從無法路由傳送的靜態 IP (`169.254.169.254`) 取得中繼資料服務。
如果虛擬機器不是在虛擬網路中建立，則針對雲端服務和傳統 VM 的預設案例，需要其他邏輯來探索可使用的端點。 請參閱此範例以了解如何[探索主機端點](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)。

### <a name="versioning"></a>版本控制 
執行個體中繼資料服務已建立版本。 版本是必要項目，且目前版本為 `2017-03-01`。

> [!NOTE] 
> 先前排定事件的預覽版支援作為 API 版本的 {latest}。 此格式將不再受到支援且之後會遭到取代。

### <a name="using-headers"></a>使用標頭
查詢中繼資料服務時，您必須提供 `Metadata: true` 標頭以免不小心重新導向要求。

### <a name="enabling-scheduled-events"></a>啟用排定的事件
第一次要求排定的事件時，Azure 會在您的虛擬機器上隱含啟用此功能。 因此，您可能會在第一次呼叫中遇到長達兩分鐘的延遲回應。

### <a name="user-initiated-maintenance"></a>使用者起始的維護
使用者透過 Azure 入口網站、API、CLI 或 PowerShell 起始的虛擬機器維護，將會產生「排定的事件」。 這可讓您測試應用程式中的維護準備邏輯，讓應用程式可以為使用者啟動的維護預作準備。

重新啟動虛擬機器會排定 `Reboot` 類型的事件。 重新部署虛擬機器會排定 `Redeploy` 類型的事件。

> [!NOTE] 
> 目前最多可同時排程 10 個使用者起始的維護作業。 「排定的事件」正式運作之前，將會放寬這項限制。

> [!NOTE] 
> 使用者起始的維護會導致「排程的事件」，這個狀況目前尚無法設定。 預計在未來版本中，將可針對此狀況作設定。

## <a name="using-the-api"></a>使用 API

### <a name="query-for-events"></a>查詢事件
您只要進行下列呼叫，即可查詢排定的事件：

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01
```

回應包含排定的事件陣列。 空白陣列表示目前沒有任何排定的事件。
在有排定事件的情況下，回應會包含事件陣列︰ 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},              
        }
    ]
}
```

### <a name="event-properties"></a>事件屬性
|屬性  |  說明 |
| - | - |
| EventId | 此事件的全域唯一識別碼。 <br><br> 範例： <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | 此事件造成的影響。 <br><br> 值： <br><ul><li> `Freeze`︰虛擬機器已排定會暫停幾秒鐘。 CPU 會暫止，但不會影響記憶體、開啟的檔案或網路連線。 <li>`Reboot`：虛擬機器已排定要重新開機 (非持續性記憶體都會遺失)。 <li>`Redeploy`︰虛擬機器已排定要移至另一個節點 (暫時磁碟都會遺失)。 |
| ResourceType | 受此事件影響的資源類型。 <br><br> 值： <ul><li>`VirtualMachine`|
| 資源| 受此事件影響的資源清單。 其中最多只能包含來自一個[更新網域](manage-availability.md)的機器，但不能包含更新網域中的所有機器。 <br><br> 範例： <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| 事件狀態 | 此事件的狀態。 <br><br> 值： <ul><li>`Scheduled`︰此事件已排定在 `NotBefore` 屬性所指定的時間之後啟動。<li>`Started`︰已啟動事件。</ul> 如果未提供任何 `Completed` 或類似的狀態，事件完成時，將不會再傳回事件。
| NotBefore| 自此之後可能會啟動此事件的時間。 <br><br> 範例： <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>事件排定
系統會根據事件類型，為每個事件在未來安排最少的時間量。 事件的 `NotBefore` 屬性會反映這個時間。 

|EventType  | 最短時間通知 |
| - | - |
| 凍結| 15 分鐘 |
| 重新啟動 | 15 分鐘 |
| 重新部署 | 10 分鐘 |

### <a name="starting-an-event"></a>啟動事件 

在您得知即將發生的事件，並完成正常關機邏輯之後，即可使用 `EventId` 向中繼資料服務進行 `POST` 呼叫，以核准未處理的事件。 對 Azure 來說，這意謂著它可以將通知時間縮到最短 (可能的話)。 

```
curl -H Metadata:true -X POST -d '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01
```

> [!NOTE] 
> 認可事件時，即會允許事件中所有 `Resources` 的事件繼續進行，而不僅是認可此事件的虛擬機器。 因此，您可以選擇一個領導者來協調認可；最簡單的方法是選擇 `Resources` 欄位的第一部機器。




## <a name="python-sample"></a>Python 範例 

下列範例會查詢中繼資料服務來找出已排定的事件，並核准每個未處理的事件。

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01"
headers = "{Metadata:true}"
this_host = socket.gethostname()

def get_scheduled_events():
   req = urllib2.Request(metadata_url)
   req.add_header('Metadata', 'true')
   resp = urllib2.urlopen(req)
   data = json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ","_")
        if this_host in resources:
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            # Add logic for handling events here

def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)
   
if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>後續步驟 

- 深入了解[執行個體中繼資料服務](instance-metadata-service.md)中提供的 API。
- 了解 [Azure 中 Linux 虛擬機器預定進行的維修](planned-maintenance.md)。

