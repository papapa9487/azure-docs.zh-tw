---
title: "Azure Service Fabric Reliable Services 生命週期的概觀 | Microsoft Docs"
description: "了解 Service Fabric Reliable Services中不同的生命週期事件"
services: Service-Fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa;
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: 80eb68346dd05c256c60725eb082aa0651fe7cbd
ms.contentlocale: zh-tw
ms.lasthandoff: 08/19/2017

---

# <a name="reliable-services-lifecycle-overview"></a>Reliable Services 生命週期概觀
> [!div class="op_single_selector"]
> * [Windows 上的 C# ](service-fabric-reliable-services-lifecycle.md)
> * [在 Linux 上使用 Java](service-fabric-reliable-services-lifecycle-java.md)
>
>

在考慮 Reliable Services 的生命週期時，生命週期的基礎最重要。 一般而言：

* 在啟動期間
  * 建構服務
  * 它們有機會建構和傳回零個以上的接聽程式
  * 開啟任何傳回的接聽程式，能夠與服務進行通訊
  * 會呼叫服務的 runAsync 方法，以允許服務進行長時間執行的工作或背景工作
* 在關閉期間
  * 會取消傳遞給 runAsync 的取消權杖，並關閉接聽程式
  * 完成後，就解構服務物件本身

這些事件的確切順序還有一些細節可討論。 特別的是，根據 Reliable Service 是無狀態或具狀態而定，事件的順序可能稍有變化。 此外，對於具狀態服務，我們必須處理「主要」複本互換情況。 在此序列期間，「主要」角色會轉移至另一個複本 (或回來)，但服務不會關閉。 最後，我們必須考慮錯誤或失敗狀況。

## <a name="stateless-service-startup"></a>無狀態服務啟動
無狀態服務的生命週期相當簡單。 以下是事件的順序︰

1. 建構服務
2. 接著，兩件事平行發生︰
    - 叫用 `StatelessService.createServiceInstanceListeners()`，並「開啟」任何傳回的接聽程式 (在每個接聽程式上呼叫 `CommunicationListener.openAsync()`)
    - 會呼叫服務的 runAsync 方法 (`StatelessService.runAsync()`)
3. 如果有的話，會呼叫服務本身的 onOpenAsync 方法 (準確來說，會呼叫 `StatelessService.onOpenAsync()`。 這是不常用的覆寫，但可用)。

請務必留意，建立及開啟接聽程式的呼叫及 runAsync 之間，並沒有一定的順序。 接聽程式可以在 runAsync 啟動之前開啟。 同樣地，runAsync 也可能在通訊接聽程式開啟或建構之前便已經叫用。 如果需要任何同步處理，則留給實作者去負責。 常見的解決方案︰

* 有時，必須等到建立其他一些資訊或完成工作後，接聽程式才能運作。 針對無狀態服務，通常可以在服務的建構函式中、`createServiceInstanceListeners()` 呼叫期間，或接聽程式本身的建構過程中完成工作。
* 有時候，runAsync 中的程式碼需要等到接聽程式開啟之後才會啟動。 在此情況下，額外的協調有必要。 一個常用的解決方案，是在接聽程式內設置旗標以指出接聽程式何時完成，並在實際工作繼續之前，於 runAsync 中先檢查此旗標。

## <a name="stateless-service-shutdown"></a>無狀態服務關閉
關閉無狀態服務時，也依循相同的模式，只是順序相反：

1. 平行
    - 「關閉」任何開啟的接聽程式 (在每個接聽程式上呼叫 `CommunicationListener.closeAsync()`)
    - 取消傳遞至 `runAsync()` 的取消權杖 (檢查取消權杖的 `isCancelled` 屬性傳回 true，還有呼叫權杖的 `throwIfCancellationRequested` 方法是否會擲回 `CancellationException`)
2. 當每個接聽程式上完成 `closeAsync()`，且 `runAsync()` 也完成時，就呼叫服務的 `StatelessService.onCloseAsync()` 方法 (如果有的話) (這也是不常用的覆寫)。
3. `StatelessService.onCloseAsync()` 完成之後，就解構服務物件

## <a name="notes-on-service-lifecycle"></a>服務生命週期的注意事項
* `runAsync()` 方法和 `createServiceInstanceListeners` 呼叫都是選擇性。 一個服務可能具有其中一個、兩者或都沒有。 例如，若服務本身自行負責回應使用者呼叫，則不需要實作 `runAsync()`。 只需要通訊接聽程式及其相關聯的程式碼。 同樣地，建立和傳回通訊接聽程式是選擇性，因為服務可能只有背景工作要處理，所以只需要實作 `runAsync()`
* 服務可以成功完成 `runAsync()` 並返回。 這不算是失敗狀況，只是表示服務的背景工作完成。 對於具狀態 Reliable Services，如果服務從主要降級，然後又升級回到主要，則會再次呼叫 `runAsync()`。
* 如果服務擲回某些非預期的例外狀況而離開 `runAsync()`，則算是失敗，將會關閉服務物件並回報健全狀況錯誤。
* 雖然從這些方法進行傳回並沒有時間限制，您會立即喪失寫入的能力，因而無法完成任何實際工作。 建議您盡快在收到取消要求後傳回。 如果您的服務未於合理的時間內回應這些 API 呼叫，Service Fabric 可能會強制終止服務。 這通常只發生在應用程式升級期間或刪除服務時。 此逾時預設為 15 分鐘。
* `onCloseAsync()` 路徑失敗會導致呼叫 `onAbort()`，這是服務清除並釋放已宣告之任何資源的最後努力機會。

> [!NOTE]
> Java 中尚未支援具狀態 Reliable Services。
>
>

## <a name="next-steps"></a>後續步驟
* [Reliable Services 簡介](service-fabric-reliable-services-introduction.md)
* [Reliable Services 快速入門](service-fabric-reliable-services-quick-start.md)
* [Reliable Services 的進階用法](service-fabric-reliable-services-advanced-usage.md)

