---
title: "取消及刪除 Azure 匯入/匯出作業 | Microsoft Docs"
description: "了解如何取消及刪除 Microsoft Azure 匯入/匯出服務的作業。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: fd3d66f0-1dbb-4c75-9223-307d5abaeefc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: e0a7ff391e5a03ed563912dea54c7cfe73111bcf
ms.lasthandoff: 03/30/2017


---

# <a name="canceling-and-deleting-azure-importexport-jobs"></a>取消及刪除 Azure 匯入/匯出作業

您可以藉由呼叫 [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update) 作業並將 `CancelRequested` 元素設定為 `true`，要求作業為 `Packaging` 狀態之前將它取消。 會儘可能將作業取消。 如果磁碟機正在傳輸資料，即使已經要求取消，資料可能會繼續傳送。

 已取消的作業會移到 `Completed` 狀態並保留 90 天，屆時則會刪除。

 若要刪除作業，在傳送作業之前請先呼叫 [Delete Job](/rest/api/storageimportexport/jobs#Jobs_Delete) 作業，(也就是處於 `Creating` 狀態時)。 您也可以在作業為 `Completed` 狀態時將它刪除。 在刪除作業之後，便無法再透過 REST API 或 Azure 入口網站存取其資訊和狀態。

## <a name="next-steps"></a>後續步驟

* [使用匯入/匯出服務 REST API](storage-import-export-using-the-rest-api.md)

