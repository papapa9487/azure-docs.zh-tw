---
title: "開始使用 SQL 資料倉儲威脅偵測"
description: "如何開始使用威脅偵測"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: c9073dd9-6c62-4735-8457-dfb9f859c900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: security
ms.date: 10/31/2016
ms.author: rortloff;barbkess
ms.openlocfilehash: f4a2376fe4fb710d031c35ca7fdbf4c7bb0f3caa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-threat-detection"></a>開始使用威脅偵測
> [!div class="op_single_selector"]
> * [稽核](sql-data-warehouse-auditing-overview.md)
> * [威脅偵測](sql-data-warehouse-security-threat-detection.md)
> 
> 

## <a name="overview"></a>Overview
威脅偵測會偵測異常資料庫活動，指出資料庫有潛在的安全性威脅。 威脅偵測處於預覽階段，SQL 資料倉儲支援此功能。

威脅偵測提供新的一層安全性，在發生異常活動時會提供安全性警示，讓客戶偵測並回應潛在威脅。 使用者可以使用 [Azure SQL 資料倉儲稽核](sql-data-warehouse-auditing-overview.md) 來探查可疑的事件，以判斷這些事件是否是因為有人嘗試存取、破壞或利用資料倉儲中的資料而造成。
您不必是安全性專家，也不需要管理進階的安全性監視系統，威脅偵測讓您輕鬆解決資料倉儲的潛在威脅。

威脅偵測會偵測異常資料庫活動，指出潛在的 SQL 插入式攻擊。 SQL 插入式攻擊是網際網路上常見的 Web 應用程式安全性問題之一，用於攻擊資料導向應用程式。 攻擊者利用應用程式弱點將惡意的 SQL 陳述式插入應用程式輸入欄位，以破壞或修改資料庫中的資料。

## <a name="set-up-threat-detection-for-your-database"></a>設定資料庫的威脅偵測
1. 啟動 Azure 入口網站，位址是 [https://portal.azure.com](https://portal.azure.com)。
2. 瀏覽至您要監視的 SQL 資料倉儲的組態刀鋒視窗。 在 [設定] 刀鋒視窗中，選取 [稽核和威脅偵測]。
   
    ![導覽窗格][1]
3. 在 [稽核和威脅偵測] 組態刀鋒視窗中，[開啟] 稽核，將會顯示威脅偵測設定。
   
    ![導覽窗格][2]
4. [開啟]  威脅偵測。
5. 設定在偵測到異常資料倉儲活動時，將收到安全性警示的電子郵件清單。
6. 在 [稽核和威脅偵測] 組態刀鋒視窗中按一下 [儲存]，以儲存新的或更新的稽核和威脅偵測原則。
   
    ![導覽窗格][3]

## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>偵測到可疑事件時探索異常資料倉儲活動
1. 偵測到異常資料庫活動時，您將收到電子郵件通知。 <br/>
   電子郵件將提供可疑安全性事件的相關資訊，包括異常活動的性質、資料庫名稱、伺服器名稱和事件時間。 此外，還會提供可能原因和建議動作的相關資訊，以協助您調查和減輕資料庫的潛在威脅。<br/>
   
    ![導覽窗格][4]
2. 在電子郵件中，按一下 [Azure SQL 稽核記錄檔]  連結會啟動 Azure 傳統入口網站，並顯示可疑事件前後的相關稽核記錄。
   
    ![導覽窗格][5]
3. 按一下稽核記錄以檢視可疑資料庫活動的詳細資訊，例如 SQL 陳述式、失敗原因和用戶端的 IP。
   
    ![導覽窗格][6]
4. 在 [稽核記錄] 刀鋒視窗中，按一下 [在 Excel 中開啟] 開啟預先設定的 Excel 範本，以匯入可疑事件前後的稽核記錄，執行更深入的分析。<br/>
   **附註：**在 Excel 2010 或更新版本中，需要有 Power Query 和 [快速合併] 設定
   
    ![導覽窗格][7]
5. 若要設定 [快速合併] 設定 - 在 [POWER QUERY] 功能區索引標籤中，選取 [選項] 以顯示 [選項] 對話方塊。 選取 [隱私權] 區段，選擇第二個選項 - [忽略隱私權等級並可能改善效能]：
   
    ![導覽窗格][8]
6. 若要載入 SQL 稽核記錄檔，請確定 [設定] 索引標籤中的參數已正確設定，然後選取 [資料] 功能區，並按一下 [全部重新整理] 按鈕。
   
    ![導覽窗格][9]
7. 結果會出現在 [SQL 稽核記錄檔]  工作表，可讓您對偵測到的異常活動執行更深入的分析，減輕應用程式中的安全性事件造成的影響。

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-data-warehouse-security-threat-detection/4_td_email.png
[5]: ./media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png
[6]: ./media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png
[7]: ./media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png
[8]: ./media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png
[9]: ./media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png
