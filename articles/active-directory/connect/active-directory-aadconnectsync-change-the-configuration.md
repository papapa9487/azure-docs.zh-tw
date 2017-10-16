---
title: "Azure AD Connect 同步：在 Azure AD Connect 同步中進行組態變更 | Microsoft Docs"
description: "逐步解說如何對 Azure AD Connect 同步處理中的組態進行變更。"
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 7b9df836-e8a5-4228-97da-2faec9238b31
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 63a7ae9d39e1a74294637172efd607ee41b2d69b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-how-to-make-a-change-to-the-default-configuration"></a>Azure AD Connect 同步處理：如何變更預設組態
本主題的目的在於逐步解說如何對 Azure AD Connect 同步處理中的預設組態進行變更。其中提供一些常見案例的步驟。 具備此知識，您應該能夠根據自己的商務規則對自己的組態進行一些簡單的變更。

## <a name="synchronization-rules-editor"></a>同步處理規則編輯器
同步處理規則編輯器用於查看和變更預設組態。 您可以在 [開始] 功能表的 [Azure AD Connect]  群組之下找到它。  
![內含同步處理規則編輯器的開始功能表](./media/active-directory-aadconnectsync-change-the-configuration/startmenu2.png)

當您開啟它時，您會看到預設的現成可用規則。

![同步處理規則編輯器](./media/active-directory-aadconnectsync-change-the-configuration/sre2.png)

### <a name="navigating-in-the-editor"></a>在編輯器中瀏覽
編輯器頂端的下拉式清單可讓您快速找到特定規則。 例如，如果您想要查看已納入屬性 proxyAddresses 的規則，您可將下拉式清單變更如下︰  
![SRE 篩選](./media/active-directory-aadconnectsync-change-the-configuration/filtering.png)  
若要重設篩選並載入全新的組態，請按鍵盤上的 **F5** 。

右上方有 [新增規則] 按鈕。 此按鈕用於建立您自己的自訂規則。

底部有可供您處理選取的同步處理規則的按鈕。 [編輯] 和 [刪除] 會如您預期般執行。  會產生 PowerShell 指令碼以便重新建立同步處理規則。 此程序可讓您將同步處理規則從一部伺服器移到另一部伺服器。

## <a name="create-your-first-custom-rule"></a>建立您的第一個自訂規則
最常見的變更是屬性流程的變更。 您來源目錄中的資料可能不會與 Azure AD 中的一樣。 在本節的範例中，您要確保使用者的名稱一律為 **適當的大小寫**。

### <a name="disable-the-scheduler"></a>停用排程器
[排程器](active-directory-aadconnectsync-feature-scheduler.md) 預設會每隔 30 分鐘執行一次。 您要確保在您進行變更時，排程器未啟動，並疑難排解您的新規則。 若要暫時停用排程器，請啟動 PowerShell，然後執行 `Set-ADSyncScheduler -SyncCycleEnabled $false`

![停用排程器](./media/active-directory-aadconnectsync-change-the-configuration/schedulerdisable.png)  

### <a name="create-the-rule"></a>建立規則
1. 按一下 [新增規則] 。
2. 在 [說明] 頁面上輸入下列各項：  
   ![輸入規則篩選](./media/active-directory-aadconnectsync-change-the-configuration/description2.png)  
   * 名稱：賦予規則描述性名稱。
   * 描述︰讓其他人可以了解規則用途的一些說明。
   * 連接的系統︰可在其中找到物件的系統。 在此案例中，請選取 [Active Directory 連接器]。
   * 已連線的系統/Metaverse 物件類型︰分別選取 [使用者] 和 [人員]。
   * 連結類型︰將此值變更為 [聯結] 。
   * 優先順序︰提供在系統中是唯一的值。 較低的數值表示優先順序較高。
   * 標籤︰保留空白。 只有 Microsoft 提供的現成可用規則應該在此方塊中填入值。
3. 在 [範圍篩選器] 頁面上，輸入 **givenName ISNOTNULL**。  
   ![輸入規則範圍篩選器](./media/active-directory-aadconnectsync-change-the-configuration/scopingfilter.png)  
   此區段用來定義應套用規則的物件。 如果保留空白，規則會套用到所有的使用者物件。 但會包括會議室、服務帳戶，以及其他非人員的使用者物件。
4. 在 [聯結規則] 上，將它保留空白。
5. 在 [轉換] 頁面上，將 FlowType 變更為 [運算式]。 選取目標屬性 **givenName**，並在 [來源] 中輸入 `PCase([givenName])`。
   ![輸入規則轉換](./media/active-directory-aadconnectsync-change-the-configuration/transformations.png)  
   同步處理引擎會區分函式名稱和屬性名稱的大小寫。 如果您輸入錯誤，您會在新增規則時看到警告。 編輯器可讓您儲存並繼續進行，因此您必須重新開啟規則並予以更正。
6. 按一下 [新增] 以儲存規則。

新的自訂規則應與其他同步處理規則一起顯示在系統中。

### <a name="verify-the-change"></a>驗證變更
利用這項新變更，您可確保其如預期般運作，而且不會擲回任何錯誤。 視您擁有的物件數目而言，有兩種不同的方式可執行此步驟。

1. 在所有物件上執行完整同步處理
2. 在單一物件上執行預覽和完整同步處理

從 [開始] 功能表啟動 [同步處理服務]  。 本節中的步驟全都在此工具中執行。

1. **所有物件的完整同步處理**  
   從 [動作] 中選取 [執行]  。 識別您在前一節中進行變更的連接器，在此例中為 Active Directory 網域服務，並加以選取。 從 [動作] 中選取 [執行]，然後選取 [完整同步處理] 和 [確定]。
   ![完整同步處理](./media/active-directory-aadconnectsync-change-the-configuration/fullsync.png)  
   物件現已在 Metaverse 中更新。 您現在想要查看 Metaverse 中的物件。
2. **單一物件的預覽和完整同步處理**  
   從 [動作] 中選取 [執行]  。 識別您在前一節中進行變更的連接器，在此例中為 Active Directory 網域服務，並加以選取。 選取 [搜尋連接器空間] 。 使用範圍來尋找您要用來測試變更的物件。 選取物件，然後按一下 [預覽] 。 在新的畫面中，選取 [認可預覽] 。  
   ![Commit preview](./media/active-directory-aadconnectsync-change-the-configuration/commitpreview.png)  
   變更現已認可至 Metaverse。

**查看 Metaverse 中的物件**  
您現在可挑選幾個範例物件，確定這是預期的值並已套用規則。 從頂端選取 [Metaverse 搜尋]  。 新增您需要的任何篩選，以尋找相關的物件。 從搜尋結果中，開啟物件。 查看屬性值，同時在 [同步處理規則]  資料行中確認已如預期套用規則。  
![Metaverse search](./media/active-directory-aadconnectsync-change-the-configuration/mvsearch.png)  

### <a name="enable-the-scheduler"></a>停用排程器
如果一切如同預期，您可以再次啟用排程器。 從 PowerShell，執行 `Set-ADSyncScheduler -SyncCycleEnabled $true`。

## <a name="other-common-attribute-flow-changes"></a>其他常見屬性流程變更
上一節說明如何變更屬性流程。 本節提供了一些其他的範例。 如何建立同步處理規則的步驟已縮減，但您可以在上一節中找到完整的步驟。

### <a name="use-another-attribute-than-the-default"></a>使用預設以外的其他屬性
Fabrikam 中有對名字、姓氏和顯示名稱使用當地字母的樹系。 在擴充屬性中可以找到以拉丁字母表示的這些屬性。 在 Azure AD 和 Office 365 中建立全域通訊清單時，組織反而想要使用這些屬性。

在使用預設組態時，當地樹系中的物件看起來像這樣：  
![屬性流程 1](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp1.png)

若要建立具有其他屬性流程的規則，請執行下列作業：

* 從 [開始] 功能表啟動 [同步處理規則編輯器]  。
* 在左側依然選取 [輸入] 的情況下，按一下 [新增規則] 按鈕。
* 賦予規則名稱和描述。 選取內部部署 Active Directory 和相關的物件類型。 在 [連結類型] 中，選取 [聯結]。 為優先順序挑選一個其他規則還沒使用的數字。 現成可用的規則是從 100 開始，因此此範例可以使用 50 這個值。
  ![屬性流程 2](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp2.png)
* 將範圍留空 (也就是，應該套用到樹系中的所有使用者物件)。
* 將聯結規則留空 (也就是，讓現成可用的規則處理任何聯結)。
* 在 [轉換] 中，建立下列流程：  
  ![屬性流程 3](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp3.png)
* 按一下 [新增] 以儲存規則。
* 移至 [同步處理服務管理員] 。 在 [連接器] 上，選取我們已在其中新增規則的連接器。 依序選取 [執行] 和 [完整同步處理]。 完整同步處理會使用目前的規則重新計算所有物件。

這是具有此自訂規則之相同物件的結果：  
![屬性流程 4](./media/active-directory-aadconnectsync-change-the-configuration/attributeflowjp4.png)

### <a name="length-of-attributes"></a>屬性的長度
字串屬性預設會設定為可索引，且最大長度為 448 個字元。 如果您使用的字串屬性可能包含更多字元，則確定在屬性流程中包含下列項目：  
`attributeName` <- `Left([attributeName],448)`

### <a name="changing-the-userprincipalsuffix"></a>變更 userPrincipalSuffix
由於使用者未必會知道 Active Directory 中的 userPrincipalName 屬性，因此這個屬性可能不適合做為登入識別碼。 Azure AD Connect 同步處理安裝精靈允許挑選不同的屬性，例如 mail。 但是在某些情況下必須計算屬性。 例如，Contoso 公司有兩個 Azure AD 目錄，一個用於生產環境，一個用於測試。 他們想讓其測試租用戶中的使用者在登入識別碼中使用其他後置詞。  
`userPrincipalName` <- `Word([userPrincipalName],1,"@") & "@contosotest.com"`

在此運算式中，取出第一個 @-sign 左邊的所有內容 (Word)，然後使用固定的字串串連。

### <a name="convert-a-multi-value-to-a-single-value"></a>將多重值轉換成單一值
Active Directory 中的某些屬性在結構描述中是多重值，但是在 [Active Directory 使用者和電腦] 中看起來是單一值。 描述屬性是其中一個範例。  
`description` <- `IIF(IsNullOrEmpty([description]),NULL,Left(Trim(Item([description],1)),448))`

在此運算式中，如果屬性有值，請取屬性中的第一個項目 (Item)、移除開頭和結尾的空格 (Trim)，然後保留字串中的前 448 個字元 (Left)。

### <a name="do-not-flow-an-attribute"></a>請勿傳送屬性
如需本節案例的背景，請參閱 [控制屬性流程程序](active-directory-aadconnectsync-understanding-declarative-provisioning.md#control-the-attribute-flow-process)。

有兩種方式可防止傳送屬性。 第一個可在安裝精靈中使用，而且可讓您 [移除選取的屬性](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering)。 如果您先前不曾同步處理該屬性，則適用這個選項。 不過，如果您已開始同步處理這個屬性，且稍後利用這個功能來移除它，則同步處理引擎會停止管理屬性，而現有的值會留在 Azure AD 中。

如果您想要移除屬性的值並確定未來不會傳送它，就需要改為建立自訂規則。

在 Fabrikam，我們在同步處理到雲端的屬性中發現了一些不應該存在的屬性。 我們想要確定這些屬性會從 Azure AD 中移除。  
![不正確的擴充屬性](./media/active-directory-aadconnectsync-change-the-configuration/badextensionattribute.png)

* 建立新的輸入同步處理規則並填入說明![說明](./media/active-directory-aadconnectsync-change-the-configuration/syncruledescription.png)
* 建立類型為 **Expression** 且來源為 **AuthoritativeNull** 的屬性流程。 即使優先順序較低的同步處理規則會嘗試填入值，常值 **AuthoritativeNull** 還是會指出 MV 中的值應該是空的。
  ![擴充屬性的轉換](./media/active-directory-aadconnectsync-change-the-configuration/syncruletransformations.png)
* 儲存同步處理規則。 啟動 [同步處理服務]、尋找連接器、選取 [執行] 和 [完整同步處理]。 此步驟會重新計算所有屬性流程。
* 藉由搜尋連接器空間，來確認即將匯出所需的變更。
  ![分段刪除](./media/active-directory-aadconnectsync-change-the-configuration/deletetobeexported.png)

## <a name="create-rules-with-powershell"></a>使用 PowerShell 建立規則
當您只需要進行一些變更時，使用同步處理規則編輯器就足以完成工作。 如果您需要進行許多變更，則 PowerShell 可能是較好的選擇。 某些進階功能只有 PowerShell 才提供。

### <a name="get-the-powershell-script-for-an-out-of-box-rule"></a>取得 PowerShell 指令碼，獲得可立即使用的內建規則
若要查看已建立內建規則的 PowerShell 指令碼，請在同步處理規則編輯器中選取規則，然後按一下 [匯出]。 此動作會為您提供已建立規則的 PowerShell 指令碼。

### <a name="advanced-precedence"></a>進階優先順序
內建同步處理規則具有從 100 開始算起的優先順序值。 如果您有許多樹系且需要進行許多自訂變更，那麼 99 同步處理規則可能不太夠。

您可以指示同步處理引擎，指定要在內建規則前面插入其他規則。 若要取得此行為，請依照下列步驟執行︰

1. 在同步處理規則編輯器中標記第一個內建同步處理規則 (此規則是 **In from AD-User Join**)，然後選取 [匯出]。 複製 SR 識別碼值。  
![變更前的 PowerShell](./media/active-directory-aadconnectsync-change-the-configuration/powershell1.png)  
2. 建立新的同步處理規則。 您可以使用同步處理規則編輯器來建立它。 將規則匯出到 PowerShell 指令碼。
3. 在屬性 **PrecedenceBefore** 中，插入內建規則的識別碼值。 將 **Precedence** 設定為 **0**。 確定 [識別碼] 屬性是唯一的，而且您沒有重複使用另一個規則的 GUID。 此外，請確定 **ImmutableTag** 屬性未設定；此屬性只應該針對內建規則設定。 儲存 PowerShell 指令碼並執行它。 得到的結果是系統會對您的自訂規則指派優先順序值 100，而且所有其他內建規則會自此開始遞增。  
![變更後的 PowerShell](./media/active-directory-aadconnectsync-change-the-configuration/powershell2.png)  

您可以依據自己的需求，擁有許多個使用相同 **PrecedenceBefore** 值的自訂同步處理規則。


## <a name="enable-synchronization-of-preferreddatalocation"></a>啟用 PreferredDataLocation 的同步處理
Azure AD Connect 可對 1.1.524.0 版和更新版本之**使用者**物件的 **PreferredDataLocation** 屬性進行同步處理。 更具體地說，我們已導入下列變更︰

* Azure AD 連接器中**使用者**物件類型的結構描述已擴充而納入了 PreferredDataLocation 屬性，此屬性是字串類型，且具備單一值。

* Metaverse 中**人員**物件類型的結構描述已擴充而納入了 PreferredDataLocation 屬性，此屬性是字串類型，且具備單一值。

內部部署 Active Directory 中沒有對應的 PreferredDataLocation 屬性，因此根據預設，PreferredDataLocation 屬性並未啟用同步處理。 您必須手動啟用同步處理。

> [!IMPORTANT]
> Azure AD 目前允許使用 Azure AD PowerShell，直接設定「已同步處理的使用者物件」和「雲端使用者物件」上的 PreferredDataLocation 屬性。 在啟用了 PreferredDataLocation 屬性的同步處理之後，您就必須停止使用 Azure AD PowerShell 來設定**已同步處理的使用者物件**上的屬性，因為 Azure AD Connect 會根據內部部署 Active Directory 中的來源屬性值將這些物件加以覆寫。

> [!IMPORTANT]
> 到了 2017 年 9 月 1 日時，Azure AD 就不會再允許使用 Azure AD PowerShell，來直接設定**已同步處理的使用者物件**上的 PreferredDataLocation 屬性。 若要設定「已同步處理的使用者物件」上的 PreferredLocation 屬性，您就只能使用 Azure AD Connect。

在啟用 PreferredDataLocation 屬性的同步處理之前，您必須︰

 * 首先，決定要使用哪個內部部署 Active Directory 屬性來作為來源屬性。 此屬性的類型必須是**字串**，且具備**單一值**。

 * 如果您先前已使用 Azure AD PowerShell，來設定 Azure AD 中現有「已同步處理的使用者物件」上的 PreferredDataLocation 屬性，則必須將這些屬性值**向下移植**到內部部署 Active Directory 中對應的使用者物件內。
 
    > [!IMPORTANT]
    > 如果您未將這些屬性值向下移植到內部部署 Active Directory 中對應的使用者物件，Azure AD Connect 將會在 PreferredDataLocation 屬性的同步處理啟用時，移除 Azure AD 中現有的屬性值。

 * 目前我們會建議您在至少幾個內部部署 AD 使用者物件上設定來源屬性，以供稍後用來進行驗證。
 
為 PreferredDataLocation 屬性啟用同步處理的步驟可總結為以下幾項︰

1. 停用同步排程器，並確認沒有任何同步處理在進行中

2. 在內部部署 AD 連接器結構描述中新增來源屬性

3. 在 Azure AD 連接器結構描述中新增 PreferredDataLocation

4. 建立輸入同步處理規則，以從內部部署 Active Directory 傳輸屬性值

5. 建立輸出同步處理規則，以將屬性值傳輸到 Azure AD

6. 執行完整的同步處理週期

7. 啟用同步排程器

> [!NOTE]
> 本節的剩餘內容會詳述這些步驟。 用來說明這些步驟的環境是某個 Azure AD 部署，其具有單一樹系拓撲，但沒有自訂的同步處理規則。 如果您設定了多樹系拓撲和自訂同步處理規則，或具有預備伺服器，則需要據此現況調整這些步驟。

### <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>步驟 1：停用同步排程器，並確認沒有任何同步處理在進行中
請確定在您更新同步處理規則時系統不會進行同步處理，以免將不想要的變更匯出到 Azure AD。 若要停用內建的同步排程器︰

 1. 在 Azure AD Connect 伺服器上啟動 PowerShell 工作階段。

 2. 執行 Cmdlet 以停用排定的同步處理︰`Set-ADSyncScheduler -SyncCycleEnabled $false`
 
 3. 移至 [開始] → [同步處理服務] 來啟動 **Synchronization Service Manager**。
 
 4. 移至 [作業] 索引標籤，確認沒有任何作業是「進行中」狀態。

![同步處理服務管理員 - 確認沒有進行中的作業](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step1.png)

### <a name="step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema"></a>步驟 2：在內部部署 AD 連接器結構描述中新增來源屬性
並非所有 AD 屬性都會匯入至內部部署 AD 連接器空間。 若要在所匯入屬性的清單中新增來源屬性︰

 1. 移至 Synchronization Service Manager 中的 [連接器] 索引標籤。
 
 2. 以滑鼠右鍵按一下 [內部部署 AD 連接器]，然後選取 [屬性]。
 
 3. 在彈出的對話方塊中，移至 [選取屬性] 索引標籤。
 
 4. 確定您已在屬性清單中勾選來源屬性。
 
 5. 按一下 [確定] 來進行儲存。

![在內部部署 AD 連接器結構描述中新增來源屬性](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step2.png)

### <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>步驟 3：在 Azure AD 連接器結構描述中新增 PreferredDataLocation
根據預設，系統不會在 Azure AD Connect 空間中匯入 PreferredDataLocation 屬性。 若要在所匯入屬性的清單中新增 PreferredDataLocation 屬性︰

 1. 移至 Synchronization Service Manager 中的 [連接器] 索引標籤。

 2. 以滑鼠右鍵按一下 [Azure AD 連接器]，然後選取 [屬性]。

 3. 在彈出的對話方塊中，移至 [選取屬性] 索引標籤。

 4. 確定您已在屬性清單中勾選 PreferredDataLocation 屬性。

 5. 按一下 [確定] 來進行儲存。

![在 Azure AD 連接器結構描述中新增來源屬性](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step3.png)

### <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>步驟 4：建立輸入同步處理規則，以從內部部署 Active Directory 傳輸屬性值
輸入同步處理規則允許屬性值從內部部署 Active Directory 的來源屬性傳輸到 Metaverse︰

1. 移至 [開始] → [同步處理規則編輯器] 來啟動**同步處理規則編輯器**。

2. 將搜尋篩選條件的 [方向] 設定為 [輸入]。

3. 按一下 [新增規則] 按鈕以建立新的輸入規則。

4. 在 [描述] 索引標籤下，提供下列設定︰
 
    | 屬性 | 值 | 詳細資料 |
    | --- | --- | --- |
    | 名稱 | 提供名稱 | 例如，「In from AD – User PreferredDataLocation」 |
    | 說明 | 提供描述 |  |
    | 連線系統 | 選取內部部署 AD 連接器 |  |
    | 連線系統物件類型 | **使用者** |  |
    | Metaverse 物件類型 | **人員** |  |
    | 連結類型 | **Join** |  |
    | 優先順序 | 選擇 1 - 99 之間的數字 | 1 - 99 已保留供自訂同步處理規則使用。 請勿挑選已由其他同步處理規則使用的值。 |

5. 移至 [範圍篩選器] 索引標籤，並**使用下列子句來新增單一範圍篩選器群組**：
 
    | 屬性 | 運算子 | 值 |
    | --- | --- | --- |
    | adminDescription | NOTSTARTWITH | 使用者\_ | 
 
    範圍篩選器會決定此輸入同步處理規則要套用至哪個內部部署 AD 物件。 在此範例中，我們所使用的範圍篩選器和「In from AD – User Common」OOB 同步處理規則所使用的相同，這是為了避免系統將同步處理規則套用到透過 Azure AD 使用者回寫功能所建立的使用者物件。 您可能需要根據 Azure AD Connect 部署來調整範圍篩選器。

6. 移至 [轉換] 索引標籤，並實作下列轉換規則︰
 
    | 流程類型 | 目標屬性 | 來源 | 套用一次 | 合併類型 |
    | --- | --- | --- | --- | --- |
    | 直接 | PreferredDataLocation | 挑選來源屬性 | 未核取 | 更新 |

7. 按一下 [新增] 來建立輸入規則。

![建立輸入同步處理規則](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step4.png)

### <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>步驟 5：建立輸出同步處理規則，以將屬性值傳輸到 Azure AD
輸出同步處理規則允許屬性值從 Metaverse 傳輸到 Azure AD 中的 PreferredDataLocation 屬性︰

1. 移至**同步處理規則**編輯器。

2. 將搜尋篩選條件的 [方向] 設定為 [輸出]。

3. 按一下 [新增規則] 按鈕。

4. 在 [描述] 索引標籤下，提供下列設定︰

    | 屬性 | 值 | 詳細資料 |
    | --- | --- | --- |
    | 名稱 | 提供名稱 | 例如，「Out to AAD – User PreferredDataLocation」 |
    | 說明 | 提供描述 |
    | 連線系統 | 選取 AAD 連接器 |
    | 連線系統物件類型 | User ||
    | Metaverse 物件類型 | **人員** ||
    | 連結類型 | **Join** ||
    | 優先順序 | 選擇 1 - 99 之間的數字 | 1 - 99 已保留供自訂同步處理規則使用。 請勿挑選已由其他同步處理規則使用的值。 |

5. 移至 [範圍篩選器] 索引標籤，並**使用兩個子句來新增單一範圍篩選器群組**：
 
    | 屬性 | 運算子 | 值 |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | User |
    | cloudMastered | NOTEQUAL | True |

    範圍篩選器會決定此輸出同步處理規則要套用至哪個 Azure AD 物件。 在此範例中，我們會使用來自「Out to AD – User Identity」OOB 同步處理規則的同一個範圍篩選器。 它可避免同步處理規則套用到並非從內部部署 Active Directory 同步過來的使用者物件。 您可能需要根據 Azure AD Connect 部署來調整範圍篩選器。
    
6. 移至 [轉換] 索引標籤，並實作下列轉換規則︰

    | 流程類型 | 目標屬性 | 來源 | 套用一次 | 合併類型 |
    | --- | --- | --- | --- | --- |
    | 直接 | PreferredDataLocation | PreferredDataLocation | 未核取 | 更新 |

7. 關閉 [新增] 來建立輸出規則。

![建立輸出同步處理規則](./media/active-directory-aadconnectsync-change-the-configuration/preferredDataLocation-step5.png)

### <a name="step-6-run-full-synchronization-cycle"></a>步驟 6：執行完整的同步處理週期
由於我們已在 AD 和 Azure AD 連接器結構描述中同時新增屬性，並導入了自訂同步處理規則，因此一般來說，我們必須經歷完整的同步處理週期。 建議您先驗證變更，再將它們匯出至 Azure AD。 在手動執行構成完整同步處理週期的步驟時，您可以使用下列步驟來驗證變更。 

1. 在**內部部署 AD 連接器**上執行**完整匯入**步驟：

   1. 移至 Synchronization Service Manager 中的 [作業] 索引標籤。

   2. 以滑鼠右鍵按一下 [內部部署 AD 連接器]，然後選取 [執行...]

   3. 在彈出的對話方塊中選取 [完整匯入]，然後按一下 [確定]。
    
   4. 等候作業完成。

    > [!NOTE]
    > 如果所匯入屬性的清單中已包含來源屬性，您可以略過內部部署 AD 連接器上的完整匯入。 換句話說，在[步驟 2：在內部部署 AD 連接器結構描述中新增來源屬性](#step-2-add-the-source-attribute-to-the-on-premises-ad-connector-schema)期間，您不必進行任何變更。

2. 在 **Azure AD 連接器**上執行**完整匯入**步驟：

   1. 以滑鼠右鍵按一下 [Azure AD 連接器]，然後選取 [執行...]

   2. 在彈出的對話方塊中選取 [完整匯入]，然後按一下 [確定]。
   
   3. 等候作業完成。

3. 驗證現有使用者物件上的同步處理規則變更︰

來自內部部署 Active Directory 的來源屬性和來自 Azure AD 的 PreferredDataLocation 已匯入個別的連接器空間。 在繼續進行「完整同步處理」步驟之前，建議您先**預覽**內部部署 AD 連接器空間上的現有使用者物件。 您所挑選的物件應該要填入來源屬性。 若能成功**預覽**到 Metaverse 中已填入 PreferredDataLocation，則表示您已正確設定同步處理規則。 如需如何進行**預覽**的相關資訊，請參閱[驗證變更](#verify-the-change)一節。

4. 在**內部部署 AD 連接器**上執行**完整同步處理**步驟：

   1. 以滑鼠右鍵按一下 [內部部署 AD 連接器]，然後選取 [執行...]
  
   2. 在彈出的對話方塊中選取 [完整同步處理]，然後按一下 [確定]。
   
   3. 等候作業完成。

5. 確認要對 Azure AD 執行的**擱置匯出**：

   1. 以滑鼠右鍵按一下 [Azure AD 連接器]，然後選取 [搜尋連接器空間]。

   2. 在彈出的 [搜尋連接器空間] 對話方塊中：

      1. 將 [範圍] 設定為 [擱置匯出]。
      
      2. 將 3 個核取方塊全部勾選，包括 [新增]、[修改] 和 [刪除]。
      
      3. 按一下 [搜尋] 按鈕以取得有變更要匯出的物件清單。 若要檢查給定物件的變更，請對物件按兩下。
      
      4. 確認變更符合預期。

6. 在 **Azure AD 連接器**上執行**完整匯出**步驟
      
   1. 以滑鼠右鍵按一下 [Azure AD 連接器]，然後選取 [執行...]
   
   2. 在彈出的 [執行連接器] 對話方塊中選取 [匯出]，然後按一下 [確定]。
   
   3. 等待「匯出至 Azure AD」完成。

> [!NOTE]
> 您可能會發現，Azure AD 連接器的步驟並未包含「完整同步處理」步驟和「匯出」步驟。 屬性值只會從內部部署 Active Directory 傳輸到 Azure AD，因此 Azure AD 不需要這些步驟。

### <a name="step-7-re-enable-sync-scheduler"></a>步驟 7：重新啟用同步排程器
重新啟用內建的同步排程器︰

1. 啟動 PowerShell 工作階段。

2. 執行 Cmdlet 以重新啟用排定的同步處理︰`Set-ADSyncScheduler -SyncCycleEnabled $true`



## <a name="next-steps"></a>後續步驟
* 如需組態模型的詳細資訊，請參閱 [了解宣告式佈建](active-directory-aadconnectsync-understanding-declarative-provisioning.md)。
* 如需運算式語言的詳細資訊，請參閱 [了解宣告式佈建運算式](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)。

**概觀主題**

* [Azure AD Connect 同步處理：了解及自訂同步處理](active-directory-aadconnectsync-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)
