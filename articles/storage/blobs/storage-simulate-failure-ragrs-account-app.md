---
title: "模擬在 Azure 中存取讀取權限備援儲存體時失敗 | Microsoft Docs"
description: "模擬存取讀取權限異地備援儲存體時發生錯誤"
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 2919eb0e301000b53f4f63799e9c65aad45ca9f2
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2017
---
# <a name="simulate-a-failure-in-accessing-read-access-redundant-storage"></a>模擬存取讀取權限備援儲存體時失敗

本教學課程是一個系列的第二部分。 在本教學課程中，會對 [read-access geo-redundant](../common/storage-redundancy.md#read-access-geo-redundant-storage) (RA-GRS) 儲存體帳戶的要求，插入使用 Fiddler 的失敗回應，以模擬失敗情況並使應用程式從次要端點讀取。

![應用程式案例](media/storage-simulate-failure-ragrs-account-app/scenario.png)

在本系列的第二部分中，您將瞭解如何：

> [!div class="checklist"]
> * 執行和暫停應用程式
> * 模擬失敗
> * 模擬主要端點還原

## <a name="prerequisites"></a>必要條件

若要完成本教學課程：

* [下載並安裝 Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

若要完成本教學課程，您必須先完成上一個儲存體教學課程：[使用 Azure 儲存體讓應用程式資料具有高可用性][previous-tutorial]。

## <a name="launch-fiddler"></a>啟動 Fiddler

開啟 Fiddler，選取 [規則] 和 [自訂規則]。

![自訂 Fiddler 規則](media/storage-simulate-failure-ragrs-account-app/figure1.png)

Fiddler ScriptEditor 啟動，顯示 **SampleRules.js** 檔案。 此檔案是用來自訂 Fiddler。 請將下列範例程式碼貼至 `OnBeforeResponse` 函式中。 新程式碼會被轉為註解，以確保其所建立的邏輯不會立即實作。 完成後，選取 [檔案] 和 [儲存] 以儲存變更。

```javascript
    /*
        // Simulate data center failure
        // After it is successfully downloading the blob, pause the code in the sample,
        // uncomment these lines of script, and save the script.
        // It will intercept the (probably successful) responses and send back a 503 error. 
        // When you're ready to stop sending back errors, comment these lines of script out again 
        //     and save the changes.

        if ((oSession.hostname == "contosoragrs.blob.core.windows.net") 
            && (oSession.PathAndQuery.Contains("HelloWorld"))) {
            oSession.responseCode = 503;  
        }
    */
```

![貼上自訂規則](media/storage-simulate-failure-ragrs-account-app/figure2.png)

## <a name="start-and-pause-the-application"></a>啟動和暫停應用程式

在 Visual Studio 中，按下 **F5** 或選取 [啟動]，開始對應用程式進行偵錯。 應用程式開始從主要端點讀取時，在主控台視窗中按下**任意鍵**以暫停應用程式。

## <a name="simulate-failure"></a>模擬失敗

暫停應用程式後，現可將在上個步驟中儲存於 Fiddler 的自訂規則取消註解。 此範例程式碼會尋找 RA-GRS 儲存體帳戶要求，若路徑包含映像名稱 `HelloWorld`，則其會傳回 `503 - Service Unavailable` 回應程式碼。

瀏覽至 Fiddler，然後選取 [規則] -> [自訂規則...]。將下列幾行取消註解，並將 `STORAGEACCOUNTNAME` 取代為您儲存體帳戶的名稱。 選取 [檔案] -> [儲存]，以儲存變更。

```javascript
         if ((oSession.hostname == "STORAGEACCOUNTNAME.blob.core.windows.net")
         && (oSession.PathAndQuery.Contains("HelloWorld"))) {
         oSession.responseCode = 503;
         }
```

若要繼續執行應用程式，請按下**任意鍵**。

應用程式再次開始執行後，針對主要端點的要求即會開始發生失敗。 應用程式會嘗試重新連線至主要端點 5 次。 超過五次的失敗嘗試閥值後，其會從次要唯讀端點要求映像。 應用程式從次要端點成功取出 20 次映像後，即會嘗試連線至主要端點。 若仍然無法連線至主要端點，則應用程式會繼續從次要端點讀取。 此模式即是在上個教學課程中述及的[斷路器模式](/azure/architecture/patterns/circuit-breaker.md)。

![貼上自訂規則](media/storage-simulate-failure-ragrs-account-app/figure3.png)

## <a name="simulate-primary-endpoint-restoration"></a>模擬主要端點還原

使用上個步驟中設定的 Fiddler 自訂規則集，會使主要端點要求失敗。 若要再次模擬主要端點功能，請移除邏輯以插入 `503` 錯誤。

若要暫停應用程式，請按下**任意鍵**。

### <a name="remove-the-custom-rule"></a>移除自訂規則

瀏覽至 Fiddler，然後選取 [規則] 和 [自訂規則...]。註解或移除 `OnBeforeResponse` 函式中的自訂邏輯，並保留預設函式。 選取 [檔案] 和 [儲存] 以儲存變更。

![移除自訂規則](media/storage-simulate-failure-ragrs-account-app/figure5.png)

完成時，請按下**任意鍵**繼續執行應用程式。 此應用程式會繼續從主要端點讀取，直至達到 999 次讀取為止。

![繼續執行應用程式](media/storage-simulate-failure-ragrs-account-app/figure4.png)

## <a name="next-steps"></a>後續步驟

在系列的第二部分中，您已瞭解模擬失敗以測試讀取權限異地備援儲存體的相關資訊，例如：

> [!div class="checklist"]
> * 執行和暫停應用程式
> * 模擬失敗
> * 模擬主要端點還原

遵循以下連結以查看預先建立的儲存體範例。

> [!div class="nextstepaction"]
> [Azure 儲存體指令碼範例](storage-samples-blobs-cli.md)

[previous-tutorial]: storage-create-geo-redundant-storage.md