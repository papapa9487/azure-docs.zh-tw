---
title: "Azure 資料目錄中支援的資料來源 | Microsoft Docs"
description: "本文列出目前支援之資料來源的規格。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: jstevens
editor: 
tags: 
ms.assetid: fd4345ca-2ed8-4c5e-9c4b-f954be2fc9f9
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: d6867c73bc6ea3c238cceef8a68466d451f3365c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="supported-data-sources-in-azure-data-catalog"></a>Azure 資料目錄中支援的資料來源

您可以使用公用 API 或 Click Once 註冊工具，或是直接在「Azure 資料目錄」Web 入口網站中手動輸入資訊，來發佈中繼資料。 下表摘要說明現今目錄支援的所有資料來源，以及每個資料來源適用的發佈功能。 此外，也列出每個資料來源可以從我們的入口網站「開啟」體驗啟動的外部資料工具。 第二個表格包含每個資料來源連線屬性的較技術性規格。


## <a name="list-of-supported-data-sources"></a>支援的資料來源的清單

<table>
    <tr>
       <td><b>資料來源物件</b></td>
       <td><b>API</b></td>
       <td><b>手動輸入</b></td>
       <td><b>註冊工具</b></td>
       <td><b>開啟工具</b></td>
       <td><b>注意事項</b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store 目錄</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store 檔案</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Blob 儲存體</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure 儲存體目錄</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure 儲存體資料表</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>
    <tr>
      <td>HDFS 目錄</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HDFS 檔案</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Hive 資料表</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Hive 檢視</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>MySQL 資料表</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>MySQL 檢視</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Oracle 資料庫資料表</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Oracle 資料庫檢視</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>其他 (一般資產)</td>
      <td>✓ </td>
      <td>✓ </td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure SQL 資料倉儲資料表</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI、SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL 資料倉儲檢視</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI、SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 維度</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services KPI</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 量值</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 資料表</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services 報表</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>[瀏覽器]</font></td>
      <td><font size=2>僅限原生模式伺服器。不支援 SharePoint 模式。</font></td>
    </tr>
    <tr>
      <td>SQL Server 資料表</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI、SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SQL Server 檢視</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Excel、Power BI、SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Teradata 資料表</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Teradata 檢視</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SAP HANA 檢視</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2>Power BI</font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>DB2 資料表</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>DB2 檢視</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>檔案系統檔案</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>FTP 目錄</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>FTP 檔案</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HTTP 報告</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HTTP 端點</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>HTTP 檔案</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>OData 實體集</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>OData 函式</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>PostgreSQL 資料表</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>PostgreSQL 檢視</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SAP HANA 檢視</td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td> Salesforce 物件</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>SharePoint 清單 </td>
      <td>✓ </td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Azure Cosmos DB 集合</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>一般 ODBC 資料表</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>一般 ODBC 檢視</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Cassandra 資料表</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2>以一般 ODBC 資產形式發佈</font></td>
    </tr>
    <tr>
      <td>Cassandra 檢視</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2>以一般 ODBC 資產形式發佈</font></td>
    </tr>
    <tr>
      <td>Sybase 資料表</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>Sybase 檢視</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
    <tr>
      <td>MongoDB 資料表</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2>以一般 ODBC 資產形式發佈</font></td>
    </tr>
    <tr>
      <td>MongoDB 檢視</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2>以一般 ODBC 資產形式發佈</font></td>
    </tr>
</table>

如果您需要對其他來源的支援，請向 [Azure 資料目錄論壇](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)提交功能要求。


## <a name="data-source-reference-specification"></a>資料來源參考規格
> [!NOTE]
> 下表中的「DSL 結構」資料行僅列出「Azure 資料目錄」所使用「位址」屬性包的連線屬性。 也就是說，「位址」屬性包可以針對 Azure 資料目錄保存但並未使用的資料來源，包含其他連接屬性。

<table>
    <tr>
       <td><b>來源類型</b></td>
       <td><b>資產類型</b></td>
       <td><b>物件類型</b></td>
       <td><b>DSL 結構<b></td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>容器</td>
      <td>資料湖</td>
      <td>
        <font size=2> 通訊協定：webhdfs <br>驗證：{基本、oauth} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Azure Data Lake Store</td>
      <td>資料表</td>
      <td>目錄、檔案</td>
      <td>
        <font size=2> 通訊協定：webhdfs <br>驗證：{基本、oauth} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Azure 儲存體</td>
      <td>容器</td>
      <td>容器</td>
      <td>
        <font size=2> 通訊協定︰azure-blobs <br>驗證：{azure-access-key} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 帳戶 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 容器 </font>
      </td>
    </tr>
    <tr>
      <td>Azure 儲存體</td>
      <td>資料表</td>
      <td>Blob、目錄</td>
      <td>
        <font size=2> 通訊協定︰azure-blobs <br>驗證：{azure-access-key} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 帳戶 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 容器 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; name </font>
      </td>
    </tr>
    <tr>
      <td>Azure 儲存體</td>
      <td>容器</td>
      <td>容器</td>
      <td>
        <font size=2> 通訊協定：azure-tables <br>驗證：{azure-access-key} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 帳戶 </font>
      </td>
    </tr>
    <tr>
      <td>Azure 儲存體</td>
      <td>資料表</td>
      <td>資料表</td>
      <td>
        <font size=2> 通訊協定：azure-tables <br>驗證：{azure-access-key} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domain <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 帳戶 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; name </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>容器</td>
      <td>虛擬叢集</td>
      <td>
        <font size=2> 通訊協定：cosmos <br>驗證：{基本、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>資料表</td>
      <td>資料流、資料流集、檢視</td>
      <td>
        <font size=2> 通訊協定：cosmos <br>驗證：{基本、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>容器</td>
      <td>網站</td>
      <td>
        <font size=2> 通訊協定：http <br>驗證：{無、基本、windows、oauth} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Datazen</td>
      <td>報告</td>
      <td>報告、儀表板</td>
      <td>
        <font size=2> 通訊協定：http <br>驗證：{無、基本、windows、oauth} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>容器</td>
      <td>資料庫</td>
      <td>
        <font size=2> 通訊協定：db2 <br>驗證：{基本、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>資料表</td>
      <td>資料表、檢視</td>
      <td>
        <font size=2> 通訊協定：db2 <br>驗證：{基本、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema </font>
      </td>
    </tr>
    <tr>
      <td>檔案系統</td>
      <td>資料表</td>
      <td>檔案</td>
      <td>
        <font size=2> 通訊協定：file <br>驗證：{無、基本、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; path </font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>資料表</td>
      <td>目錄、檔案</td>
      <td>
        <font size=2> 通訊協定：ftp <br>驗證：{無、基本、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>容器</td>
      <td>叢集</td>
      <td>
        <font size=2> 通訊協定：webhdfs <br>驗證：{基本、oauth} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>資料表</td>
      <td>目錄、檔案</td>
      <td>
        <font size=2> 通訊協定：webhdfs <br>驗證：{基本、oauth} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>容器</td>
      <td>資料庫</td>
      <td>
        <font size=2> 通訊協定︰hive <br>驗證：{HDInsight、基本、使用者名稱、無} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>connectionProperties︰ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>資料表</td>
      <td>資料表、檢視</td>
      <td>
        <font size=2> 通訊協定︰hive <br>驗證：{HDInsight、基本、使用者名稱、無} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>connectionProperties︰ <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>容器</td>
      <td>網站</td>
      <td>
        <font size=2> 通訊協定：http <br>驗證：{無、基本、windows、oauth} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>報告</td>
      <td>報告、儀表板</td>
      <td>
        <font size=2> 通訊協定：http <br>驗證：{無、基本、windows、oauth} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>資料表</td>
      <td>端點、檔案</td>
      <td>
        <font size=2> 通訊協定：http <br>驗證：{無、基本、windows、oauth} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>容器</td>
      <td>資料庫</td>
      <td>
        <font size=2> 通訊協定：mysql <br>驗證：{通訊協定、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>資料表</td>
      <td>資料表、檢視</td>
      <td>
        <font size=2> 通訊協定：mysql <br>驗證：{通訊協定、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>容器</td>
      <td>實體容器</td>
      <td>
        <font size=2> 通訊協定：odata <br>驗證：{無、基本、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>資料表</td>
      <td>實體集、函式</td>
      <td>
        <font size=2> 通訊協定：odata <br>驗證：{無、基本、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; resource </font>
      </td>
    </tr>
    <tr>
      <td>Oracle 資料庫</td>
      <td>容器</td>
      <td>資料庫</td>
      <td>
        <font size=2> 通訊協定：oracle <br>驗證：{通訊協定、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>Oracle 資料庫</td>
      <td>資料表</td>
      <td>資料表、檢視</td>
      <td>
        <font size=2> 通訊協定：oracle <br>驗證：{通訊協定、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>容器</td>
      <td>資料庫</td>
      <td>
        <font size=2> 通訊協定：postgresql <br>驗證：{基本、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>資料表</td>
      <td>資料表、檢視</td>
      <td>
        <font size=2> 通訊協定：postgresql <br>驗證：{基本、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>容器</td>
      <td>網站</td>
      <td>
        <font size=2> 通訊協定：http <br>驗證：{無、基本、windows、oauth} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>報告</td>
      <td>報告、儀表板</td>
      <td>
        <font size=2> 通訊協定：http <br>驗證：{無、基本、windows、oauth} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>資料表</td>
      <td>交互式資料</td>
      <td>
        <font size=2> 通訊協定：power-query <br>驗證：{oauth} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>資料表</td>
      <td>Object</td>
      <td>
        <font size=2> 通訊協定：salesforce-com <br>驗證：{基本、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; loginServer <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; class <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; itemName </font>
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>容器</td>
      <td>伺服器</td>
      <td>
        <font size=2> 通訊協定：sap-hana-sql <br>驗證：{通訊協定、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server </font>
      </td>
    </tr>
    <tr>
      <td>SAP HANA</td>
      <td>資料表</td>
      <td>檢視</td>
      <td>
        <font size=2> 通訊協定：sap-hana-sql <br>驗證：{通訊協定、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>資料表</td>
      <td>列出</td>
      <td>
        <font size=2> 通訊協定：sharepoint-list <br>驗證：{基本、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>SQL 資料倉儲</td>
      <td>命令</td>
      <td>預存程序</td>
      <td>
        <font size=2> 通訊協定：tds <br>驗證：{通訊協定、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL 資料倉儲</td>
      <td>TableValuedFunction</td>
      <td>資料表值函式</td>
      <td>
        <font size=2> 通訊協定：tds <br>驗證：{通訊協定、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL 資料倉儲</td>
      <td>容器</td>
      <td>資料庫</td>
      <td>
        <font size=2> 通訊協定：tds <br>驗證：{通訊協定、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>SQL 資料倉儲</td>
      <td>資料表</td>
      <td>資料表、檢視</td>
      <td>
        <font size=2> 通訊協定：tds <br>驗證：{通訊協定、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>命令</td>
      <td>預存程序</td>
      <td>
        <font size=2> 通訊協定：tds <br>驗證：{通訊協定、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>資料表值函式</td>
      <td>
        <font size=2> 通訊協定：tds <br>驗證：{通訊協定、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>容器</td>
      <td>資料庫</td>
      <td>
        <font size=2> 通訊協定：tds <br>驗證：{通訊協定、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>資料表</td>
      <td>資料表、檢視</td>
      <td>
        <font size=2> 通訊協定：tds <br>驗證：{通訊協定、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 多維度</td>
      <td>容器</td>
      <td>模型</td>
      <td>
        <font size=2> 通訊協定：analysis-services <br>驗證：{windows、基本、匿名、無} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 多維度</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> 通訊協定：analysis-services <br>驗證：{windows、基本、匿名、無} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 多維度</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> 通訊協定：analysis-services <br>驗證：{windows、基本、匿名、無} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 多維度</td>
      <td>資料表</td>
      <td>維度</td>
      <td>
        <font size=2> 通訊協定：analysis-services <br>驗證：{windows、基本、匿名、無} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Dimension} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表格式</td>
      <td>容器</td>
      <td>模型</td>
      <td>
        <font size=2> 通訊協定：analysis-services <br>驗證：{windows、基本、匿名、無} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表格式</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> 通訊協定：analysis-services <br>驗證：{windows、基本、匿名、無} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表格式</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> 通訊協定：analysis-services <br>驗證：{windows、基本、匿名、無} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services 表格式</td>
      <td>資料表</td>
      <td>資料表</td>
      <td>
        <font size=2> 通訊協定：analysis-services <br>驗證：{windows、基本、匿名、無} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Table} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>容器</td>
      <td>伺服器</td>
      <td>
        <font size=2> 通訊協定：reporting-services <br>驗證：{windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>報告</td>
      <td>報告</td>
      <td>
        <font size=2> 通訊協定：reporting-services <br>驗證：{windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; path <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>容器</td>
      <td>資料庫</td>
      <td>
        <font size=2> 通訊協定：teradata <br>驗證：{通訊協定、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>資料表</td>
      <td>資料表、檢視</td>
      <td>
        <font size=2> 通訊協定：teradata <br>驗證：{通訊協定、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>容器</td>
      <td>模型</td>
      <td>
        <font size="2"> 通訊協定︰mssql-mds <br>驗證：{windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>資料表</td>
      <td>實體</td>
      <td>
        <font size="2"> 通訊協定︰mssql-mds <br>驗證：{windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; model <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; version <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; entity </font>
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>容器</td>
      <td>資料庫</td>
      <td>
        <font size=2> 通訊協定︰document-db <br>驗證：{azure-access-key} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>Azure Cosmos DB</td>
      <td>集合</td>
      <td>集合</td>
      <td>
        <font size=2> 通訊協定︰document-db <br>驗證：{azure-access-key} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 集合 </font>
      </td>
    </tr>
    <tr>
      <td>一般 ODBC</td>
      <td>容器</td>
      <td>資料庫</td>
      <td>
        <font size=2> 通訊協定：odbc <br>驗證：{基本、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 選項 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>一般 ODBC</td>
      <td>資料表</td>
      <td>資料表、檢視</td>
      <td>
        <font size=2> 通訊協定：odbc <br>驗證：{基本、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 選項 <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema </font>
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>容器</td>
      <td>資料庫</td>
      <td>
        <font size=2> 通訊協定：sybase <br>驗證：{基本、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database </font>
      </td>
    </tr>
    <tr>
      <td>Sybase</td>
      <td>資料表</td>
      <td>資料表、檢視</td>
      <td>
        <font size=2> 通訊協定：sybase <br>驗證：{基本、windows} <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; object </font>
      </td>
    </tr>
    <tr>
      <td>其他 (以上皆非)</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2> 通訊協定：generic-asset <br>位址： <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; assetId </font>
      </td>
    </tr>
</table>
