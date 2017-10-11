---
title: "了解如何安全存取 Azure Cosmos DB 中的資料 | Microsoft Docs"
description: "深入了解 Azure Cosmos DB 中的存取控制概念，其中包括主要金鑰、唯讀金鑰、使用者和權限。"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 8641225d-e839-4ba6-a6fd-d6314ae3a51c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: mimig
ms.openlocfilehash: 383e04f91eec2f465b381ce30f2d6d24c488b731
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="securing-access-to-azure-cosmos-db-data"></a>安全存取 Azure Cosmos DB 資料
本文提供儲存於 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)中資料的安全存取概觀。

Azure Cosmos DB 會使用兩種類型的金鑰來驗證使用者，以允許存取其資料和資源。 

|金鑰類型|資源|
|---|---|
|[主要金鑰](#master-keys) |用於系統管理資源︰資料庫帳戶、資料庫、使用者和權限|
|[資源權杖](#resource-tokens)|用於應用程式資源︰集合、文件、附件、預存程序、觸發程序和 UDF|

<a id="master-keys"></a>

## <a name="master-keys"></a>主要金鑰 

主要金鑰可讓資料庫帳戶存取所有系統管理資源。 主要金鑰：  
- 允許存取帳戶、資料庫、使用者和權限。 
- 無法用來提供集合和文件的更細微存取權。
- 在帳戶建立期間建立。
- 可隨時重新產生。

每個帳戶包含兩個主要金鑰︰主要金鑰和次要金鑰。 雙重金鑰的目的是讓您可以重新產生或輸替金鑰，以持續存取您的帳戶和資料。 

除了 Cosmos DB 帳戶的兩個主要金鑰，還有兩個唯讀金鑰。 這些唯讀金鑰只允許帳戶上的讀取作業。 唯讀金鑰不提供存取權來讀取權限資源。

您可以使用 Azure 入口網站來擷取和重新產生主要、次要、唯讀和讀寫主要金鑰。 相關指示請參閱[檢視、複製和重新產生存取金鑰](manage-account.md#keys)。

![Azure 入口網站中的存取控制 (IAM) - 示範 NoSQL 資料庫安全性](./media/secure-access-to-data/nosql-database-security-master-key-portal.png)

輸替主要金鑰的程序很簡單。 瀏覽至 Azure 入口網站來擷取次要金鑰，在您的應用程式中以次要金鑰取代主要金鑰，然後在 Azure 入口網站中輸替主要金鑰。

![Azure 入口網站中的主要金鑰輪替 - 示範 NoSQL 資料庫安全性](./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png)

### <a name="code-sample-to-use-a-master-key"></a>使用主要金鑰的程式碼範例

下列程式碼範例說明如何使用 Cosmos DB 帳戶端點和主要金鑰，以具現化 DocumentClient 並建立資料庫。 

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);

client = new DocumentClient(new Uri(endpointUrl), authorizationKey);

// Create Database
Database database = await client.CreateDatabaseAsync(
    new Database
    {
        Id = databaseName
    });
```

<a id="resource-tokens"></a>

## <a name="resource-tokens"></a>資源權杖

資源權杖允許存取資料庫內的應用程式資源。 資源權杖：
- 允許存取特定的集合、分割索引鍵、文件、附件、預存程序、觸發程序和 UDF。
- 在[使用者](#users)被授與特定資源的[權限](#permissions)時建立。
- 使用 POST、GET 或 PUT 呼叫處理權限資源時重新建立。
- 使用特別為使用者、資源和權限建構的雜湊資源權杖。
- 是與可自訂的有效期間繫結的時間。 預設有效時間範圍是一小時。 但可以明確指定權杖存留期，最多五小時。
- 提供安全的替代方式來分發主要金鑰。 
- 可讓用戶端根據所授與的權限，讀取、寫入和刪除 Cosmos DB 帳戶中的資源。

若要對無法託付主要金鑰的用戶端提供 Cosmos DB 帳戶內資源的存取權，您可以使用資源權杖 (方法是建立 Cosmos DB 使用者和權限)。  

Cosmos DB 資源權杖提供一個安全的替代方式，無需主要或唯讀金鑰，便可讓用戶端根據授與他們的權限，以讀取、寫入和刪除 Cosmos DB 帳戶中的資源。

以下是典型的設計模式，其中資源權杖可能會被要求、產生和傳遞給用戶端：

1. 設定中間層服務，以協助行動應用程式分享使用者相片。 
2. 中間層服務擁有 Cosmos DB 帳戶的主要金鑰。
3. 使用者的行動裝置上安裝相片應用程式。 
4. 登入時，相片應用程式會建立中間層服務的使用者身分識別。 這項身分識別建立的機制完全取決於應用程式。
5. 建立身分識別後，中間層服務會根據身分識別要求權限。
6. 中間層服務會將資源權杖送回電話應用程式。
7. 電話應用程式可以繼續使用資源權杖，利用資源權杖所定義的權限並在資源權杖所允許的間隔內，直接存取 Cosmos DB 資源。 
8. 資源權杖過期時，後續要求會收到 401 未經授權的例外狀況。  此時，電話應用程式會重新建立身分識別，並要求新的資源權杖。

    ![Azure Cosmos DB 資源權杖工作流程](./media/secure-access-to-data/resourcekeyworkflow.png)

資源權杖的產生和管理由原生 Cosmos DB 用戶端程式庫處理。不過，如果您使用 REST，您必須建構要求/驗證標頭。 如需有關建立 REST 驗證標頭的詳細資訊，請參閱 [Cosmos DB 資源的存取控制 (英文)](https://docs.microsoft.com/rest/api/documentdb/access-control-on-documentdb-resources) 或 [SDK 的原始程式碼](https://github.com/Azure/azure-documentdb-node/blob/master/source/lib/auth.js)。

如需用來產生或代理資源權杖的中間層服務的範例，請參閱 [ResourceTokenBroker 應用程式](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers)。

<a id="users"></a>

## <a name="users"></a>使用者
Cosmos DB 使用者會與 Cosmos DB 資料庫相關聯。  每個資料庫都包含零個或多個 Cosmos DB 使用者。  下列程式碼範例示範如何建立 Cosmos DB 使用者資源。

```csharp
//Create a user.
User docUser = new User
{
    Id = "mobileuser"
};

docUser = await client.CreateUserAsync(UriFactory.CreateDatabaseUri("db"), docUser);
```

> [!NOTE]
> 每個 Cosmos DB 使用者都具有 PermissionsLink 屬性，可用來擷取與使用者相關聯的[權限](#permissions)清單。
> 
> 

<a id="permissions"></a>

## <a name="permissions"></a>權限
Cosmos DB 權限資源會與 Cosmos DB 使用者相關聯。  每位使用者都包含零個或多個 Cosmos DB 權限。  當使用者嘗試存取特定的應用程式資源時，權限資源會提供使用者所需的安全性權杖存取權。
權限資源可能提供兩種可用的存取等級：

* 全部：使用者具有資源的完整權限。
* 讀取：使用者只能讀取資源的內容，但無法執行資源的寫入、更新或刪除作業。

> [!NOTE]
> 為執行 Cosmos DB 預存程序，使用者必須具備即將執行預存程序之集合的「所有」權限。
> 
> 

### <a name="code-sample-to-create-permission"></a>建立權限的程式碼範例

下列程式碼範例示範如何建立權限資源，讀取權限資源的資源權杖，並將權限與先前所建立的[使用者](#users)產生關聯。

```csharp
// Create a permission.
Permission docPermission = new Permission
{
    PermissionMode = PermissionMode.Read,
    ResourceLink = documentCollection.SelfLink,
    Id = "readperm"
};
  
docPermission = await client.CreatePermissionAsync(UriFactory.CreateUserUri("db", "user"), docPermission);
Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);
```

如果您已指定集合的分割索引鍵，則集合的權限、文件和附件資源也必須包含 ResourceLink 以外的 ResourcePartitionKey。

### <a name="code-sample-to-read-permissions-for-user"></a>讀取使用者權限的程式碼範例

為了輕鬆取得所有與特定使用者相關聯的權限資源，Cosmos DB 會為每個使用者物件提供權限摘要。  下列程式碼片段示範如何擷取與先前所建立的使用者相關聯的權限、建構權限清單，並代表使用者具現化新的 DocumentClient。

```csharp
//Read a permission feed.
FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(
  UriFactory.CreateUserUri("db", "myUser"));
 List<Permission> permList = new List<Permission>();

foreach (Permission perm in permFeed)
{
    permList.Add(perm);
}

DocumentClient userClient = new DocumentClient(new Uri(endpointUrl), permList);
```

## <a name="next-steps"></a>後續步驟
* 若要深入了解 Cosmos DB 資料庫安全性，請參閱 [Cosmos DB 資料庫安全性](database-security.md)。
* 若要了解如何管理主要和唯讀金鑰，請參閱[如何管理 Azure Cosmos DB 帳戶](manage-account.md#keys)。
* 若要了解如何建構 Cosmos DB 授權權杖，請參閱 [Cosmos DB 資源的存取控制 (英文)](https://docs.microsoft.com/rest/api/documentdb/access-control-on-documentdb-resources)。
