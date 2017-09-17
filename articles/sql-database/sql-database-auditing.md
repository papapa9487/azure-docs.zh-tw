---
title: "開始使用 Azure SQL 資料庫稽核 | Microsoft Docs"
description: "開始使用 Azure SQL 資料庫稽核"
services: sql-database
documentationcenter: 
author: giladm
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: security
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: giladm
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: ea45fe72a499daa363dc9e43f82c94af38bf6e85
ms.contentlocale: zh-tw
ms.lasthandoff: 08/31/2017

---
# <a name="get-started-with-sql-database-auditing"></a>開始使用 SQL Database 稽核
Azure SQL 資料庫稽核會追蹤資料庫事件，並將事件寫入您 Azure 儲存體帳戶中的稽核記錄。 稽核也具備下列功能：

* 協助您保持法規遵循、了解資料庫活動，以及深入了解可指出商務考量或疑似安全違規的不一致和異常。

* 啟用及推動遵循法規標準，但不保證符合法規。 如需有關支援標準法規的 Azure 程式詳細資訊，請參閱 [Azure 信任中心](https://azure.microsoft.com/support/trust-center/compliance/)。


## <a id="subheading-1"></a>Azure SQL 資料庫稽核概觀
您可以使用 SQL 資料庫稽核完成下列工作：


* **保留** 所選事件的稽核記錄。 您可以定義要稽核的資料庫動作類別。
* **報告** 資料庫活動。 您可以使用預先設定的報告和儀表板，以便快速開始使用活動和事件報告。
* **分析** 報告。 您可以尋找可疑事件、異常活動及趨勢。

您可以依照[設定資料庫的稽核](#subheading-2)一節中的說明，針對不同類型的事件類別設定稽核。

系統會將稽核記錄檔寫入 Azure 訂用帳戶的 Azure Blob 儲存體。


## <a id="subheading-8"></a>定義伺服器層級與資料庫層級的稽核原則

您可以針對特定資料庫定義稽核原則，或將稽核原則定義為預設伺服器原則：

* 伺服器原則會套用至伺服器上所有現有和新建立的資料庫。

* 如果伺服器 Blob 稽核已啟用，它一律會套用到資料庫 (也就是將會稽核資料庫)，不論資料庫稽核設定為何。

* 如果在伺服器和資料庫上都啟用 Blob 稽核，這將「不會」覆寫或變更伺服器 Blob 稽核的任何設定。 這兩種稽核將會並存。 換句話說，系統將會對資料庫進行兩次相同的稽核 (一次是由伺服器原則，一次是由資料庫原則)。

   > [!NOTE]
   > 您應該避免同時啟用伺服器 Blob 稽核與資料庫 Blob 稽核，除非：
    > * 您需要為特定資料庫使用不同的儲存體帳戶或保留期間。
    > * 您想要針對特定資料庫稽核不同於伺服器上其餘資料庫所稽核的事件類型或類別。 例如，您可能只需要針對特定資料庫稽核資料表插入。
   > 
   > 否則，建議只啟用伺服器層級 Blob 稽核，並讓所有資料庫的資料庫層級稽核保留在停用狀態。


## <a id="subheading-2"></a>設定資料庫的稽核
下節描述使用 Azure 入口網站進行稽核的設定。

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 移至您想要稽核的 SQL 資料庫/SQL 伺服器 [設定] 刀鋒視窗。 在 [設定] 刀鋒視窗中，選取 [稽核與威脅偵測]。

    <a id="auditing-screenshot"></a> ![導覽窗格][1]
3. 如果您想要設定伺服器稽核原則 (其將套用至此伺服器上所有現有和新建立的資料庫)，您可以選取資料庫稽核刀鋒視窗中的 [檢視伺服器設定] 連結。 然後，您可以檢視或修改伺服器稽核設定。

    ![導覽窗格][2]
4. 如果您想要啟用資料庫層級的 Blob 稽核 (同時啟用或不啟用伺服器層級的稽核)，請針對 [稽核] 選取 [開啟]，並針對 [稽核類型] 選取 [Blob]。

    如果已啟用伺服器 Blob 稽核，資料庫設定的稽核將會與伺服器 Blob 稽核並存。  

    ![導覽窗格][3]
5. 若要開啟 [稽核記錄儲存體] 刀鋒視窗，請選取 [儲存體詳細資料]。 選取將儲存記錄的 Azure 儲存體帳戶，然後選取將舊記錄刪除之前的保留期間。 然後按一下 [確定] 。 
   >[!TIP] 
   >若要充分利用稽核報告範本，請讓所有稽核的資料庫都使用相同的儲存體帳戶。 

    <a id="storage-screenshot"></a> ![導覽窗格][4]
6. 如果您想要自訂稽核的事件，您可以透過 PowerShell 或 REST API 來自訂。 如需詳細資訊，請參閱[自動化 (PowerShell/REST API)](#subheading-7) 一節。
7. 設定您的稽核設定之後，您可以開啟新的威脅偵測功能，並設定電子郵件以接收安全性警示。 使用威脅偵測時，您會接收與指示潛在安全性威脅的異常資料庫活動相關的主動式警示。 如需詳細資訊，請參閱[開始使用威脅偵測](sql-database-threat-detection-get-started.md)。
8. 按一下 [儲存] 。





## <a id="subheading-3"></a>分析稽核記錄和報告
稽核記錄會在您於安裝期間選擇的 Azure 儲存體帳戶中彙總。 您可以使用工具 (例如 [Azure 儲存體總管](http://storageexplorer.com/)) 來查看稽核記錄。

Blob 稽核記錄是以 Blob 檔案集合的方式儲存在名為 **sqldbauditlogs** 的容器內。

如需有關 Blob 稽核記錄儲存體資料夾階層、Blob 命名慣例和記錄格式的進一步詳細資訊，請參閱 [Blob 稽核記錄格式參考 (.docx 檔案下載)](https://go.microsoft.com/fwlink/?linkid=829599) (英文)。

有幾種方法可以用於檢視 Blob 稽核記錄：

* 使用 [Azure 入口網站](https://portal.azure.com)。  開啟相關的資料庫。 在資料庫的 [稽核與威脅偵測] 刀鋒視窗的頂端，按一下 [檢視稽核記錄]。

    ![導覽窗格][7]

    隨即開啟 [稽核記錄] 刀鋒視窗，您可以在其中檢視記錄。

    - 您可以按一下 [稽核記錄] 刀鋒視窗頂端的 [篩選] 來檢視特定日期。
    - 您可以在由伺服器原則或資料庫原則稽核建立的稽核記錄之間切換。

       ![導覽窗格][8]

* 使用系統函數 **sys.fn_get_audit_file** (T-SQL) 以表格格式傳回稽核記錄資料。 如需有關如何使用此函數的詳細資訊，請參閱 [sys.fn_get_audit_file 文件](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql) (英文)。


* 使用 SQL Server Management Studio (SSMS 17 或更新版本) 中的 [合併稽核檔案]：  
    1. 從 SSMS 功能表選取 [檔案] > [開啟] > [合併稽核檔案]。

        ![導覽窗格][9]
    2. 隨即開啟 [新增稽核檔案] 對話方塊。 選取其中一個 [新增] 選項以選擇是否要從本機磁碟合併稽核檔案，或從 Azure 儲存體匯入稽核檔案 (您將需要提供您的 Azure 儲存體詳細資料和帳戶金鑰)。

    3. 已新增要合併的所有檔案之後，請按一下 [確定] 以完成合併作業。

    4. 合併的檔案會在 SSMS 中開啟，您可以在其中檢視和分析該檔案，以及將其匯出至 XEL 或 CSV 檔案或資料表。

* 使用我們建立的[同步應用程式](https://github.com/Microsoft/Azure-SQL-DB-auditing-OMS-integration)。 其會在 Azure 中執行，並利用 Operations Management Suite (OMS) Log Analytics 公開 API 將 SQL 稽核記錄推送至 OMS。 同步應用程式會透過 OMS Log Analytics 儀表板，將 SQL 稽核記錄推送至 OMS Log Analytics。 

* 使用 Power BI。 您可以在 Power BI 中檢視和分析稽核記錄資料。 深入了解 [Power BI，並存取可下載的範本](https://blogs.msdn.microsoft.com/azuresqldbsupport/2017/05/26/sql-azure-blob-auditing-basic-power-bi-dashboard/)。

* 透過入口網站或使用工具 (例如 [Azure 儲存體總管](http://storageexplorer.com/)) 從 Azure 儲存體 Blob 容器下載記錄檔。
    * 在您將記錄檔下載到本機之後，您可以按兩下檔案，以在 SSMS 中開啟、檢視及分析記錄。
    * 您也可以透過 Azure 儲存體總管同時下載多個檔案。 以滑鼠右鍵按一下特定子資料夾 (例如，包含特定日期所有記錄檔的子資料夾)，然後選取 [另存新檔] 以儲存在本機資料夾中。

* 其他方法：
   * 下載多個檔案 (或包含一整天記錄檔的子資料夾，如本清單中上一個項目中所述) 之後，您可以在本機合併這些檔案，如稍早所述的 SSMS 合併稽核檔案指示中所述。

   * 以程式設計方式檢視 Blob 稽核記錄：

     * 使用[擴充事件讀取器](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/) C# 程式庫。
     * 使用 PowerShell [查詢擴充事件檔案](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/)。




## <a id="subheading-5"></a>實際作法
<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">稽核異地複寫資料庫</a>
使用異地複寫資料庫時，若要在次要資料庫上設定稽核，您可以啟用**次要伺服器**上的稽核，或是啟用主要資料庫上的稽核 (在此情況下，次要資料庫的稽核原則會與主要資料庫相同)。

* 伺服器層級 (**建議**)：啟動**主要伺服器**和**次要伺服器**上的稽核 - 將根據其個別的伺服器層級原則對主要和次要資料庫分開進行稽核。

* 資料庫層級：只能從主要資料庫稽核設定來設定次要資料庫的資料庫層級稽核。
   * 必須在「主要資料庫本身」 (而不是在伺服器上) 啟用 Blob 稽核。
   * 在主要資料庫上啟用 Blob 稽核之後，它也會在次要資料庫上變成啟用狀態。

     >[!IMPORTANT]
     >使用資料庫層級稽核時，次要資料庫的儲存體設定將會和主要資料庫上的設定完全相同，這會導致跨地區流量。 除非需要進行資料庫層級稽核，否則建議僅在主要和次要伺服器上啟用伺服器層級稽核，並讓所有資料庫的資料庫層級稽核保留在停用狀態。
<br>

### <a id="subheading-6">儲存體金鑰重新產生</a>
在生產中，您可能會定期重新整理儲存體金鑰。 重新整理金鑰時，您需要重新儲存稽核原則。 程序如下：

1. 開啟 [儲存體詳細資料] 刀鋒視窗。 在 [儲存體存取金鑰] 方塊中，選取 [次要]，然後按一下 [確定]。 然後按一下稽核組態刀鋒視窗頂端的 [儲存]。

    ![導覽窗格][5]
2. 移至儲存體組態刀鋒視窗，並重新產生主要存取金鑰。

    ![導覽窗格][6]
3. 返回稽核組態刀鋒視窗，將儲存體存取金鑰從次要切換成主要，然後按一下 [確定]。 然後按一下稽核組態刀鋒視窗頂端的 [儲存]。
4. 返回儲存體組態刀鋒視窗，並重新產生次要存取金鑰 (為下一個金鑰重新整理週期做準備)。

## <a id="subheading-7"></a>自動化 (PowerShell/REST API)
您也可以使用下列自動化工具在 Azure SQL Database 中設定稽核：

* **PowerShell Cmdlet**：

   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]

   如需指令碼範例，請參閱[使用 PowerShell 設定稽核與威脅偵測](scripts/sql-database-auditing-and-threat-detection-powershell.md)。

* **REST API - Blob 稽核**：

   * [建立或更新資料庫 Blob 稽核原則](https://msdn.microsoft.com/library/azure/mt695939.aspx)
   * [建立或更新伺服器 Blob 稽核原則](https://msdn.microsoft.com/library/azure/mt771861.aspx)
   * [取得資料庫 Blob 稽核原則](https://msdn.microsoft.com/library/azure/mt695938.aspx)
   * [取得伺服器 Blob 稽核原則](https://msdn.microsoft.com/library/azure/mt771860.aspx)
   * [取得伺服器 Blob 稽核操作結果](https://msdn.microsoft.com/library/azure/mt771862.aspx)


<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)  

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png

[101]: /powershell/module/azurerm.sql/get-azurermsqldatabaseauditing
[102]: /powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditing
[103]: /powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing
[104]: /powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing
[105]: /powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditing
[106]: /powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditing

