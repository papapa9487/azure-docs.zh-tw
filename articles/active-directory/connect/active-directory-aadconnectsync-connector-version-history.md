---
title: "連接器版本發行歷程記錄 | Microsoft Docs"
description: "本主題列出所有適用於 Forefront Identity Manager (FIM) 和 Microsoft Identity Manager (MIM) 的連接器版本"
services: active-directory
documentationcenter: 
author: fimguy
manager: femila
editor: 
ms.assetid: 6a0c66ab-55df-4669-a0c7-1fe1a091a7f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/06/2017
ms.author: fimguy
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 98eb9b3a58737da2436eed591d69a900166c6af9
ms.contentlocale: zh-tw
ms.lasthandoff: 09/07/2017

---
# <a name="connector-version-release-history"></a>連接器版本發行歷程記錄
適用於 Forefront Identity Manager (FIM) 和 Microsoft Identity Manager (MIM) 的連接器會經常更新。

> [!NOTE]
> 本主題僅討論 FIM 與 MIM。 不支援在 Azure AD Connect 上安裝這些連接器。 升級至指定的組建時，會在 AADConnect 上預先安裝發行的連接器。

本主題會列出所有已發行的連接器版本。

相關連結：

* [下載最新的連接器](http://go.microsoft.com/fwlink/?LinkId=717495)
* [一般 LDAP 連接器](active-directory-aadconnectsync-connector-genericldap.md) 參考文件
* [一般 SQL 連接器](active-directory-aadconnectsync-connector-genericsql.md) 參考文件
* [Web 服務連接器](http://go.microsoft.com/fwlink/?LinkID=226245) 參考文件
* [PowerShell 連接器](active-directory-aadconnectsync-connector-powershell.md) 參考文件
* [Lotus Domino 連接器](active-directory-aadconnectsync-connector-domino.md) 參考文件


## <a name="116040-aadconnect-116140"></a>1.1.604.0 (AADConnect 1.1.614.0)


### <a name="fixed-issues"></a>已修正的問題：

* 一般 Web 服務︰
  * 已修正在有兩個或多個端點時無法建立 SOAP 專案的問題。
* 一般 SQL：
  * 在匯入作業中，當 GSQL 儲存至連接器空間時，並未正確轉換時間。 GSQL 連接器空間的預設日期和時間格式已從 'yyyy-MM-dd hh:mm:ssZ' 變更為 'yyyy-MM-dd HH:mm:ssZ'。

## <a name="115510-aadconnect-115530"></a>1.1.551.0 (AADConnect 1.1.553.0)

### <a name="fixed-issues"></a>已修正的問題：

* 一般 Web 服務︰
  * Wsconfig 工具未正確地從 REST 服務方法的「範例要求」轉換 Json 陣列。 這會造成 REST 要求的 Json 陣列發生序列化問題。
  * Web 服務連接器組態工具不支援在 JSON 屬性名稱中使用空間符號 
    * 可以手動將取代模式新增至 WSConfigTool.exe.config 檔案，例如 ```<appSettings> <add key=”JSONSpaceNamePattern” value="__" /> </appSettings>```

* Lotus Notes：
  * 當 [允許組織/組織單位使用自訂認證者] 選項停用時，連接器在匯出 (更新) 期間會失敗。在匯出流程之後，所有屬性都匯出至 Domino，但在匯出時，KeyNotFoundException 會傳回給 Sync。 
    * 這是因為重新命名作業在嘗試變更下列其中一個屬性來變更 DN (UserName 屬性) 時失敗：  
      - 姓氏
      - FirstName
      - MiddleInitial
      - AltFullName
      - AltFullNameLanguage
      - ou
      - altcommonname

  * 當 [允許組織/組織單位使用自訂認證者] 選項啟用，但必要的認證者仍然空白時，就會出現 KeyNotFoundException。

### <a name="enhancements"></a>增強功能：

* 一般 SQL：
  * **情節：重新設計的實作：** "*" 功能
  * **解決方案說明：**已變更[多重值參照屬性處理方法](active-directory-aadconnectsync-connector-genericsql.md)。


### <a name="fixed-issues"></a>已修正的問題：

* 一般 Web 服務︰
  * 如果 WebService 連接器存在，則無法匯入伺服器組態
  * WebService 連接器不使用多個 Web 服務

* 一般 SQL：
  * 不列出單一值所參照屬性的物件類型
  * 移除多重值資料表中的值時，變更追蹤策略上的差異匯入會刪除物件
  * GSQL 連線器中的 OverflowException 與 AS/400 上的 DB2

Lotus：
  * 已新增在開啟 GlobalParameters 頁面之前可啟用\停用搜尋 OU 的選項

## <a name="114430"></a>1.1.443.0

發行日期：2017 年 3 月

### <a name="enhancements"></a>增強功能

* 一般 SQL：</br>
  **案例徵兆︰**SQL 連接器的已知限制，一個物件類型只允許一個參考，且成員需要交互參考。 </br>
  **解決方案說明︰**在已選擇 "*" 選項之參考的處理步驟中，物件類型的所有組合會傳回給同步處理引擎。

>[!Important]
- 這會造成許多預留位置
- 必須確定名稱在物件類型之間是唯一的。


* 一般 LDAP：</br>
 **案例︰**只選取了特定資料分割中的幾個容器時，仍會對整個資料分割執行搜尋。 詳細資料會依同步處理服務來進行篩選，而不是依可能會造成效能降低的 MA。 </br>

 **解決方案說明︰**變更了 GLDAP 連接器的程式碼，使其可以瀏覽過所有容器，並搜尋各容器中的物件，而非在整個資料分割中搜尋。


* Lotus Domino：

  **案例︰**在匯出期間移除人員時支援刪除 Domino 郵件。 </br>
  **解決方案︰**可設定在匯出期間移除人員時刪除 Domino 郵件的支援。

### <a name="fixed-issues"></a>已修正的問題：
* 一般 Web 服務︰
 * 透過 WebService 組態工具在預設的 SAP wsconfig 專案中變更服務 URL 時，發生下列錯誤︰找不到路徑的一部分

      ``'C:\Users\cstpopovaz\AppData\Local\Temp\2\e2c9d9b0-0d8a-4409-b059-dceeb900a2b3\b9bedcc0-88ac-454c-8c69-7d6ea1c41d17\cfg.config\cloneconfig.xml'. ``

* 一般 LDAP：
 * GLDAP 連接器無法看到 AD LDS 中的所有屬性
 * 從 LDAP 目錄結構描述偵測不到任何 UPN 屬性時，精靈會中斷
 * 如果未選取 "objectclass" 屬性，完整匯入期間不會顯示差異匯入的探索失敗錯誤
 * 「設定資料分割和階層」組態頁面，不會在下列位置中顯示類型等於 Novel 伺服器之資料分割的任何物件：一般  
LDAP MA。 頁面上只會顯示來自 RootDSE 資料分割的物件。


* 一般 SQL：
 * 修正未匯入一般 SQL 浮水印差異匯入多重值屬性的錯誤
 * 匯出作業刪除\新增多重值屬性的值時，不會在資料來源中刪除\新增這些值。  


* Lotus Notes：
 * Metaverse 會正確顯示特定欄位 [全名]，但匯出至 Notes 時，屬性值會是 Null 或空白。
 * 修正重複認證者錯誤
 * 在 Lotus Domino 連接器上連同其他物件選取了沒有任何資料的物件時，我們會在執行完整匯入時收到探索錯誤。
 * 當 Lotus Domino 連接器上正在執行差異匯入時，於執行結束時，Microsoft.IdentityManagement.MA.LotusDomino.Service.exe 服務有時會傳回應用程式錯誤。
 * 群組成員資格全都正常運作並保留下來，唯獨在執行匯出以試著從成員資格中移除某位使用者時，雖會顯示更新成功，但使用者實際上並未從 Lotus Notes 的成員資格中移除。
 * Lotus MA 的組態 GUI 中新增了選擇「將項目附加在底部」做為匯出模式的機會，可在多重值屬性匯出期間將新項目附加在底部。
 * 連接器會新增從「郵件資料夾」和「識別碼保存庫」中刪除檔案所需要的邏輯。
 * 刪除成員資格時不會跨 NAB 成員進行。
 * 值應該會成功從多重值屬性中刪除

## <a name="111170"></a>1.1.117.0
發行日期：2016 年 3 月

**一般 SQL 連接器**  
的 [一般 SQL 連接器](active-directory-aadconnectsync-connector-genericsql.md)初始版本。

**新功能︰**

* 一般 LDAP 連接器：
  * 新增使用 Isode 進行差異匯入的支援。
* Web 服務連接器：
  * 已更新 csEntryChangeResult 活動和 setImportErrorCode 活動，可讓物件層級的錯誤傳回至同步處理引擎。
  * 已更新 SAP6 和 SAP6User 範本，以使用新的物件層級錯誤功能。
* Lotus Domino 連接器：
  * 進行匯出時，您需要針對每個通訊錄使用一個認證者。 您現在可以針對所有認證者使用相同的密碼，以便於管理。

**已修正的問題：**

* 一般 LDAP 連接器：
  * 針對 IBM Tivoli DS，並未正確偵測到某些參考屬性。
  * 針對差異匯入期間的 Open LDAP，已截斷字串開頭和結尾的空格。
  * 針對 Novell 和 NetIQ，在 OU/容器之間移動物件且同時將物件重新命名的匯出會失敗。
* Web 服務連接器：
  * 如果 Web 服務針對相同繫結具有多個端點，則連接器不會正確探索這些端點。
* Lotus Domino 連接器：
  * 無法將 fullName 屬性匯出到郵件傳入資料庫。
  * 新增和移除群組成員的匯出只會匯出新增的成員。
  * 如果 Notes 文件無效 (將 isValid 屬性設為 false)，則連接器會失敗。

## <a name="older-releases"></a>較舊的版本
在 2016 年 3 月之前，已利用支援主題方式發行連接器。

**一般 LDAP**

* [KB3078617](https://support.microsoft.com/kb/3078617) - 1.0.0597，2015 年 9 月
* [KB3044896](https://support.microsoft.com/kb/3044896) - 1.0.0549，2015 年 3 月
* [KB3031009](https://support.microsoft.com/kb/3031009) - 1.0.0534，2015 年 1 月
* [KB3008177](https://support.microsoft.com/kb/3008177) - 1.0.0419，2014 年 9 月
* [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082，2014 年 3 月

**WebServices**

* [KB3008178](https://support.microsoft.com/kb/3008178) - 1.0.0419，2014 年 9 月

**PowerShell**

* [KB3008179](https://support.microsoft.com/kb/3008179) - 1.0.0419，2014 年 9 月

**Lotus Domino**

* [KB3096533](https://support.microsoft.com/kb/3096533) - 1.0.0597，2015 年 9 月
* [KB3044895](https://support.microsoft.com/kb/3044895) - 1.0.0549，2015 年 3 月
* [KB2977286](https://support.microsoft.com/kb/2977286) - 5.3.0712，2014 年 8 月
* [KB2932635](https://support.microsoft.com/kb/2932635) - 5.3.1003，2014 年 2 月  
* [KB2899874](https://support.microsoft.com/kb/2899874) - 5.3.0721，2013 年 10 月
* [KB2875551](https://support.microsoft.com/kb/2875551) - 5.3.0534，2013 年 8 月

## <a name="next-steps"></a>後續步驟
深入了解 [Azure AD Connect 同步](active-directory-aadconnectsync-whatis.md) 組態。

深入了解 [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

