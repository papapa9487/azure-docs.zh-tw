---
title: "租用戶管理員提高存取權限 - Azure AD | Microsoft Docs"
description: "本主題說明角色型存取控制 (RBAC) 的內建角色。"
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
editor: rqureshi
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2017
ms.author: andredm
ms.openlocfilehash: c1f49e2c7836a56f37aafcaad0cb74278213a720
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>使用角色型存取控制以租用戶系統管理員提高存取權限

角色型存取控制可協助租用戶系統管理員取得存取權限暫時提升，以便他們可以比平常授與更高的權限。 租用戶管理員可以視需要將自己提升至使用者存取系統管理員角色。 該角色提供租用戶系統管理員權限，可在 "/" 的範圍內授與自己或其他角色。

這項功能很重要，因為它可讓租用戶管理員查看組織中的所有訂用帳戶。 它也可讓自動化應用程式 (例如發票開立與稽核) 存取所有的訂用帳戶，並提供精確的組織狀態檢視來進行帳單或資產管理。  

## <a name="use-elevateaccess-for-tenant-access-with-azure-ad-admin-center"></a>透過 Azure AD 系統管理中心使用 elevateAccess 來提供租用戶存取權

1. 移至 [Azure Active Directory 系統管理中心](https://aad.portal.azure.com)，然後使用您的認證來進行登入。

2. 從 Azure AD 左側功能表中選擇 [屬性]。

3. 在 [屬性] 刀鋒視窗中，尋找 [全域管理員可管理 Azure 訂用帳戶]，選擇 [是]，然後選擇 [儲存]。
    > [!IMPORTANT] 
    > 選擇 [是] 時，系統會針對您目前用來登入入口網站的使用者，在根 "/" (根範圍) 指派 [使用者存取系統管理員] 角色。 **這可讓該使用者看到所有其他 Azure 訂用帳戶。**
    
    > [!NOTE] 
    > 選擇 [否] 時，系統會針對您目前用來登入入口網站的使用者，在根 "/" (根範圍) 移除 [使用者存取系統管理員] 角色。

> [!TIP] 
> 在印象中，這是 Azure Active Directory 的全域屬性，但卻是依個別使用者而定，只對目前登入的使用者才有作用。 當您在 Azure Active Directory 中具有全域管理員權限時，對於您目前用來登入 Azure Active Directory 系統管理中心的使用者，您可以叫用 elevateAccess 功能。

![Azure AD 系統管理中心 - 屬性 - Globaladmin 可以管理 Azure 訂用帳戶 - 螢幕擷取畫面](./media/role-based-access-control-tenant-admin-access/aad-azure-portal-global-admin-can-manage-azure-subscriptions.png)

## <a name="view-role-assignments-at-the--scope-using-powershell"></a>使用 PowerShell 檢視 "/" 範圍的角色指派
若要檢視 **/** 範圍的**使用者存取管理員**指派，請使用 `Get-AzureRmRoleAssignment` PowerShell Cmdlet。
    
```
Get-AzureRmRoleAssignment* | where {$_.RoleDefinitionName -eq "User Access Administrator" -and $_SignInName -eq "<username@somedomain.com>" -and $_.Scope -eq "/"}
```

**範例輸出**：

RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0    
Scope              : /    
DisplayName        : username    
SignInName         : username@somedomain.com    
RoleDefinitionName : User Access Administrator    
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9    
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc    
ObjectType         : User    

## <a name="delete-the-role-assignment-at--scope-using-powershell"></a>使用 PowerShell 刪除 "/" 範圍的角色指派：
您可以使用下列 PowerShell Cmdlet 刪除指派：
```
Remove-AzureRmRoleAssignment -SignInName <username@somedomain.com> -RoleDefinitionName "User Access Administrator" -Scope "/" 
```

## <a name="use-elevateaccess-to-give-tenant-access-with-the-rest-api"></a>透過 REST API 使用 elevateAccess 來提供租用戶存取權

基本程序適用於下列步驟︰

1. 使用 REST，呼叫 elevateAccess，這會授與您在 "/" 的範圍的使用者存取系統管理員角色。

    ```
    POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
    ```

2. 建立[角色指派](/rest/api/authorization/roleassignments)以在任何範圍的指派任何角色。 下列範例顯示在 "/" 的範圍指派讀取者角色的屬性︰

    ```
    { "properties":{
    "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/acdd72a7338548efbd42f606fba81ae7",
    "principalId": "cbc5e050-d7cd-4310-813b-4870be8ef5bb",
    "scope": "/"
    },
    "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
    }
    ```

3. 在使用者存取的系統管理員時，您也可以在 "/" 的範圍刪除角色指派。

4. 撤銷您的使用者存取系統管理員權限，直到再次需要這些權限。


## <a name="how-to-undo-the-elevateaccess-action-with-the-rest-api"></a>如何使用 REST API 復原 elevateAccess 動作

當您呼叫 elevateAccess 時，您建立自己的角色指派，因此您需要刪除作業才能撤銷這些權限。

1.  呼叫 GET role definitions (其中 roleName = User Access Administrator)，以判斷「使用者存取系統管理員」角色的名稱 GUID。
    1.  GET *https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User+Access+Administrator*

        ```
        {"value":[{"properties":{
        "roleName":"User Access Administrator",
        "type":"BuiltInRole",
        "description":"Lets you manage user access to Azure resources.",
        "assignableScopes":["/"],
        "permissions":[{"actions":["*/read","Microsoft.Authorization/*","Microsoft.Support/*"],"notActions":[]}],
        "createdOn":"0001-01-01T08:00:00.0000000Z",
        "updatedOn":"2016-05-31T23:14:04.6964687Z",
        "createdBy":null,
        "updatedBy":null},
        "id":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
        "type":"Microsoft.Authorization/roleDefinitions",
        "name":"18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"}],
        "nextLink":null}
        ```

        從 name 參數儲存 GUID，在此案例中為 **18d7d88d-d35e-4fb5-a5c3-7773c20a72d9**。

2. 您也必須列出租用戶管理員在租用戶範圍的所有角色指派。 請列出發出提高存取權限呼叫之 TenantAdmin 的 PrincipalId 在租用戶範圍的所有指派。 這會列出 ObjectID 之租用戶中的所有指派。 
    1. GET *https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'*
    
        >[!NOTE] 
        >租用戶管理員不應該有許多指派，如果上述查詢傳回太多指派，您也可以查詢只在租用戶範圍層級的所有指派，然後篩選結果：GET *https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()*
        
    2. 上述呼叫會傳回角色指派清單。 請尋找 scope 是 "/"、RoleDefinitionId 結尾是您在步驟 1 中找到的角色名稱 GUID 且 PrincipalId 與「租用戶管理員」的 ObjectId 相符的角色指派。該角色指派看起來會像這樣︰

        ```
        {"value":[{"properties":{
        "roleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
        "principalId":"{objectID}",
        "scope":"/",
        "createdOn":"2016-08-17T19:21:16.3422480Z",
        "updatedOn":"2016-08-17T19:21:16.3422480Z",
        "createdBy":"93ce6722-3638-4222-b582-78b75c5c6d65",
        "updatedBy":"93ce6722-3638-4222-b582-78b75c5c6d65"},
        "id":"/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
        "type":"Microsoft.Authorization/roleAssignments",
        "name":"e7dd75bc-06f6-4e71-9014-ee96a929d099"}],
        "nextLink":null}
        ```
        
        同樣地，從 name 參數儲存 GUID，在此案例中為 **e7dd75bc-06f6-4e71-9014-ee96a929d099**。

    3. 最後，使用反白顯示的 **RoleAssignment ID** 來刪除「提高存取權限」所新增的指派：

        DELETE https://management.azure.com /providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01

## <a name="next-steps"></a>後續步驟

- 深入了解[使用 REST 管理角色型存取控制](role-based-access-control-manage-access-rest.md)

- 在 Azure 入口網站中[管理存取權指派](role-based-access-control-manage-assignments.md)
