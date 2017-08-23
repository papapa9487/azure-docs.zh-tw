---
title: "適用於 PostgreSQL 的 Azure 資料庫中的伺服器記錄 | Microsoft Docs"
description: "在適用於 PostgreSQL 的 Azure 資料庫中產生查詢和錯誤記錄。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c384171c806e929a425e4299c81f0fb2321dc91b
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017

---
# <a name="server-logs-in-azure-database-for-postgresql"></a>適用於 PostgreSQL 的 Azure 資料庫中的伺服器記錄 
適用於 PostgreSQL 的 Azure 資料庫會產生查詢和錯誤記錄。 不過，不支援存取交易記錄。 這些記錄可用來識別、疑難排解和修復設定錯誤和次佳效能。 如需詳細資訊，請參閱[錯誤報告和記錄 (英文)](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html)。

## <a name="access-server-logs"></a>存取伺服器記錄
您可以使用 Azure 入口網站、[Azure CLI](howto-configure-server-logs-using-cli.md) 和 Azure REST API，來列出和下載 Azure PostgreSQL 伺服器錯誤記錄。

## <a name="log-retention"></a>記錄保留
您可以使用與您伺服器相關聯的 **log\_retention\_period** 參數，來設定系統記錄的保留期。 此參數的單位為天 (需要確認)。 預設值為三天。 最大值為 7 天。 請注意，您的伺服器必須配置足夠的儲存體，才能包含保留的記錄檔。
記錄檔每 1 小時或每 100 MB 的大小就會輪換 (端視何者先達到)。

## <a name="configure-logging-for-azure-postgresql-server"></a>設定 Azure PostgreSQL 伺服器的記錄
您可以針對伺服器啟用查詢記錄和錯誤記錄。 錯誤記錄可包含自動清空、連接和檢查點資訊。

您可以藉由設定兩個伺服器參數來啟用 PostgreSQL DB 執行個體的查詢記錄︰log\_statement 和 log\_min\_duration\_statement。

**log\_statement** 參數控制要記錄哪些 SQL 陳述式。 我們建議將此參數設為 ***all*** 以記錄所有陳述式；預設值為 none (需要確認)。

**log\_min\_duration\_statement** 參數會以毫秒為單位，設定要記錄之陳述式的限制。 系統會記錄所有執行時間比此參數設定還長的 SQL 陳述式。 預設 (需要確認) 會停用此參數並設為負 1 (-1)。 啟用此參數，有助於追蹤應用程式中未最佳化的查詢。

**log\_min\_messages** 讓您能夠控制要將哪些訊息層級寫入伺服器記錄。 預設值為 WARNING。 (需要確認)

如需有關這些設定的詳細資訊，請參閱[錯誤報告和記錄 (英文)](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html) 文件。 若要特別設定適用於 PostgreSQL 的 Azure 資料庫伺服器參數，請參閱[適用於 PostgreSQL 的 Azure 資料庫中的伺服器記錄](concepts-server-logs.md)。

## <a name="next-steps"></a>後續步驟
- 若要使用 Azure CLI 命令列介面存取記錄，請參閱[使用 Azure CLI 設定和存取伺服器記錄](howto-configure-server-logs-using-cli.md)
- 如需伺服器參數的詳細資訊，請參閱[使用 Azure CLI 自訂伺服器設定參數](howto-configure-server-parameters-using-cli.md)。
