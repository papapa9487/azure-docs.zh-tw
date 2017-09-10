---
title: "Log Analytics 資料安全性 | Microsoft Docs"
description: "深入了解 Log Analytics 如何保護您的隱私權和保護您的資料安全。"
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: a33bb05d-b310-4f2c-8f76-f627e600c8e7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 9ae1629462d375c1061d9c7b25975c789ee661c9
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="log-analytics-data-security"></a>Log Analytics 資料安全性
Microsoft 致力於保護您的隱私權和保護資料安全，同時提供軟體和服務，幫助您管理組織的 IT 基礎結構。 我們了解當您將資料委託給他人管理時，將需要嚴格的安全性。 Microsoft 從撰寫程式碼到運作服務均遵守嚴格的規範與安全性指導方針。

保全和保護資料在 Microsoft 是第一要務。 如有任何問題、建議或關於下列任一項資訊的問題 (包括我們的安全性原則)，請與我們連絡：[Azure 支援選項](http://azure.microsoft.com/support/options/)。

本文說明 Operations Management Suite (OMS) 中 Log Analytics 資料收集、處理和保全方式。 您可以使用代理程式連接到 Web 服務、使用 System Center Operations Manager 來收集操作資料，或從 Azure 診斷擷取資料供 Log Analytics 使用。 收集的資料會使用憑證型驗證和 SSL 3 透過網際網路傳送至裝載於 Microsoft Azure 的 Log Analytics 服務。 資料會先由代理程式壓縮再傳送出去。

Log Analytics 服務會使用下列方法安全地管理您以雲端為基礎的資料：

* 資料隔離
* 資料保留
* 實體安全性
* 事件管理
* 法規遵循
* 安全性標準認證

## <a name="data-segregation"></a>資料隔離
客戶資料以邏輯方式分開儲存在 OMS 服務的每個元件上。 每個組織加上標記的所有資料。 這項標記作業在整個資料生命週期持續發生，它會強制執行服務的每個層級。 每個客戶都有專用的 Azure Blob 可裝載長期資料

## <a name="data-retention"></a>資料保留
系統會根據價格方案來儲存及保留已編製索引的記錄檔搜尋資料。 如需詳細資訊，請參閱 [Log Analytics 定價](https://azure.microsoft.com/pricing/details/log-analytics/)。

Microsoft 會在 OMS 工作區關閉 30 天後刪除客戶資料。 Microsoft 也會刪除資料所在的 Azure 儲存體帳戶。 移除客戶資料時，不會終結任何實體磁碟機。

下表列出 OMS 中可用的一些解決方案與它們所收集的資料類型範例。

| **Solution** | **資料類型** |
| --- | --- |
| 組態評估 |組態資料、中繼資料及狀態資料 |
| 容量規劃 |效能資料和中繼資料 |
| 反惡意程式碼 |組態資料和中繼資料 |
| 系統更新評估 |中繼資料和狀態資料 |
| 記錄檔管理 |使用者定義的事件記錄檔、Windows 事件記錄檔和/或 IIS 記錄檔 |
| 變更追蹤 |軟體清查和 Windows 服務中繼資料 |
| SQL 和 Active Directory 評估 |WMI 資料、登錄資料、效能資料和 SQL Server 動態管理檢視結果 |

下表顯示資料類型範例：

| **資料類型** | **欄位** |
| --- | --- |
| 警示 |警示名稱、警示描述、BaseManagedEntityId、問題識別碼、IsMonitorAlert、RuleId、ResolutionState、優先順序、嚴重性、分類、擁有者、ResolvedBy、TimeRaised、TimeAdded、LastModified、LastModifiedBy、LastModifiedExceptRepeatCount、TimeResolved、TimeResolutionStateLastModified、TimeResolutionStateLastModifiedInDB、RepeatCount |
| 組態 |CustomerID、AgentID、EntityID、ManagedTypeID、ManagedTypePropertyID、CurrentValue、ChangeDate |
| Event |EventId、EventOriginalID、BaseManagedEntityInternalId、RuleId、PublisherId、PublisherName、FullNumber、Number、Category、ChannelLevel、LoggingComputer、EventData、EventParameters、TimeGenerated、TimeAdded <br>**附註：**當您使用自訂欄位將事件寫入 Windows 事件記錄檔時，OMS 會收集它們。 |
| 中繼資料 |BaseManagedEntityId、ObjectStatus、OrganizationalUnit、ActiveDirectoryObjectSid、PhysicalProcessors、NetworkName、IPAddress、ForestDNSName、NetbiosComputerName、VirtualMachineName、LastInventoryDate、HostServerNameIsVirtualMachine、IP 位址、NetbiosDomainName、LogicalProcessors、DNSName、DisplayName、DomainDnsName、ActiveDirectorySite、PrincipalName、OffsetInMinuteFromGreenwichTime |
| 效能 |ObjectName、CounterName、PerfmonInstanceName、PerformanceDataId、PerformanceSourceInternalID、SampleValue、TimeSampled、TimeAdded |
| 狀況 |StateChangeEventId、StateId、NewHealthState、OldHealthState、Context、TimeGenerated、TimeAdded、StateId2、BaseManagedEntityId、MonitorId、HealthState、LastModified、LastGreenAlertGenerated、DatabaseTimeModified |

## <a name="physical-security"></a>實體安全性
OMS 服務中的 Log Analytics 是由 Microsoft 人員操縱，所有活動都有記錄並且可供稽核。 服務完全在 Azure 中執行，並符合 Azure 通用工程準則。 您可以在 [Microsoft Azure 安全性概觀](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf)的第 18 頁上檢視 Azure 資產之實體安全性的詳細資料。 不再負責管理 OMS 服務的人員，其用來確保區域安全的實體存取權限 (包括傳輸和終止) 將會在一個工作天內變更。 若要了解我們使用的全域實體基礎結構，請參閱 [Microsoft 資料中心](https://www.microsoft.com/en-us/server-cloud/cloud-os/global-datacenters.aspx)。

## <a name="incident-management"></a>事件管理
OMS 具備所有 Microsoft 服務都會遵守的事件管理程序。 總結來說，我們：

* 使用共同責任模型，在此模型中，一部分的安全性責任歸屬 Microsoft，一部分則歸屬客戶
* 管理 Azure 安全性事件
  * 在偵測到事件時啟動調查
  * 由待命的事件回應小組成員評估事件的影響和嚴重性。 根據證據，評估結果不一定會導致進一步上報給安全性回應小組。
  * 由安全性回應專家診斷事件，以進行技術或鑑識調查、找出圍堵、緩和及因應策略。 如果安全性小組認為客戶資料可能已公開給非法或未經授權的個人，則會開始並行執行客戶事件通知程序。  
  * 恢復穩定並從事件中復原。 事件回應小組會建立復原計畫來減輕問題。 隔離受影響系統之類的危機圍堵步驟可能會立即進行，與診斷並行。 度過立即的危險之後，可能會進行較長期的緩和措施。  
  * 將事件結案並進行事後檢討。 事件回應小組會建立概述事件細節的事後檢討，以便修改原則、程序和處理程序來防止事件再次發生。
* 通知客戶發生了安全性事件
  * 確定受影響客戶的範圍，並盡可能為受影響當事人提供詳盡的通知
  * 建立通知，為客戶提供足夠詳盡的資訊，在不會過度延遲通知程序的情況下，讓他們能夠在他們那一端進行調查，並符合他們對其使用者所做的任何承諾。
  * 確認並視需要宣佈事件。
  * 在不會導致不合理延遲並符合法律或契約承諾的情況下，以事件通知告知客戶。 安全性事件的通知會以 Microsoft 選擇的任何方式 (包括透過電子郵件) 傳送給客戶的一或多位系統管理員。
* 進行小組準備及訓練
  * Microsoft 人員必須完成安全性和認知訓練，這可幫助他們識別及報告可疑的安全性問題。  
  * 在 Microsoft Azure 服務工作的操作員身負圍繞在裝載客戶資料之機密系統存取權的附加訓練義務。
  * Microsoft 安全性回應人員獲得專門針對其角色的訓練

如果發生客戶資料遺失事件，我們會在一天內通知每位客戶。 不過，OMS 從未發生過客戶資料遺失。 此外，我們會複製所建立的資料並分散於各地。

如需 Microsoft 如何回應安全性事件的詳細資訊，請參閱[雲端中的 Microsoft Azure 安全性回應](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf)。

## <a name="compliance"></a>法規遵循
OMS 軟體開發和服務小組的資訊安全性即控管程式可支援其商務需求，並且會遵守 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/)和 [Microsoft 信任中心法規遵循](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx)所述的法規。 上述位置也會描述 OMS 是如何建立安全性需求、識別安全性控制，以及管理和監視風險。 每年我們都會檢閱原則、標準、程序和指導方針。

每個 OMS 開發小組成員都會獲得正式的應用程式安全性訓練。 在內部，我們使用版本控制系統來開發軟體。 每個軟體專案都受到版本控制系統的保護。

Microsoft 備有會監看及評估 Microsoft 中所有服務的安全性和法規遵循小組。 資訊安全人員會組成小組，而且他們與開發 OMS 的工程部門並無關聯。 安全人員有他們自己的管理鏈，並且會獨立評估產品和服務，以確保安全性與法規遵循。

Microsoft 的董事會會收到有關 Microsoft 所有資訊安全程式的年度報表通知。

OMS 軟體開發和服務小組會積極地與 Microsoft 法律和規範小組及其他產業合作夥伴合作取得各種認證。

## <a name="certifications-and-attestations"></a>認證和證明
OMS Log Analytics 符合下列需求︰

* [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/en-us/blog/iso22301/)
* PCI 安全標準委員會的[支付卡產業 (PCI 相容) 資料安全標準 (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI)。
* [服務組織控制 (SOC) 1 類型 1 和 SOC 2 類型 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) 相容
* 擁有 HIPAA 商業夥伴合約之公司的 [HIPAA 和 HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)
* Windows 通用工程準則
* Microsoft 高可信度電腦運算 (英文)
* 做為 Azure 服務，OMS 使用的元件會遵守 Azure 的相容性需求。 若要深入了解，請前往 [Microsoft 信任中心法規遵循](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx)。

> [!NOTE]
> 在某些認證/證明中，Log Analytics 會以舊名稱 *Operational Insights* 列出。
>
>


## <a name="cloud-computing-security-data-flow"></a>雲端運算安全性資料流程
下圖顯示的雲端安全性架構為貴公司的資訊流程，以及當移至 Log Analytics 服務時如何受到保護，最後由您在 OMS 入口網站中看到。 圖表後面詳述每個步驟的詳細資訊。

![OMS 資料收集與安全性的影像](./media/log-analytics-security/log-analytics-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1.註冊使用 Log Analytics 並收集資料
為了讓您的組織將資料傳送至 Log Analytics，您會設定 Windows 代理程式，在 Azure 虛擬機器上執行的代理程式，或 OMS Agents for Linux。 如果您使用 Operations Manager 代理程式，則可在 Operations 主控台中使用組態精靈設定它們。 使用者 (可能是您、其他個別使用者或一群人) 會建立一或多個 OMS 帳戶 (OMS 工作區)，並且使用下列其中一個帳戶來註冊代理程式：

* [組織識別碼](../active-directory/sign-up-organization.md)
* [Microsoft 帳戶 - Outlook、Office Live、MSN](http://www.microsoft.com/account/default.aspx)

OMS 工作區是資料收集、彙總、分析以及呈現的位置。 工作區主要是做為資料分割資料，每個工作區都是唯一的。 例如，您可能想要使用一個 OMS 工作區管理實際執行資料，使用另一個工作區管理測試資料。 工作區也會協助系統管理員控制資料的使用者存取。 每個工作區可以有多個與其關聯的使用者帳戶，每個使用者帳戶可以存取多個 OMS 工作區。 您必須根據資料中心區域建立工作區。 每個工作區都會複寫到區域中的其他資料中心，這主要是為了確保 OMS 服務可用性。

針對 Operations Manager，當組態精靈完成時，每個 Operations Manager 管理群組就會建立與 Log Analytics 服務的連接。 然後，您會使用 [新增電腦精靈] 來選擇允許管理群組中的哪些電腦將資料傳送至服務。 對於其他代理程式類型，每個都安全地連接至 OMS 服務。

連線系統與 Log Analytics 服務之間的所有通訊都會加密。  會使用 TLS (HTTPS) 通訊協定來加密。  隨後會進行 Microsoft SDL 程序，使用最先進的密碼編譯通訊協定確保 Log Analytics 保持最新狀態。

會收集 Log Analytics 資料的每個類型代理程式。 所收集的資料類型取決於使用的解決方案類型。 若要查看資料集合摘要，請參閱[從方案庫新增 Log Analytics 方案](log-analytics-add-solutions.md)。 此外，大部分方案都會有更詳細的集合資訊。 解決方案是預先定義的檢視、記錄搜尋查詢、資料收集規則，以及處理邏輯的組合。 只有系統管理員可以使用 Log Analytics 來匯入方案。 在匯入解決方案之後，便會移到 Operations Manager 管理伺服器 (如果使用的話)，然後移至您所選擇的代理程式。 之後，代理程式會收集資料。

## <a name="2-send-data-from-agents"></a>2.從代理程式傳送資料
您使用註冊金鑰來註冊所有類型的代理程式，而代理程式與 Log Analytics 服務之間會使用憑證型驗證和 SSL 在連接埠 443 建立安全的連線。 OMS 使用密碼存放區來產生及維護金鑰。 私密金鑰每 90 天會輪替一次，其儲存在 Azure 中，並由遵守嚴格法規與相容性作法的 Azure 作業人員管理。

使用 Operations Manager，您會向 Log Analytics 服務註冊工作區，然後會在 Operations Manager 管理伺服器之間建立安全的 HTTPS 連線。

對於 Azure 虛擬機器上執行的 Windows 代理程式，唯讀的儲存體金鑰可用來讀取 Azure 資料表中的診斷事件。

如果任何代理程式因為任何原因而無法與服務通訊，收集的資料會儲存在本機的暫時快取，且管理伺服器在 2 個小時內每 8 分鐘會嘗試重新傳送資料。 代理程式的快取資料會受到作業系統的認證存放區保護。 如果服務在 2 個小時後無法處理資料，代理程式會將資料排入佇列。 如果佇列已滿，OMS 會開始卸除資料類型，最先卸除的是效能資料。 代理程式佇列限制為登錄機碼，因此您可以視需要加以修改。 收集的資料會壓縮，並且傳送給服務，略過內部部署資料庫，因此不會對它們產生任何負載。 傳送收集的資料之後，會從快取中移除。

如上所述，代理程式中的資料會透過 SSL 傳送到 Microsoft Azure 資料中心。 (選擇性) 您可以使用 ExpressRoute 為資料提供額外的安全性。 ExpressRoute 可供直接從現有 WAN 網路 (例如網路服務提供者所提供的多重通訊協定標籤交換 (MPLS) VPN) 連線至 Azure。 如需詳細資訊，請參閱 [ExpressRoute](https://azure.microsoft.com/services/expressroute/)。

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3.Log Analytics 服務接收和處理資料
Log Analytics 服務會確保內送資料是來自信任的來源，方法是驗證憑證和與 Azure 驗證的資料完整性。 未處理的原始資料會儲存為 [Microsoft Azure 儲存體](../storage/common/storage-introduction.md)中的 Blob，且未加密。 不過，每個 Azure 儲存體 Blob 都有唯一一組僅供該使用者存取的金鑰。 所儲存的資料類型取決於匯入和用來收集資料的解決方案類型。 然後，Log Analytics 服務會為 Azure 儲存體 Blob 處理未經處理的資料。

## <a name="4-use-log-analytics-to-access-the-data"></a>4.使用 Log Analytics 來存取資料
您可以在 OMS 入口網站使用組織帳戶或您先前設定的 Microsoft 帳戶來登入 Log Analytics。 OMS 入口網站與 OMS 中的 Log Analytics 之間的所有流量會透過安全的 HTTPS 通道傳送。 在使用 OMS 入口網站時，使用者用戶端 (網頁瀏覽器) 上會產生工作階段識別碼，而且資料會儲存在本機快取，直到工作階段終止。 終止時便會刪除快取。 未包含個人識別資訊的用戶端 Cookie 不會自動移除。 工作階段 Cookie 會標示為 HTTPOnly，並受到保護。 經過預先決定的閒置時間後，OMS 入口網站工作階段就會終止。

使用 OMS 入口網站，您可以將資料匯出至 CSV 檔案，而且您可以使用搜尋 API 存取資料。 CSV 匯出是限制為每此只能匯出 50,000 個資料列，而 API 資料則限制為每次只能搜尋 5,000 個資料列。

## <a name="next-steps"></a>後續步驟
* [開始使用 Log Analytics](log-analytics-get-started.md) 以深入了解 Log Analytics，並幾分鐘內就啟動並執行。

