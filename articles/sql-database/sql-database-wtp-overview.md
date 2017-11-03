---
title: "Azure SQL Database 多租用戶應用程式簡介 - Wingtip SaaS | Microsoft Docs"
description: "藉由使用採用 Azure SQL Database - Wingtip SaaS 的範例多租用戶應用程式來學習"
keywords: SQL Database Azure
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: sstein
ms.openlocfilehash: 46c9a3eadc2c23959b4d08649c6c0215d44b493e
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="introduction-to-a-sql-database-multi-tenant-saas-app-example"></a>SQL Database 多租用戶 SaaS 應用程式範例的簡介

Wingtip SaaS 應用程式是一個多租用戶應用程式範例，可示範 SQL Database 的獨特優點。 此應用程式使用租用戶各有資料庫的 SaaS 應用程式模式來維護多租用戶。 此應用程式是設計成用來展示支援 SaaS 案例 (包括數個 SaaS 設計和管理模式) 的 Azure SQL Database 功能。 為了快速啟動並執行，五分鐘內就會部署 Wingtip SaaS 應用程式！

在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub 存放庫可取得應用程式原始程式碼和管理指令碼。 若要執行指令碼，請[將 Learning Modules 資料夾](#download-and-unblock-the-wingtip-saas-scripts)下載到您的本機電腦。

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL Database Wingtip SaaS 教學課程

部署應用程式之後，請瀏覽在初始部署時建立的下列教學課程。 這些教學課程會探索常見 SaaS 模式，其利用 SQL Database、SQL 資料倉儲和其他 Azure 服務的內建功能。 教學課程包含 PowerShell 指令碼，以及可大幅簡化理解的詳細說明，並且在您的應用程式中實作相同的 SaaS 管理模式。


| 教學課程 | 說明 |
|:--|:--|
|[部署及探索 Wingtip SaaS 應用程式](sql-database-saas-tutorial.md)| **從這裡開始！** 將 Wingtip SaaS 應用程式部署至 Azure 訂用帳戶並加以探索。 |
|[佈建租用戶並在目錄中註冊](sql-database-saas-tutorial-provision-and-catalog.md)| 了解應用程式如何使用目錄資料庫連線至租用戶，以及目錄如何將租用戶對應至其資料。 |
|[監視及管理效能](sql-database-saas-tutorial-performance-monitoring.md)| 了解如何使用 SQL Database 的監視功能，以及如何設定超過效能閾值時的警示。 |
|[透過 Log Analytics (OMS) 監視](sql-database-saas-tutorial-log-analytics.md) | 了解如何使用 [Log Analytics](../log-analytics/log-analytics-overview.md) 來監視跨多個集區的大量資源。 |
|[還原單一租用戶](sql-database-saas-tutorial-restore-single-tenant.md)| 了解如何將租用戶資料庫還原到先前的時間點。 同時包含還原到平行資料庫 (讓現有的租用戶資料庫留在線上) 的步驟。 |
|[管理租用結構描述](sql-database-saas-tutorial-schema-management.md)| 了解如何更新結構描述，以及更新跨所有 Wingtip SaaS 租用戶的參考資料。 |
|[執行臨機操作分析](sql-database-saas-tutorial-adhoc-analytics.md) | 建立臨機操作分析資料庫並對所有租用戶執行即時分散式查詢。  |
|[執行租用戶分析](sql-database-saas-tutorial-tenant-analytics.md) | 將租用戶資料擷取到分析資料庫或資料倉儲中，以便執行離線分析查詢。 |



## <a name="application-architecture"></a>應用程式架構

Wingtip SaaS 應用程式會使用「各租用戶資料庫」模型，並使用 SQL 彈性集區將效率最大化。 若要將租用戶佈建和對應至其資料，則會使用目錄資料庫。 核心 Wingtip SaaS 應用程式會使用具有三個範例租用戶的集區，再加上目錄資料庫。 完成許多 Wingtip SaaS 教學課程，可藉由引進分析資料庫、跨資料庫結構描述管理等等，造成初始部署的附加元件。


![Wingtip SaaS 架構](media/sql-database-wtp-overview/app-architecture.png)


在進行教學課程並使用應用程式時，務必將焦點放在與資料層相關的 SaaS 模式。 換句話說，著重於資料層，且不要過度分析應用程式本身。 了解這些 SaaS 模式的實作方式是在應用程式中實作這些模式的關鍵，同時考慮針對您特定的業務需求進行任何必要的修改。

## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>下載並解除封鎖 Wingtip SaaS 指令碼

從外部來源下載 zip 檔案並進行解壓縮時，Windows 可能會封鎖可執行的內容 (指令碼、dll)。 從 zip 檔案解壓縮指令碼時，***請遵循下列步驟先解除封鎖 .zip 檔案，再進行解壓縮***。 這可確保允許執行指令碼。

1. 瀏覽至 [Wingtip SaaS github 存放庫](https://github.com/Microsoft/WingtipSaaS)。
1. 按一下 [複製或下載]。
1. 按一下 [下載 ZIP]，並儲存檔案。
1. 以滑鼠右鍵按一下 **WingtipSaaS-master.zip** 檔案，然後選取 [內容]。
1. 在 [一般] 索引標籤上，選取 [解除封鎖]。
1. 按一下 [確定] 。
1. 將檔案解壓縮。

指令碼位於 *..\\WingtipSaaS-master\\Learning Modules* 資料夾中。


## <a name="working-with-the-wingtip-saas-powershell-scripts"></a>使用 Wingtip SaaS PowerShell 指令碼

若要充分利用此範例，您必須深入了解所提供的指令碼。 使用中斷點和逐步執行指令碼，並檢查不同 SaaS 模式的實作方式詳細資料。 若要輕鬆地逐步執行所提供的指令碼和模組，以獲得充分瞭解，我們建議使用 [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise)。

### <a name="update-the-configuration-file-for-your-deployment"></a>更新部署的組態檔

使用您在部署期間設定的資源群組和使用者值，編輯 **UserConfig.psm1** 檔案：

1. 開啟 PowerShell ISE 並載入...\\Learning Modules\\*UserConfig.psm1* 
1. 將 ResourceGroupName 和 Name 更新為您部署的特定值 (只在第 10 行和第 11 行)。
1. 儲存變更！

在這裏設定這些值，只是要讓您必須更新每個指令碼中的這些部署特有值。

### <a name="execute-scripts-by-pressing-f5"></a>按 F5 執行指令碼

數個指令碼會使用 $PSScriptRoot 來瀏覽資料夾，而只有在按 F5 執行指令碼時才會評估 $PSScriptRoot。  醒目提示並執行選取項目 (**F8**) 可能會導致錯誤，因此請在執行指令碼時按 **F5**。

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>逐步執行指令碼來檢查實作

瞭解指令碼的最佳方法是逐步執行它們以了解其作用。 查看包含的 **Demo-** 指令碼，其可呈現輕鬆遵循的高階工作流程。 **Demo-** 指令碼會顯示完成每個工作所需的步驟，所以設定中斷點並且更深入探詢個別的呼叫，以查看不同 SaaS 模式的實作詳細資料。

探索及逐步執行 PowerShell 指令碼的秘訣：

* 在 PowerShell ISE 中開啟 **Demo-** 指令碼。
* 執行或繼續使用 **F5** (不建議使用 **F8**，因為在執行指令碼的選取項目時不會評估 $PSScriptRoot)。
* 按一下或選取一行，然後按 **F9** 放置中斷點。
* 使用 **F10** 不進入函式或指令碼呼叫。
* 使用 **F11** 逐步執行函式或指令碼呼叫。
* 使用 **Shift + F11** 跳離目前的函式或指令碼呼叫。


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>使用 SSMS 瀏覽資料庫結構描述並執行 SQL 查詢

使用 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 來連線及瀏覽應用程式伺服器和資料庫。

部署一開始有兩部可連線的 SQL Database 伺服器 - *tenants1-&lt;User&gt;* 伺服器和 *catalog-&lt;User&gt;* 伺服器。 若要確保示範連線成功，這兩部伺服器都有允許所有 IP 通過的[防火牆規則](sql-database-firewall-configure.md)。


1. 開啟 SSMS 並連線到 tenants1-*&lt;User&gt;.database.windows.net* 伺服器。
1. 按一下 [連線] > **[資料庫引擎...]**：

   ![目錄伺服器](media/sql-database-wtp-overview/connect.png)

1. 示範認證︰ 登入= developer、密碼 = P@ssword1

   ![connection](media\sql-database-wtp-overview\tenants1-connect.png)

1. 重複步驟 2-3 並連線到 catalog-&lt;User&gt;.database.windows.net 伺服器。

成功連線之後，您會看到這兩部伺服器。 視您已佈建的租用戶而定，您的資料庫清單可能有所不同：

![物件總管](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>後續步驟

[部署 Wingtip SaaS 應用程式](sql-database-saas-tutorial.md)
