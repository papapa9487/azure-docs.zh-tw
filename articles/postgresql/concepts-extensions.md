---
title: "在適用於 PostgreSQL 的 Azure 資料庫中使用 PostgreSQL 擴充功能 | Microsoft Docs"
description: "描述下列功能：在適用於 PostgreSQL 的 Azure 資料庫中，使用擴充功能來擴充資料庫功能。"
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/29/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 4f85e27d01341d3d9d61d1984add32e72a53b6ac
ms.contentlocale: zh-tw
ms.lasthandoff: 07/04/2017

---
# <a name="postgresql-extensions-in-azure-database-for-postgresql"></a>適用於 PostgreSQL 的 Azure 資料庫中的 PostgreSQL 擴充功能
PostgreSQL 提供下列功能：使用擴充功能來擴充您的資料庫功能。 擴充功能可在單一封裝中搭配使用多個相關的 SQL 物件，並可使用單一命令從您的資料庫加以載入或移除。 載入資料庫後的擴充功能運作方式就像內建功能一樣。 如需 PostgreSQL 擴充功能的詳細資訊，請參閱[將相關物件封裝成擴充功能 (英文)](https://www.postgresql.org/docs/9.6/static/extend-extensions.html)。

## <a name="how-to-use-postgresql-extensions"></a>如何使用 PostgreSQL 擴充功能？
您必須先針對資料庫安裝 PostgreSQL 擴充功能，然後才能使用它們。 若要安裝特定的擴充功能，請從 psql 工具執行 [CREATE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) 命令，以將封裝的物件載入至您的資料庫。

適用於 PostgreSQL 的 Azure 資料庫支援的部分重要擴充功能如此處所列。 不支援此處列出之外的其他擴充功能。 您無法針對適用於 PostgreSQL 的 Azure 資料庫服務，自行建立擴充功能。

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>適用於 PostgreSQL 的 Azure 資料庫支援的擴充功能
下表列出適用於 PostgreSQL 的 Azure 資料庫目前支援的標準 PostgreSQL 擴充功能。 您也可以藉由查詢 pg\_available\_extensions 來取得此資訊。 

### <a name="data-types-extensions"></a>資料類型擴充功能

> [!div class="mx-tableFixed"]
| **擴充功能** | **說明** |
|---|---|
| [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | 提供不區分大小寫的字元字串類型 |
| [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | 提供用來儲存索引鍵/值組之集合的資料類型 |

### <a name="functions-extensions"></a>函數擴充功能

> [!div class="mx-tableFixed"]
| **擴充功能** | **說明** |
|---|---|
| [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | 提供數個函數來判斷字串之間的相似性與距離。 |
| [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | 提供函數和運算子來操作無 null 的整數陣列。 |
| [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | 提供密碼編譯函數 |
| [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | 依時間或識別碼來管理資料分割資料表 |
| [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | 提供可根據三併詞比對判斷英數文字相似度的函數和運算子 |
| [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | 產生通用唯一識別碼 (UUID) |

### <a name="full-text-search-extensions"></a>全文檢索搜尋擴充功能

> [!div class="mx-tableFixed"]
| **擴充功能** | **說明** |
|---|---|
| [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | 文字搜尋字典，可從詞彙中移除重音符號 (變音符號)。 |

### <a name="index-types-extensions"></a>索引類型擴充功能

> [!div class="mx-tableFixed"]
| **擴充功能** | **說明** |
|---|---|
| [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | 提供範例 GIN 運算子類別，可針對特定資料類型實作類似 B 型樹狀結構的行為。 |
| [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | 提供可實作 B 型樹狀結構的 GiST 索引運算子類別。 |

### <a name="language-extensions"></a>語言擴充功能

> [!div class="mx-tableFixed"]
| **擴充功能** | **說明** |
|---|---|
| [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | PL/pgSQL 可載入的程序性語言 |

### <a name="miscellaneous-extensions"></a>其他擴充功能

> [!div class="mx-tableFixed"]
| **擴充功能** | **說明** |
|---|---|
| [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | 提供一種方法，即時檢查共用緩衝區快取中發生的狀況。 |
| [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | 提供一種方式，來將關聯資料載入至緩衝區快取。 |
| [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | 提供一種方法，來追蹤伺服器所執行之所有 SQL 陳述式的執行統計資料。 |
| [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | 可用來存取儲存於外部 PostgreSQL 伺服器之資料的外部資料包裝函式 |

### <a name="postgis"></a>PostGIS

> [!div class="mx-tableFixed"]
| **擴充功能** | **說明** |
|---|---|
| [PostGIS](http://www.postgis.net/)、postgis\_topology、postgis\_tiger\_geocoder、postgis\_sfcgal | 適用於 PostgreSQL 的空間與地理物件。 |
| address\_standardizer、address\_standardizer\_data\_us | 用來將位址剖析為組成項目。 用來支援對位址進行地理編碼的正規化步驟。 |
| [pgrouting](http://pgrouting.org/) | 擴充 PostGIS / PostgreSQL 地理空間資料庫，以提供地理空間路由功能。 |

## <a name="next-steps"></a>後續步驟
沒有看到想要使用擴充功能？ 請告訴我們。 在我們的[客戶意見反應論壇 (英文)](https://feedback.azure.com/forums/597976-azure-database-for-postgresql) 中投票給現有的要求，或建立新的意見反應和期望。

