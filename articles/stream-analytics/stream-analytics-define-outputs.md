---
title: "串流分析輸出︰儲存體、分析的選項 | Microsoft Docs"
description: "深入了解串流分析資料輸出的選項 (包括使用於分析結果的 Power BI)。"
keywords: "資料轉換, 分析結果, 資料儲存體選項"
services: stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: ba6697ac-e90f-4be3-bafd-5cfcf4bd8f1f
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 52d131384c61b57d31873530304c644d6e9c11f1
ms.contentlocale: zh-tw
ms.lasthandoff: 09/07/2017

---
# <a name="stream-analytics-outputs-options-for-storage-analysis"></a>串流分析輸出︰儲存體、分析的選項
當您在編寫串流分析工作時，請考慮產生資料的取用方式。 您要如何檢視串流分析工作的結果，並將儲存於何處？

為了要啟用各種應用程式模式，Azure 串流分析針對儲存輸出及檢視分析結果提供了數種不同的選項。 這讓您能輕鬆地檢視工作輸出，還讓您對於資料倉儲和其他用途之工作輸出的取用及儲存方式更有彈性。 任何在工作中設定的輸出，都必須在工作開始之前，以及在事件開始運作之前就已經存在。 例如，如果您把 Blob 儲存體當做輸出來使用，工作就不會自動建立儲存體帳戶。 使用者必須在 Azure 串流分析工作開始之前建立儲存體帳戶。

## <a name="azure-data-lake-store"></a>Azure Data Lake Store
串流分析支援 [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)。 此儲存體可讓您存放任何大小、類型和擷取速度的資料，以便進行運作和探究分析。 此外，串流分析需要經過授權，才能存取 Data Lake Store。 如需有關授權，以及如何註冊 Data Lake Store (如有需要) 的詳細資料，請參閱 [Data Lake 輸出](stream-analytics-data-lake-output.md)文章。

### <a name="authorize-an-azure-data-lake-store"></a>授權 Azure Data Lake Store
在 Azure 入口網站中選取 Data Lake Storage 作為輸出時，系統會提示您授權與現有 Data Lake Store 的連線。  

![授權 Data Lake Store](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

接著請填寫 Data Lake Store 輸出的屬性，如下所示︰

![授權 Data Lake Store](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

下表列出建立 Data Lake Store 輸出所需的屬性名稱及其描述。

<table>
<tbody>
<tr>
<td><B>屬性名稱</B></td>
<td><B>說明</B></td>
</tr>
<tr>
<td>輸出別名</td>
<td>此為易記名稱，用於在查詢中將查詢輸出指向這個 Data Lake Store。</td>
</tr>
<tr>
<td>帳戶名稱</td>
<td>您傳送輸出的 Data Lake Storage 帳戶名稱。 您將看到 Data Lake Store 帳戶的下拉式清單，登入入口網站的使用者可存取該下拉式清單。</td>
</tr>
<tr>
<td>路徑前置詞模式</td>
<td>用來在指定的 Data Lake Store 帳戶中寫入檔案的檔案路徑。 <BR>{date}、{time}<BR>範例 1：folder1/logs/{date}/{time}<BR>範例 2：folder1/logs/{date}</td>
</tr>
<tr>
<td>日期格式 [<I>選用</I>]</td>
<td>如果前置詞路徑中使用日期權杖，您可以選取組織檔案要用的日期格式。 範例：YYYY/MM/DD</td>
</tr>
<tr>
<td>時間格式 [<I>選用</I>]</td>
<td>如果前置詞路徑中使用時間權杖，請指定組織檔案要用的時間格式。 目前唯一支援的值為 HH。</td>
</tr>
<tr>
<td>事件序列化格式</td>
<td>輸出資料的序列化格式。 支援 JSON、CSV 和 Avro。</td>
</tr>
<tr>
<td>編碼</td>
<td>若為 CSV 或 JSON 格式，必須指定編碼。 UTF-8 是目前唯一支援的編碼格式。</td>
</tr>
<tr>
<td>分隔符號</td>
<td>僅適用於 CSV 序列化。 串流分析可支援多種序列化 CSV 資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。</td>
</tr>
<tr>
<td>格式</td>
<td>僅適用於 JSON 序列化。 分隔的行會指定輸出的格式化方式為利用新行分隔每個 JSON 物件。 陣列會指定輸出將會格式化為 JSON 物件的陣列。</td>
</tr>
</tbody>
</table>

### <a name="renew-data-lake-store-authorization"></a>更新 Data Lake Store 授權
如果您在建立工作之後或上次驗證過後變更了密碼，則需要重新驗證您的 Data Lake Store 帳戶。

![授權 Data Lake Store](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  

## <a name="sql-database"></a>SQL Database
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 做為資料輸出。 串流分析工作會寫入至 Azure SQL Database 中的現有資料表。  請注意，資料表結構描述必須完全符合工作輸出的欄位及其類型。 您也可以透過 SQL Database 輸出選項，將 [Azure SQL 資料倉儲](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) 指定為輸出 (這是一個預覽功能)。 下表列出屬性名稱及其描述以建立 SQL Database 輸出。

| 屬性名稱 | 說明 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個資料庫。 |
| 資料庫 |您傳送輸出的資料庫名稱 |
| 伺服器名稱 |SQL Database 伺服器名稱 |
| 使用者名稱 |具有寫入資料庫存取權限的使用者名稱 |
| 密碼 |連接到資料庫的密碼 |
| 資料表 |將在其中寫入輸出的資料表名稱。 資料表名稱會區分大小寫，且資料表的結構描述應該完全符合工作輸出所產生的欄位數目和其類型。 |

> [!NOTE]
> 目前「串流分析」中的工作輸出支援 Azure SQL Database 提供項目。 不過，不支援執行已連結資料庫之 SQL Server 的「Azure 虛擬機器」。 這在未來的版本中有可能變更。
> 
> 

## <a name="blob-storage"></a>Blob 儲存體
若要將大量非結構化資料儲存於雲端，Blob 儲存體提供具有成本效益且可擴充的解決方案。  如需 Azure Blob 儲存體及其使用方式的簡介，請參閱 [如何使用 Blob](../storage/blobs/storage-dotnet-how-to-use-blobs.md)中的文件。

下表列出屬性名稱及其描述以建立 blob 輸出。

<table>
<tbody>
<tr>
<td>屬性名稱</td>
<td>說明</td>
</tr>
<tr>
<td>輸出別名</td>
<td>此為易記名稱，用於在查詢中將查詢輸出指向這個 blob 儲存體。</td>
</tr>
<tr>
<td>儲存體帳戶</td>
<td>您傳送輸出的儲存體帳戶名稱。</td>
</tr>
<tr>
<td>儲存體帳戶金鑰</td>
<td>與儲存體帳戶相關聯的密碼金鑰。</td>
</tr>
<tr>
<td>儲存體容器</td>
<td>容器提供邏輯分組給儲存在 Microsoft Azure Blob 服務中的 blob。 當您將 blob 上傳至 Blob 服務時，您必須指定該 blob 的容器。</td>
</tr>
<tr>
<td>路徑前置詞模式 [選用]</td>
<td>用來在指定容器中寫入 Blob 的檔案路徑。<BR>在該路徑內，您也可以選擇使用下列 2 個變數的一或多個執行個體來指定 blob 的寫入頻率：<BR>{date}、{time}<BR>範例 1：cluster1/logs/{date}/{time}<BR>範例 2：cluster1/logs/{date}</td>
</tr>
<tr>
<td>日期格式 [選用]</td>
<td>如果前置詞路徑中使用日期權杖，您可以選取組織檔案要用的日期格式。 範例：YYYY/MM/DD</td>
</tr>
<tr>
<td>時間格式 [選用]</td>
<td>如果前置詞路徑中使用時間權杖，請指定組織檔案要用的時間格式。 目前唯一支援的值為 HH。</td>
</tr>
<tr>
<td>事件序列化格式</td>
<td>輸出資料的序列化格式。  支援 JSON、CSV 和 Avro。</td>
</tr>
<tr>
<td>編碼</td>
<td>若為 CSV 或 JSON 格式，必須指定編碼。 UTF-8 是目前唯一支援的編碼格式。</td>
</tr>
<tr>
<td>分隔符號</td>
<td>僅適用於 CSV 序列化。 串流分析可支援多種序列化 CSV 資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。</td>
</tr>
<tr>
<td>格式</td>
<td>僅適用於 JSON 序列化。 分隔的行會指定輸出的格式化方式為利用新行分隔每個 JSON 物件。 陣列會指定輸出將會格式化為 JSON 物件的陣列。 只有在作業停止或串流分析已移動到下一個時間範圍時，才會關閉這個陣列。 一般情況下，最好使用分行的 JSON，因為它不需要任何特殊處理，同時仍會寫入輸出檔案。</td>
</tr>
</tbody>
</table>

## <a name="event-hub"></a>事件中樞
[事件中樞](https://azure.microsoft.com/services/event-hubs/) 是具高延展性的發佈-訂閱事件擷取器。 它每秒可以收集數百萬個事件。  事件中樞做為輸出的其中一個使用時機，就是當串流分析工作的輸出將成為另一個串流工作的輸入時。

設定事件中樞串流做為輸出時，需要幾個參數。

| 屬性名稱 | 說明 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個事件中樞。 |
| 服務匯流排 命名空間 |服務匯流排命名空間是一個容器，包含一組訊息實體。 建立新的事件中樞時，也會建立服務匯流排命名空間 |
| 事件中樞 |事件中樞輸出的名稱 |
| 事件中樞原則名稱 |共用的存取原則，可以在事件中樞的 [設定] 索引標籤上建立。每一個共用存取原則會有名稱、權限 (由您設定) 和存取金鑰 |
| 事件中樞原則金鑰 |用來驗證服務匯流排命名空間之存取權的共用存取金鑰 |
| 資料分割索引鍵資料行 [選用] |這個資料行包含事件中樞輸出的資料分割索引鍵。 |
| 事件序列化格式 |輸出資料的序列化格式。  支援 JSON、CSV 和 Avro。 |
| 編碼 |對於 CSV 和 JSON 而言，UTF-8 是目前唯一支援的編碼格式 |
| 分隔符號 |僅適用於 CSV 序列化。 串流分析可支援多種以 CSV 格式序列化資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| 格式 |僅適用於 JSON 類型。 分隔的行會指定輸出的格式化方式為利用新行分隔每個 JSON 物件。 陣列會指定輸出將會格式化為 JSON 物件的陣列。 |

## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/) 當做串流分析工作的輸出，來為分析結果提供豐富的視覺體驗。 這項功能可以用於可運作的儀表板、產生報告，以及度量驅動的報告。

### <a name="authorize-a-power-bi-account"></a>授權 Power BI 帳戶
1. 在 Azure 入口網站中選取 Power BI 作為輸出時，您將會收到提示，要授權現有的 Power BI 使用者或建立新的 Power BI 帳戶。  
   
   ![授權 Power BI 使用者](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  
2. 如果您尚未擁有帳戶，請建立新帳戶，然後按一下 [立即授權]。  會顯示類似下列的畫面：  
   
   ![Azure 帳戶 Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  
3. 在此步驟中，請提供工作或學校帳戶以授權 Power BI 輸出。 如果您尚未註冊 Power BI，請選擇 [立即註冊]。 您用於 Power BI 的工作或學校帳戶可能與您目前用來登入的 Azure 訂用帳戶不同。

### <a name="configure-the-power-bi-output-properties"></a>設定 Power BI 輸出屬性
驗證 Power BI 帳戶之後，您可以設定 Power BI 輸出的屬性。 下表是設定 Power BI 輸出的屬性名稱清單及其描述。

| 屬性名稱 | 說明 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個 Power BI 輸出。 |
| 群組工作區 |若要與其他 Power BI 使用者共用資料，您可以選取 Power BI 帳戶內的群組，若您不希望寫入群組，請選擇「我的工作區」。  更新現有的群組需要更新 Power BI 驗證。 |
| 資料集名稱 |提供 Power BI 輸出需要使用的資料集名稱 |
| 資料表名稱 |提供 Power BI 輸出資料集的資料表名稱。 目前，串流分析工作的 Power BI 輸出中，一個資料集只能有一個資料表 |

如需設定 Power BI 輸出和儀表板的逐步解說，請參閱 [Azure 串流分析與 Power BI](stream-analytics-power-bi-dashboard.md) 文章。

> [!NOTE]
> 未在 Power BI 儀表板中明確建立資料集和資料表。 當工作開始並將輸出提取至 Power BI 時，就會自動填入資料集和資料表。 請注意，如果工作查詢沒有產生任何結果，將不會建立資料集和資料表。 也請注意，如果 Power BI 已經具有與串流分析工作中提供的名稱相同的資料集和資料表，可能會覆寫現有的資料。
> 
> 

### <a name="schema-creation"></a>建立結構描述
如果 Power BI 資料集和資料表尚不存在，則 Azure 串流分析會代表使用者建立一個。 在其他情況下，則會以新的值更新資料表。目前的限制是一個資料集內只能存在一個資料表。

### <a name="data-type-conversion-from-asa-to-power-bi"></a>從 ASA 至 Power BI 的資料類型轉換
如果輸出結構描述變更，則 Azure 串流分析會在執行階段動態更新資料模型。 所有資料行名稱變更、資料行類型變更以及資料行新增或移除都會加以追蹤。

如果 POWER BI 資料集和資料表不存在，此資料表包含從[串流分析資料類型](https://msdn.microsoft.com/library/azure/dn835065.aspx)至 Power BI [實體資料模型 (EDM) 類型](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/)的資料類型轉換。


從串流分析 | 至 Power BI
-----|-----|------------
bigint | Int64
nvarchar(max) | String
datetime | DateTime
float | 兩倍
記錄陣列 | 字串類型、常數值 “IRecord” 或 “IArray”

### <a name="schema-update"></a>更新結構描述
串流分析會根據輸出中的第一組事件來推斷資料模型結構描述。 之後會視需要更新資料模型結構描述，以容納原始結構描述放不下的連入事件。

應該避免 `SELECT *` 查詢，以防止跨越資料列的動態結構描述更新。 除了潛在的效能影響以外，也可能導致結果所花費的時間不定。 應選取必須顯示在 Power BI 儀表板上的確切欄位。 此外，資料值應該與所選的資料類型相符。


先前/目前 | Int64 | String | DateTime | 兩倍
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | 兩倍
兩倍 | 兩倍 | String | String | 兩倍
String | String | String | String |  | String | 
DateTime | String | String |  DateTime | String


### <a name="renew-power-bi-authorization"></a>更新 Power BI 授權
如果您在建立工作之後或上次驗證過後變更了密碼，則需要重新驗證您的 Power BI 帳戶。 如果您在 Azure Active Directory (AAD) 租用戶上設定 Multi-Factor Authentication (MFA)，則也需要每 2 週更新一次 Power BI 授權。 此問題發生時的徵兆就是沒有工作輸出，且作業記錄檔中出現「驗證使用者錯誤」：

  ![Power BI 重新整理權杖錯誤](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

若要解決這個問題，請停止執行工作並移至 Power BI 輸出。  按一下 [更新授權] 連結，並從 [上次停止時間] 重新啟動您的工作以避免資料遺失。

  ![Power BI 更新授權](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>資料表儲存體
[Azure 表格儲存體](../storage/common/storage-introduction.md)提供高可用性且可大幅擴充的儲存體，可讓應用程式自動調整來滿足使用者需求。 資料表儲存體是 Microsoft 的 NoSQL 索引鍵/屬性存放區，其中可以使用結構化資料，但結構描述的限制較少。 使用 Azure 資料表儲存資料時，資料可長期儲存而且調閱方便。

下表列出屬性名稱及其描述以建立資料表輸出。

| 屬性名稱 | 說明 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個資料表儲存體。 |
| 儲存體帳戶 |您傳送輸出的儲存體帳戶名稱。 |
| 儲存體帳戶金鑰 |與儲存體帳戶相關聯的存取金鑰。 |
| 資料表名稱 |資料表的名稱。 如果資料表不存在，將會建立資料表。 |
| 資料分割索引鍵 |包含資料分割索引鍵的輸出資料行名稱。 在構成實體主索引鍵第一個部分的指定資料表內，資料分割索引鍵是資料分割的唯一識別碼。 大小最高為 1 KB 的字串值。 |
| 列索引鍵 |包含資料列索引鍵的輸出資料行名稱。 資料列索引鍵是指定資料分割內實體的唯一識別碼。 它可構成實體主索引鍵的第二個部分。 資料列索引鍵是大小可能高達 1 KB 的字串值。 |
| 批次大小 |批次作業的記錄數目。 預設值通常足以應付大部分的工作，如需修改此設定的詳細資訊，請參閱 [資料表批次作業規格](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) 。 |

## <a name="service-bus-queues"></a>服務匯流排佇列
[服務匯流排佇列](https://msdn.microsoft.com/library/azure/hh367516.aspx) 會採用「先進先出」(FIFO) 訊息傳遞機制。 通常會預期由接收者依訊息加入佇列的時間順序來接收和處理訊息，而且每則訊息只能由一個訊息取用者接收和處理。

下表列出屬性名稱及其描述以建立佇列輸出。

| 屬性名稱 | 說明 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個服務匯流排佇列。 |
| 服務匯流排 命名空間 |服務匯流排命名空間是一個容器，包含一組訊息實體。 |
| 佇列名稱 |服務匯流排佇列的名稱。 |
| 佇列原則名稱 |當您建立佇列時，您也可以在 [佇列設定] 索引標籤上建立共用的存取原則。每一個共用存取原則會有名稱、權限 (由您設定) 和存取金鑰。 |
| 佇列原則金鑰 |用來驗證服務匯流排命名空間之存取權的共用存取金鑰 |
| 事件序列化格式 |輸出資料的序列化格式。  支援 JSON、CSV 和 Avro。 |
| 編碼 |對於 CSV 和 JSON 而言，UTF-8 是目前唯一支援的編碼格式 |
| 分隔符號 |僅適用於 CSV 序列化。 串流分析可支援多種以 CSV 格式序列化資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |
| 格式 |僅適用於 JSON 類型。 分隔的行會指定輸出的格式化方式為利用新行分隔每個 JSON 物件。 陣列會指定輸出將會格式化為 JSON 物件的陣列。 |

## <a name="service-bus-topics"></a>服務匯流排主題
「服務匯流排佇列」提供從傳送者到接收者的一對一通訊方法，而 [服務匯流排主題](https://msdn.microsoft.com/library/azure/hh367516.aspx) 則是提供一對多的通訊形式。

下表列出屬性名稱及其描述以建立資料表輸出。

| 屬性名稱 | 說明 |
| --- | --- |
| 輸出別名 |此為易記名稱，用於在查詢中將查詢輸出指向這個服務匯流排主題。 |
| 服務匯流排 命名空間 |服務匯流排命名空間是一個容器，包含一組訊息實體。 建立新的事件中樞時，也會建立服務匯流排命名空間 |
| 主題名稱 |主題為訊息實體，類似於事件中樞和佇列。 它們可以收集各種裝置和服務的事件資料流。 建立主題時也會給予其特定名稱。 除非已建立訂用帳戶，否則傳送至主題的訊息將無法使用，所以請確保該主題內有一或多個訂用帳戶 |
| 主題原則名稱 |當您建立主題時，您也可以在 [主題設定] 索引標籤上建立共用的存取原則。每一個共用存取原則會有名稱、權限 (由您設定) 和存取金鑰 |
| 主題原則金鑰 |用來驗證服務匯流排命名空間之存取權的共用存取金鑰 |
| 事件序列化格式 |輸出資料的序列化格式。  支援 JSON、CSV 和 Avro。 |
| 編碼 |若為 CSV 或 JSON 格式，必須指定編碼。 UTF-8 是目前唯一支援的編碼格式 |
| 分隔符號 |僅適用於 CSV 序列化。 串流分析可支援多種以 CSV 格式序列化資料常用的分隔符號。 支援的值是逗號、分號、空格、索引標籤和分隔號。 |

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) 是全域散發的多模型資料庫服務，在全球各地提供無限的彈性調整、透過無從驗證結構描述資料模型的豐富查詢和自動索引、保證低延遲以及領先業界的完整 SLA。

以下清單詳細說明用於建立 Azure Cosmos DB 輸出的屬性名稱及其描述。

* **輸出別名**：在您的 ASA 查詢中參照此輸出時所用的別名。  
* **帳戶名稱**：Cosmos DB 帳戶的名稱或端點 URI。  
* **帳戶金鑰**：Cosmos DB 帳戶的共用存取金鑰。  
* **資料庫**：Cosmos DB 資料庫名稱。  
* **集合名稱模式**：要使用之集合的集合名稱或其模式。 您可以使用選用的 {partition} 語彙基元來建構集合名稱的格式，其中的資料分割會從 0 開始。 以下是有效的範例輸入：  
  1\) MyCollection – 必須要有一個名為 “MyCollection” 的集合存在。  
  2\) MyCollection{partition} – 這些集合必須存在 – "MyCollection0”、“MyCollection1”、“MyCollection2” 等，依此類推。  
* **資料分割索引鍵** - 選擇性。 只有當您在集合名稱模式中使用 {parition} 語彙基元時，才需要此索引鍵。 輸出事件中的欄位名稱會用來為跨集合的資料分割輸出指定索引鍵。 若為單一集合輸出，則可使用任何任意的輸出欄，例如 PartitionId。  
* **文件識別碼** ：可省略。 輸出事件中的欄位名稱會用來指定主索引鍵，此為插入或更新作業的依據。  


## <a name="get-help"></a>取得說明
如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>後續步驟
以上就是串流分析 (物聯網資料串流分析專用的受管理服務) 的簡介。 若要深入了解此服務，請參閱：

* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

