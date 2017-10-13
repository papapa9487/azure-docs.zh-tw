---
title: "BizTalk 服務在每個狀態下可執行的工作 | Microsoft Docs"
description: "不同 MABS 狀態允許的動作/作業：停止、啟動、重新啟動、暫停、繼續、刪除、調整、更新組態和備份"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: aea738f3-ec76-4099-a41b-e17fea9e252f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2016
ms.author: mandia
ms.openlocfilehash: 918154852cf4ee1b80792eaddb588f5b0237da7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="what-you-can-and-cant-do-using-the-biztalk-service-state"></a>使用 BizTalk 服務狀態可執行以及不可執行的作業分別有哪些

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

視 BizTalk 服務的目前狀態而定，有些作業是可以或無法在 BizTalk 服務上執行。

例如，您在 Azure 傳統入口網站中佈建新的 BizTalk 服務。 順利完成時，BizTalk 服務會處於 `active` 狀態。 在作用中狀態下，您可以停止、暫停及刪除 BizTalk 服務。 如果您停止 BizTalk 服務，但停止失敗，則 BizTalk 服務會進入 `StopFailed` 狀態。 在 `StopFailed` 狀態下，您可以重新啟動 BizTalk 服務。 如果您嘗試不允許的作業 (例如繼續執行)，則會發生下列錯誤：

`Operation not allowed`

## <a name="view-the-possible-states"></a>檢視可能的狀態

下表列出當 BizTalk 服務處於特定狀態時可執行的作業或動作。 ✔ 表示可在該狀態下執行作業。 空白項目表示無法在此狀態下執行作業。

| 服務狀態 | Start | 停止 | 重新啟動 | 暫止 | 繼續 | 刪除 | 調整 | 更新 <br/> 組態 | 備份 |
| --- | --- | --- | --- | --- | --- | --- |--- | --- | --- |
| Active |  | ✔ | ✔ | ✔ |  | ✔ |✔ |✔ |✔ |
| 已停用 |  |  |  |  |  | ✔ | |  |  | 
| 暫止 |  |  |  |  | ✔ | ✔ | |  | ✔ |
| 已停止 | ✔ |  | ✔ |  |  | ✔ | |  | ✔ |
| 服務更新失敗 |  |  |  |  |  | ✔ | |  |  | 
| 停用失敗 |  |  |  |  |  | ✔ | |  |  | 
| 啟用失敗 |  |  |  |  |  | ✔ | |  |  | 
| 啟動失敗 <br/> 停止失敗 <br/> 重新啟動失敗 | ✔ | ✔ | ✔ |  |  | ✔ | | ✔ | |
| 暫止失敗 <br/> 繼續失敗|  |  |  | ✔ | ✔ | ✔ | |  |  | 
| 建立失敗 <br/> 還原失敗 |  |  |  |  |  | ✔ | |  |  | 
| 設定更新失敗  |  |  | ✔ |  |  | ✔ | |✔ | |
| 調整失敗 |  |  |  |  |  | ✔ |✔ | |  |  | 



## <a name="see-also"></a>另請參閱
* [使用 Azure 傳統入口網站建立 BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
* [您可以在 BizTalk 服務的儀表板、監視和調整索引標籤中執行哪些作業](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [您可以從 BizTalk 服務中的開發人員、基本、標準和高級版本取得哪些功能](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [如何備份和還原 BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk 服務中說明的節流](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
* [針對 BizTalk 服務擷取服務匯流排以及存取控制簽發者名稱和簽發者金鑰值](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
* [如何開始使用 Azure BizTalk 服務 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

