---
title: "Azure Active Directory 中以屬性為基礎的動態群組成員資格 | Microsoft Docs"
description: "如何建立動態群組成員資格的進階規則，包括支援的運算式規則運算子和參數。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: fb434cc2-9a91-4ebf-9753-dd81e289787e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.openlocfilehash: b8aa841cca63c0c4eb45105e3ccff91920ad35e3
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory"></a>在 Azure Active Directory 中針對動態群組成員資格建立以屬性為基礎的規則
在 Azure Active Directory (Azure AD) 中，您可以建立進階規則，以對群組啟用更複雜的屬性型動態成員資格。 本文將詳細說明用以建立適用於使用者或裝置之動態成員資格規則的屬性和語法。

當使用者或裝置的任何屬性變更時，系統會評估目錄中的所有動態群組規則，以查看變更是否會觸發任何的群組新增或移除。 如果使用者或裝置滿足群組上的規則，就會將他們新增為該群組的成員。 如果他們不再符合此規則，則會予以移除。

> [!NOTE]
> - 您可以為安全性群組或 Office 365 群組的動態成員資格設定規則。
>
> - 此功能要求新增到至少一個動態群組的每個使用者成員都具有 Azure AD Premium P1 授權。 不一定要實際將授權指派給使用者，讓他們成為動態群組的成員，但是您必須在租用戶中有最小數量的授權，以涵蓋所有這類使用者。 例如：如果您的租用戶中所有動態群組有總計 1000 個唯一使用者，則必須至少具有 Azure AD Premium P1 或以上版本的 1000 個授權，以符合授權需求。
>
> - 您可以為裝置或使用者建立動態群組，但無法建立同時包含使用者和裝置物件的規則。

> - 目前無法依據擁有使用者的屬性建立裝置群組。 裝置成員資格規則只能參考目錄中裝置物件的直接屬性。

## <a name="to-create-an-advanced-rule"></a>建立進階規則
1. 使用具備全域管理員或使用者帳戶管理員身分的帳戶來登入 [Azure AD 系統管理中心](https://aad.portal.azure.com)。
2. 選取 [使用者和群組]。
3. 選取 [所有群組]，然後選取 [新增群組]。

   ![Add new group](./media/active-directory-groups-dynamic-membership-azure-portal/new-group-creation.png)

4. 在 [群組]  刀鋒視窗上，輸入新群組的名稱和描述。 依據您是要為使用者還是裝置建立規則，在 [成員資格類型] 選取 [動態使用者] 或 [動態裝置]，然後選取 [新增動態查詢]。 您可以使用規則建立器來建立簡單的規則，或自行撰寫進階的規則。 這篇文章包含可用的使用者和裝置屬性的詳細資訊，以及進階規則的範例。

   ![新增動態成員資格規則](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)

5. 建立規則之後，在刀鋒視窗的底部選取 [新增查詢]。
6. 選取 [更多服務]  on the  來建立群組。

> [!TIP]
> 如果您輸入的進階規則不正確，建立群組可能會失敗。 入口網站右上角將會顯示通知，當中包含系統為什麼無法接受規則的解釋。 請仔細閱讀，了解您需要如何調整規則而讓規則有效。

## <a name="constructing-the-body-of-an-advanced-rule"></a>建構進階規則的主體
您可以為群組的動態成員資格建立的進階規則基本上是一個二進位運算式，其中包含三個部分，且會產生 true 或 false 的結果。 這三個部分包括：

* 左側的參數
* 二進位運算子
* 右側的常數

完整的進階規則外觀如下：(leftParameter binaryOperator "RightConstant")，其中可選擇性地使用左右括號來括住整個二進位運算式，也可以選擇性地使用雙引號、只有在其為字串時需要括住右側常數，且左側參數的語法是 user.property。 進階規則可以包含多個二進位運算式，並以 -and、 -or 和 -not 邏輯運算子分隔。

以下是正確建構的進階規則的範例：
```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```
如需支援的參數和運算式規則運算子的完整清單，請參閱下列各節。 如需了解有哪些用於裝置規則的屬性，請參閱 [使用屬性來建立裝置物件的規則](#using-attributes-to-create-rules-for-device-objects)。

進階規則主體的總長度不得超過 2048 個字元。

> [!NOTE]
> 字串和 regex 運算都不區分大小寫。 您也可以使用 *null* 作為常數來執行 Null 檢查，例如 user.department -eq *null*。
> 包含引號 " 的字串應該使用 ' 字元逸出，例如 user.department -eq \`"Sales"。

## <a name="supported-expression-rule-operators"></a>支援的運算式規則運算子
下表列出所有支援的運算式規則運算子及其用於進階規則主體中的語法：

| 運算子 | 語法 |
| --- | --- |
| Not Equals |-ne |
| Equals |-eq |
| Not Starts With |-notStartsWith |
| 開頭為 |-startsWith |
| Not Contains |-notContains |
| Contains |-contains |
| Not Match |-notMatch |
| Match |-match |
| 在 | -in |
| 不在 | -notIn |

## <a name="operator-precedence"></a>運算子優先順序

所有運算子都會列在下面 (由高至低排定優先順序)。 同一行上運算子的優先順序相等：
````
-any -all
-or
-and
-not
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
````
不管有沒有連字號前置詞，均可使用所有運算子。 優先順序不符合您的需求時，才需要括號。
例如：
```
   user.department –eq "Marketing" –and user.country –eq "US"
```
相當於：
```
   (user.department –eq "Marketing") –and (user.country –eq "US")
```
## <a name="using-the--in-and--notin-operators"></a>使用 -In 和 -notIn 運算子

如果您想要針對許多不同的值比較使用者屬性的值，您可以使用 -In 或 -notIn 運算子。 使用 -In 運算子的範例如下︰
```
    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]
```
請注意值清單的開頭和結尾有使用 "[" 和 "]"。 當 user.department 的值等於清單中的其中一個值時，這個條件會評估為 True。


## <a name="query-error-remediation"></a>查詢錯誤補救
下表列出一般的錯誤及其更正方式

| 查詢剖析錯誤 | 錯誤的使用方式 | 更正的使用方式 |
| --- | --- | --- |
| 錯誤：不支援屬性。 |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>請確定此屬性位於[支援的屬性清單](#supported-properties)中。 |
| 錯誤：屬性不支援運算子。 |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>屬性類型不支援使用的運算子 (在此範例中，-contains 不能在布林類型上使用)。 屬為屬性類型使用正確的運算子。 |
| 錯誤：查詢編譯錯誤。 |1. (user.department -eq "Sales") (user.department -eq "Marketing")<br/><br/>2. (user.userPrincipalName -match "*@domain.ext") |1.缺少運算子。 使用這兩個 -and 或 -or 聯結述詞<br/><br/>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br/><br/>2. 使用 -match 的規則運算式發生錯誤<br/><br/>(user.userPrincipalName -match ".*@domain.ext")，此外：(user.userPrincipalName -match "@domain.ext$")|

## <a name="supported-properties"></a>支援的屬性
以下是您可以在進階規則中使用的所有使用者屬性：

### <a name="properties-of-type-boolean"></a>布林型別的屬性
允許的運算子

* -eq
* -ne

| 屬性 | 允許的值 | 使用量 |
| --- | --- | --- |
| accountEnabled |true false |user.accountEnabled -eq true |
| dirSyncEnabled |true false |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>字串類型的屬性
允許的運算子

* -eq
* -ne
* -notStartsWith
* -startsWith
* -contains
* -notContains
* -match
* -notMatch
* -in
* -notIn

| 屬性 | 允許的值 | 使用量 |
| --- | --- | --- |
| city |任何字串值或 *null* |(user.city -eq "value") |
| country |任何字串值或 *null* |(user.country -eq "value") |
| companyName | 任何字串值或 *null* | (user.companyName -eq "value") |
| department |任何字串值或 *null* |(user.department -eq "value") |
| displayName |任何字串值 |(user.displayName -eq "value") |
| employeeId |任何字串值 |(user.employeeId -eq "value")<br>(user.employeeId -ne *null*) |
| facsimileTelephoneNumber |任何字串值或 *null* |(user.facsimileTelephoneNumber -eq "value") |
| givenName |任何字串值或 *null* |(user.givenName -eq "value") |
| jobTitle |任何字串值或 *null* |(user.jobTitle -eq "value") |
| mail |任何字串值或 *null* (使用者的 SMTP 位址) |(user.mail -eq "value") |
| mailNickName |任何字串值 (使用者的郵件別名) |(user.mailNickName -eq "value") |
| mobile |任何字串值或 *null* |(user.mobile -eq "value") |
| objectId |使用者物件的 GUID |(user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | 已從內部部署環境同步至雲端之使用者的內部部署安全性識別碼 (SID)。 |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |任何字串值或 *null* |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |任何字串值或 *null* |(user.postalCode -eq "value") |
| preferredLanguage |ISO 639-1 code |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |任何字串值或 *null* |(user.sipProxyAddress -eq "value") |
| state |任何字串值或 *null* |(user.state -eq "value") |
| streetAddress |任何字串值或 *null* |(user.streetAddress -eq "value") |
| surname |任何字串值或 *null* |(user.surname -eq "value") |
| telephoneNumber |任何字串值或 *null* |(user.telephoneNumber -eq "value") |
| usageLocation |兩個字母的國家 (地區) 代碼 |(user.usageLocation -eq "US") |
| userPrincipalName |任何字串值 |(user.userPrincipalName -eq "alias@domain") |
| userType |member guest *null* |(user.userType -eq "Member") |

### <a name="properties-of-type-string-collection"></a>字串集合類型的屬性
允許的運算子

* -contains
* -notContains

| 屬性 | 允許的值 | 使用量 |
| --- | --- | --- |
| otherMails |任何字串值 |(user.otherMails -contains "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>多重值屬性
允許的運算子

* -any (集合中至少有一個項目符合條件時滿足)
* -all (集合中的所有項目符合條件時滿足)

| 屬性 | 值 | 使用量 |
| --- | --- | --- |
| assignedPlans |集合中的每個物件都會公開下列字串屬性：capabilityStatus、service、servicePlanId |user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |

多重值屬性是相同類型之物件的集合。 您可以使用 -any 和 -all 運算子，分別將條件套用至集合中的一個或所有項目。 例如：

assignedPlans 是多重值屬性，可列出所有指派給使用者的服務方案。 下列運算式將會選取擁有 Exchange Online (方案 2) 服務方案 (也處於 [已啟用] 狀態) 的使用者：

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(GUID 識別碼可識別 Exchange Online (方案 2) 服務方案。)

> [!NOTE]
> 如果您想要識別已啟用 Office 365 (或其他 Microsoft Online 服務) 功能的所有使用者，例如以一組特定原則鎖定他們，此屬性非常有用。

下列運算式將會選取有任何服務方案與 Intune 服務 (透過服務名稱 "SCO" 來識別) 相關聯的所有使用者：
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

## <a name="use-of-null-values"></a>使用 Null 值

若要在規則中指定 null 值，您可以使用 *null* 值。 注意，請不要在 *null* 文字前後使用引號，這麼做會使系統將它解釋為常值字串值。 參考 null 值的正確方法如下：
```
   user.mail –ne null
```

## <a name="extension-attributes-and-custom-attributes"></a>擴充屬性和自訂屬性
動態成員資格規則支援擴充屬性和自訂屬性。

擴充屬性會從內部部署 Windows Server AD 進行同步處理，並採用 "ExtensionAttributeX" 格式，其中 X 等於 1-15。
以下是使用擴充屬性的規則範例：
```
(user.extensionAttribute15 -eq "Marketing")
```
自訂屬性會從內部部署 Windows Server AD 或從連線的 SaaS 應用程式進行同步處理，並採用 "user.extension[GUID]\__[Attribute]" 格式，其中 [GUID] 是 AAD 中的唯一識別碼 (適用於在 AAD 中建立屬性的應用程式)，而 [Attribute] 是其建立的屬性名稱。
以下是使用自訂屬性的規則範例：
```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  
```
使用 [Graph 總管] 查詢使用者的屬性並搜尋屬性名稱，即可在目錄中找到自訂屬性名稱。

## <a name="direct-reports-rule"></a>「直屬員工」規則
您可以建立一個群組，其中包含某位經理的所有直屬員工。 當經理的直屬員工在未來變更時，系統將會自動調整群組的成員資格。

> [!NOTE]
> 1. 若要讓規則得以運作，請確定已對您租用戶中的使用者正確設定 [經理識別碼] 屬性。 您可以在其 **[設定檔] 索引標籤**上檢查使用者的目前值。
> 2. 此規則只支援**直屬**員工。 目前無法建立巢狀階層的群組，例如包含直屬員工及其員工的群組。

**設定群組**

1. 依照[建立進階規則](#to-create-the-advanced-rule)一節中的步驟 1-5 操作，然後在 [成員資格類型] 中選取 [動態使用者]。
2. 在 [動態成員資格規則]  刀鋒視窗上，使用下列語法來輸入規則：

    *Direct Reports for "{obectID_of_manager}"*

    有效規則的範例：
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. 儲存規則之後，即會將具有指定經理識別碼值的所有使用者新增至群組。

## <a name="using-attributes-to-create-rules-for-device-objects"></a>使用屬性來建立裝置物件的規則
您也可以建立規則以在群組中選取成員資格的裝置物件。 可以使用下列裝置屬性。

 裝置屬性  | 值 | 範例
 ----- | ----- | ----------------
 accountEnabled | true false | (device.accountEnabled -eq true)
 displayName | 任何字串值 |(device.displayName -eq "Rob Iphone”)
 deviceOSType | 任何字串值 | (device.deviceOSType -eq "iPad") -or (device.deviceOSType -eq "iPhone")
 deviceOSVersion | 任何字串值 | (device.OSVersion -eq "9.1")
 deviceCategory | 有效的裝置類別名稱 | (device.deviceCategory -eq "BYOD")
 deviceManufacturer | 任何字串值 | (device.deviceManufacturer -eq "Samsung")
 deviceModel | 任何字串值 | (device.deviceModel -eq "iPad Air")
 deviceOwnership | 個人、公司、未知 | (device.deviceOwnership -eq "Company")
 domainName | 任何字串值 | (device.domainName -eq "contoso.com")
 enrollmentProfileName | Apple 裝置註冊設定檔名稱 | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | true false | (device.isRooted -eq true)
 managementType | MDM (適用於行動裝置)<br>PC (適用於 Intune PC 代理程式管理的電腦) | (device.managementType -eq "MDM")
 organizationalUnit | 符合內部部署 Active Directory 所設定的組織單位名稱的任何字串值 | (device.organizationalUnit -eq "US PCs")
 deviceId | 有效的 Azure AD 裝置識別碼 | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | 有效的 Azure AD 物件識別碼 |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")



## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>將動態成員資格變更為靜態，反之亦然
您可以變更在群組中管理成員資格的方式。 當您想要在系統中保留相同的群組名稱和識別碼，讓任何現有的群組參考仍然有效時，這非常實用；建立新的群組需要更新這些參考。

我們正在更新 Azure 入口網站以支援此功能。 同時，您可以使用 [Azure 傳統入口網站](https://manage.windowsazure.com) (依照[這裡](active-directory-groups-dynamic-membership-azure-portal.md)的指示) 或使用 PowerShell Cmdlet，如下所示。

> [!WARNING]
> 將現有的靜態群組變更為動態群組時，系統將從群組中移除所有現有的成員，然後再處理成員資格規則，以加入新的成員。 如果群組用來控制對應用程式或資源的存取，則在完全處理成員資格規則之前，原始成員可能會失去存取權。
>
> 建議的作法是，事先測試新的成員資格規則，以確保群組中的新成員資格如預期般運作。

**使用 PowerShell 變更群組上的成員資格管理**

> [!NOTE]
> 若要變更動態群組屬性，您必須使用**預覽版本** [Azure AD PowerShell 第 2 版](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)中的 Cmdlet。 您可以從[這裡](https://www.powershellgallery.com/packages/AzureADPreview)安裝預覽版本。

以下是在現有群組上切換成員資格管理的函式範例。 請注意，請小心並正確地操作 GroupTypes 屬性，並保留可能存在該處，與動態成員資格不相關的任何值。

```
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
若要讓群組變成靜態的：
```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```
若要讓群組變成動態的：
```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```
## <a name="next-steps"></a>後續步驟
這些文章提供有關 Azure Active Directory 中群組的其他資訊。

* [查看現有的群組](active-directory-groups-view-azure-portal.md)
* [建立新群組並新增成員](active-directory-groups-create-azure-portal.md)
* [管理群組的設定](active-directory-groups-settings-azure-portal.md)
* [管理群組的成員資格](active-directory-groups-membership-azure-portal.md)
* [管理群組中使用者的動態規則](active-directory-groups-dynamic-membership-azure-portal.md)
