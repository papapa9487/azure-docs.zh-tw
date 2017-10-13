---
title: "Azure Service Fabric Reliable State Manager 與 Reliable Collection 內部 | Microsoft Docs"
description: "深入了解 Reliable Collection 概念和 Azure Service Fabric 設計。"
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: mcoskun
ms.openlocfilehash: d607449a16e886337ab1bd96213fbb4231124353
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-fabric-reliable-state-manager-and-reliable-collection-internals"></a>Azure Service Fabric Reliable State Manager 與 Reliable Collection 內部
本文件深入探討 Reliable State Manager 和 Reliable Collections，以了解核心元件在幕後的運作方式。

> [!NOTE]
> 這份文件仍在處理中。 新增對這篇文章的評論，告訴我們您想要深入了解哪些主題。
>

##  <a name="local-persistence-model-log-and-checkpoint"></a>本機持續性模型︰記錄和檢查點
可靠的狀態管理員和可靠的集合會遵循持續性模型，該模型稱為「記錄檔和檢查點」(Log and Checkpoint)。
在此模型中，每個狀態變更會先記錄在磁碟上，然後套用在記憶體中。
完成狀態本身也只是偶爾保存 (也稱為 檢查點)。
好處是差異會轉變成磁碟上的只開頭附加循序寫入以改善效能。

為了進一步了解「記錄檔和檢查點」模型，我們來看看無限磁碟的案例。
可靠的狀態管理員會在複寫作業之前，記錄每一項作業。
記錄可讓 Reliable Collections 只在記憶體中套用作業。
由於記錄會保存下來，即使複本失敗而必須重新啟動，Reliable State Manager 在其記錄中仍具有足夠的資訊，以重新執行複本已遺失的所有作業。
由於磁碟是無限的，因此永遠都不需要移除記錄檔記錄，而可靠的集合就只需要管理記憶體內部的狀態。

現在讓我們看看磁碟有限的案例。
隨著記錄檔一直累積記錄，可靠的狀態管理員會用完磁碟空間。
在這種情況發生前，可靠的狀態管理員必須截斷其記錄檔，以騰出空間給較新的記錄。
Reliable State Manager 會要求 Reliable Collections 對磁碟進行其記憶體內部狀態的檢查點檢查。
此時，Reliable Collections 會持續其記憶體中狀態。
一旦可靠的集合完成其檢查點檢查，可靠的狀態管理員即可截斷記錄檔以釋放磁碟空間。
當複本必須重新啟動時，Reliable Collections 會復原其檢查點的狀態，而 Reliable State Manager 則會復原並播放在最後一個檢查點之後發生的所有狀態變更。

檢查點新增另一個值可改善一般情況下的復原時間。 記錄包含最後一個檢查點之後發生的所有作業。
因此它可能包含某個項目的多個版本，如 Reliable Dictionary 中特定資料列的多個值。
相較之下，Reliable Collection 只會對每個索引鍵值的最新版本進行檢查點檢查。

## <a name="next-steps"></a>後續步驟
* [交易和鎖定](service-fabric-reliable-services-reliable-collections-transactions-locks.md)

