---
title: "使用作業記錄檔對 BizTalk 服務進行疑難排解 | Microsoft Docs"
description: "使用作業記錄疑難排解 BizTalk 服務。 MABS，WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 1081a9c6-58cc-4a76-8ac8-11e5e7a6ea27
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: c0c83361f94ffd9c30d7fcc551ff4b85ad7d6fa5
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>BizTalk 服務：使用作業記錄檔進行疑難排解

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

## <a name="what-are-the-operation-logs"></a>什麼是作業記錄檔
「作業記錄檔」是 Azure 傳統入口網站提供的一個管理服務功能，可讓您檢視在 Azure 服務上執行的作業 (包括 BizTalk 服務) 的歷程記錄檔。 它可讓您檢視與 BizTalk 訂用帳戶的管理作業相關歷程資料，最遠可回溯 180 天。

> [!NOTE]
> 這項功能只會在 BizTalk 服務啟動、備份等期間，針對服務的管理作業擷取記錄檔。 這類作業無論是從 Azure 傳統入口網站還是透過 [BizTalk 服務 REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx) 來執行，都會受到追蹤。 如需使用管理服務進行追蹤的作業完整清單，請參閱[使用 Azure 管理服務進行追蹤的作業](#bizops)。<br/><br/>
> 此功能不會對 BizTalk 服務執行階段的相關活動 (例如橋接器等項目所處理的訊息) 擷取記錄檔。 若要檢視這些記錄檔，請使用 BizTalk 服務入口網站中的 [追蹤] 檢視。 如需詳細資訊，請參閱[追蹤訊息](http://msdn.microsoft.com/library/azure/hh949805.aspx)。
> 
> 

## <a name="view-biztalk-services-operation-logs"></a>檢視 BizTalk 服務作業記錄檔
1. 在 Azure 傳統入口網站中，選取 [管理服務]，然後選取 [作業記錄檔] 索引標籤。
2. 您可以根據不同的參數篩選記錄檔，例如訂用帳戶、日期範圍、服務類型 (例如 BizTalk 服務)、服務名稱或作業狀態 (例如「成功」、「失敗」)。
3. 選取核取記號以檢視篩選清單。 下圖顯示 testbiztalkservice 的相關活動：![檢視作業記錄檔][ViewLogs] 
4. 若要檢視特定作業的詳細資訊，請選取資料列，然後按一下底部工作列中的 [ **詳細資料** ]。

## <a name="bizops"></a>使用 Azure 管理服務進行追蹤的作業
下表列出可使用 Azure 管理服務進行追蹤的作業：

| 作業名稱 | 工作 |
| --- | --- |
| CreateBizTalkService |建立新 BizTalk 服務的作業 |
| DeleteBizTalkService |刪除 BizTalk 服務的作業 |
| RestartBizTalkService |重新啟動 BizTalk 服務的作業 |
| StartBizTalkService |啟動 BizTalk 服務的作業 |
| StopBizTalkService |停止 BizTalk 服務的作業 |
| DisableBizTalkService |停用 BizTalk 服務的作業 |
| EnableBizTalkService |啟用 BizTalk 服務的作業 |
| BackupBizTalkService |備份 BizTalk 服務的作業 |
| RestoreBizTalkService |從指定的備份還原 BizTalk 服務的作業 |
| SuspendBizTalkService |暫停 BizTalk 服務的作業 |
| ResumeBizTalkService |繼續 BizTalk 服務的作業 |
| ScaleBizTalkService |擴大或縮小 BizTalk 服務的作業 |
| ConfigUpdateBizTalkService |更新 BizTalk 服務組態的作業 |
| ServiceUpdateBizTalkService |將 BizTalk 服務升級或降級為不同版本的作業 |
| PurgeBackupBizTalkService |清除超過保留週期的 BizTalk 服務備份的作業 |

## <a name="see-also"></a>另請參閱
* [備份 BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=325584)
* [從備份還原 BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=325582)
* [BizTalk 服務：開發人員、基本、標準和高級版本圖表](http://go.microsoft.com/fwlink/p/?LinkID=302279)
* [BizTalk 服務：使用 Azure 傳統入口網站進行佈建](http://go.microsoft.com/fwlink/p/?LinkID=302280)
* [BizTalk 服務：佈建狀態圖](http://go.microsoft.com/fwlink/p/?LinkID=329870)
* [BizTalk 服務：儀表板、監視和調整索引標籤](http://go.microsoft.com/fwlink/p/?LinkID=302281)
* [BizTalk 服務：節流](http://go.microsoft.com/fwlink/p/?LinkID=302282)
* [BizTalk 服務：簽發者名稱和簽發者金鑰](http://go.microsoft.com/fwlink/p/?LinkID=303941)
* [如何開始使用 Azure BizTalk 服務 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png

