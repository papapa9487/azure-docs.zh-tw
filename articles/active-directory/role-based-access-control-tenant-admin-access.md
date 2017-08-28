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
ms.date: 05/09/2017
ms.author: andredm
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 0fa44799a0bd49d3d96a1916f32e6452405abce8
ms.contentlocale: zh-tw
ms.lasthandoff: 05/17/2017

---
# <a name="elevate-access-as-a-tenant-admin-with-role-based-access-control"></a>使用角色型存取控制以租用戶系統管理員提高存取權限

角色型存取控制可協助租用戶系統管理員取得存取權限暫時提升，以便他們可以比平常授與更高的權限。 租用戶管理員可以視需要將自己提升至使用者存取系統管理員角色。 該角色提供租用戶系統管理員權限，可在 "/" 的範圍內授與自己或其他角色。

這項功能很重要，因為它可讓租用戶管理員查看組織中的所有訂用帳戶。 它也可讓自動化應用程式 (如發票開立與稽核) 存取所有的訂用帳戶，並針對帳單或資產管理提供組織狀態的精確檢視。  

## <a name="how-to-use-elevateaccess-to-give-tenant-access"></a>如何使用 elevateAccess 提供租用戶存取

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


## <a name="how-to-undo-the-elevateaccess-action"></a>如何復原 elevateAccess 動作

當您呼叫 elevateAccess 時，您建立自己的角色指派，因此您需要刪除作業才能撤銷這些權限。

1.  呼叫 [GET roleDefinitions](/rest/api/authorization/roledefinitions#RoleDefinitions_Get)，其中 roleName = 使用者存取系統管理員，以判斷使用者存取系統管理員角色的 GUID 名稱。 回應應該如下所示：

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

2. 呼叫 [GET roleAssignments](/rest/api/authorization/roleassignments#RoleAssignments_Get)，其中 principalId = 您自己的 ObjectId。 這樣會列出您租用戶中的所有指派。 尋找範圍是 "/" 的位置，且 RoleDefinitionId 以您在步驟 1 中所找到的角色名稱 GUID 結尾。 角色指派看起來應該像這樣︰

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

3. 最後，呼叫 [DELETE roleAssignments](/rest/api/authorization/roleassignments#RoleAssignments_DeleteById)，其中 roleAssignmentId = 您在步驟 2 中找到的名稱 GUID。

## <a name="next-steps"></a>後續步驟

- 深入了解[使用 REST 管理角色型存取控制](role-based-access-control-manage-access-rest.md)

- 在 Azure 入口網站中[管理存取權指派](role-based-access-control-manage-assignments.md)

