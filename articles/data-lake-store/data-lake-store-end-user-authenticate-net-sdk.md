---
title: "使用者驗證︰使用 .NET SDK 利用 Azure Active Directory 向 Data Lake Store 進行 | Microsoft Docs"
description: "了解如何使用 .NET SDK，利用 Azure Active Directory 向 Data Lake Store 完成使用者驗證"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/29/2017
ms.author: nitinme
ms.openlocfilehash: a2a9754b462e39713e6580baab84c064237306a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="end-user-authentication-with-data-lake-store-using-net-sdk"></a>使用 .NET SDK 向 Data Lake Store 進行使用者驗證
> [!div class="op_single_selector"]
> * [使用 Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [使用 .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [使用 Python](data-lake-store-end-user-authenticate-python.md)
> * [使用 REST API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

在本文中，您會了解如何使用 .NET SDK 向 Azure Data Lake Store 進行使用者驗證。 如需使用 .NET SDK 向 Data Lake Store 進行服務對服務驗證，請參閱[使用 .NET SDK 向 Data Lake Store 進行服務對服務驗證](data-lake-store-service-to-service-authenticate-net-sdk.md)。

## <a name="prerequisites"></a>必要條件
* **Visual Studio 2013、2015 或 2017**。 以下指示使用 Visual Studio 2017。

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

* **建立 Active Directory 「原生」應用程式**。 您必須已經完成[使用 Azure Active Directory 向 Data Lake Store 進行使用者驗證](data-lake-store-end-user-authenticate-using-active-directory.md)中的步驟。

## <a name="create-a-net-application"></a>建立 .NET 應用程式
1. 開啟 Visual Studio，建立主控台應用程式。
2. 從 [檔案] 功能表中，按一下 [新增]，再按 [專案]。
3. 在 [ **新增專案**] 中，輸入或選取下列值：

   | 屬性 | 值 |
   | --- | --- |
   | 類別 |範本/Visual C#/Windows |
   | 範本 |主控台應用程式 |
   | 名稱 |CreateADLApplication |

4. 按一下 [確定]  以建立專案。

5. 將 NuGet 封裝新增至您的專案。

   1. 在方案總管中以滑鼠右鍵按一下專案名稱，然後按一下 [ **管理 NuGet 封裝**]。
   2. 在 [NuGet 封裝管理員] 索引標籤中，確定 [封裝來源] 設為 [nuget.org]，且已選取 [包含發行前版本] 核取方塊。
   3. 搜尋並安裝下列 NuGet 封裝：

      * `Microsoft.Azure.Management.DataLake.Store` - 本教學課程使用 v2.1.3-preview。
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - 本教學課程使用 v2.2.12。

        ![新增 NuGet 來源](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "建立新的 Azure Data Lake 帳戶")
   4. 關閉 [NuGet 封裝管理員]。

6. 開啟 **Program.cs**，刪除現有的程式碼，然後納入下列陳述式以新增命名空間的參考。

        using System;
        using System.IO;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
        using System.Threading;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest.Azure.Authentication;

## <a name="end-user-authentication"></a>使用者驗證
在 .NET 用戶端應用程式中加入這個程式碼片段。 以從 Azure AD 原生應用程式 (列為必要元件) 中擷取的值取代預留位置值。 這個程式碼片段可讓您**以互動方式**向 Data Lake Store 驗證應用程式，這表示系統會提示您輸入您的 Azure 認證。

為了方便使用，下列程式碼片段會使用對任何 Azure 訂用帳戶都有效的用戶端識別碼和重新導向 URI 的預設值。 在下列程式碼片段中，您只需要提供租用戶識別碼的值。 您可以使用[取得租用戶識別碼](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id)所提供的指示，取得租用戶識別碼。
    
    private static void Main(string[] args)
    {
        // User login via interactive popup
        // Use the client ID of an existing AAD native application.
        SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
        var tenant_id = "<AAD_tenant_id>"; // Replace this string with the user's Azure Active Directory tenant ID
        var nativeClientApp_applicationId = "1950a258-227b-4e31-a9cf-717495945fc2";
        var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_applicationId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
        var creds = UserTokenProvider.LoginWithPromptAsync(tenant_id, activeDirectoryClientSettings).Result;
    }

上面這個程式碼片段有幾項須知：

* 為了協助您更快完成本教學課程，此程式碼片段使用所有 Azure 訂用帳戶預設可用的 Azure AD 網域和用戶端識別碼。 因此，您可以**在應用程式中原封不動地使用此程式碼片段**。
* 但是，如果您想要使用自己的 Azure AD 網域和應用程式用戶端識別碼，您必須建立 Azure AD 原生應用程式，然後使用您所建立之應用程式的 Azure AD 租用戶識別碼、用戶端識別碼和重新導向 URI。 如需相關指示，請參閱[建立 Active Directory 應用程式以使用 Data Lake Store 進行使用者驗證](data-lake-store-end-user-authenticate-using-active-directory.md)。

  
## <a name="next-steps"></a>後續步驟
在本文中，您已了解如何使用 .NET SDK，使用使用者驗證向 Azure Data Lake Store 進行驗證。 您現在可以看看下列文章，了解如何使用 .NET SDK 與 Azure Data Lake Store 搭配。

* [使用 .NET SDK 在 Data Lake Store 上進行帳戶管理作業](data-lake-store-get-started-net-sdk.md)
* [使用 .NET SDK 在 Data Lake Store 上進行資料作業](data-lake-store-data-operations-net-sdk.md)

