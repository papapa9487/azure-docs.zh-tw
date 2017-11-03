---
title: "SQL Database 的連接庫 | Microsoft Docs"
description: "提供模組下載連結，這些模組可讓開發人員從各種用戶端程式設計語言連線到 SQL Server 與 SQL Database。"
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: genemi
ms.assetid: 13d899d3-cf46-4e4d-8919-cf4b41ca836d
ms.service: sql-database
ms.custom: develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: genemi
ms.openlocfilehash: bdf83fac9bd0ac6790062f802748a18045c7a171
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="connectivity-libraries-and-frameworks-for-microsoft-sql-server"></a>適用於 Microsoft SQL Server 的連線程式庫和架構

請查看[快速入門教學課程](http://aka.ms/sqldev)，以快速開始使用 C#、Java、Node.js、PHP 及 Python 之類的程式語言，並在 Linux 或 Windows 上使用 SQL Server 或在 macOS 上使用 Docker 來建置應用程式。

下表列出的連線程式庫或「驅動程式」可供用戶端應用程式從各種不同的語言使用來連線，以及使用在內部部署或雲端中，或在 Linux、Windows 上執行的 Microsoft SQL Server 或使用 Docker，還可以用於 Azure SQL Database 和 Azure SQL 資料倉儲。 

| 語言 | 平台 | 其他資源 | 下載 | 開始使用 |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [Microsoft ADO.NET for SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [下載](https://www.microsoft.com/net/download/) | [快速入門](https://www.microsoft.com/en-us/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows、Linux、macOS | [Microsoft JDBC Driver for SQL Server](http://msdn.microsoft.com/library/mt484311.aspx) | [下載](https://go.microsoft.com/fwlink/?linkid=852460) |  [快速入門](https://www.microsoft.com/en-us/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows、Linux、macOS| [PHP SQL Driver for SQL Server](http://msdn.microsoft.com/library/dn865013.aspx) | 作業系統： <br/> \* [Windows](https://www.microsoft.com/download/details.aspx?id=20098) <br/> \* [Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \* [macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [快速入門](https://www.microsoft.com/en-us/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows、Linux、macOS | [Node.js Driver for SQL Server](http://msdn.microsoft.com/library/mt652093.aspx) | [安裝](https://msdn.microsoft.com/library/mt652094.aspx) |  [快速入門](https://www.microsoft.com/en-us/sql-server/developer-get-started/node/ubuntu)
| Python | Windows、Linux、macOS | [Python SQL 驅動程式](http://msdn.microsoft.com/library/mt652092.aspx) | 安裝選項： <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [快速入門](https://www.microsoft.com/en-us/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows、Linux、macOS | [Ruby Driver for SQL Server](http://msdn.microsoft.com/library/mt691981.aspx) | [安裝](https://msdn.microsoft.com/library/mt711041.aspx) | [快速入門](https://www.microsoft.com/en-us/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows、Linux、macOS | [Microsoft ODBC Driver for SQL Server](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) | [下載](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) |  

下表列出一些物件關聯式對應 (ORM) 架構和 Web 架構範例，可供用戶端應用程式搭配使用在內部部署或雲端中，或在 Linux、Windows 上執行的 Microsoft SQL Server 或搭配使用 Docker，還可以用於 Azure SQL Database 和 Azure SQL 資料倉儲。 

| 語言 | 平台 | ORM |
| :-- | :-- | :-- |
| C# | Windows、Linux、macOS | [Entity Framework](https://docs.microsoft.com/en-us/ef)<br>[Entity Framework Core](https://docs.microsoft.com/en-us/ef/core/index) |
| Java | Windows、Linux、macOS |[Hibernate ORM](http://hibernate.org/orm)|
| PHP | Windows、Linux | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows、Linux、macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| Python | Windows、Linux、macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows、Linux、macOS | [Ruby on Rails](http://rubyonrails.org/) |

## <a name="related-links"></a>相關連結
- [SQL Server 驅動程式](http://msdn.microsoft.com/library/mt654049.aspx)，適用於從用戶端應用程式連接
- [使用 .NET (C#) 連接到 SQL Database](sql-database-connect-query-dotnet.md)
- [使用 PHP 連接到 SQL Database](sql-database-connect-query-php.md)
- [使用 Node.js 連接到 SQL Database](sql-database-connect-query-nodejs.md)
- [使用 Java 連接到 SQL Database](sql-database-connect-query-java.md)
- [使用 Python 連接到 SQL Database](sql-database-connect-query-python.md)
- [使用 Ruby 連接到 SQL Database](sql-database-connect-query-ruby.md)
