---
title: "教學課程︰以內部部署 Active Directory 和 Azure Active Directory 設定自動化使用者佈建的 Workday | Microsoft Docs"
description: "了解如何使用 Workday 做為 Active Directory 和 Azure Active Directory 身分識別資料的來源。"
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: femila
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/26/2017
ms.author: asmalser
ms.openlocfilehash: 86f5591cd2d67d7f734b7148b79c8ee388336283
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning-with-on-premises-active-directory-and-azure-active-directory"></a>教學課程︰以內部部署 Active Directory 和 Azure Active Directory 設定自動化使用者佈建的 Workday
本教學課程的目標是說明將人員從 Workday 匯入 Active Directory 和 Azure Active Directory，以及將某些屬性選擇性回寫至 Workday 需要執行的步驟。 



## <a name="overview"></a>概觀

[Azure Active Directory 使用者佈建服務](active-directory-saas-app-provisioning.md)與 [Workday Human Resources API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) 整合以佈建使用者帳戶。 Azure AD 使用此連接啟用下列使用者佈建工作流程：

* **將使用者佈建至 Active Directory** - 將選取的使用者集合從 Workday 同步至一或多個 Active Directory 樹系。 

* **將僅限雲端使用者佈建至 Azure Active Directory** - 可以使用 [AAD Connect](connect/active-directory-aadconnect.md)，將存在於 Active Directory 和 Azure Active Directory 的混合式使用者佈建至後者。 不過，可以使用 Azure AD 使用者佈建服務，將僅限雲端使用者從 Workday 直接佈建至 Azure Active Directory。

* **將電子郵件地址回寫至 Workday** - Azure AD 使用者佈建服務可以將選取的 Azure AD 使用者屬性回寫至 Workday，例如電子郵件地址。

### <a name="scenarios-covered"></a>涵蓋的案例

Azure AD 使用者佈建服務支援的 Workday 使用者佈建工作流程，可讓下列人力資源和身分識別生命週期管理案例的自動化：

* **雇用新員工** - 將新員工新增至 Workday 時，系統會在 Active Directory、Azure Active Directory、Office 365 (選擇性) 和 [Azure AD 支援的其他 SaaS 應用程式](active-directory-saas-app-provisioning.md)中自動建立使用者帳戶，並將電子郵件地址寫回 Workday。

* **員工屬性和設定檔更新** - 在 Workday 中更新員工記錄時 (例如姓名、職稱或經理)，系統會在 Active Directory、Azure Active Directory、Office 365 (選擇性) 和 [Azure AD 支援的其他 SaaS 應用程式](active-directory-saas-app-provisioning.md)中自動更新其使用者帳戶。

* **員工離職** - 在 Workday 中將員工設定為離職時，系統會在 Active Directory、Azure Active Directory、Office 365 (選擇性) 和 [Azure AD 支援的其他 SaaS 應用程式](active-directory-saas-app-provisioning.md)中自動停用其使用者帳戶。

* **重新雇用員工** - 在 Workday 中重新雇用員工時，系統會自動重新啟用其舊帳戶或將其重新佈建 (取決於您的喜好設定) 至 Active Directory、Azure Active Directory、Office 365 (選擇性) 和 [Azure AD 支援的其他 SaaS 應用程式](active-directory-saas-app-provisioning.md)。


## <a name="planning-your-solution"></a>規劃您的解決方案

開始進行 Workday 整合之前，請檢查下列必要條件，並閱讀下列有關如何讓目前的 Active Directory 結構和使用者佈建需求與 Azure Active Directory 提供解決方案相符的指導。

### <a name="prerequisites"></a>必要條件

本教學課程中說明的案例假設您已經具有下列項目：

* 具有全域系統管理員存取權的有效 Azure AD Premium P1 訂用帳戶
* 可供測試和整合之用的 Workday 實作租用戶
* 可供測試之用的 Workday 系統管理員權限，以建立系統整合使用者和進行變更以測試員工資料
* 佈建至 Active Directory 的使用者，需要執行 Windows Service 2012 或更新版本的已加入網域伺服器，才能裝載[內部部署同步代理程式](https://go.microsoft.com/fwlink/?linkid=847801)
* 可在 Active Directory 與 Azure AD 之間同步處理的 [Azure AD Connect](connect/active-directory-aadconnect.md)


### <a name="solution-architecture"></a>方案架構

Azure AD 提供一組豐富的佈建連接器，協助您解決從 Workday 到 Active Directory、Azure AD、SaaS 應用程式等產品的佈建和身分識別生命週期管理。 您將使用的功能和設定解決方案的方法將會視組織環境和需求而有所不同。 您的第一步便是評估組織中存在且已部署下列項目的數量：

* 您正在使用多少 Active Directory 樹系？
* 您正在使用多少 Active Directory 網域？
* 有多少 Active Directory 組織單位 (OU) 正在使用？
* 有多少 Azure Active Directory 租用戶正在使用？
* 是否需要將任何使用者佈建至 Active Directory 和 Azure Active Directory (例如「混合式」使用者)？
* 是否需要將任何使用者佈建至 Azure Active Directory，但不需要將其佈建至 Active Directory (例如「僅限雲端」使用者)？
* 是否需要將使用者電子郵件地址寫回至 Workday？

找到這些問題的答案之後，您便可以依照下列指導來計劃 Workday 佈建部署。

#### <a name="using-provisioning-connector-apps"></a>使用佈建連接器應用程式

Azure Active Directory 支援適用於 Workday 和大量其他 SaaS 應用程式的預先整合佈建連接器。 

其為具有單一來源系統 API 的單一佈建連接器介面，且有助於將資料佈建至單一目標系統。 Azure AD 支援的大部分佈建連接器都適用於單一來源和目標系統 (例如 Azure AD 至 ServiceNow)，並可透過從 Azure AD 應用程式庫新增上述應用程式 (例如 ServiceNow) 來設定。 

在 Azure AD 中佈建連接器執行個體與應用程式執行個體之間具有一對一關聯性：

| 來源系統 | 目標系統 |
| ---------- | ---------- | 
| Azure AD 租用戶 | SaaS 應用程式 |


不過，將 Workday 與 Active Directory 搭配使用時，需要考慮多個來源和目標系統：

| 來源系統 | 目標系統 | 注意事項 |
| ---------- | ---------- | ---------- |
| Workday | Active Directory 樹系 | 系統會將每個樹系視為相異目標系統 |
| Workday | Azure AD 租用戶 | 根據僅限雲端使用者的要求 |
| Active Directory 樹系 | Azure AD 租用戶 | 目前由 AAD Connect 處理此流程 |
| Azure AD 租用戶 | Workday | 適用於電子郵件地址的回寫 |

為了讓這些工作流程便利於多個來源和目標系統，Azure AD 提供可以從 Azure AD 應用程式庫新增的多個佈建連接器應用程式：

![AAD 應用程式庫](./media/active-directory-saas-workday-inbound-tutorial/WD_Gallery.PNG)

* **Workday to Active Directory Provisioning** - 此應用程式可協助將使用者帳戶從 Workday 佈建至單一 Active Directory 樹系。 如果您擁有多個樹系，則可以針對要進行佈建的每個 Active Directory 樹系，從 Azure AD 應用程式庫新增此應用程式的執行個體。

* **Workday to Azure AD Provisioning** -雖然應使用 AAD Connect 這項工具將 Active Directory 使用者同步處理至 Azure Active Directory，但此應用程式可用於協助將僅限雲端使用者從 Workday 佈建至單一 Azure Active Directory 租用戶。

* **Workday Writeback** - 此應用程式可協助將使用者的電子郵件地址從 Azure Active Directory 回寫至 Workday。

> [!TIP]
> 您可以使用一般「Workday」應用程式設定 Workday 與 Azure Active Directory 之間的單一登入。 

如何安裝及設定這些特殊佈建連接器應用程式是本教學課程其餘章節的主題。 您選擇要設定的應用程式將取決於需要進行佈建的系統，以及環境中的 Active Directory 樹系和 Azure AD 租用戶數量。

![概觀](./media/active-directory-saas-workday-inbound-tutorial/WD_Overview.PNG)

## <a name="configure-a-system-integration-user-in-workday"></a>在 Workday 中設定系統整合使用者
所有 Workday 佈建連接器的常見需求，是其需要 Workday 系統整合帳戶的認證才能連線至 Workday Human Resources API。 本章節說明如何在 Workday 中建立系統整合者帳戶。

> [!NOTE]
> 您可以略過此程序，改用 Workday 全域系統管理員帳戶做為系統整合帳戶。 這可能會在示範中正常運作，但不建議用於生產環境部署。

### <a name="create-an-integration-system-user"></a>建立整合系統使用者

**建立整合系統使用者：**

1. 使用系統管理員帳戶登入 Workday 租用戶。 在 工作日工作台 中的搜尋方塊輸入 create user，然後按一下建立整合系統使用者。 
   
    ![建立使用者](./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "建立使用者")
2. 為新的「整合系統使用者」 提供使用者名稱和密碼來完成「建立整合系統使用者」  工作。  
 * 保持 [下次登入時要求新密碼] 選項未核取，因為該使用者會以程式設計的方式登入。 
 * 保持 [工作階段逾時分鐘數] 為預設值 [0]，這會防止使用者的工作階段提早逾時。 
   
    ![建立整合系統使用者](./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "建立整合系統使用者")

### <a name="create-a-security-group"></a>建立安全性群組
您需要建立未受限制的整合系統安全性群組，並將使用者指派到該群組。

**建立安全性群組：**

1. 在搜尋方塊中輸入 create security group，然後按一下建立安全性群組 連結。 
   
    ![建立安全性群組](./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "建立安全性群組")
2. 完成**建立安全性群組**工作。  
3. 從 [租用安全性群組類型] 下拉式清單選取 [整合系統安全性群組—未受限制]。
4. 建立安全性群組以供明確新增使用者。 
   
    ![建立安全性群組](./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "建立安全性群組")

### <a name="assign-the-integration-system-user-to-the-security-group"></a>將整合系統使用者指派到安全性群組

**指派整合系統使用者：**

1. 在搜尋方塊中輸入 edit security group，然後按一下編輯全性群組 連結。 
   
    ![編輯安全性群組](./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "編輯安全性群組")
2. 依名稱搜尋新的整合安全性群組，並選取該群組。 
   
    ![編輯安全性群組](./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "編輯安全性群組")
3. 將新的整合系統使用者指派到安全性群組。 
   
    ![系統安全性群組](./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "系統安全性群組")  

### <a name="configure-security-group-options"></a>設定安全性群組選項
在此步驟中，您授予新的安全性群組，對受下列網域安全性原則保護之物件進行 **Get** 和 **Put** 作業：

* 外部帳戶佈建
* 人員資料：公用人員報告
* 人員資料：所有職位
* 人員資料：目前人員配置資訊
* 人員資料：人員個人檔案的職稱

**設定安全性群組選項：**

1. 在搜尋方塊中輸入 domain security policies，然後按一下功能區域的網域安全性原則。  
   
    ![網域安全性原則](./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "網域安全性原則")  
2. 搜尋 system 並選取 [系統]  功能區域。  按一下 [確定] 。  
   
    ![網域安全性原則](./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "網域安全性原則")  
3. 在 [系統] 功能區域的安全性原則清單中，展開 [安全性管理]，並選取 [外部帳戶佈建] 網域安全性原則。  
   
    ![網域安全性原則](./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "網域安全性原則")  
4. 按一下 [編輯權限]，然後在 [編輯權限]對話 畫面上，將新的安全性群組新增到具有 **Get** 和 **Put** 整合權限的群組清單中。 
   
    ![編輯權限](./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "編輯權限")  
5. 重複上述步驟 1，以返回選取功能區域的畫面，這次改為搜尋 staffing，然後選取 [人員配置] 功能區域，再按一下 [確定]。
   
    ![網域安全性原則](./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "網域安全性原則")  
6. 在 [人員配置] 功能區域的安全性原則清單中，展開 [人員資料：人員配置]，並對其餘的各安全性原則重複上述步驟 4：

   * 人員資料：公用人員報告
   * 人員資料：所有職位
   * 人員資料：目前人員配置資訊
   * 人員資料：人員個人檔案的職稱
   
7. 重複上述步驟 1，以返回選取功能區域的畫面，這次改為搜尋 **Contact Information**，然後選取 [人員配置] 功能區域，再按一下 [確定]。

8.  在 [人員配置] 功能區域的安全性原則清單中，展開 [人員資料：公司連絡資訊]，並對下列安全性原則重複上述步驟 4：

    * 人員資料：公司電子郵件

    ![網域安全性原則](./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "網域安全性原則")  
    
### <a name="activate-security-policy-changes"></a>啟用安全性原則變更

**啟用安全性原則變更：**

1. 在搜尋方塊中輸入 activate，然後按一下啟用擱置的安全性原則變更 連結。 
   
    ![啟用](./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "啟用") 
2. 輸入供稽核用的註解並按一下 [確定] 按鈕，以開始「啟用擱置的安全性原則變更」工作。 
   
    ![啟用擱置的安全性](./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "啟用擱置的安全性")   
3. 在下一個畫面核取 確認 核取方塊，然後按一下確定 以完成工作。 
   
    ![啟用擱置的安全性](./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "啟用擱置的安全性")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>設定將使用者從 Workday 佈建至 Active Directory
請遵循下列指示來設定將使用者帳戶從 Workday 佈建至需要進行佈建的每個 Active Directory 樹系。

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>第 1 部分：新增佈建連接器應用程式和建立 Workday 連接

**若要設定 Workday 至 Active Directory 佈建：**

1.  移至 <https://portal.azure.com>

2.  在左側導覽列中，選取 [Azure Active Directory]

3.  依序選取 [企業應用程式] 和 [所有應用程式]。

4.  選取 [新增應用程式]，然後選取 [全部] 類別。

5.  搜尋 **Workday Provisioning to Active Directory**，並從資源庫新增該應用程式。

6.  新增應用程式並顯示應用程式詳細資料畫面之後，請選取 [佈建]

7.  將 [佈建模式] 變更為 [自動]

8.  完成 [系統管理員認證] 區段，如下所示：

   * **系統管理員使用者名稱** – 輸入 Workday 整合系統帳戶的使用者名稱，並附加租用戶網域名稱。 **其應該類似於：username@contoso4**

   * **系統管理員密碼 –** 輸入 Workday 整合系統帳戶的密碼

   * **租用戶 URL –** 輸入租用戶 Workday Web 服務端點的 URL。 其應該類似於：https://wd3-impl-services1.workday.com/ccx/service/contoso4，其中請將 contoso4 取代為正確的租用戶名稱，並將 wd3-impl 取代為正確的環境字串。

   * **Active Directory 樹系 -** Get-ADForest powershell commandlet 所傳回 Active Directory 樹系的「名稱」。 此字串通常類似於：*contoso.com*

   * **Active Directory 容器 -** 輸入包含 AD 樹系中所有使用者的容器字串。 範例：*OU=Standard Users,OU=Users,DC=contoso,DC=test*

   * **電子郵件通知 –** 輸入您的電子郵件地址，然後勾選 [發生失敗時傳送電子郵件] 核取方塊。

   * 按一下 [測試連線] 按鈕。 如果連線測試成功，請按一下頂端的 [儲存] 按鈕。 如果失敗，請仔細檢查 Workday 認證在 Workday 中是否有效。 

![Azure 入口網站](./media/active-directory-saas-workday-inbound-tutorial/WD_1.PNG)

### <a name="part-2-configure-attribute-mappings"></a>第 2 部分：設定屬性對應 

在本節中，您會設定使用者資料從 Workday 流動至 Active Directory 的方式。

1.  在 [佈建] 索引標籤的 [對應] 下，按一下 [將 Workday 人員同步至內部部署]。

2.  在 [來源物件範圍] 欄位中，您可以透過定義一組屬性型篩選，選取應該佈建至 AD 的 Workday 使用者集合範圍。 預設範圍是「Workday 中的所有使用者」。 範例篩選：

   * 範例：人員識別碼介於 1000000 到 2000000 之間的使用者範圍

      * 屬性：WorkerID

      * 運算子：REGEX Match

      * 值：(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 範例：僅員工和非約聘人員 

      * 屬性：EmployeeID

      * 運算子：IS NOT NULL

3.  在 [目標物件動作] 欄位中，您可以全域篩選允許在 Active Directory 上執行哪些動作。 最常見的動作是 [建立] 和 [更新]。

4.  在 [屬性對應] 區段中，您可以定義個別 Workday 屬性如何對應至 Active Directory 屬性。

5. 按一下現有的屬性對應以進行更新，或按一下畫面底端的 [新增新對應] 以新增新對應。 個別屬性對應支援下列屬性：

      * **對應類型**

         * **直接** – 將 Workday 屬性的值寫入 AD 屬性，且不進行變更

         * **常數** - 將靜態的常數字串值寫入 AD 屬性

         * **運算式** – 可讓您根據一或多個 Workday 屬性，將自訂值寫入 AD 屬性。 [如需詳細資訊，請參閱這篇有關運算式的文章](active-directory-saas-writing-expressions-for-attribute-mappings.md)。

      * **來源屬性** - 來自 Workday 的使用者屬性。

      * **預設值** – 選用。 如果來源屬性具有空值，則對應將會改為寫入此值。
            最常見的設定是將其保留空白。

      * **目標屬性** – Active Directory 中的使用者屬性。

      * **使用此屬性比對物件** – 是否應該將此對應用於唯一識別 Workday 與 Active Directory 之間的使用者。 此設定通常會設定於 Workday 的 [人員識別碼] 欄位，且通常會在 Active Directory 中對應至其中一個員工識別碼屬性。

      * **比對優先順序** – 您可以設定多個比對屬性。 具有多個屬性時，系統會以此欄位定義的順序進行評估。 只要找到相符項目，便不會評估進一步比對屬性。

      * **套用此對應**
       
         * **一律** – 將此對應套用於使用者建立和更新動作

         * **僅限建立期間** - 僅將此對應套用於使用者建立動作

6. 若要儲存您的對應，請按一下 [屬性對應] 區段頂端的 [儲存]。

![Azure 入口網站](./media/active-directory-saas-workday-inbound-tutorial/WD_2.PNG)

**下列為 Workday 與 Active Directory 之間的一些範例屬性對應，以及一些常用運算式**

-   對應至 parentDistinguishedName AD 屬性的運算式可用於根據一或多個 Workday 來源屬性，將使用者佈建至特定 OU。 此範例會根據使用者在 Workday 中的城市資料，將其置於不同的 OU。

-   對應至 userPrincipalName AD 屬性的運算式會建立 firstName.LastName@contoso.com 的 UPN。其也會取代不合法的特殊字元。

-   [此為有關撰寫運算式的文件](active-directory-saas-writing-expressions-for-attribute-mappings.md)

  
| WORKDAY 屬性 | ACTIVE DIRECTORY 屬性 |  比對識別碼？ | 建立/更新 |
| ---------- | ---------- | ---------- | ---------- |
|  **WorkerID**  |  EmployeeID | **是** | 僅於建立時寫入 | 
|  **Municipality**   |   l   |     | 建立 + 更新 |
|  **Company**         | company   |     |  建立 + 更新 |
|  **CountryReferenceTwoLetter**      |   co |     |   建立 + 更新 |
| **CountryReferenceTwoLetter**    |  c  |     |         建立 + 更新 |
| **SupervisoryOrganization**  | department  |     |  建立 + 更新 |
|  **PreferredNameData**  |  displayName |     |   建立 + 更新 |
| **EmployeeID**    |  cn    |   |   僅於建立時寫入 |
| **Fax**      | facsimileTelephoneNumber     |     |    建立 + 更新 |
| **名字**   | givenName       |     |    建立 + 更新 |
| **Switch(\[Active\], , "0", "True", "1",)** |  accountDisabled      |     | 建立 + 更新 |
| **Mobile**  |    mobile       |     |       建立 + 更新 |
| **EmailAddress**    | mail    |     |     建立 + 更新 |
| **ManagerReference**   | manager  |     |  建立 + 更新 |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  建立 + 更新 |
| **PostalCode**  |   postalCode  |     | 建立 + 更新 |
| **LocalReference** |  preferredLanguage  |     |  建立 + 更新 |
| **Replace(Mid(Replace(\[EmployeeID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         僅於建立時寫入 |
| **姓氏**   |   sn   |     |  建立 + 更新 |
| **CountryRegionReference** |  st     |     | 建立 + 更新 |
| **AddressLineData**    |  streetAddress  |     |   建立 + 更新 |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | 建立 + 更新 |
| **BusinessTitle**   |  title     |     |  建立 + 更新 |
| **Join("@",Replace(Replace(Replace(Replace(Replace(Replace(Replace( Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace( Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Join(".", [FirstName], [LastName]), , "([Øø])", , "oe", , ), , "[Ææ]", , "ae", , ), , "([äãàâãåáąÄÃÀÂÃÅÁĄA])", , "a", , ), , "([B])", , "b", , ), , "([CçčćÇČĆ])", , "c", , ), , "([ďĎD])", , "d", , ), , "([ëèéêęěËÈÉÊĘĚE])", , "e", , ), , "([F])", , "f", , ), , "([G])", , "g", , ), , "([H])", , "h", , ), , "([ïîìíÏÎÌÍI])", , "i", , ), , "([J])", , "j", , ), , "([K])", , "k", , ), , "([ľłŁĽL])", , "l", , ), , "([M])", , "m", , ), , "([ñńňÑŃŇN])", , "n", , ), , "([öòőõôóÖÒŐÕÔÓO])", , "o", , ), , "([P])", , "p", , ), , "([Q])", , "q", , ), , "([řŘR])", , "r", , ), , "([ßšśŠŚS])", , "s", , ), , "([TŤť])", , "t", , ), , "([üùûúůűÜÙÛÚŮŰU])", , "u", , ), , "([V])", , "v", , ), , "([W])", , "w", , ), , "([ýÿýŸÝY])", , "y", , ), , "([źžżŹŽŻZ])", , "z", , ), " ", , , "", , ), "contoso.com")**   | userPrincipalName     |     | 建立 + 更新                                                   
| **Switch(\[Municipality\], "OU=Standard Users,OU=Users,OU=Default,OU=Locations,DC=contoso,DC=com", "Dallas", "OU=Standard Users,OU=Users,OU=Dallas,OU=Locations,DC=contoso,DC=com", "Austin", "OU=Standard Users,OU=Users,OU=Austin,OU=Locations,DC=contoso,DC=com", "Seattle", "OU=Standard Users,OU=Users,OU=Seattle,OU=Locations,DC=contoso,DC=com", “London", "OU=Standard Users,OU=Users,OU=London,OU=Locations,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  建立 + 更新 |
  
### <a name="part-3-configure-the-on-premises-synchronization-agent"></a>第 3 部分：設定內部部署同步代理程式

若要佈建至 Active Directory 內部部署，您必須在所需 Active Directory 樹系的已加入網域伺服器上安裝代理程式。 您需要網域系統管理員 (或企業系統管理員) 認證才能完成此程序。

**[您可以在此處下載內部部署同步代理程式](https://go.microsoft.com/fwlink/?linkid=847801)**

安裝代理程式之後，請執行下列 PowerShell 命令以設定環境的代理程式。

**命令 1**

> cd C:\\Program Files\\Microsoft Azure Active Directory Synchronization Agent\\Modules\\AADSyncAgent

> import-module AADSyncAgent.psd1

**命令 2**

> Add-ADSyncAgentActiveDirectoryConfiguration

* 輸入：針對「目錄名稱」輸入 AD 樹系名稱，如第 \#2 部分所輸入
* 輸入：Active Directory 樹系的系統管理員使用者名稱和密碼

**命令 3**

> Add-ADSyncAgentAzureActiveDirectoryConfiguration

* 輸入：Azure AD 租用戶的全域系統管理員使用者名稱和密碼

>[!IMPORTANT]
>目前有一項已知問題：若使用自訂網域，則全域管理員認證無法運作 (範例：admin@contoso.com)。 因應措施是以 onmicrosoft.com 網域來建立和使用全域管理員帳戶 (範例：admin@contoso.onmicrosoft.com)


**命令 4**

> Get-AdSyncAgentProvisioningTasks

* 動作：確認已傳回資料。 此命令會自動探索 Azure AD 租用戶中的 Workday 佈建應用程式。 範例輸出︰

> 名稱：我的 AD 樹系
>
> 已啟用：True
>
> DirectoryName：mydomain.contoso.com
>
> 已認證：False
>
> 識別碼：WDAYdnAppDelta.c2ef8d247a61499ba8af0a29208fb853.4725aa7b-1103-41e6-8929-75a5471a5203

**命令 5**

> Start-AdSyncAgentSynchronization -Automatic

**命令 6**

> net stop aadsyncagent

**命令 7**

> net start aadsyncagent

>[!TIP]
>除了在 Powershell 中的「net」命令以外，也可以使用 **Services.msc** 來啟動和停止同步處理代理程式服務。 若在執行 Powershell 命令時收到錯誤訊息，請確定 **Microsoft Azure AD 連線佈建代理程式**是在 **Services.msc** 下執行。

![服務](./media/active-directory-saas-workday-inbound-tutorial/Services.png)  

**針對歐盟客戶的其他設定**

若您的 Azure Active Directory 租用戶位於其中一個 EU 資料中心，請遵循下列的額外步驟。

1. 開啟 **Services.msc**，並停止 **Microsoft Azure AD 連線佈建代理程式**服務。
2. 移至代理程式安裝資料夾 (例如：C:\Program Files\Microsoft Azure AD Connect Provisioning Agent)。
3. 在文字編輯器中開啟 **SyncAgnt.exe.config**。
4. 以 **https://eu.manage.hub.syncfabric.windowsazure.com/Management** 取代 https://manage.hub.syncfabric.windowsazure.com/Management
5. 以 **https://eu.provision.hub.syncfabric.windowsazure.com/Provisioning** 取代 https://provision.hub.syncfabric.windowsazure.com/Provisioning
6. 儲存 **SyncAgnt.exe.config** 檔案。
7. 開啟 **Services.msc**，並啟動 **Microsoft Azure AD 連線佈建代理程式**服務。

**代理程式疑難排解**

裝載代理程式的 Windows Server 機器上的 [Windows 事件記錄檔](https://technet.microsoft.com/en-us/library/cc722404(v=ws.11).aspx)中，包含了所有代理程式所執行的作業事件。 若要檢視這些事件：
    
1. 請開啟 **Eventvwr.msc**。
2. 選取 [Windows 記錄檔 > 應用程式]。
3. 檢視來源 **AADSyncAgent** 底下記錄的所有事件。 
4. 請檢查錯誤和警告。

若 Powershell 命令中所提供的 Active Directory 或 Azure Active Directory 認證有任何權限問題，您將會看到這種錯誤： 
    
![事件記錄檔](./media/active-directory-saas-workday-inbound-tutorial/Windows_Event_Logs.png) 


### <a name="part-4-start-the-service"></a>第 4 部分：啟動服務
完成第 1-3 部分之後，您就可以在 Azure 入口網站中重新啟動佈建服務。

1.  在 [佈建] 索引標籤中，將 [佈建狀態] 設定為 [開啟]。

2. 按一下 [儲存] 。

3. 這會啟動初始同步，取決於 Workday 中的使用者人數，其可能要花費數小時。

4. 您可隨時檢查 Azure 入口網站中的 [稽核記錄] 索引標籤，查看佈建服務執行了哪些動作。 稽核記錄會列出佈建服務執行的所有個別同步處理事件，例如從 Workday 外部讀取了哪些使用者，接著又新增到或更新到 Active Directory 中。 **[請參閱佈建報告指南，瞭解有關如何讀取稽核記錄的詳細指示](active-directory-saas-provisioning-reporting.md)**

5.  請檢查裝載代理程式的 Windows Server 機器上的 [Windows 事件記錄檔](https://technet.microsoft.com/en-us/library/cc722404(v=ws.11).aspx)中，有哪些新錯誤或警告。 可透過在伺服器上啟動 **Eventvwr.msc** 並選取 [Windows 記錄檔 > 應用程式]，來檢視這些事件。 所有佈建相關的訊息都會記錄在來源 **AADSyncAgent** 底下。 
    

6. 完成之後，其會寫入 [佈建] 索引標籤中的稽核摘要報告內，如下所示。

![Azure 入口網站](./media/active-directory-saas-workday-inbound-tutorial/WD_3.PNG)


## <a name="configuring-user-provisioning-to-azure-active-directory"></a>設定將使用者佈建至 Azure Active Directory
設定 Azure Active Directory 佈建的方法將取決於佈建需求，如下表所述。

| 案例 | 方案 |
| -------- | -------- |
| **需要佈建至 Active Directory 和 Azure AD 的使用者** | 使用 **[AAD Connect](connect/active-directory-aadconnect.md)** |
| **只需要佈建至 Active Directory 的使用者** | 使用 **[AAD Connect](connect/active-directory-aadconnect.md)** |
| **只需要佈建至 Azure AD 的使用者 (僅限雲端)** | 使用應用程式庫中的 **Workday to Azure Active Directory Provisioning** 應用程式 |

如需有關設定 Azure AD Connect 的指示，請參閱 [Azure AD Connect 文件](connect/active-directory-aadconnect.md)。

下列各節說明如何設定 Workday 與 Azure AD 之間的連接以佈建僅限雲端使用者。

> [!IMPORTANT]
> 如果您擁有需要佈建至 Azure AD 的僅限雲端使用者且沒有內部部署 Active Directory，請僅遵循下列程序。

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>第 1 部分：新增 Azure AD 佈建連接器應用程式和建立 Workday 連接

**若要針對僅限雲端使用者設定 Workday 至 Azure Active Directory 佈建：**

1.  移至 <https://portal.azure.com>。

2.  在左側導覽列中，選取 [Azure Active Directory]

3.  依序選取 [企業應用程式] 和 [所有應用程式]。

4.  選取 [新增應用程式]，然後選取 [全部] 類別。

5.  搜尋 **Workday to Azure AD Provisioning**，並從資源庫新增該應用程式。

6.  新增應用程式並顯示應用程式詳細資料畫面之後，請選取 [佈建]

7.  將 [佈建模式] 變更為 [自動]

8.  完成 [系統管理員認證] 區段，如下所示：

   * **系統管理員使用者名稱** – 輸入 Workday 整合系統帳戶的使用者名稱，並附加租用戶網域名稱。 其應該類似於：username@contoso4

   * **系統管理員密碼 –** 輸入 Workday 整合系統帳戶的密碼

   * **租用戶 URL –** 輸入租用戶 Workday Web 服務端點的 URL。 其應該類似於：https://wd3-impl-services1.workday.com/ccx/service/contoso4，其中請將 contoso4 取代為正確的租用戶名稱，並將 wd3-impl 取代為正確的環境字串。 如果不知道此 URL，請與您的 Workday 整合夥伴或支援代表合作，來判斷要使用的正確 URL。

   * **電子郵件通知 –** 輸入您的電子郵件地址，然後勾選 [發生失敗時傳送電子郵件] 核取方塊。

   * 按一下 [測試連線] 按鈕。

   * 如果連線測試成功，請按一下頂端的 [儲存] 按鈕。 如果失敗，請仔細檢查 Workday URL 和認證在 Workday 中是否有效。


### <a name="part-2-configure-attribute-mappings"></a>第 2 部分：設定屬性對應 

在本節中，您會針對僅限雲端使用者設定使用者資料從 Workday 流動至 Azure Active Directory 的方式。

1.  在 [佈建] 索引標籤的 [對應] 下，按一下 [將人員同步至 Azure AD]。

2.   在 [來源物件範圍] 欄位中，您可以透過定義一組屬性型篩選，選取應該佈建至 Azure AD 的 Workday 使用者集合範圍。 預設範圍是「Workday 中的所有使用者」。 範例篩選：

   * 範例：人員識別碼介於 1000000 到 2000000 之間的使用者範圍

      * 屬性：WorkerID

      * 運算子：REGEX Match

      * 值：(1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 範例：僅約聘人員和非正式員工

      * 屬性：ContingentID

      * 運算子：IS NOT NULL

3.  在 [目標物件動作] 欄位中，您可以全域篩選允許在 Azure AD 上執行哪些動作。 最常見的動作是 [建立] 和 [更新]。

4.  在 [屬性對應] 區段中，您可以定義個別 Workday 屬性如何對應至 Active Directory 屬性。

5. 按一下現有的屬性對應以進行更新，或按一下畫面底端的 [新增新對應] 以新增新對應。 個別屬性對應支援下列屬性：

   * **對應類型**

      * **直接** – 將 Workday 屬性的值寫入 AD 屬性，且不進行變更

      * **常數** - 將靜態的常數字串值寫入 AD 屬性

      * **運算式** – 可讓您根據一或多個 Workday 屬性，將自訂值寫入 AD 屬性。 [如需詳細資訊，請參閱這篇有關運算式的文章](active-directory-saas-writing-expressions-for-attribute-mappings.md)。

   * **來源屬性** - 來自 Workday 的使用者屬性。

   * **預設值** – 選用。 如果來源屬性具有空值，則對應將會改為寫入此值。
            最常見的設定是將其保留空白。

   * **目標屬性** – Azure AD 中的使用者屬性。

   * **使用此屬性比對物件** – 是否應該將此對應用於唯一識別 Workday 與 Azure AD 之間的使用者。 此設定通常會設定於 Workday 的 [人員識別碼] 欄位，且通常會在 Azure AD 中對應至員工識別碼屬性 (新) 或擴充屬性。

   * **比對優先順序** – 您可以設定多個比對屬性。 具有多個屬性時，系統會以此欄位定義的順序進行評估。 只要找到相符項目，便不會評估進一步比對屬性。

   * **套用此對應**

     * **一律** – 將此對應套用於使用者建立和更新動作

     * **僅限建立期間** - 僅將此對應套用於使用者建立動作

6. 若要儲存您的對應，請按一下 [屬性對應] 區段頂端的 [儲存]。

### <a name="part-3-start-the-service"></a>第 3 部分：啟動服務
完成第 1-2 部分之後，您可以啟動佈建服務。

1.  在 [佈建] 索引標籤中，將 [佈建狀態] 設定為 [開啟]。

2. 按一下 [儲存] 。

3. 這會啟動初始同步，取決於 Workday 中的使用者人數，其可能要花費數小時。

4. 您可以在 [稽核記錄] 索引標籤中檢視個別同步事件。**[請參閱佈建報告指南，瞭解有關如何讀取稽核記錄的詳細指示](active-directory-saas-provisioning-reporting.md)**

5. 完成之後，其會寫入 [佈建] 索引標籤中的稽核摘要報告內，如下所示。


## <a name="configuring-writeback-of-email-addresses-to-workday"></a>設定將電子郵件地址回寫至 Workday
請遵循下列指示來設定將使用者電子郵件地址從 Azure Active Directory 回寫至 Workday。

### <a name="part-1-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>第 1 部分：新增佈建連接器應用程式和建立 Workday 連接

**若要設定 Workday 至 Active Directory 佈建：**

1.  移至 <https://portal.azure.com>

2.  在左側導覽列中，選取 [Azure Active Directory]

3.  依序選取 [企業應用程式] 和 [所有應用程式]。

4.  選取 [新增應用程式]，然後選取 [全部] 類別。

5.  搜尋 **Workday Writeback**，並從資源庫新增該應用程式。

6.  新增應用程式並顯示應用程式詳細資料畫面之後，請選取 [佈建]

7.  將 [佈建模式] 變更為 [自動]

8.  完成 [系統管理員認證] 區段，如下所示：

   * **系統管理員使用者名稱** – 輸入 Workday 整合系統帳戶的使用者名稱，並附加租用戶網域名稱。 其應該類似於：username@contoso4

   * **系統管理員密碼 –** 輸入 Workday 整合系統帳戶的密碼

   * **租用戶 URL –** 輸入租用戶 Workday Web 服務端點的 URL。 其應該類似於：https://wd3-impl-services1.workday.com/ccx/service/contoso4，其中請將 contoso4 取代為正確的租用戶名稱，並將 wd3-impl 取代為正確的環境字串 (如有必要)。

   * **電子郵件通知 –** 輸入您的電子郵件地址，然後勾選 [發生失敗時傳送電子郵件] 核取方塊。

   * 按一下 [測試連線] 按鈕。 如果連線測試成功，請按一下頂端的 [儲存] 按鈕。 如果失敗，請仔細檢查 Workday URL 和認證在 Workday 中是否有效。


### <a name="part-2-configure-attribute-mappings"></a>第 2 部分：設定屬性對應 


在本節中，您會設定使用者資料從 Workday 流動至 Active Directory 的方式。

1.  在 [佈建] 索引標籤的 [對應] 下，按一下 [將 Azure AD 使用者同步至 Workday]。

2.  在 [來源物件範圍] 欄位中，您可以選擇性地篩選應該將電子郵件地址寫回至 Workday 的 Azure Active Directory 使用者集合。 預設範圍是「Azure AD 中的所有使用者」。 

3.  在 [屬性對應] 區段中，您可以定義個別 Workday 屬性如何對應至 Active Directory 屬性。 根據預設，存在電子郵件地址的對應。 不過，您必須更新比對識別碼以在 Azure AD 中比對使用者及其在 Workday 中的對應項目。 常用的比對方法是將 Workday 人員識別碼或員工識別碼同步至 Azure AD 中的 extensionAttribute1-15，然後在 Azure AD 中使用此屬性再次比對 Workday 的使用者。

4.  若要儲存您的對應，請按一下 [屬性對應] 區段頂端的 [儲存]。

### <a name="part-3-start-the-service"></a>第 3 部分：啟動服務
完成第 1-2 部分之後，您可以啟動佈建服務。

1.  在 [佈建] 索引標籤中，將 [佈建狀態] 設定為 [開啟]。

2. 按一下 [儲存] 。

3. 這會啟動初始同步，取決於 Workday 中的使用者人數，其可能要花費數小時。

4. 您可以在 [稽核記錄] 索引標籤中檢視個別同步事件。**[請參閱佈建報告指南，瞭解有關如何讀取稽核記錄的詳細指示](active-directory-saas-provisioning-reporting.md)**

5. 完成之後，其會寫入 [佈建] 索引標籤中的稽核摘要報告內，如下所示。

## <a name="known-issues"></a>已知問題

* 執行 **Add-ADSyncAgentAzureActiveDirectoryConfiguration** Powershell 命令時，目前有一項已知問題：若使用自訂網域，則全域管理員認證無法運作 (範例：admin@contoso.com)。 因應措施是在 Azure AD 以 onmicrosoft.com 網域來建立和使用全域管理員帳戶 (範例：admin@contoso.onmicrosoft.com)。

* 已解決之前位於歐盟的 Azure AD 租用戶上並未出現稽核記錄的問題。 不過，位於歐盟的 Azure AD 租用戶需要進行其他代理程式設定。 欲知詳情，請參閱[第 3 部分：設定內部部署同步代理程式](#Part 3: Configure the on-premises synchronization agent)

## <a name="additional-resources"></a>其他資源
* [教學課程：設定 Workday 與 Azure Active Directory 之間的單一登入](active-directory-saas-workday-tutorial.md)
* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>後續步驟

* [瞭解如何針對佈建活動檢閱記錄和取得報告](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting)
