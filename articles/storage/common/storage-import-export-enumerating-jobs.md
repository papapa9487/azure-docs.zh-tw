---
title: "所有 Azure 匯入/匯出作業的清單 | MicrosoftDocs"
description: "了解如何列出訂用帳戶中所有「Azure 匯入/匯出」服務作業的清單。"
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f2e619be-1bbd-4a54-9472-9e2f70a83b64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 1977bfc0e516088310f45ecdd960287eeed2c2d8
ms.lasthandoff: 03/30/2017


---

# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>列舉 Azure 匯入/匯出服務中的作業
若要列舉訂用帳戶中的所有作業，請呼叫 [List Jobs](/rest/api/storageimportexport/jobs#Jobs_List)作業。 `List Jobs` 會傳回一份作業以及下列屬性︰

-   作業的類型 (匯入或匯出)

-   目前的作業狀態

-   作業的相關聯儲存體帳戶

## <a name="next-steps"></a>後續步驟

* [使用匯入/匯出服務 REST API](storage-import-export-using-the-rest-api.md)

