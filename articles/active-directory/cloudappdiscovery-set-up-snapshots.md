---
title: "在 Azure Active Directory 中建立 Cloud App Discovery 快照集報告 | Microsoft Docs"
description: "提供有關使用 Cloud App Discovery 尋找和管理應用程式、它的優勢和運作方式的資訊。"
services: active-directory
keywords: "雲端應用程式探索, 管理應用程式"
documentationcenter: 
author: curtand
manager: femila
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: curtand
ms.reviewer: nigu
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 38e3f71fb4817de7aa95b99e662856f586e72530
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="create-cloud-app-discovery-snapshot-reports"></a>建立 Cloud App Discovery 快照集報告

設定自動記錄收集器之前，請手動上傳記錄，讓 Cloud App Security 進行剖析。 如果您還沒有記錄，但想要查看記錄大致外觀的範例，請使用下面程序來下載範例記錄檔，以查看您的記錄檔應看起來的樣子。

## <a name="to-create-a-snapshot-report"></a>建立快照集報告

1. 從您組織中的使用者用來存取網際網路的防火牆和 Proxy 伺服器收集記錄檔。 在尖峰流量期間，蒐集代表您組織中使用者活動的記錄。
2. 在 [Cloud App Security 功能表列](https://portal.cloudappsecurity.com)中，選取 [探索]，然後 [建立快照集報告]。
  
  ![建立新的快照集報告](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-command.png)
3. 輸入 [報告名稱] 和 [描述]。
    
  ![新的快照集報告](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-form.png)
4. 選取您要上傳記錄檔的 [資料來源]。
5. 根據您可下載的範例來驗證您的記錄格式，並確定它的格式正確。 按一下 [檢視並確認]，然後按一下 [下載範例記錄]。 然後比較您的記錄與所提供的範例，並確定它是相容的。
  
  ![驗證記錄格式](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-verify.png)
  >  [!NOTE]
  > 快照集和自動上傳都支援 FTP 範例格式，然而只有自動上傳支援 syslog。 下載範例記錄時會下載範例 FTP 記錄。
6. 選擇您要上傳的**流量記錄**。 您可以一次最多上傳 20 個檔案。 也支援壓檔案。
  
7. 按一下 [建立] 。 上傳完成之後，可能需要一些時間，才能進行剖析和分析。 若是如此，Cloud App Discovery 會在準備就緒時傳送電子郵件通知。

8. 選取 [管理快照集報告] 並選取您的快照集報告。
  
  ![快照集報告管理](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-manage.png)

## <a name="next-steps"></a>後續步驟

* [開始使用 Azure AD 中的 Cloud App Discovery](cloudappdiscovery-get-started.md)
* [設定自動記錄上傳以便進行連續報告](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [使用自訂記錄剖析器](https://docs.microsoft.com/cloud-app-security/custom-log-parser)

