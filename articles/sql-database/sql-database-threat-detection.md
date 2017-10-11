---
title: "威脅偵測 - Azure SQL Database | Microsoft Docs"
description: "威脅偵測會偵測異常資料庫活動，指出資料庫有潛在的安全性威脅。"
services: sql-database
documentationcenter: 
author: rmatchoro
manager: jhubbard
editor: v-romcal
ms.assetid: b50d232a-4225-46ed-91e7-75288f55ee84
ms.service: sql-database
ms.custom: security
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 06/19/2017
ms.author: ronmat; ronitr
ms.openlocfilehash: bd3de9ed0131edc683763b0fe7f4a2ae74533944
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="sql-database-threat-detection"></a>SQL Database 威脅偵測

SQL 威脅偵測會偵測意圖存取或攻擊資料庫，並可能會造成損害的異常活動。

## <a name="overview"></a>概觀

SQL 威脅偵測提供新的一層安全性，在發生異常活動時會提供安全性警示，讓客戶偵測並回應潛在威脅。  一旦有可疑活動、潛在弱點、SQL 插入式攻擊和異常資料庫存取模式發生，使用者將會收到警示。 SQL 威脅偵測警示會提供可疑活動的詳細資料，以及如何調查與降低威脅的建議。 使用者可以使用 [SQL Database 稽核](sql-database-auditing.md)來探索可疑的事件，以判斷事件的原因是否是有人嘗試存取、破壞或利用資料庫中的資料。 您不必是安全性專家，也不需要管理進階的安全性監視系統，威脅偵測讓您輕鬆解決資料庫的潛在威脅。

例如，SQL 插入式攻擊是網際網路上常見的 Web 應用程式安全性問題之一，用於攻擊資料導向應用程式。 攻擊者利用應用程式弱點將惡意的 SQL 陳述式插入應用程式輸入欄位，破壞或修改資料庫中的資料。

SQL 威脅偵測整合了警示與 [Azure 資訊安全中心](https://azure.microsoft.com/en-us/services/security-center/)。每部受保護 SQL Database 伺服器的收費與 Azure 資訊安全中心標準層相同，全部是每月每個節點 $15，其中每部受保護的 SQL Database 伺服務各會計為一個節點。 敬邀您免費試用 60 天。 

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>使用 Azure 入口網站為資料庫設定威脅偵測
1. 啟動 Azure 入口網站，位址是 [https://portal.azure.com](https://portal.azure.com)。
2. 瀏覽至您要監視的 SQL Database 的組態刀鋒視窗。 在 [設定] 刀鋒視窗中，選取 [稽核和威脅偵測]。 
    ![導覽窗格][1]
3. 在 [稽核與威脅偵測] 組態刀鋒視窗中，[開啟] 稽核，這會顯示威脅偵測設定。
  
    ![導覽窗格][2]
4. [開啟]  威脅偵測。
5. 設定在偵測到異常資料庫活動時將收到安全性警示的電子郵件清單。
6. 按一下 [稽核與威脅偵測] 刀鋒視窗中的 [儲存]，以儲存新的或已更新的稽核與威脅偵測設定。
       
    ![導覽窗格][3]

## <a name="set-up-threat-detection-using-powershell"></a>使用 PowerShell 設定威脅偵測

如需指令碼範例，請參閱[使用 PowerShell 設定稽核與威脅偵測](scripts/sql-database-auditing-and-threat-detection-powershell.md)。

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>偵測到可疑事件時探索異常資料庫活動
1. 偵測到異常資料庫活動時，您將收到電子郵件通知。 <br/>
   電子郵件將提供可疑安全性事件的相關資訊，包括異常活動的性質、資料庫名稱、伺服器名稱、應用程式名稱和事件時間。 此外，該電子郵件還會提供可能原因和建議動作的相關資訊，以協助您調查和減輕資料庫的潛在威脅。<br/>
     
    ![導覽窗格][4]
2. 電子郵件警示包含 SQL 稽核記錄的直接連結。 按一下此連結會啟動 Azure 入口網站，並開啟可疑活動發生時間前後的 SQL 稽核記錄。 按一下稽核記錄可檢視可疑資料庫活動的詳細資料，讓您輕鬆找出遭到執行的 SQL 陳述式 (存取的人、動作和時間)，以及判斷該事件屬於正當或是惡意 (例如SQL 插入式攻擊的應用程式弱點遭到利用或有人破壞機密資料等)。<br/>
   ![導覽窗格][5]


## <a name="explore-threat-detection-alerts-for-your-database-in-the-azure-portal"></a>在 Azure 入口網站中探索資料庫的威脅偵測警示

SQL Database 威脅偵測將自有的警示與 [Azure 資訊安全中心](https://azure.microsoft.com/en-us/services/security-center/)整合。 在 Azure 入口網站中，資料庫刀鋒視窗內的 SQL 動態安全性圖格會追蹤威脅 (作用中) 的狀態。 

   ![導覽窗格][6]
   
1. 按一下 SQL 安全性圖格會啟動 Azure 資訊安全中心的警示刀鋒視窗，以及提供在資料庫中偵測到的 SQL 威脅 (作用中) 概觀。 

  ![導覽窗格][7]

2. 按一下特定警示會提供其他詳細資料和調查此威脅的建議，並對未來的威脅採取補救措施。

  ![導覽窗格][8]


## <a name="next-steps"></a>後續步驟

* 若要深入了解威脅偵測，請造訪 [Azure 部落格](https://azure.microsoft.com/en-us/blog/azure-sql-database-threat-detection-general-availability-in-spring-2017/) 
* 深入了解 [Azure SQL Database 稽核](sql-database-auditing.md)
* 深入了解 [Azure 資訊安全中心](https://docs.microsoft.com/en-us/azure/security-center/security-center-intro)
* 如需有關價格的詳細資訊，請參閱 [SQL Database 價格頁面](https://azure.microsoft.com/en-us/pricing/details/sql-database/)  
* 如需 PowerShell 指令碼範例，請參閱[使用 PowerShell 設定稽核與威脅偵測](scripts/sql-database-auditing-and-threat-detection-powershell.md)



<!--Image references-->
[1]: ./media/sql-database-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-database-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-database-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-database-threat-detection/4_td_email.png
[5]: ./media/sql-database-threat-detection/5_td_audit_record_details.png
[6]: ./media/sql-database-threat-detection/6_td_security_tile_view_alerts.png
[7]: ./media/sql-database-threat-detection/7_td_SQL_security_alerts_list.png
[8]: ./media/sql-database-threat-detection/8_td_SQL_security_alert_details.png


