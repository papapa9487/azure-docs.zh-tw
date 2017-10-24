---
title: "服務對服務驗證︰使用 .NET SDK 利用 Azure Active Directory 向 Data Lake Store 進行 | Microsoft Docs"
description: "了解如何使用 .NET SDK 利用 Azure Active Directory 向 Data Lake Store 完成服務對服務驗證"
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
ms.date: 10/05/2017
ms.author: nitinme
ms.openlocfilehash: 72e9e2e10992d928dcfd85538497ba12c6e1c6f5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="service-to-service-authentication-with-data-lake-store-using-net-sdk"></a>使用 .NET SDK 向 Data Lake Store 進行服務對服務驗證
> [!div class="op_single_selector"]
> * [使用 Java](data-lake-store-service-to-service-authenticate-java.md)
> * [使用 .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [使用 Python](data-lake-store-service-to-service-authenticate-python.md)
> * [使用 REST API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

在本文中，您會了解如何使用 .NET SDK 向 Azure Data Lake Store 進行服務對服務驗證。 如需使用 .NET SDK 向 Data Lake Store 進行使用者驗證，請參閱[使用 .NET SDK 向 Data Lake Store 進行使用者驗證](data-lake-store-end-user-authenticate-net-sdk.md)。


## <a name="prerequisites"></a>必要條件
* **Visual Studio 2013、2015 或 2017**。 以下指示使用 Visual Studio 2017。

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

* **建立 Azure Active Directory "Web" 應用程式**。 您必須已經完成[使用 Azure Active Directory 向 Data Lake Store 進行服務對服務驗證](data-lake-store-service-to-service-authenticate-using-active-directory.md)中的步驟。

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

## <a name="service-to-service-authentication-with-client-secret"></a>使用用戶端祕密進行服務對服務驗證
在 .NET 用戶端應用程式中加入這個程式碼片段。 以從 Azure AD Web 應用程式 (列為必要元件) 中擷取的值取代預留位置值。  這個程式碼片段可讓您使用 Azure AD Web 應用程式的用戶端祕密/金鑰，**以非互動方式**向 Data Lake Store 驗證應用程式。 

    private static void Main(string[] args)
    {    
        // Service principal / appplication authentication with client secret / key
        // Use the client ID of an existing AAD "Web App" application.
        SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
        var domain = "<AAD-directory-domain>";
        var webApp_clientId = "<AAD-application-clientid>";
        var clientSecret = "<AAD-application-client-secret>";
        var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
        var creds = ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential).Result;
    }

## <a name="service-to-service-authentication-with-certificate"></a>使用憑證進行服務對服務驗證

在 .NET 用戶端應用程式中加入這個程式碼片段。 以從 Azure AD Web 應用程式 (列為必要元件) 中擷取的值取代預留位置值。 這個程式碼片段可讓您使用 Azure AD Web 應用程式的憑證，**以非互動方式**向 Data Lake Store 驗證應用程式。 如需有關如何建立 Azure AD 應用程式的指示，請參閱[使用憑證建立服務主體](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate)。

    
    private static void Main(string[] args)
    {
        // Service principal / application authentication with certificate
        // Use the client ID and certificate of an existing AAD "Web App" application.
        SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
        var domain = "<AAD-directory-domain>";
        var webApp_clientId = "<AAD-application-clientid>";
        var clientCert = <AAD-application-client-certificate>
        var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
        var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate).Result;
    }


## <a name="next-steps"></a>後續步驟
在本文中，您已了解如何使用 .NET SDK，使用服務對服務驗證向 Azure Data Lake Store 進行驗證。 您現在可以看看下列文章，了解如何使用 .NET SDK 與 Azure Data Lake Store 搭配。

* [使用 .NET SDK 在 Data Lake Store 上進行帳戶管理作業](data-lake-store-get-started-net-sdk.md)
* [使用 .NET SDK 在 Data Lake Store 上進行資料作業](data-lake-store-data-operations-net-sdk.md)


