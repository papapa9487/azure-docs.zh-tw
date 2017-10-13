---
title: "Azure SQL 資料倉儲服務的所有主題 | Microsoft Docs"
description: "http://azure.microsoft.com/documentation/articles/ 上名為「SQL 資料倉儲」的 Azure 服務的所有主題表格 (標題與說明)。"
services: sql-data-warehouse
documentationcenter: 
author: barbkess
manager: jhubbard
editor: 
ms.assetid: a26a6dec-9c08-4415-8f58-4ee1dd41f718
ms.service: sql-data-warehouse
ms.workload: sql-data-warehouse
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: reference
ms.date: 03/30/2017
ms.author: barbkess
ms.openlocfilehash: 9fe41f12960dc099700e01573b4f03ebf63f8749
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="all-topics-for-azure-sql-data-warehouse-service"></a>Azure SQL 資料倉儲服務的所有主題
本主題會列出每一個直接適用於 Azure **SQL 資料倉儲** 服務的主題。 您可以使用 **Ctrl+F**，利用關鍵字搜尋本網頁，以尋找目前感興趣的主題。

## <a name="new"></a>新增
| &nbsp; | 課程名稱 | 說明 |
| ---:|:--- |:--- |
| 1 |[SQL 資料倉儲備份](sql-data-warehouse-backups.md) |了解 SQL 資料倉儲內建資料庫備份，它可以讓您將 Azure SQL 資料倉儲還原到還原點或不同的地理區域。 |

## <a name="updated-articles-sql-data-warehouse"></a>更新的文章, SQL 資料倉儲
本節列出具有重大更新內容的最近更新文章。 針對每篇更新的文章，會顯示一段新增的 Markdown 文字概略程式碼片段。 文章的更新日期範圍是在 **2016-08-22** 到 **2016-10-05**。

| &nbsp; | 文章 | 更新的文字、程式碼片段 | 更新時機 |
| ---:|:--- |:--- |:--- |
| 2 |[從 Azure Blob 儲存體將資料載入 SQL 資料倉儲 (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) |/- 若要追蹤位元組和檔案 SELECT  r.command,  s.request_id,  r.status,  count(distinct input_name) as nbr_files,  sum(s.bytes_processed)/1024/1024 as gb_processed FROM  sys.dm_pdw_exec_requests r  inner join sys.dm_pdw_dms_external_work s  on r.request_id = s.request_id WHERE  r. label  = 'CTAS : Load  cso . DimProduct  '  OR r. label  = 'CTAS : Load  cso . FactOnlineSales  ' GROUP BY  r.command,  s.request_id,  r.status ORDER BY  nbr_files desc,  gb_processed desc; |2016-09-07 |
| 3 |[SQL 資料倉儲還原](sql-data-warehouse-restore-database-overview.md) |**是否可以還原暫停的資料倉儲？** 若要還原已經暫停的資料倉儲，您需要先讓它恢復上線。 在資料倉儲恢復上線之後，您有七天的還原點可以選擇。 **還原到異地備援區域** 如果您正在使用異地備援儲存體，您可以將資料倉儲還原到您位於不同地理區域的已配對資料中心。 資料倉儲已經從上次每日備份還原。 **還原時間軸** 您可以將資料庫還原到過去七天內的任何還原點。 快照集每四到八個小時會啟動，並且可供使用七天。 當快照集存在時間超過七天，它就會過期，而且無法再使用其還原點。 **還原成本** 針對已還原資料倉儲的儲存體費用，會以 Azure 進階儲存體費率收費。 如果您將已還原的資料倉儲暫停，會依據 Azure 進階儲存體費率向您收取儲存體費用。 暫停的好處是您不會被收取費用 |2016-09-29 |

## <a name="get-started"></a>開始使用
| &nbsp; | 課程名稱 | 說明 |
| ---:|:--- |:--- |
| 4 |[適用於 Azure SQL 資料倉儲的驗證](sql-data-warehouse-authentication.md) |適用於 Azure SQL 資料倉儲的 Azure Active Directory (AAD) 與 SQL Server 驗證。 |
| 5 |[Azure SQL 資料倉儲最佳做法](sql-data-warehouse-best-practices.md) |開發 Azure SQL 資料倉儲的解決方案時應該知道的建議和最佳作法。 這些可協助您成功。 |
| 6 |[適用於 Azure SQL 資料倉儲的驅動程式](sql-data-warehouse-connection-strings.md) |適用於 SQL 資料倉儲的連接字串和驅動程式 |
| 7 |[連接到 Azure SQL 資料倉儲](sql-data-warehouse-connect-overview.md) |如何尋找您的伺服器名稱和 Azure SQL 資料倉儲的連接字串 |
| 8 |[使用 Azure Machine Learning 分析資料](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md) |使用 Azure Machine Learning，根據 Azure 資料倉儲中儲存的資料建置預測性機器學習模型。 |
| 9 |[查詢 Azure SQL 資料倉儲 (sqlcmd)](sql-data-warehouse-get-started-connect-sqlcmd.md) |使用 sqlcmd 命令列公用程式查詢 Azure SQL 資料倉儲。 |
| 10 |[使用 Transact-SQL (TSQL) 建立 SQL 資料倉儲資料庫](sql-data-warehouse-get-started-create-database-tsql.md) |了解如何使用 TSQL 建立 Azure SQL 資料倉儲 |
| 11 |[如何為 SQL 資料倉儲建立支援票證](sql-data-warehouse-get-started-create-support-ticket.md) |如何在 Azure SQL 資料倉儲中建立支援票證。 |
| 12 |[使用 Azure Data Factory 載入資料](sql-data-warehouse-get-started-load-with-azure-data-factory.md) |了解如何使用 Azure Data Factory 載入資料 |
| 13 |[在 SQL 資料倉儲中使用 PolyBase 載入資料](sql-data-warehouse-get-started-load-with-polybase.md) |瞭解 PolyBase 是什麼及如何用於資料倉儲案例。 |
| 14 |[建立 Azure SQL 資料倉儲](sql-data-warehouse-get-started-provision.md) |了解如何在 Azure 入口網站中建立 Azure SQL 資料倉儲 |
| 15 |[使用 PowerShell 建立 SQL 資料倉儲](sql-data-warehouse-get-started-provision-powershell.md) |使用 PowerShell 建立 SQL 資料倉儲 |
| 16 |[使用 Power BI 視覺化資料](sql-data-warehouse-get-started-visualize-with-power-bi.md) |使用 Power BI 視覺化 SQL 資料倉儲資料 |
| 17 |[查詢 Azure SQL 資料倉儲 (Visual Studio)](sql-data-warehouse-query-visual-studio.md) |使用 Visual Studio 查詢 SQL 資料倉儲。 |

## <a name="develop"></a>開發
| &nbsp; | 課程名稱 | 說明 |
| ---:|:--- |:--- |
| 18 |[最佳化 SQL 資料倉儲的交易](sql-data-warehouse-develop-best-practices-transactions.md) |在 Azure SQL 資料倉儲中撰寫有效率交易更新的最佳作法指引 |
| 19 |[SQL 資料倉儲中的並行存取和工作負載管理](sql-data-warehouse-develop-concurrency.md) |了解 Azure SQL 資料倉儲中的並行存取和工作負載管理以開發解決方案。 |
| 20 |[在 SQL 資料倉儲中的 Create Table As Select (CTAS)](sql-data-warehouse-develop-ctas.md) |在 Azure SQL 資料倉儲中利用 create table as select (CTAS) 陳述式撰寫程式碼做為開發解決方案的提示。 |
| 21 |[SQL 資料倉儲中的動態 SQL](sql-data-warehouse-develop-dynamic-sql.md) |使用 Azure SQL 資料倉儲中的動態 SQL 以開發解決方案的秘訣。 |
| 22 |[根據 SQL 資料倉儲中的選項分組](sql-data-warehouse-develop-group-by-options.md) |根據 Azure SQL 資料倉儲中的選項實作群組以便開發解決方案的秘訣。 |
| 23 |[在 SQL 資料倉儲中使用標籤來檢測查詢](sql-data-warehouse-develop-label.md) |在 Azure SQL 資料倉儲中使用標籤來檢測查詢以開發解決方案的秘訣。 |
| 24 |[SQL 資料倉儲中的迴圈](sql-data-warehouse-develop-loops.md) |在 Azure SQL 資料倉儲中使用 Transact-SQL 迴圈及取代資料指標以開發解決方案的秘訣。 |
| 25 |[SQL 資料倉儲中的預存程序](sql-data-warehouse-develop-stored-procedures.md) |在 Azure SQL 資料倉儲中實作預存程序以便開發解決方案的秘訣。 |
| 26 |[SQL 資料倉儲中的交易](sql-data-warehouse-develop-transactions.md) |在 Azure SQL 資料倉儲中實作交易以便開發解決方案的秘訣。 |
| 27 |[SQL 資料倉儲中使用者定義的結構描述](sql-data-warehouse-develop-user-defined-schemas.md) |在 Azure SQL 資料倉儲中使用 Transact-SQL 結構描述開發解決方案的秘訣。 |
| 28 |[在 SQL 資料倉儲中指派變數](sql-data-warehouse-develop-variable-assignment.md) |在 Azure SQL 資料倉儲中指派 TRANSACT-SQL 變數以便開發解決方案的秘訣。 |
| 29 |[SQL 資料倉儲中的檢視](sql-data-warehouse-develop-views.md) |在 Azure SQL 資料倉儲中使用 Transact-SQL 檢視開發解決方案的秘訣。 |
| 30 |[SQL 資料倉儲的設計決策和程式碼撰寫技術](sql-data-warehouse-overview-develop.md) |SQL 資料倉儲的開發概念、設計決策、建議和程式碼撰寫技巧。 |

## <a name="manage"></a>管理
| &nbsp; | 課程名稱 | 說明 |
| ---:|:--- |:--- |
| 31 |[管理 Azure SQL 資料倉儲中的計算能力 (概觀)](sql-data-warehouse-manage-compute-overview.md) |Azure SQL 資料倉儲中的效能相應放大功能。 藉由調整 DWU 以相應放大或暫停和繼續計算資源來節省成本。 |
| 32 |[管理 Azure SQL 資料倉儲中的計算能力 (Azure 入口網站)](sql-data-warehouse-manage-compute-portal.md) |管理計算能力的 Azure 入口網站工作。 透過調整 DWU 以調整計算資源。 或者，暫停和繼續計算資源以節省成本。 |
| 33 |[管理 Azure SQL 資料倉儲中的計算能力 (PowerShell)](sql-data-warehouse-manage-compute-powershell.md) |管理計算能力的 PowerShell 工作。 透過調整 DWU 以調整計算資源。 或者，暫停和繼續計算資源以節省成本。 |
| 34 |[管理 Azure SQL 資料倉儲中的計算能力 (REST)](sql-data-warehouse-manage-compute-rest-api.md) |管理計算能力的 PowerShell 工作。 透過調整 DWU 以調整計算資源。 或者，暫停和繼續計算資源以節省成本。 |
| 35 |[管理 Azure SQL 資料倉儲中的計算能力 (T-SQL)](sql-data-warehouse-manage-compute-tsql.md) |透過調整 DWU 以相應放大效能的 Transact-SQL (T-SQL) 工作。 透過在非尖峰時間進行縮減以節省成本。 |
| 36 |[使用 DMV 監視工作負載](sql-data-warehouse-manage-monitor.md) |了解如何使用 DMV 監視工作負載。 |
| 37 |[管理 Azure SQL 資料倉儲中的資料庫](sql-data-warehouse-overview-manage.md) |管理 SQL 資料倉儲資料庫的概觀。 包含管理工具、DWU 與相應放大效能、疑難排解查詢效能、建立良好的安全性原則，以及從資料損毀或區域性停電還原資料庫。 |
| 38 |[監視 Azure SQL 資料倉儲中的使用者查詢](sql-data-warehouse-overview-manage-user-queries.md) |監視 Azure SQL 資料倉儲中使用者查詢之考量、最佳做法及工作的概觀。 |
| 39 |[SQL 資料倉儲還原](sql-data-warehouse-restore-database-overview.md) |復原 Azure SQL 資料倉儲中資料庫之資料庫還原選項的概觀。 |
| 40 |[還原 Azure SQL 資料倉儲 (入口網站)](sql-data-warehouse-restore-database-portal.md) |還原 Azure SQL 資料倉儲的 Azure 入口網站工作。 |
| 41 |[還原 Azure SQL 資料倉儲 (PowerShell)](sql-data-warehouse-restore-database-powershell.md) |還原 Azure SQL 資料倉儲的 PowerShell 工作。 |
| 42 |[還原 Azure SQL 資料倉儲 (REST API)](sql-data-warehouse-restore-database-rest-api.md) |還原 Azure SQL 資料倉儲的 REST API 工作。 |

## <a name="tables-and-indexes"></a>資料表與索引
| &nbsp; | 課程名稱 | 說明 |
| ---:|:--- |:--- |
| 43 |[SQL 資料倉儲中的資料表的資料類型](sql-data-warehouse-tables-data-types.md) |開始使用 Azure SQL 資料倉儲資料表的資料類型。 |
| 44 |[在 SQL 資料倉儲中散發資料表](sql-data-warehouse-tables-distribute.md) |開始在 Azure SQL 資料倉儲中散發資料表。 |
| 45 |[在 SQL 資料倉儲中編製資料表的索引](sql-data-warehouse-tables-index.md) |開始在 Azure SQL 資料倉儲中編製資料表的索引 |
| 46 |[SQL 資料倉儲中的資料表概觀](sql-data-warehouse-tables-overview.md) |開始使用 Azure SQL 資料倉儲資料表。 |
| 47 |[在 SQL 資料倉儲中分割資料表](sql-data-warehouse-tables-partition.md) |開始在 Azure SQL 資料倉儲中分割資料表。 |
| 48 |[管理 SQL 資料倉儲中的資料表的統計資料](sql-data-warehouse-tables-statistics.md) |開始使用 Azure SQL 資料倉儲中的資料表的統計資料。 |
| 49 |[SQL 資料倉儲中的暫存資料表](sql-data-warehouse-tables-temporary.md) |開始使用 Azure SQL 資料倉儲中的暫存資料表。 |

## <a name="integrate"></a>整合
| &nbsp; | 課程名稱 | 說明 |
| ---:|:--- |:--- |
| 50 |[搭配使用 Azure Data Factory 與 SQL 資料倉儲](sql-data-warehouse-integrate-azure-data-factory.md) |搭配使用 Azure Data Factory (ADF) 與 SQL 資料倉儲以便開發解決方案的秘訣。 |
| 51 |[搭配使用 Azure 機器學習服務與 SQL 資料倉儲](sql-data-warehouse-integrate-azure-machine-learning.md) |搭配使用 Azure 機器學習服務與 SQL 資料倉儲以便開發解決方案的秘訣。 |
| 52 |[搭配使用 Azure 串流分析與 SQL 資料倉儲](sql-data-warehouse-integrate-azure-stream-analytics.md) |搭配使用 Azure 串流分析與 SQL 資料倉儲以便開發解決方案的秘訣。 |
| 53 |[搭配使用 Power BI 與 SQL 資料倉儲](sql-data-warehouse-integrate-power-bi.md) |搭配使用 Power BI 與 Azure SQL 資料倉儲以便開發解決方案的秘訣。 |
| 54 |[以 SQL 資料倉儲來搭配運用其他服務](sql-data-warehouse-overview-integrate.md) |工具以及提供可與 SQL 資料倉儲整合之解決方案的合作夥伴。 |

## <a name="load"></a>載入
| &nbsp; | 課程名稱 | 說明 |
| ---:|:--- |:--- |
| 55 |[從 Azure blob 儲存體將資料載入 Azure SQL 資料倉儲 (Azure Data Factory)](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md) |了解如何使用 Azure Data Factory 載入資料 |
| 56 |[從 Azure Blob 儲存體將資料載入 SQL 資料倉儲 (PolyBase)](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) |了解如何此用 PolyBase 從 Azure Blob 儲存體將資料載入 SQL 資料倉儲。 從公用資料將幾個資料表載入 Contoso 零售資料倉儲結構描述。 |
| 57 |[將資料從 SQL Server 載入 Azure SQL 資料倉儲 (AZCopy)](sql-data-warehouse-load-from-sql-server-with-azcopy.md) |使用 bcp 將資料從 SQL Server 匯出到一般檔案、使用 AZCopy 將資料匯入至 Azure Blob 儲存體，以及使用 PolyBase 將資料擷取到 Azure SQL 資料倉儲中。 |
| 58 |[將資料從 SQL Server 載入 Azure SQL 資料倉儲 (一般檔案)](sql-data-warehouse-load-from-sql-server-with-bcp.md) |對於小型資料，請使用 bcp 將資料從 SQL Server 匯出至一般檔案，以及將資料直接匯入 Azure SQL 資料倉儲。 |
| 59 |[將資料從 SQL Server 載入 Azure SQL 資料倉儲 (SSIS)](sql-data-warehouse-load-from-sql-server-with-integration-services.md) |示範如何建立 SQL Server Integration Services (SSIS) 封裝，以將資料從各種資料來源移至 SQL 資料倉儲。 |
| 60 |[在 SQL 資料倉儲中使用 PolyBase 載入資料](sql-data-warehouse-load-from-sql-server-with-polybase.md) |使用 bcp 將資料從 SQL Server 匯出到一般檔案、使用 AZCopy 將資料匯入至 Azure Blob 儲存體，以及使用 PolyBase 將資料擷取到 Azure SQL 資料倉儲中。 |
| 61 |[在 SQL 資料倉儲中使用 PolyBase 的指南](sql-data-warehouse-load-polybase-guide.md) |在 SQL 資料倉儲案例中使用 PolyBase 的指導方針和建議。 |
| 62 |[將範例資料載入 SQL 資料倉儲](sql-data-warehouse-load-sample-databases.md) |將範例資料載入 SQL 資料倉儲 |
| 63 |[使用 bcp 載入資料](sql-data-warehouse-load-with-bcp.md) |瞭解 bcp 是什麼及如何用於資料倉儲案例。 |
| 64 |[將資料載入 Azure SQL 資料倉儲](sql-data-warehouse-overview-load.md) |了解將資料載入 SQL 資料倉儲的常見案例 這包含使用 PolyBase、Azure Blob 儲存體、一般檔案及寄送磁碟。 您也可以使用協力廠商工具。 |

## <a name="migrate"></a>移轉
| &nbsp; | 課程名稱 | 說明 |
| ---:|:--- |:--- |
| 65 |[將您的 SQL 程式碼移轉至 SQL 資料倉儲](sql-data-warehouse-migrate-code.md) |將 SQL 程式碼移轉至 Azure SQL 資料倉儲來開發解決方案的秘訣。 |
| 66 |[移轉資料](sql-data-warehouse-migrate-data.md) |將您的資料移轉至 Azure SQL 資料倉儲來開發解決方案的秘訣。 |
| 67 |[資料倉儲移轉公用程式 (預覽)](sql-data-warehouse-migrate-migration-utility.md) |移轉至 SQL 資料倉儲。 |
| 68 |[將您的結構描述移轉至 SQL 資料倉儲](sql-data-warehouse-migrate-schema.md) |將您的結構描述移轉至 Azure SQL 資料倉儲來開發解決方案的秘訣。 |
| 69 |[將您的解決方案移轉至 SQL 資料倉儲](sql-data-warehouse-overview-migrate.md) |將您的解決方案帶入 Azure SQL 資料倉儲平台的移轉指導。 |

## <a name="partners"></a>合作夥伴
| &nbsp; | 課程名稱 | 說明 |
| ---:|:--- |:--- |
| 70 |[SQL 資料倉儲商業智慧合作夥伴](sql-data-warehouse-partner-business-intelligence.md) |具有可支援「SQL 資料倉儲」之解決方案的協力廠商商業智慧合作夥伴清單。 |
| 71 |[SQL 資料倉儲資料整合合作夥伴](sql-data-warehouse-partner-data-integration.md) |具有可支援「Azure SQL 資料倉儲」之資料整合解決方案的協力廠商合作夥伴清單。 |
| 72 |[SQL 資料倉儲資料管理合作夥伴](sql-data-warehouse-partner-data-management.md) |具有可支援「SQL 資料倉儲」之解決方案的協力廠商資料管理合作夥伴清單。 |

## <a name="reference"></a>參考
| &nbsp; | 課程名稱 | 說明 |
| ---:|:--- |:--- |
| 73 |[SQL 資料倉儲的參考主題](sql-data-warehouse-overview-reference.md) |SQL 資料倉儲的參考內容連結。 |
| 74 |[適用於 SQL 資料倉儲的 PowerShell Cmdlet 和 REST API](sql-data-warehouse-reference-powershell-cmdlets.md) |尋找 Azure SQL 資料倉儲的前幾個 PowerShell Cmdlet，包括如何暫停和繼續資料庫。 |
| 75 |[語言元素](sql-data-warehouse-reference-tsql-language-elements.md) |SQL 資料倉儲所使用的 Transact-SQL Language 元素的參考內容連結清單。 |
| 76 |[Transact-SQL 主題](sql-data-warehouse-reference-tsql-statements.md) |SQL 資料倉儲所使用的 Transact-SQL 主題的參考內容連結。 |
| 77 |[系統檢視表](sql-data-warehouse-reference-tsql-system-views.md) |SQL 資料倉儲的系統檢視表內容的連結。 |

## <a name="security"></a>安全性
| &nbsp; | 課程名稱 | 說明 |
| ---:|:--- |:--- |
| 78 |[SQL 資料倉儲 -  下層用戶端對稽核和動態資料遮罩的支援](sql-data-warehouse-auditing-downlevel-clients.md) |了解 SQL 資料倉儲下層用戶端對資料稽核的支援 |
| 79 |[Azure SQL 資料倉儲中的稽核](sql-data-warehouse-auditing-overview.md) |開始使用 Azure SQL 資料倉儲中的稽核 |
| 80 |[開始使用 SQL 資料倉儲中的透明資料加密 (TDE)](sql-data-warehouse-encryption-tde.md) |SQL 資料倉儲中的透明資料加密 (TDE) |
| 81 |[開始使用透明資料加密 (TDE)](sql-data-warehouse-encryption-tde-tsql.md) |SQL 資料倉儲中的透明資料加密 (TDE) (T-SQL) |
| 82 |[保護 SQL 資料倉儲中的資料庫](sql-data-warehouse-overview-manage-security.md) |保護 Azure SQL 資料倉儲中的資料庫以便開發解決方案的秘訣。 |

## <a name="miscellaneous"></a>其他資訊
| &nbsp; | 課程名稱 | 說明 |
| ---:|:--- |:--- |
| 83 |[安裝適用於 SQL 資料倉儲的 Visual Studio 和 SSDT](sql-data-warehouse-install-visual-studio.md) |安裝適用於 Azure SQL 資料倉儲的 Visual Studio 和 SQL Server Development Tools (SSDT) |
| 84 |[移轉至進階儲存體詳細資料](sql-data-warehouse-migrate-to-premium-storage.md) |將現有「SQL 資料倉儲」移轉到進階儲存體的指示 |
| 85 |[開始使用威脅偵測](sql-data-warehouse-security-threat-detection.md) |如何開始使用威脅偵測 |
| 86 |[SQL 資料倉儲容量限制](sql-data-warehouse-service-capacity-limits.md) |SQL 資料倉儲的連接、資料庫、資料表及查詢最大值。 |
| 87 |[針對 Azure SQL 資料倉儲問題進行疑難排解](sql-data-warehouse-troubleshoot.md) |針對 Azure SQL 資料倉儲問題進行疑難排解。 |

