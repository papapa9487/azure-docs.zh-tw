---
title: "載入 - Azure Data Lake Store 到 SQL 資料倉儲 | Microsoft Docs"
description: "了解如何使用 PolyBase 外部資料表將資料從 Azure Data Lake Store 載入到 Azure SQL 資料倉儲。"
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 09/15/2017
ms.author: cakarst;barbkess
ms.openlocfilehash: bb478484fba5a76fa12d5d1976919224965b6e0d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-from-azure-data-lake-store-into-sql-data-warehouse"></a>將資料從 Azure Data Lake Store 載入到 SQL 資料倉儲
本文件說明使用 PolyBase 將您自己的資料從 Azure Data Lake Store (ADLS) 載入到 SQL 資料倉儲時需要執行的所有步驟。
雖然您能夠使用外部資料表對 ADLS 中儲存的資料執行臨機操作查詢，但是我們建議的最佳做法是將資料匯入到 SQL 資料倉儲。

在本教學課程中，您將了解如何：

1. 建立要從 Azure Data Lake Store 載入的外部資料庫物件。
2. 連接到 Azure Data Lake Store 目錄。
3. 將資料載入到 Azure SQL 資料倉儲。

## <a name="before-you-begin"></a>開始之前
若要執行此教學課程，您需要：

* Azure Active Directory 應用程式，用於服務對服務驗證。 若要建立，請依照 [Active Directory 驗證](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)中的指示執行

>[!NOTE] 
> 您需要 Active Directory 應用程式的用戶端識別碼、金鑰及 OAuth2.0 Token 端點值，以便從 SQL 資料倉儲連接到 Azure Data Lake。 上面的連結提供如何取得這些值的詳細資訊。
>請注意，Azure Active Directory 應用程式註冊使用「應用程式識別碼」作為「用 戶端」。

* SQL Server Management Studio 或 SQL Server Data Tools，用來下載 SSMS 和連接，請參閱[查詢 SSMS](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-query-ssms)

* 一個 Azure SQL 資料倉儲，若要建立一個，請依照下列連結中的指示執行：https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-provision

* 一個啟用或未啟用加密的 Azure Data Lake Store。 若要建立一個，請依照下列連結中的指示執行：https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal




## <a name="configure-the-data-source"></a>設定資料來源
PolyBase 使用 T-SQL 外部物件以定義外部資料的位置和屬性。 外部物件儲存在 SQL 資料倉儲中，而且它會參考儲存在外部的資料。


###  <a name="create-a-credential"></a>建立認證
若要存取您的 Azure Data Lake Store，您將需要建立一個資料庫主要金鑰，以加密要在下一個步驟中使用的認證密碼。
接著，您必須建立資料庫範圍認證，其中儲存了在 AAD 中設定的服務主體認證。 對於使用 PolyBase 連接到 Windows Azure 儲存體 Blob 的人來說，請注意認證語法是不同的。
若要連線到 Azure Data Lake Store，您必須**先**建立 Azure Active Directory 應用程式、建立存取金鑰，並對應用程式授與 Azure Data Lake 資源的存取權。 這些步驟的執行指示位於[這裡](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-authenticate-using-active-directory)。

```sql
-- A: Create a Database Master Key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.
-- For more information on Master Key: https://msdn.microsoft.com/en-us/library/ms174382.aspx?f=255&MSPPError=-2147217396

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Pass the client id and OAuth 2.0 Token Endpoint taken from your Azure Active Directory Application
-- SECRET: Provide your AAD Application Service Principal key.
-- For more information on Create Database Scoped Credential: https://msdn.microsoft.com/en-us/library/mt270260.aspx

CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '<client_id>@<OAuth_2.0_Token_EndPoint>',
    SECRET = '<key>'
;

-- It should look something like this:
CREATE DATABASE SCOPED CREDENTIAL ADLCredential
WITH
    IDENTITY = '536540b4-4239-45fe-b9a3-629f97591c0c@https://login.microsoftonline.com/42f988bf-85f1-41af-91ab-2d2cd011da47/oauth2/token',
    SECRET = 'BjdIlmtKp4Fpyh9hIvr8HJlUida/seM5kQ3EpLAmeDI='
;
```


### <a name="create-the-external-data-source"></a>建立外部資料來源
使用此 [CREATE EXTERNAL DATA SOURCE][CREATE EXTERNAL DATA SOURCE] 命令以儲存資料的位置及類型。 若要在 Azure 入口網站尋找 ADL URI，請瀏覽至您的 Azure Data Lake Store，然後查看「基本資訊」面板。

```sql
-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure Data Lake Store.
-- LOCATION: Provide Azure Data Lake accountname and URI
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
WITH (
    TYPE = HADOOP,
    LOCATION = 'adl://<AzureDataLake account_name>.azuredatalakestore.net',
    CREDENTIAL = ADLCredential
);
```



## <a name="configure-data-format"></a>設定資料格式
若要從 ADLS 匯入資料，您需要指定外部檔案格式。 這個命令有特定的格式選項，用以描述您的資料。
以下是常用檔案格式的範例，它是一個以管線符號分隔的文字檔案。
請查閱我們的 T-SQL 文件，以取得 [CREATE EXTERNAL FILE FORMAT][CREATE EXTERNAL FILE FORMAT] 的完整清單

```sql
-- D: Create an external file format
-- FIELD_TERMINATOR: Marks the end of each field (column) in a delimited text file
-- STRING_DELIMITER: Specifies the field terminator for data of type string in the text-delimited file.
-- DATE_FORMAT: Specifies a custom format for all date and time data that might appear in a delimited text file.
-- Use_Type_Default: Store all Missing values as NULL

CREATE EXTERNAL FILE FORMAT TextFileFormat
WITH
(   FORMAT_TYPE = DELIMITEDTEXT
,    FORMAT_OPTIONS    (   FIELD_TERMINATOR = '|'
                    ,    STRING_DELIMITER = ''
                    ,    DATE_FORMAT         = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,    USE_TYPE_DEFAULT = FALSE
                    )
);
```

## <a name="create-the-external-tables"></a>建立外部資料表
您在指定資料來源和檔案格式之後，便可以開始建立外部資料表。 外部資料表是您與外部資料進行互動的方式。 PolyBase 使用遞迴目錄周遊，來讀取在位置參數中指定之目錄下所有子目錄中的所有檔案。 此外，下列範例將示範如何建立物件。 您需要自訂陳述式，以處理 ADLS 中的資料。

```sql
-- D: Create an External Table
-- LOCATION: Folder under the ADLS root folder.
-- DATA_SOURCE: Specifies which Data Source Object to use.
-- FILE_FORMAT: Specifies which File Format Object to use
-- REJECT_TYPE: Specifies how you want to deal with rejected rows. Either Value or percentage of the total
-- REJECT_VALUE: Sets the Reject value based on the reject type.

-- DimProduct
CREATE EXTERNAL TABLE [dbo].[DimProduct_external] (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL
)
WITH
(
    LOCATION='/DimProduct/'
,   DATA_SOURCE = AzureDataLakeStore
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;

```

## <a name="external-table-considerations"></a>外部資料表考量
建立外部資料表很簡單，但是有一些必須討論的細微差異。

使用 PolyBase 載入資料屬於強型別。 這表示內嵌的每個資料列都必須滿足資料表結構描述定義。
如果指定的資料列不符合結構描述定義，載入時就會拒絕該列。

REJECT_TYPE 和 REJECT_VALUE 選項可讓您定義最終的資料表中必須出現多少資料列或多少百分比的資料。
在載入期間，如果達到拒絕值，載入即失敗。 資料列遭拒最常見的原因是結構描述定義不相符。
例如，如果檔案中的資料是字串，卻對資料行指定不正確的整數結構描述，則會無法載入每個資料列。

[位置] 會指定您想要開始讀取資料的最上層目錄。
在此案例中，如果 /DimProduct/ 下面有子目錄，PolyBase 將匯入子目錄內的所有資料。 Azure Data Lake Store 使用角色型存取控制 (RBAC) 來控制資料存取。 這表示服務主體必須具有在位置參數中所定義之目錄，以及最終目錄和檔案之子系的讀取權限。 這可讓 PolyBase 驗證及載入讀取該資料。 

## <a name="load-the-data"></a>載入資料
若要從 Azure Data Lake Store 載入資料，請使用 [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] 陳述式。 使用 CTAS 執行載入作業會使用您已建立的強型別外部資料表。

CTAS 建立新的資料表，並將選取陳述式的結果填入該資料表。 CTAS 定義新資料表，以使它擁有和選取陳述式之結果相同的資料行和資料類型。 如果您選取外部資料表上的所有資料行，則新資料表會是外部資料表中資料行和資料類型的複本。

在此範例中，我們會從我們的外部資料表 DimProduct_external 建立一個名為 DimProduct 的雜湊分散式資料表。

```sql

CREATE TABLE [dbo].[DimProduct]
WITH (DISTRIBUTION = HASH([ProductKey]  ) )
AS
SELECT * FROM [dbo].[DimProduct_external]
OPTION (LABEL = 'CTAS : Load [dbo].[DimProduct]');
```


## <a name="optimize-columnstore-compression"></a>最佳化資料行存放區壓縮
根據預設，SQL 資料倉儲會將資料表儲存為叢集資料行存放區索引。 載入完成後，某些資料列可能不會被壓縮為資料行存放區。  有許多原因會導致發生此情況。 若要深入了解，請參閱[管理資料行存放區索引][manage columnstore indexes]。

若要最佳化載入後的查詢效能和資料行存放區壓縮，請重建資料表以強制資料行存放區索引對所有資料列進行壓縮。

```sql

ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD;

```

如需維護資料行存放區索引的詳細資訊，請參閱[管理資料行存放區索引][manage columnstore indexes]一文。

## <a name="optimize-statistics"></a>最佳化統計資料
您最好在載入後立刻建立單一資料行統計資料。 針對統計資料，您將會有一些選項。 例如，如果您在每個資料行上建立單一資料行統計資料，可能會需要很長的時間才能重建所有統計資料。 如果您知道某些資料行不會被包含在查詢述詞中，您可以略過為那些資料行建立統計資料。

如果您決定要在每個資料表的每個資料行上建立單一資料行統計資料，您可以使用[統計資料][statistics]一文中的預存程序程式碼範例 `prc_sqldw_create_stats`。

下列範例為建立統計資料的好起點。 它會在維度資料表中的每個資料行上，以及在事實資料表中的每個聯結資料行上建立單一資料行統計資料。 您之後隨時可以將單一或多個資料行統計資料新增到其他事實資料表資料行上。


## <a name="achievement-unlocked"></a>成就解鎖！
您已成功將資料載入到 Azure SQL 資料倉儲。 太棒了！

## <a name="next-steps"></a>後續步驟
載入資料是開發使用 SQL 資料倉儲之資料倉儲解決方案的第一步。 請查看我們在[資料表](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-overview)和 [T-SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-loops.md) 上提供的開發資源。


<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[manage columnstore indexes]: sql-data-warehouse-tables-index.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CREATE EXTERNAL DATA SOURCE]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Load the full Contoso Retail Data Warehouse]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
