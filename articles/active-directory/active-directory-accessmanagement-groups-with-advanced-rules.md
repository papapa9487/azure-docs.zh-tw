---
title: "根據 Azure Active Directory 中的物件屬性以動態方式填入群組 | Microsoft Docs"
description: "說明如何建立群組成員資格的進階規則，包括支援的運算式規則運算子和參數。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 04813a42-d40a-48d6-ae96-15b7e5025884
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: oldportal
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: ae2a2e477137bc117111b147e1f088d528a55de5
ms.contentlocale: zh-tw
ms.lasthandoff: 08/30/2017

---

# <a name="populate-groups-dynamically-based-on-object-attributes"></a>根據物件屬性以動態方式填入群組
Azure 傳統入口網站可讓您對 Azure Active Directory (Azure AD) 群組啟用更複雜的屬性型動態成員資格。  

當使用者或裝置的任何屬性變更時，系統會評估目錄中的所有動態群組規則，以查看變更是否會觸發任何的群組新增或移除。 如果使用者或裝置滿足群組上的規則，就會將他們新增為該群組的成員。 如果他們不再符合此規則，則會予以移除。

> [!NOTE]
> - 您可以為安全性群組或 Office 365 群組的動態成員資格設定規則。
>
> - 此功能要求新增到至少一個動態群組的每個使用者成員都具有 Azure AD Premium P1 授權。
>
> - 您可以為裝置或使用者建立動態群組，但無法建立同時包含使用者和裝置物件的規則。

> - 目前無法依據擁有使用者的屬性建立裝置群組。 裝置成員資格規則只能參考目錄中裝置物件的直接屬性。

## <a name="to-create-an-advanced-rule"></a>建立進階規則
1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com)中，選取 [Active Directory]，然後開啟您組織的目錄。
2. 選取 [群組]  索引標籤，然後開啟您想要編輯的群組。
3. 選取 [設定] 索引標籤，選取 [進階的規則] 選項，然後在文字方塊中輸入進階規則。

## <a name="constructing-the-body-of-an-advanced-rule"></a>建構進階規則的主體
您可以為群組的動態成員資格建立的進階規則基本上是一個二進位運算式，其中包含三個部分，且會產生 true 或 false 的結果。 這三個部分包括：

* 左側的參數
* 二進位運算子
* 右側的常數

完整的進階規則外觀如下：(leftParameter binaryOperator "RightConstant")，其中需要左右括號括住整個二進位運算式、需要雙引號括住右側的常數，且左側參數的語法是 user.property。 進階規則可以包含多個二進位運算式，並以 -and、 -or 和 -not 邏輯運算子分隔。
以下是正確建構的進階規則的範例：

* (user.department -eq "Sales") -or (user.department -eq "Marketing")
* (user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")

如需支援的參數和運算式規則運算子的完整清單，請參閱下列各節。


請注意，屬性前面必須加上正確的物件類型︰使用者或裝置。
以下規則會驗證失敗︰mail –ne null

正確的規則會是︰

user.mail –ne null

進階規則主體的總長度不得超過 2048 個字元。

> [!NOTE]
> 字串和 regex 運算都不區分大小寫。
> 包含引號 " 的字串應該使用 ' 字元逸出，例如 user.department -eq \`"Sales"。
> 只能將引號用於字串類型值，而且只能使用英文引號。
>
>

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

所有運算子會依照優先順序 (從低至高) 列在底下，同一行中的運算子具有相等的優先順序 -any -all -or -and -not -eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn

不管有沒有連字號前置詞，均可使用所有運算子。

請注意，不一定需要括號，您只需要在優先順序不符合您的需求時加上括號，例如︰

   user.department –eq "Marketing" –and user.country –eq "US"

相當於：

   (user.department –eq "Marketing") –and (user.country –eq "US")

## <a name="using-the--in-and--notin-operators"></a>使用 -In 和 -notIn 運算子

如果您想要針對許多不同的值比較使用者屬性的值，您可以使用 -In 或 -notIn 運算子。 使用 -In 運算子的範例如下︰

    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]

請注意值清單的開頭和結尾有使用 "[" 和 "]"。 當 user.department 的值等於清單中的其中一個值時，這個條件會評估為 True。

## <a name="query-error-remediation"></a>查詢錯誤補救
下表列出可能的錯誤以及其更正方式

| 查詢剖析錯誤 | 錯誤的使用方式 | 更正的使用方式 |
| --- | --- | --- |
| 錯誤：不支援屬性。 |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/>屬性應該符合 [支援的屬性清單](#supported-properties)。 |
| 錯誤：屬性不支援運算子。 |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/>屬性屬於布林類型。 使用上述清單中的布林型別支援的運算子 (-eq 或-ne)。 |
| 錯誤：查詢編譯錯誤。 |(user.department -eq "Sales") -and (user.department -eq "Marketing")(user.userPrincipalName -match "*@domain.ext") |(user.department -eq "Sales") -and (user.department -eq "Marketing")<br/>邏輯運算子應該符合上述支援的屬性清單中的一個屬性。(user.userPrincipalName -match ".*@domain.ext") 或 (user.userPrincipalName -match "@domain.ext$") 規則運算式發生錯誤。 |
| 錯誤：二進位運算式不是正確的格式。 |(user.department –eq “Sales”) (user.department -eq "Sales")(user.department-eq"Sales") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>查詢有多個錯誤。 括號不在正確的位置。 |
| 錯誤：設定動態成員資格時發生未知的錯誤。 |(user.accountEnabled -eq "True" AND user.userPrincipalName -contains "alias@domain") |(user.accountEnabled -eq true) -and (user.userPrincipalName -contains "alias@domain")<br/>查詢有多個錯誤。 括號不在正確的位置。 |

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
| city |任何字串值或 $null |(user.city -eq "value") |
| country |任何字串值或 $null |(user.country -eq "value") |
| companyName | 任何字串值或 $null | (user.companyName -eq "value") |
| department |任何字串值或 $null |(user.department -eq "value") |
| displayName |任何字串值 |(user.displayName -eq "value") |
| facsimileTelephoneNumber |任何字串值或 $null |(user.facsimileTelephoneNumber -eq "value") |
| givenName |任何字串值或 $null |(user.givenName -eq "value") |
| jobTitle |任何字串值或 $null |(user.jobTitle -eq "value") |
| mail |任何字串值或 $null (使用者的 SMTP 位址) |(user.mail -eq "value") |
| mailNickName |任何字串值 (使用者的郵件別名) |(user.mailNickName -eq "value") |
| mobile |任何字串值或 $null |(user.mobile -eq "value") |
| objectId |使用者物件的 GUID |(user.objectId -eq "1111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | 已從內部部署環境同步至雲端之使用者的內部部署安全性識別碼 (SID)。 |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |任何字串值或 $null |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |任何字串值或 $null |(user.postalCode -eq "value") |
| preferredLanguage |ISO 639-1 code |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |任何字串值或 $null |(user.sipProxyAddress -eq "value") |
| state |任何字串值或 $null |(user.state -eq "value") |
| streetAddress |任何字串值或 $null |(user.streetAddress -eq "value") |
| surname |任何字串值或 $null |(user.surname -eq "value") |
| telephoneNumber |任何字串值或 $null |(user.telephoneNumber -eq "value") |
| usageLocation |兩個字母的國家 (地區) 代碼 |(user.usageLocation -eq "US") |
| userPrincipalName |任何字串值 |(user.userPrincipalName -eq "alias@domain") |
| userType |member guest $null |(user.userType -eq "Member") |

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

若要在規則中指定 null 值，您可以使用 "null" 或 $null。 範例：

   user.mail –ne null is equivalent to user.mail –ne $null

## <a name="extension-attributes-and-custom-attributes"></a>擴充屬性和自訂屬性
動態成員資格規則支援擴充屬性和自訂屬性。

擴充屬性會從內部部署 Windows Server AD 進行同步處理，並採用 "ExtensionAttributeX" 格式，其中 X 等於 1-15。
以下是使用擴充屬性的規則範例：

(user.extensionAttribute15 -eq "Marketing")

自訂屬性會從內部部署 Windows Server AD 或從連線的 SaaS 應用程式進行同步處理，並採用 "user.extension[GUID]\__[Attribute]" 格式，其中 [GUID] 是 AAD 中的唯一識別碼 (適用於在 AAD 中建立屬性的應用程式)，而 [Attribute] 是其建立的屬性名稱。
以下是使用自訂屬性的規則範例：

user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

使用 [Graph 總管] 查詢使用者的屬性並搜尋屬性名稱，即可在目錄中找到自訂屬性名稱。 目前我們不支援從內部部署 Active Directory 同步處理的多重值屬性。 

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
您也可以建立規則以在群組中選取成員資格的裝置物件。 可以使用下列裝置屬性︰

| 屬性              | 允許的值                  | 使用量                                                       |
|-------------------------|---------------------------------|-------------------------------------------------------------|
| accountEnabled          | true false                      | (device.accountEnabled -eq true)                            |
| displayName             | 任何字串值                | (device.displayName -eq "Rob Iphone”)                       |
| deviceOSType            | 任何字串值                | (device.deviceOSType -eq "IOS")                             |
| deviceOSVersion         | 任何字串值                | (device.OSVersion -eq "9.1")                                |
| deviceCategory          | 任何字串值                | (device.deviceCategory -eq "")                              |
| deviceManufacturer      | 任何字串值                | (device.deviceManufacturer -eq "Microsoft")                 |
| deviceModel             | 任何字串值                | (device.deviceModel -eq "IPhone 7+")                        |
| deviceOwnership         | 任何字串值                | (device.deviceOwnership -eq "")                             |
| domainName              | 任何字串值                | (device.domainName -eq "contoso.com")                       |
| enrollmentProfileName   | 任何字串值                | (device.enrollmentProfileName -eq "")                       |
| isRooted                | true false                      | (device.deviceOSType -eq true)                              |
| managementType          | 任何字串值                | (device.managementType -eq "")                              |
| organizationalUnit      | 任何字串值                | (device.organizationalUnit -eq "")                          |
| deviceId                | 有效的 deviceId                | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d") |
| objectId                | 有效的 AAD objectId            | (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d") |

> [!NOTE]
> 無法在 Azure 傳統入口網站中使用 [簡單規則] 下拉式清單建立這些裝置規則。
>
>

## <a name="next-steps"></a>後續步驟
這些文章提供有關 Azure Active Directory 的其他資訊。

* [疑難排解群組的動態成員資格](active-directory-accessmanagement-troubleshooting.md)
* [使用 Azure Active Directory 群組管理資源的存取權](active-directory-manage-groups.md)
* [設定群組設定的 Azure Active Directory Cmdlet](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

