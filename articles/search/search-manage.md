---
title: "Azure 入口網站中 Azure 搜尋服務的服務管理"
description: "使用 Azure 入口網站來管理 Azure 搜尋服務 (Microsoft Azure 上的雲端託管搜尋服務)。"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: c87d1fdd-b3b8-4702-a753-6d7e29dbe0a2
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: 916a08aacca428530bc4f728d5de422e04bed8bc
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2017
---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Azure 入口網站中 Azure 搜尋服務的服務管理
> [!div class="op_single_selector"]
> * [入口網站](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python (英文)](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure 搜尋服務是完全受管理、以雲端為基礎的搜尋服務，用來在自訂應用程式內建置豐富的搜尋經驗。 本文章涵蓋服務管理  工作，可供您在 [Azure 入口網站](https://portal.azure.com)中針對已佈建的搜尋服務執行。 *Service administration* 原先的設計是輕量級的，限制為下列工作︰

* 管理及保護 API 金鑰  (用於讀取或寫入您的服務存取權) 的存取權。
* 變更資料分割和複本的配置來調整服務容量。
* 監視資源使用量，相對於服務層的最大限制。

請注意，「升級」不是管理工作。 因為資源是在佈建服務時進行配置，所以移動到其他層需要新的服務。 如需詳細資料，請參閱[建立 Azure 搜尋服務](search-create-service-portal.md)。

> [!Tip]
> 尋找有關如何分析搜尋流量或查詢效能的說明嗎？ 深入解析查詢磁碟區等作業、使用者搜尋哪些詞彙，以及搜尋結果將客戶引導至索引中特定文件的成功率。 如需詳細資訊，請參閱 [Azure 搜尋服務的搜尋流量分析](search-traffic-analytics.md)、[監視使用量和查詢度量](search-monitor-usage.md)、[效能和最佳化](search-performance-optimization.md)。

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>系統管理員權限
Azure 訂用帳戶管理員或共同管理員可以佈建或解除委任服務本身。

在服務中，任何有服務 URL 和系統管理 API 金鑰存取權的人員，都有服務的讀寫存取權限。 讀寫存取權限提供新增、刪除或修改伺服器物件的能力，這些物件包含 API 金鑰、索引、索引子、資料來源、排程，以及透過 [RBAC 定義的角色](#rbac)實作的角色指派。

與 Azure 搜尋服務互動的所有使用者皆屬於下列模式之一︰讀寫存取服務 (系統管理員權限) 或唯讀存取服務 (查詢權限)。 如需詳細資訊，請參閱[管理 API 金鑰](#manage-keys)。

<a id="sys-info"></a>

## <a name="set-rbac-roles-for-administrative-access"></a>設定系統管理存取權的 RBAC 角色
Azure 特別為透過入口網站或 Resource Manager API 管理的所有服務提供[全域角色型授權模型](../active-directory/role-based-access-control-configure.md) 。 「擁有者」、「參與者」和「讀取者」角色可針對指派給各角色的 Active Directory 使用者、群組和安全性主體，決定服務管理層級。 

針對 Azure 搜尋服務，RBAC 權限會決定下列管理工作︰

| 角色 | 工作 |
| --- | --- |
| 擁有者 |建立或刪除服務或服務上的任何物件，包括 api 索引鍵、索引、索引子、索引子資料來源和索引子排程。<p>檢視服務狀態，包括計數和儲存體大小。<p>新增或刪除角色成員資格 (只有「擁有者」可以管理角色成員資格)。<p>訂用帳戶管理員和服務擁有者在擁有者角色具有自動成員資格。 |
| 參與者 |與「擁有者」相同層級的存取權，減去 RBAC 角色管理。 例如，參與者可以檢視和重新產生 `api-key`，但不能修改角色成員資格。 |
| 讀取者 |檢視服務狀態及查詢金鑰。 這個角色的成員無法變更服務組態，也無法檢視管理金鑰。 |

角色不會授與服務端點的存取權限。 搜尋服務作業 (例如索引管理、索引母體擴展，以及搜尋資料查詢) 可透過 api-key 而非角色來控制。 如需詳細資訊，請參閱[什麼是角色存取控制](../active-directory/role-based-access-control-what-is.md)中的「管理授權與資料作業」。

<a id="secure-keys"></a>
## <a name="logging-and-system-information"></a>記錄與系統資訊
Azure 搜尋服務不會透過入口網站或程式設計介面公開個別服務的記錄檔。 在基本層以上，Microsoft 會監視所有 Azure 搜尋服務以達到服務等級協定 (SLA) 的 99.9%可用性。 如果服務很慢或要求輸送量低於 SLA 閾值，支援團隊會檢閱他們可用的記錄檔並解決問題。

就關於您服務的一般資訊而言，您可以透過下列方式取得資訊︰

* 在入口網站中、在服務儀表板上、透過通知、屬性和狀態訊息。
* 使用 [PowerShell](search-manage-powershell.md) 或 [Management REST API](https://docs.microsoft.com/rest/api/searchmanagement/) 來[取得服務屬性](https://docs.microsoft.com/rest/api/searchmanagement/services)，或索引資源使用狀況的狀態。
* 如先前所述，透過[搜尋流量分析](search-traffic-analytics.md)。

<a id="manage-keys"></a>

## <a name="manage-api-keys"></a>管理 API 金鑰
搜尋服務的所有要求需要專為您的服務而產生的 API 金鑰。 此 API 金鑰是驗證存取您搜尋服務端點的唯一機制。 

API 金鑰是由隨機產生的數字和字母所組成的字串。 您可以透過 [RBAC 權限](#rbac)來刪除或讀取金鑰，但無法以使用者定義的密碼取代金鑰。 

存取搜尋服務的金鑰有兩種類型：

* 管理員 (適用於服務的任何讀寫作業)
* 查詢 (適用於唯讀作業，例如針對索引的查詢)

與當佈建服務時會建立管理員 API 金鑰。 有兩個管理員金鑰，指定為主要和次要以將它們保持在各自的位置，但事實上，它們是可互換。 每個服務有兩個管理員金鑰，以便您在轉換其中一個時不會無法存取您的服務。 您可以重新產生任何一種管理員金鑰，但您無法增加管理員金鑰的總數量。 每個搜尋服務最多有 2 個管理員金鑰。

查詢金鑰是專為直接呼叫搜尋的用戶端應用程式所設計。 您最多可以建立 50 個查詢金鑰。 在應用程式程式碼中，您可以指定搜尋 URL 和查詢 API 金鑰以允許唯讀存取服務。 應用程式程式碼也會指定應用程式所使用的索引。 端點、可供唯讀存取的 API 金鑰以及目標索引共同定義來自用戶端應用程式連接的範圍和存取層級。

若要取得或重新產生 API 金鑰，請開啟服務儀表板。 按一下 [金鑰] 以滑動開啟金鑰管理頁面。 重新產生或建立金鑰的命令位於頁面頂端。 依預設，只會建立管理員金鑰。 查詢 API 金鑰必須以手動方式建立。

 ![][9]

<a id="rbac"></a>

## <a name="secure-api-keys"></a>保護 API 金鑰
藉由限制透過入口網站或 Resource Manager 介面 (PowerShell 或命令列介面) 的存取來確保金鑰安全性。 如前所述，訂用帳戶系統管理員可以檢視及重新產生所有的 API 金鑰。 為以防萬一，請檢閱角色指派以了解誰具有管理員金鑰存取權。

1. 在服務儀表板中，按一下 [存取] 圖示以滑動開啟 [使用者] 刀鋒視窗。
   ![][7]
2. 在 [使用者] 中，檢閱現有的角色指派。 如預期，訂用帳戶管理員已經透過擁有者角色具有服務的完整存取權。
3. 若要進一步鑽研，按一下 [訂用帳戶管理員] ，然後展開角色指派清單以查看誰在您的搜尋服務上具有共同系統管理權限。

檢視存取權限的另一個方法是按一下 [使用者] 刀鋒視窗上的 [角色]。 這麼做會顯示可用的角色以及使用者數目或指派給每個角色的群組數目。

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>監視資源使用量
在儀表板中，僅能針對服務儀表板中顯示的資訊，以及一些透過查詢服務取得的度量進行資源監視。 在服務儀表板上的使用量區段中，您可以快速判斷資料分割資源層級是否適合您的應用程式。

使用搜尋服務 REST API，可以程式設計的方式取得文件和索引的計數： 

* [取得索引統計資料](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [文件計數](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>災害復原和服務中斷

雖然我們可以回收您的資料，但如果中斷發生在叢集或資料中心層級，Azure 搜尋服務將無法提供立即的服務容錯移轉。 如果叢集在資料中心內失敗，作業小組將會偵測並處理以還原服務。 您會在服務還原期間經歷停機。 您可以根據[服務等級協定 (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/) 要求服務信用額度作為無法使用服務的補償。 

如果在遇到 Microsoft 控制之外的災難性失敗時，需要持續的服務，您可以在不同區域[佈建額外的服務](search-create-service-portal.md)並實作異地複寫策略，以確保索引在所有服務之間皆具有完整備援。

使用[索引子](search-indexer-overview.md)來填入及重新整理索引的客戶，可以透過使用相同資料來源的地理特定索引子來處理災害復原。 在不同區域中的兩個服務 (每個都執行一個索引子)，可以從相同相同的資料來源建立索引，以達到異地備援的目的。 如果從同為異地備援的資料來源建立索引，請留意，Azure 搜尋服務索引子只能在主要複本執行遞增索引。 在容錯移轉事件中，請務必將索引子重新指向新的主要複本。 

若不使用索引子，您可以使用應用程式程式碼將物件和資料平行推送到不同的搜尋服務。 如需詳細資訊，請參閱 [Azure 搜尋服務中的效能和最佳化](search-performance-optimization.md)。

## <a name="backup-and-restore"></a>備份與還原

因為 Azure 搜尋服務不是主要的資料儲存體解決方案，所以我們不提供自助備份及還原的正式機制。 如果不小心刪除索引，您用於建立及填入索引的應用程式程式碼是現行的還原選項。 

若要重建索引，請先將索引刪除 (假設它存在)，在服務中重新建立索引，然後從您的主要資料存放區擷取資料以重新載入。 此外，如果發生區域性中斷，您也可以連絡[客戶支援]()以回收索引。


<a id="scale"></a>

## <a name="scale-up-or-down"></a>擴大或縮小規模
每個搜尋服務都會以一個複本和一個資料分割的最小值開始執行。 如果您註冊的[層提供專用資源](search-limits-quotas-capacity.md)，請按一下服務儀表板中的 [級別] 圖格來調整資源使用量。

當您透過任何資源加入處理能力時，服務即會自動使用這些資源。 您無須再執行其他動作，但在新資源產生作用前，會有些許的延遲。 佈建其他資源需要 15 分鐘或更久的時間。

 ![][10]

### <a name="add-replicas"></a>新增複本
系統會藉由新增複本來增加每秒查詢 (QPS) 數量或達到高可用性。 每個複本都有一個索引的副本，因此再新增一個複本就會轉譯出可用來處理服務查詢要求的索引。 高可用性至少需要 3 個複本 (如需詳細資訊，請參閱[容量規劃](search-capacity-planning.md) )。

有許多複本的搜尋服務可透過大量索引來達到查詢要求的負載平衡。 假設有一定等級的查詢量，當有愈多服務要求可用的索引副本時，查詢輸送量的速度就會愈快。 如果您有查詢延遲的經驗，可以期待線上有其他複本時，對效能所造成的正面影響。

雖然當您新增複本時會提高輸送量，但並不會以您新增至服務之複本的正好兩倍或三倍來計算提高程度。 所有搜尋應用程式皆會因為影響查詢效能的外部因素而受影響。 複雜的查詢和網路延遲是造成查詢回應次數變化的兩個因素。

### <a name="add-partitions"></a>新增資料分割
大部分的服務應用程式內建需要多個複本，而非資料分割。 如果您註冊的是標準服務，對於需要新增文件計數的案例，可以新增資料分割。 基本層沒有提供額外的資料分割。

在標準層中，資料分割要以 12 的倍數新增 (準確來說是 1、2、3、4、6 或 12)。 這是分區化的構件。 索引會建立在 12 個分區中，並且可以全部儲存在 1 個資料分割中，或平均分配在 2、3、4、6 或 12 個資料分割中 (每個資料分割分配一個分區)。

### <a name="remove-replicas"></a>移除複本
高查詢量期間過後，在搜尋的查詢負載量回到標準時 (例如，假日銷售結束後)，您可以減少複本。

若要這麼做，請將複本滑動軸移回到較低數目即可。 無須再執行其他步驟。 降低複本數量會讓出資料中心內的虛擬機器。 相較於先前的情況，現在會在較少的 VM 上執行您的查詢和資料擷取作業。 最小的限制為一個複本。

### <a name="remove-partitions"></a>移除資料分割
相較於您無須付出多餘動作就可移除複本，如果您使用的儲存體大於可減少的儲存體，您可能需要完成一些工作。 例如，如果您的解決方案使用三個資料分割，則如果新的儲存空間小於所需，減少為一或兩個資料分割會產生錯誤。 如您可能預期的，您可以選擇刪除索引或相關索引內的文件來釋出空間，或保持目前設定。

沒有任何偵測方法可告訴您哪個索引分區儲存在哪個特定資料分割中。 每個資料分割提供大約 25 GB 的儲存體，因此您需要將儲存體減少至可讓您擁有的資料分割數量所能容納的大小。 如果您想還原成一個資料分割，所有 12 個分區皆要能符合。

若要協助進行未來規劃，您可能需要檢查儲存體 (使用[取得索引統計資料](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) 以了解實際上您可以使用的空間大小。 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>調整和部署的最佳作法
在這段 30 分鐘的影片中，會檢閱進階部署案例的最佳作法，包括地理位置分散工作負載。 您也可以查看 [Azure 搜尋服務中的效能和最佳化](search-performance-optimization.md) ，以取得涵蓋相同要點的說明頁面。

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>後續步驟
當您了解服務管理背後的概念之後，請考慮使用 [PowerShell](search-manage-powershell.md) 來將工作自動化。

也建議您檢視[效能與最佳化文章](search-performance-optimization.md)。

另一個建議是觀看先前小節所提及的影片。 影片涵蓋在本節中所提及技術的詳細資訊。

<!--Image references-->
[7]: ./media/search-manage/rbac-icon.png
[8]: ./media/search-manage/Azure-Search-Manage-1-URL.png
[9]: ./media/search-manage/Azure-Search-Manage-2-Keys.png
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



