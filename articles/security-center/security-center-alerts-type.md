---
title: "Azure 資訊安全中心不同類型的安全性警示 | Microsoft Docs"
description: "本文討論 Azure 資訊安全中心各種可用的安全性警示。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: b3e7b4bc-5ee0-4280-ad78-f49998675af1
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2017
ms.author: yurid
ms.openlocfilehash: 274c50dad9b8a1d79a71a29b04cb8e44ad91893c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="understanding-security-alerts-in-azure-security-center"></a>了解 Azure 資訊安全中心的安全性警示
本文可協助您了解 Azure 資訊安全中心各種可用的安全性警示和相關深入資訊的類型。 如需如何管理警示和事件的詳細資訊，請參閱[管理及回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)。

若要設定進階偵測，請升級至 Azure 資訊安全中心標準版。 提供 60 天的免費試用。 若要升級，請選取[安全性原則](security-center-policies.md) 中的 [定價層]。 若要深入了解，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/security-center/)。

> [!NOTE]
> 資訊安全中心已向有限預覽發行一組新的偵測，可運用通用稽核架構的 auditd 記錄來偵測 Linux 電腦上的惡意行為。 請將含有您的訂用帳戶識別碼的電子郵件傳送給[我們](mailto:ASC_linuxdetections@microsoft.com)，以加入預覽。

## <a name="what-type-of-alerts-are-available"></a>可以使用何種類型的警示？
Azure 資訊安全中心會使用不同的[偵測功能](security-center-detection-capabilities.md)，向客戶警示以其環境為目標的潛在攻擊。 這些警示包含觸發警示的項目、鎖定為目標的資源，以及攻擊來源等重要資訊。 警示中包含的資訊會以用來偵測威脅的分析類型作為基礎而有所不同。 事件可能還會包含其他內容相關的資訊，在調查威脅時很有用。  本文提供下列警示類型的相關資訊：

* 虛擬機器行為分析 (VMBA)
* 網路分析
* 資源分析
* 內容相關的資訊

## <a name="virtual-machine-behavioral-analysis"></a>虛擬機器行為分析
Azure 資訊安全中心可以使用行為分析，根據虛擬機器事件記錄的分析來識別遭到入侵的資源。 例如：程序建立事件和登入事件。 此外，還與其他訊號相互關聯，以檢查廣泛行銷活動的支援證明。

> [!NOTE]
> 如需資訊安全中心偵測功能運作方式的詳細資訊，請參閱 [Azure 資訊安全中心的偵測功能](security-center-detection-capabilities.md)。
>

### <a name="crash-analysis"></a>損毀分析
損毀傾印記憶體分析是一種方法，可用來偵測能避開傳統安全性解決方案的複雜惡意程式碼。 各種形式的惡意程式碼會藉由決不寫入至磁碟或將寫入至磁碟的軟體元件加密，試圖減少被防毒產品偵測到的機會。 這項技術會使用傳統反惡意程式碼方法，使得惡意程式碼難以偵測。 不過，可以使用記憶體分析來偵測這類惡意程式碼，因為惡意程式碼必須在記憶體中留下蹤跡才能運作。

當軟體損毀時，損毀傾印會在損毀時擷取部分的記憶體。 惡意程式碼、一般應用程式或系統問題都可能造成損毀。 藉由分析損毀傾印中的記憶體，資訊安全中心可以偵測到用來惡意探索軟體弱點、存取機密資料，以及暗中存在於遭入侵電腦的技術。 這可在對主機造成最小效能影響的情況下完成，因為此分析是由資訊安全中心後端所執行。

下列欄位通用於本文稍後顯示的損毀傾印警示範例︰

* DUMPFILE：損毀傾印檔案的名稱。
* PROCESSNAME︰損毀處理序的名稱。
* PROCESSVERSION︰損毀處理序的版本。

### <a name="shellcode-discovered"></a>探索到 Shellcode
Shellcode 是惡意程式碼惡意探索軟體弱點後執行的承載。 此警示代表毀損傾印分析偵測到的可執行程式碼顯現經常由惡意承載執行的行為。 雖然非惡意軟體可能會執行這種行為，不過這不是一般軟體開發的實務做法。

Shellcode 警示範例提供下列額外欄位︰

* ADDRESS：Shellcode 在記憶體中的位置。

以下是本類型之警示的範例：

![Shellcode 警示](./media/security-center-alerts-type/security-center-alerts-type-fig2.png)

### <a name="module-hijacking-discovered"></a>探索到模組攔截
Windows 使用動態連結程式庫 (DLL) 允許軟體使用通用 Windows 系統功能。 DLL 攔截發生於當惡意程式碼變更 DLL 載入順序，以便將惡意承載載入記憶體時，得手後有心人士便能執行任意程式碼。 此警示代表毀損傾印分析偵測到名稱相近的模組從兩個不同的路徑載入。 其中一個載入路徑來自通用 Windows 系統二進位檔位置。

合法軟體開發人員偶爾會為了非惡意的原因而變更 DLL 載入順序，如檢測、擴充 Windows 作業系統，或擴充 Windows 應用程式。 為了協助您區別 DLL 載入順序的惡意變更和潛在的良性變更，Azure 資訊安全中心會檢查載入的模組是否符合可疑設定檔。 這項檢查的結果會以警示的 “SIGNATURE” 欄位來表示，而且會反映在警示嚴重性、警示描述和警示補救步驟中。 若要研究此模組屬於合法或惡意，請分析攔截模組的磁碟複本。 例如，您可以驗證檔案的數位簽章，或執行防毒掃描。

除了先前「探索到 Shellcode」一節描述的通用欄位之外，這項警示另提供下列欄位︰

* SIGNATURE︰指出攔截模組是否符合可疑行為設定檔。
* HIJACKEDMODULE︰遭到攔截的 Windows 系統模組名稱。
* HIJACKEDMODULEPATH︰遭到攔截的 Windows 系統模組路徑。
* HIJACKINGMODULEPATH︰攔截模組的路徑。

以下是本類型之警示的範例：

![模組攔截警示](./media/security-center-alerts-type/security-center-alerts-type-fig3.png)

### <a name="masquerading-windows-module-detected"></a>偵測到偽裝的 Windows 模組
惡意程式碼可能會使用 Windows 系統二進位檔 (如 SVCHOST.EXE) 或模組 (如 NTDLL.DLL) 的常用名稱，以期能「蒙混過關」，避免系統管理員看清惡意軟體的本質。 此警示代表毀損傾印分析偵測到毀損傾印檔案含有使用 Windows 系統模組名稱的模組，但這些模組不符合 Windows 模組的其他典型條件。 分析偽裝模組的磁碟複本，可讓您進一步瞭解此模組的本質屬於合法或惡意。 分析可能包括︰

* 確認有問題的檔案隨附在合法軟體套件中。
* 驗證檔案的數位簽章。
* 針對檔案執行防毒軟體掃描。

除了先前「探索到 Shellcode」一節描述的通用欄位之外，這項警示另提供下列額外欄位︰

* DETAILS︰描述模組中繼資料是否有效，以及模組是否從系統路徑載入。
* NAME︰偽裝 Windows 模組的名稱。
* PATH︰偽裝 Windows 模組的路徑。

此警示也會從模組的 "CHECKSUM" 和 "TIMESTAMP" 等 PE 標頭擷取及顯示某些欄位。 唯有當模組有這些欄位時，它們才會出現。 如需這些欄位的詳細資料，請參閱 [Microsoft PE 和 COFF 規格](https://msdn.microsoft.com/windows/hardware/gg463119.aspx) 。

以下是本類型之警示的範例：

![偽裝的 Windows 警示](./media/security-center-alerts-type/security-center-alerts-type-fig4.png)

### <a name="modified-system-binary-discovered"></a>探索到修改過的系統二進位檔
惡意程式碼可能會修改核心系統二進位檔，以便秘密存取資料或暗中存留在遭入侵的系統上。 此警示代表毀損傾印分析偵測到記憶體中或磁碟上的核心 Windows 作業系統二進位檔已遭到修改。

合法軟體開發人員偶爾會為了非惡意的原因而修改記憶體中的系統模組，如 Detours 或處理應用程式相容性。 為了協助您區別惡意模組和可能合法的模組，Azure 資訊安全中心會檢查修改過的模組是否符合可疑設定檔。 這項檢查的結果會以警示嚴重性、警示描述和警示補救步驟來表示。

除了先前「探索到 Shellcode」一節描述的通用欄位之外，這項警示另提供下列額外欄位︰

* MODULENAME：修改過的系統二進位檔名稱。
* MODULEVERSION：修改過的系統二進位檔版本。

以下是本類型之警示的範例：

![系統二進位檔警示](./media/security-center-alerts-type/security-center-alerts-type-fig5.png)

### <a name="suspicious-process-executed"></a>已執行可疑的處理序
資訊安全中心會識別在目標虛擬機器上執行的可疑處理序，然後觸發警示。 偵測不會尋找特定名稱，但會尋找可執行檔的參數。 因此，即使攻擊者將可執行檔重新命名，資訊安全中心仍可偵測可疑的程序。

以下是本類型之警示的範例：

![可疑的處理序警示](./media/security-center-alerts-type/security-center-alerts-type-fig6-new.png)

### <a name="multiple-domains-accounts-queried"></a>已查詢多個網域帳戶
資訊安全中心可以偵測多次嘗試查詢 Active Directory 網域帳戶的動作，這通常是由攻擊者在網路偵察期間所執行。 攻擊者可以利用這項技術來查詢網域，以識別使用者、識別網域管理帳戶、識別當作網域控制站的電腦，以及識別與其他網域的潛在網域信任關係。

以下是本類型之警示的範例：

![多個網域帳戶警示](./media/security-center-alerts-type/security-center-alerts-type-fig7-new.png)

### <a name="local-administrators-group-members-were-enumerated"></a>已列舉本機系統管理員群組成員

在 Windows Server 2016 和 Windows 10 中，資訊安全中心即將在安全性事件 4798 觸發時觸發警示。 這種情況會發生在本機系統管理員群組列舉時，通常是由攻擊者在網路偵察期間所執行。 攻擊者可以利用這項技巧來查詢具有系統管理權限的使用者身分識別。

以下是本類型之警示的範例：

![本機系統管理員](./media/security-center-alerts-type/security-center-alerts-type-fig14-new.png)

### <a name="anomalous-mix-of-upper-and-lower-case-characters"></a>異常混用大寫和小寫字元

資訊安全中心將會在偵測到命令列混合使用大寫和小寫字元時觸發警示。 某些攻擊者可能會使用此技巧來規避區分大小寫或雜湊型電腦規則。

以下是本類型之警示的範例：

![異常混用](./media/security-center-alerts-type/security-center-alerts-type-fig15-new.png)

### <a name="suspected-kerberos-golden-ticket-attack"></a>可疑的 Kerberos 黃金票證攻擊

攻擊者可能會使用洩漏的 [krbtgt](https://technet.microsoft.com/library/dn745899.aspx) 金鑰來建立 Kerberos「黃金票證」，讓攻擊者模擬他們想要的任何使用者。 資訊安全中心會在偵測到這類型的活動時觸發警示。

> [!NOTE] 
> 如需 Kerberos 黃金票證的詳細資訊，請參閱 [Windows 10 認證竊取風險降低指南](http://download.microsoft.com/download/C/1/4/C14579CA-E564-4743-8B51-61C0882662AC/Windows%2010%20credential%20theft%20mitigation%20guide.docx)。

以下是本類型之警示的範例：

![黃金票證](./media/security-center-alerts-type/security-center-alerts-type-fig16-new.png)

### <a name="suspicious-account-created"></a>已建立可疑帳戶

若建立與現有內建系統管理權限帳戶非常相似的帳戶，資訊安全中心就會觸發警示。 攻擊者可以使用這項技巧來建立 Rouge 帳戶，以避免被人為驗證所察覺。
 
以下是本類型之警示的範例：

![可疑的帳戶](./media/security-center-alerts-type/security-center-alerts-type-fig17-new.png)

### <a name="suspicious-firewall-rule-created"></a>已建立可疑的防火牆規則

攻擊者可能會嘗試規避主機安全性，其方法是建立自訂防火牆規則來允許惡意應用程式與命令和控制項通訊，或經由遭入侵的主應用透過網路發動攻擊。 資訊安全中心偵測到從可疑位置的可執行檔建立的新防火牆規則時，就會觸發警示。
 
以下是本類型之警示的範例：

![防火牆規則](./media/security-center-alerts-type/security-center-alerts-type-fig18-new.png)

### <a name="suspicious-combination-of-hta-and-powershell"></a>可疑的 HTA 和 PowerShell 組合

資訊安全中心偵測到 Microsoft HTML 應用程式主機 (HTA) 正在啟動 PowerShell 命令時會觸發警示。 這是攻擊者用來啟動惡意 PowerShell 指令碼的技巧。
 
以下是本類型之警示的範例：

![HTA 和 PS](./media/security-center-alerts-type/security-center-alerts-type-fig19-new.png)


## <a name="network-analysis"></a>網路分析
資訊安全中心網路威脅偵測的運作方式如下：從 Azure IPFIX (Internet Protocol Flow Information Export) 流量自動收集安全性資訊。 它會分析這項資訊 (通常是來自多個來源的相互關聯資訊) 以識別威脅。

### <a name="suspicious-outgoing-traffic-detected"></a>偵測到可疑的連出流量
網路裝置可以受到探索和分析，其方式和其他類型的系統非常類似。 攻擊者通常會使用連接埠掃描或連接埠掃掠來開始攻擊。 在下一個範例中，您有來自 VM 的可疑安全殼層 (SSH) 流量。 在此案例中，可能有對外部資源的 SSH 暴力密碼破解或連接埠掃掠攻擊。

![可疑連出流量警示](./media/security-center-alerts-type/security-center-alerts-type-fig8.png)

此警示會提供資訊，以便您識別用來起始此攻擊的資源。 此警示也會提供資訊，以識別遭入侵的電腦、偵測時間，以及所使用的通訊協定和連接埠。 此刀鋒視窗也可提供用來解決這個問題的補救步驟清單。

### <a name="network-communication-with-a-malicious-machine"></a>與惡意電腦的網路通訊
藉由利用 Microsoft 威脅情報摘要，Azure 資訊安全中心可以偵測與惡意 IP 位址通訊的遭入侵電腦。 在許多情況下，惡意位址會是命令和控制中心。 在此情況下，資訊安全中心偵測到通訊是使用 Pony Loader 惡意程式碼 (也稱為 [Fareit](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=PWS:Win32/Fareit.AF)) 來進行。

![網路通訊警示](./media/security-center-alerts-type/security-center-alerts-type-fig9.png)

此警示會提供資訊，讓您識別用來起始此攻擊的資源、受攻擊的資源、受害者 IP、攻擊者 IP 和偵測時間。

> [!NOTE]
> 為了保護隱私權，此螢幕擷取畫面的即時 IP 位址已遭到移除。
>
>

### <a name="possible-outgoing-denial-of-service-attack-detected"></a>偵測到可能的連出拒絕服務攻擊
源自一部虛擬機器的異常網路流量可能會導致資訊安全中心觸發潛在的拒絕服務攻擊類型。

以下是本類型之警示的範例：

![連出 DOS](./media/security-center-alerts-type/security-center-alerts-type-fig10-new.png)

## <a name="resource-analysis"></a>資源分析
資訊安全中心資源分析著重於平台即服務 (PaaS) 服務，例如與 [Azure SQL Database 威脅偵測](../sql-database/sql-database-threat-detection.md)功能整合。 根據來自這些區域的分析結果，資訊安全中心會觸發資源相關警示。

### <a name="potential-sql-injection"></a>潛在的 SQL 插入式攻擊
SQL 插入式攻擊會將惡意程式碼插入字串，而此字串稍後會傳遞至 SQL Server 執行個體以進行剖析和執行。 因為 SQL Server 會執行它收到的所有語法上有效的查詢，所以建構 SQL 陳述式的任何程序皆應檢閱其中是否有插入式攻擊弱點。 SQL 威脅偵測會使用機器學習、行為分析和異常偵測，判斷 Azure SQL Database 中可能會發生的可疑事件。 例如：

* 離職員工嘗試的資料庫存取
* SQL 插入式攻擊
* 從使用者家裡異常存取生產資料庫

![潛在的 SQL 插入式攻擊警示](./media/security-center-alerts-type/security-center-alerts-type-fig11.png)

這項警示的資訊可用來識別受攻擊的資源、偵測時間和攻擊的狀態。 它也提供進一步調查步驟的連結。

### <a name="vulnerability-to-sql-injection"></a>SQL 插入式攻擊的弱點
在資料庫上偵測到應用程式錯誤時，會觸發此警示。 此警示表示 SQL 插入式攻擊的可能弱點。

![潛在的 SQL 插入式攻擊警示](./media/security-center-alerts-type/security-center-alerts-type-fig12-new.png)

### <a name="unusual-access-from-unfamiliar-location"></a>來自不熟悉位置的異常存取
在伺服器上偵測到來自不熟悉 IP 位址的存取事件 (未在最後一個期間看到) 時，會觸發此警示。

![異常存取警示](./media/security-center-alerts-type/security-center-alerts-type-fig13-new.png)

## <a name="contextual-information"></a>內容相關的資訊
在調查期間，分析師需要額外的內容，才能觸達關於威脅的本質，以及如何降低威脅的結論。  例如，偵測到網路異常，但如果不了解網路上或關於鎖定為目標的資源發生了什麼其他情況，就很難以了解後續要採取的動作。 若要提供協助，安全性事件可能包含有助於調查的成品、相關事件和資訊。 其他資訊的可用性會以偵測到的威脅及您環境的設定類型作為基礎而有所不同，且將無法用於所有的安全性事件。

若有其他資訊可供使用，會顯示在警示清單下方的安全性事件中。 這可能包括的資訊如：

- 清除事件記錄
- 從未知裝置插入的 PNP 裝置
- 不可採取動作的警示 

![異常存取警示](./media/security-center-alerts-type/security-center-alerts-type-fig20.png) 


## <a name="see-also"></a>另請參閱
在本文中，您了解到資訊安全中心的各種安全性警示類型。 如要深入了解資訊安全中心，請參閱下列主題：

* [在 Azure 資訊安全中心處理安全性事件](security-center-incident.md)
* [Azure 資訊安全中心的偵測功能](security-center-detection-capabilities.md)
* [Azure 資訊安全中心規劃和操作指南](security-center-planning-and-operations-guide.md)
* [Azure 資訊安全中心常見問題集](security-center-faq.md)：尋找有關使用服務的常見問題。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)：尋找有關 Azure 安全性與合規性的部落格文章。
