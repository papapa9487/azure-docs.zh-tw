---
title: "Operations Management Suite (OMS) 中的 Office 365 解決方案 | Microsoft Docs"
description: "本文提供在 OMS 中設定和使用 Office 365 解決方案的相關詳細資料。  它包含在 Log Analytics 中所建立之 Office 365 記錄的詳細描述。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bwren
ms.openlocfilehash: 711071eaff7ab5e5199793663aa3cbb36a1e8d8a
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2017
---
# <a name="office-365-solution-in-operations-management-suite-oms"></a>Operations Management Suite (OMS) 中的 Office 365 解決方案

![Office 365 標誌](media/oms-solution-office-365/icon.png)

適用於 Operations Management Suite (OMS) 的 Office 365 解決方案可讓您監視 Log Analytics 中的 Office 365 環境。  

- 監視 Office 365 帳戶上的使用者活動，以分析使用模式並識別行為趨勢。 例如，您可以擷取特定使用方式情節，例如在貴組織外部共用的檔案或最熱門的 SharePoint 網站。
- 監視系統管理員活動以追蹤設定變更或高權限作業。
- 偵測並調查的不必要的使用者行為，並可以針對貴組織的需求進行自訂。
- 示範稽核與合規性。 例如，您可以監視機密檔案的檔案存取作業，以協助進行稽核與合規性流程。
- 針對組織的 Office 365 活動資料使用 OMS 搜尋，執行作業疑難排解。

## <a name="prerequisites"></a>必要條件
安裝和設定此解決方案之前必須先具備下列項目。

- 組織的 Office 365 訂閱。
- 全域管理員的使用者帳戶認證。
- 若要接收稽核資料，您必須在 Office 365 訂閱中[設定稽核](https://support.office.com/en-us/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin)。  請注意，[信箱稽核](https://technet.microsoft.com/library/dn879651.aspx) \(機器翻譯\) 需要另行設定。  如果未設定稽核，您仍可安裝解決方案並收集其他資料。
 


## <a name="management-packs"></a>管理組件
此解決方案不會在已連線的管理群組中安裝任何管理組件。
  

## <a name="configuration"></a>組態
[將 Office 365 解決方案新增至訂用帳戶](../log-analytics/log-analytics-add-solutions.md)之後，您必須將它連接到 Office 365 訂閱。

1. 使用 [加入方案](../log-analytics/log-analytics-add-solutions.md)所述的程序，將警示管理方案加入您的 OMS 工作區。
2. 在 OMS 入口網站中，移至 [設定]。
3. 在 [連接的來源] 下，選取 [Office 365]。
4. 按一下 [連線至 Office 365]。<br>![連線至 Office 365](media/oms-solution-office-365/configure.png)
5. 使用具備您訂用帳戶之全域管理員身分的帳戶來登入 Office 365。 
6. 該訂用帳戶會與解決方案將要監視的工作負載一起列出。<br>![連線至 Office 365](media/oms-solution-office-365/connected.png) 


## <a name="data-collection"></a>資料收集
### <a name="supported-agents"></a>支援的代理程式
Office 365 解決方案不會從任何 [OMS 代理程式](../log-analytics/log-analytics-data-sources.md)擷取資料。  它會直接從 Office 365 擷取資料。

### <a name="collection-frequency"></a>收集頻率
每次建立一筆記錄時，Office 365 都會將 [Webhook 通知](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) \(英文\) 連同詳細資料傳送至 Log Analytics。

## <a name="using-the-solution"></a>使用解決方案
當您將 Office 365 解決方案新增至 OMS 工作區時，[Office 365] 圖格會新增至 OMS 儀表板。 此圖格會顯示計數並以圖形表示環境中的電腦數目及其更新合規性。<br><br>
![Office 365 摘要圖格](media/oms-solution-office-365/tile.png)  

按一下 [Office 365] 圖格以開啟 [Office 365] 儀表板。

![Office 365 儀表板](media/oms-solution-office-365/dashboard.png)  

此儀表板包含下表中的資料行。 每個資料行依計數列出前十個警示，這幾個警示符合該資料行中指定範圍和時間範圍的準則。 您可以按一下資料行底部的 [查看全部]，或按一下資料行標頭，以執行記錄搜尋來提供完整清單。

| 欄 | 說明 |
|:--|:--|
| 作業 | 提供所有受監視 Office 365 訂閱中之作用中使用者的相關資訊。 您也可以查看一段時間內發生的活動數。
| Exchange | 顯示 Exchange Server 活動細目，例如 Add-Mailbox 權限或 Set-Mailbox。 |
| SharePoint | 顯示使用者最常對 SharePoint 文件執行的活動。 當您從此圖格向下鑽研時，搜尋頁面會顯示這些活動的詳細資料，例如此活動的目標文件與位置。 例如，針對「已存取檔案」事件，您可以查看所存取的文件、其相關帳戶名稱，以及 IP 位址。 |
| Azure Active Directory | 包含熱門使用者活動，例如「重設使用者密碼」和「登入嘗試」。 向下鑽研時，您可以像「結果狀態」一般地查看這些活動的詳細資料。 如果您想要監視 Azure Active Directory 上的可疑活動，這會很有幫助。 |




## <a name="log-analytics-records"></a>Log Analytics 記錄

由 Office 365 解決方案在 Log Analytics 工作區中建立的所有記錄，都具有 **OfficeActivity** 的「類型」。  **OfficeWorkload** 屬性可決定該記錄所指的 Office 365 服務：Exchange、AzureActiveDirectory、SharePoint 或 OneDrive。  **RecordType** 屬性指定作業的類型。  每種作業類型會有不同的屬性，如下表所示。

### <a name="common-properties"></a>通用屬性
以下是所有 Office 365 記錄通用的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| 類型 | *OfficeActivity* |
| ClientIP | 記錄活動時所使用之裝置的 IP 位址。 IP 位址會以 IPv4 或 IPv6 位址格式顯示。 |
| OfficeWorkload | 記錄所指的 Office 365 服務。<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| 作業 | 使用者或管理員活動的名稱。  |
| OrganizationId | 貴組織的 Office 365 租用戶 GUID。 無論此值是在哪一個 Office 365 服務中發生，對於貴組織而言它將會一律相同。 |
| RecordType | 執行之作業的類型。 |
| ResultStatus | 指出 (Operation 屬性中指定的) 動作是否成功。 可能的值為 Succeeded、PartiallySucceded 或 Failed。 對於 Exchange 管理員活動，這個值將會是 True 或 False。 |
| UserId | 執行動作導致記下該記錄之使用者的 UPN (使用者主體名稱)，例如 my_name@my_domain_name。 請注意，由系統帳戶 (例如 SHAREPOINT\system 或 NTAUTHORITY\SYSTEM) 所執行之活動的記錄也會包含在內。 | 
| UserKey | UserId 屬性所識別之使用者的替代識別碼。  例如，針對由使用者在 SharePoint、商務用 OneDrive 及 Exchange 中所執行的事件，此屬性都會填入 Passport 唯一識別碼 (PUID)。 針對在其他服務中所發生的事件，以及由系統帳戶所執行的事件，此屬性也可能會將相同的值指定為 UserID 屬性|
| UserType | 執行作業的使用者類型。<br><br>Admin<br>Application<br>DcAdmin<br>Regular<br>Reserved<br>ServicePrincipal<br>System |


### <a name="azure-active-directory-base"></a>Azure Active Directory 基底
以下是所有 Azure Active Directory 記錄通用的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Azure AD 事件類型。 |
| ExtendedProperties | Azure AD 事件的擴充屬性。 |


### <a name="azure-active-directory-account-logon"></a>Azure Active Directory 帳戶登入
這些記錄會在 Active Directory 使用者嘗試登入時建立。

| 屬性 | 說明 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| 應用程式 | 觸發帳戶登入事件的應用程式，例如 Office 15。 |
| 用戶端 | 帳戶登入事件所使用的用戶端裝置、裝置作業系統及裝置瀏覽器的相關詳細資料。 |
| LoginStatus | 此屬性直接來自 OrgIdLogon.LoginStatus。 各種感興趣之登入失敗的對應可由警示演算法完成。 |
| UserDomain | 租用戶身分識別資訊 (TII)。 | 


### <a name="azure-active-directory"></a>Azure Active Directory
這些記錄會在針對 Azure Active Directory 物件進行變更或新增時建立。

| 屬性 | 說明 |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | 對之執行 (透過 Operation 屬性所識別的) 動作的使用者。 |
| Actor | 執行動作的使用者或服務主體。 |
| ActorContextId | 執行者所屬組織的 GUID。 |
| ActorIpAddress | 以 IPV4 或 IPV6 位址格式顯示的執行者 IP 位址。 |
| InterSystemsId | 在 Office 365 服務內跨元件追蹤動作的 GUID。 |
| IntraSystemId |   由 Azure Active Directory 產生來追蹤動作的 GUID。 |
| SupportTicketId | 在「代表採取動作」狀況下進行之動作的客戶支援票證識別碼。 |
| TargetContextId | 目標使用者所屬組織的 GUID。 |


### <a name="data-center-security"></a>資料中心安全性
這些記錄是從資料中心安全性稽核資料所建立。  

| 屬性 | 說明 |
|:--- |:--- |
| EffectiveOrganization | 提高權限/Cmdlet 的目標租用戶名稱。 |
| ElevationApprovedTime | 核准提高權限時的時間戳記。 |
| ElevationApprover | Microsoft 管理員的名稱。 |
| ElevationDuration | 提高權限作用中的持續時間。 |
| ElevationRequestId |  提高權限要求的唯一識別碼。 |
| ElevationRole | 要求提高權限的角色。 |
| ElevationTime | 提高權限的開始時間。 |
| Start_Time | Cmdlet 開始執行的時間。 |


### <a name="exchange-admin"></a>Exchange 管理員
這些記錄會在對 Exchange 設定做出變更時建立。

| 屬性 | 說明 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  指定 Cmdlet 是由貴組織的使用者、由 Microsoft 資料中心人員或資料中心服務帳戶，還是由委派的系統管理員執行。 False 值表示 Cmdlet 是由貴組織的人員執行。 True 值表示 Cmdlet 是由資料中心人員、資料中心服務帳戶或是委派的系統管理員執行。 |
| ModifiedObjectResolvedName |  這是 Cmdlet 所修改之物件的使用者易記名稱。 這只有在 Cmdlet 修改物件時才會記錄。 |
| OrganizationName | 租用戶的名稱。 |
| OriginatingServer | 執行 Cmdlet 之伺服器的名稱。 |
| 參數 | 搭配在 Operations 屬性中所識別的 Cmdlet 所使用之所有參數的名稱與值。 |


### <a name="exchange-mailbox"></a>Exchange 信箱
這些記錄會在針對 Exchange 信箱做出變更或新增時建立。

| 屬性 | 說明 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | 用以執行作業的電子郵件用戶端相關資訊，例如瀏覽器版本、Outlook 版本及行動裝置資訊。 |
| Client_IPAddress | 記錄作業時所使用之裝置的 IP 位址。 IP 位址會以 IPv4 或 IPv6 位址格式顯示。 |
| ClientMachineName | 裝載 Outlook 用戶端的機器名稱。 |
| ClientProcessName | 用來存取信箱的電子郵件用戶端。 |
| ClientVersion | 電子郵件用戶端的版本。 |
| InternalLogonType | 保留供內部使用。 |
| Logon_Type | 指出存取信箱並執行所記錄之作業的使用者類型。 |
| LogonUserDisplayName |    執行作業之使用者的使用者易記名稱。 |
| LogonUserSid | 執行作業之使用者的 SID。 |
| MailboxGuid | 被存取之信箱的 Exchange GUID。 |
| MailboxOwnerMasterAccountSid | 信箱擁有者帳戶的主要帳戶 SID。 |
| MailboxOwnerSid | 信箱擁有者的 SID。 |
| MailboxOwnerUPN | 擁有被存取信箱之人員的電子郵件地址。 |


### <a name="exchange-mailbox-audit"></a>Exchange 信箱稽核
這些記錄會在建立信箱稽核項目時建立。

| 屬性 | 說明 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Item | 表示對之執行作業的項目 | 
| SendAsUserMailboxGuid | 存取以傳送電子郵件之信箱的 Exchange GUID。 |
| SendAsUserSmtp | 被模擬之使用者的 SMTP 位址。 |
| SendonBehalfOfUserMailboxGuid | 存取以代為傳送電子郵件之信箱的 Exchange GUID。 |
| SendOnBehalfOfUserSmtp | 代表其傳送電子郵件之使用者的 SMTP 位址。 |


### <a name="exchange-mailbox-audit-group"></a>Exchange 信箱稽核群組
這些記錄會在針對 Exchange 群組做出變更或新增時建立。

| 屬性 | 說明 |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | 群組中每個項目的相關資訊。 |
| CrossMailboxOperations | 指出該作業是否涉及多個信箱。 |
| DestMailboxId | 只有在 CrossMailboxOperations 參數為 True 時才會設定。 指定目標信箱 GUID。 |
| DestMailboxOwnerMasterAccountSid | 只有在 CrossMailboxOperations 參數為 True 時才會設定。 針對目標信箱擁有者的主要帳戶 SID 指定 SID。 |
| DestMailboxOwnerSid | 只有在 CrossMailboxOperations 參數為 True 時才會設定。 指定目標信箱的 SID。 |
| DestMailboxOwnerUPN | 只有在 CrossMailboxOperations 參數為 True 時才會設定。 指定目標信箱之擁有者的 UPN。 |
| DestFolder | 針對移動等作業的目的地資料夾。 |
| 資料夾 | 項目群組所在的資料夾。 |
| 資料夾 |     涉及作業之來源資料夾的相關資訊，例如，在選取資料夾後將它加以刪除。 |


### <a name="sharepoint-base"></a>SharePoint 基底
這些是所有 SharePoint 記錄通用的屬性。

| 屬性 | 說明 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | 識別事件在 SharePoint 中發生。 可能的值為 SharePoint 或 ObjectModel。 |
| ItemType | 被存取或修改之物件的類型。 如需物件類型的詳細資料，請參閱 ItemType 表格。 |
| MachineDomainInfo | 裝置同步作業的相關資訊。 此資訊只會在存在於要求中時才會報告。 |
| MachineId |   裝置同步作業的相關資訊。 此資訊只會在存在於要求中時才會報告。 |
| Site_ | 使用者存取的檔案或資料夾所在之網站的 GUID。 |
| Source_Name | 觸發已稽核作業的實體。 可能的值為 SharePoint 或 ObjectModel。 |
| UserAgent | 使用者的用戶端或瀏覽器的相關資訊。 此資訊是由用戶端或瀏覽器所提供。 |


### <a name="sharepoint-schema"></a>SharePoint 結構描述
這些記錄會在針對 SharePoint 做出設定變更時建立。

| 屬性 | 說明 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | 自訂事件的選擇性字串。 |
| Event_Data |  自訂事件的選擇性承載。 |
| ModifiedProperties | 系統會針對管理員事件 (例如，將使用者新增為網站或網站集合管理員群組的成員) 包含此屬性。 此屬性包含被修改之屬性的名稱 (例如「網站管理員」群組)、已修改屬性的新值 (例如新增為網站管理員的使用者)，以及已修改物件先前的值。 |


### <a name="sharepoint-file-operations"></a>SharePoint 檔案作業
這些記錄是為了回應 SharePoint 中的檔案作業而建立。

| 屬性 | 說明 |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | 被複製或移動之檔案的副檔名。 此屬性僅針對 FileCopied 和 FileMoved 事件顯示。 |
| DestinationFileName | 被複製或移動之檔案的名稱。 此屬性僅針對 FileCopied 和 FileMoved 事件顯示。 |
| DestinationRelativeUrl | 複製或移動檔案之目的地資料夾的 URL。 SiteURL、DestinationRelativeURL 及 DestinationFileName 參數之值的組合，和 ObjectID 屬性的值相同，也就是被複製之檔案的完整路徑名稱。 此屬性僅針對 FileCopied 和 FileMoved 事件顯示。 |
| SharingType | 指派給與之共用資源之使用者的共用權限類型。 此使用者是由 UserSharedWith 參數識別。 |
| Site_Url | 使用者所存取之檔案或資料夾所在的網站 URL。 |
| SourceFileExtension | 使用者所存取之檔案的副檔名。 如果存取的物件是資料夾，此屬性將會是空白。 |
| SourceFileName |  使用者所存取之檔案或資料夾的名稱。 |
| SourceRelativeUrl | 包含使用者所存取之檔案的資料夾 URL。 SiteURL、SourceRelativeURL 及 SourceFileName 參數之值的組合，和 ObjectID 屬性的值相同，也就是使用者所存取之檔案的完整路徑名稱。 |
| UserSharedWith |  與之共用資源的使用者。 |




## <a name="sample-log-searches"></a>記錄檔搜尋範例
下表提供此方案所收集之更新記錄的記錄搜尋範例。

| 查詢 | 說明 |
| --- | --- |
|Office 365 訂閱上所有作業的計數 |Type = OfficeActivity &#124; measure count() by Operation |
|SharePoint 網站的使用情況|Type=OfficeActivity OfficeWorkload=sharepoint &#124; measure count() as Count by SiteUrl &#124; sort Count asc|
|依使用者類型分類的檔案存取作業|Type=OfficeActivity OfficeWorkload=sharepoint Operation=FileAccessed &#124; measure count() by UserType|
|使用特定關鍵字進行搜尋|Type=OfficeActivity OfficeWorkload=azureactivedirectory "MyTest"|
|監視 Exchange 上的外部動作|Type=OfficeActivity OfficeWorkload=exchange ExternalAccess = true|



## <a name="troubleshooting"></a>疑難排解

如果 Office 365 解決方案沒有以預期的方式收集資料，請在 OMS 入口網站的 [設定] -> [連接的來源] -> [Office 365] 中檢查其狀態。 下表描述每個狀態。

| 狀態 | 說明 |
|:--|:--|
| Active | Office 365 訂閱為作用中狀態，且工作負載已成功連線至您的 OMS 工作區。 |
| Pending | Office 365 訂閱為作用中狀態，但工作負載尚未成功連線至您的 OMS 工作區。 首次連線 Office 365 訂閱時，所有工作負載都會處於這個狀態，直到成功連線為止。 請等待 24 小時的時間，以讓所有工作負載切換至「作用中」。 |
| 非使用中 | Office 365 訂閱處於非作用中狀態。 請查看 Office 365 管理員頁面以取得詳細資料。 在您啟用 Office 365 訂閱之後，請取消它和 OMS 工作區之間的連結，然後再次連結它以開始接收資料。 |



## <a name="next-steps"></a>後續步驟
* 使用 [Log Analytics](../log-analytics/log-analytics-log-searches.md) 中的記錄搜尋，檢視詳細的更新資料。
* [建立自己的儀表板](../log-analytics/log-analytics-dashboards.md)來顯示您最愛的 Office 365 搜尋查詢。
* [建立警示](../log-analytics/log-analytics-alerts.md)來讓系統主動通知您重要的 Office 365 活動。  
