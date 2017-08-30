---
title: "在 Azure Data Lake Store 中使用 .NET SDK 開發應用程式 | Microsoft Docs"
description: "使用 Azure Data Lake Store .NET SDK 建立 Data Lake Store 帳戶，並在 Data Lake Store 中執行基本作業"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 79bebd10784ec74b4800e19576cbec253acf1be7
ms.openlocfilehash: 70f94a07b0102e3135eaf85e5877e3502762d7e3
ms.contentlocale: zh-tw
ms.lasthandoff: 08/03/2017

---
# <a name="get-started-with-azure-data-lake-store-using-net-sdk"></a>使用 .NET SDK 開始使用 Azure Data Lake Store
> [!div class="op_single_selector"]
> * [入口網站](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

了解如何使用 [Azure Data Lake Store .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet) 來執行基本作業，例如建立資料夾、上傳和下載資料檔案等等。如需有關 Data Lake 的詳細資訊，請參閱 [Azure Data Lake Store](data-lake-store-overview.md)。

## <a name="prerequisites"></a>必要條件
* **Visual Studio 2013、2015 或 2017**。 以下指示使用 Visual Studio 2015 Update 2。

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

* **Azure Data Lake Store 帳戶**。 如需有關如何建立帳戶的指示，請參閱 [開始使用 Azure Data Lake Store](data-lake-store-get-started-portal.md)

* **建立 Azure Active Directory 應用程式**。 您必須使用 Azure AD 應用程式來向 Azure AD 驗證 Data Lake Store 應用程式。 有不同的方法可向 Azure AD 進行驗證：**使用者驗證**或**服務對服務驗證**。 如需有關如何驗證的指示和詳細資訊，請參閱[使用者驗證](data-lake-store-end-user-authenticate-using-active-directory.md)或[服務對服務驗證](data-lake-store-authenticate-using-active-directory.md)。

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
5. 將 Nuget 封裝新增至您的專案。

   1. 在方案總管中以滑鼠右鍵按一下專案名稱，然後按一下 [ **管理 NuGet 封裝**]。
   2. 在 [Nuget 封裝管理員] 索引標籤中，確定 [封裝來源] 設為 [nuget.org]，且已選取 [包含發行前版本] 核取方塊。
   3. 搜尋並安裝下列 NuGet 封裝：

      * `Microsoft.Azure.Management.DataLake.Store` - 本教學課程使用 v2.1.3-preview。
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - 本教學課程使用 v2.2.12。

        ![新增 Nuget 來源](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "建立新的 Azure Data Lake 帳戶")
   4. 關閉 [ **Nuget 封裝管理員**]。
6. 開啟 **Program.cs**，刪除現有的程式碼，然後納入下列陳述式以新增命名空間的參考。

        using System;
        using System.IO;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
        using System.Threading;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest.Azure.Authentication;

7. 宣告如下所示的變數，並提供已存在的 Data Lake Store 名稱和資源群組名稱的值。 此外，請確定您在此處提供的本機路徑和檔案名稱必須存在於電腦。 將下列程式碼片段加在命名空間宣告之後。

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = Path.Combine(localFolderPath, "file.txt"); // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = Path.Combine(remoteFolderPath, "file.txt");
                }
            }
        }

在本文的其餘章節中，您可以了解如何使用可用的 .NET 方法來執行一些作業，例如驗證、檔案上載等。

## <a name="authentication"></a>驗證

### <a name="if-you-are-using-end-user-authentication-recommended-for-this-tutorial"></a>如果您要使用使用者驗證 (本教學課程建議的驗證方式)

使用這個項目與現有的 Azure AD 原生應用程式，**以互動方式**驗證您的應用程式，這表示系統會提示您輸入您的 Azure 認證。

為了方便使用，下列程式碼片段會針對用戶端識別碼和重新導向 URI 使用預設值，這些項目會與任何 Azure 訂用帳戶搭配使用。 為了協助您更快完成本教學課程，建議您使用此方法。 在下列程式碼片段中，只須提供您的租用戶識別碼值。 您可以使用[建立 Active Directory 應用程式](data-lake-store-end-user-authenticate-using-active-directory.md)提供的指示來擷取它。

    // User login via interactive popup
    // Use the client ID of an existing AAD Web application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenant_id = "<AAD_tenant_id>"; // Replace this string with the user's Azure Active Directory tenant ID
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(tenant_id, activeDirectoryClientSettings).Result;

上面這個程式碼片段有幾項須知：

* 為了協助您更快完成本教學課程，此程式碼片段使用所有 Azure 訂用帳戶預設可用的 Azure AD 網域和用戶端識別碼。 因此，您可以**在應用程式中原封不動地使用此程式碼片段**。
* 但是，如果您想要使用自己的 Azure AD 網域和應用程式用戶端識別碼，您必須建立 Azure AD 原生應用程式，然後使用您所建立之應用程式的 Azure AD 租用戶識別碼、用戶端識別碼和重新導向 URI。 如需相關指示，請參閱[建立 Active Directory 應用程式以使用 Data Lake Store 進行使用者驗證](data-lake-store-end-user-authenticate-using-active-directory.md)。

### <a name="if-you-are-using-service-to-service-authentication-with-client-secret"></a>如果您要使用服務對服務驗證與用戶端密碼
下列程式碼片段可供使用應用程式/服務主體的用戶端密碼/金鑰，**以非互動方式**驗證您的應用程式。 請將此方法用於現有的 Azure AD「Web 應用程式」應用程式。 如需有關如何建立 Azure AD Web 應用程式以及如何擷取以下程式碼片段必要用戶端識別碼和用戶端密碼的指示，請參閱[使用 Data Lake Store 建立 Active Directory 應用程式以進行服務對服務驗證](data-lake-store-authenticate-using-active-directory.md)。

    // Service principal / appplication authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = await ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential);

### <a name="if-you-are-using-service-to-service-authentication-with-certificate"></a>如果您要使用服務對服務驗證與憑證

第三個選項，下列程式碼片段可供使用 Azure Active Directory 應用程式/服務主體的憑證，**以非互動方式**驗證您的應用程式。 請將此方法用於現有的 [Azure AD 與憑證](../azure-resource-manager/resource-group-authenticate-service-principal.md)。

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = await ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate);

## <a name="create-client-objects"></a>建立用戶端物件
下列程式碼片段會建立 Data Lake Store 帳戶和檔案系統用戶端物件，以便對服務發出要求。

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds) { SubscriptionId = _subId };
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>列出訂用帳戶內的所有 Data Lake Store 帳戶
下列程式碼片段列出指定的 Azure 訂用帳戶中的所有 Data Lake Store 帳戶。

    // List all ADLS accounts within the subscription
    public static async Task<List<DataLakeStoreAccount>> ListAdlStoreAccounts()
    {
        var response = await _adlsClient.Account.ListAsync();
        var accounts = new List<DataLakeStoreAccount>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="create-a-directory"></a>建立目錄
下列程式碼片段顯示的 `CreateDirectory` 方法可用於在 Data Lake Store 帳戶中建立目錄。

    // Create a directory
    public static async Task CreateDirectory(string path)
    {
        await _adlsFileSystemClient.FileSystem.MkdirsAsync(_adlsAccountName, path);
    }

## <a name="upload-a-file"></a>上傳檔案
下列程式碼片段顯示的 `UploadFile` 方法可用於將檔案上傳到 Data Lake Store 帳戶。

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        _adlsFileSystemClient.FileSystem.UploadFile(_adlsAccountName, srcFilePath, destFilePath, overwrite:force);
    }

SDK 支援在本機檔案路徑與 Data Lake Store 檔案路徑之間進行遞迴上傳和下載。    

## <a name="get-file-or-directory-info"></a>取得檔案或目錄資訊
下列程式碼片段顯示的 `GetItemInfo` 方法可用於擷取 Data Lake Store 中可用檔案或目錄的相關資訊。

    // Get file or directory info
    public static async Task<FileStatusProperties> GetItemInfo(string path)
    {
        return await _adlsFileSystemClient.FileSystem.GetFileStatusAsync(_adlsAccountName, path).FileStatus;
    }

## <a name="list-file-or-directories"></a>列出檔案或目錄
下列程式碼片段顯示的 `ListItem` 方法可用於列出 Data Lake Store 帳戶中的檔案和目錄。

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## <a name="concatenate-files"></a>串連檔案
下列程式碼片段顯示的 `ConcatenateFiles` 方法可用於串連檔案。

    // Concatenate files
    public static Task ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        await _adlsFileSystemClient.FileSystem.ConcatAsync(_adlsAccountName, destFilePath, srcFilePaths);
    }

## <a name="append-to-a-file"></a>附加到檔案
下列程式碼片段顯示的 `AppendToFile` 方法可用於將資料附加到 Data Lake Store 帳戶中已儲存的檔案。

    // Append to file
    public static async Task AppendToFile(string path, string content)
    {
        using (var stream = new MemoryStream(Encoding.UTF8.GetBytes(content)))
        {
            await _adlsFileSystemClient.FileSystem.AppendAsync(_adlsAccountName, path, stream);
        }
    }

## <a name="download-a-file"></a>下載檔案
下列程式碼片段顯示的 `DownloadFile` 方法可用於從 Data Lake Store 帳戶下載檔案。

    // Download file
    public static void DownloadFile(string srcFilePath, string destFilePath)
    {
         _adlsFileSystemClient.FileSystem.DownloadFile(_adlsAccountName, srcFilePath, destFilePath);
    }

## <a name="next-steps"></a>後續步驟
* [保護 Data Lake Store 中的資料](data-lake-store-secure-data.md)
* [搭配 Data Lake Store 使用 Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [搭配 Data Lake Store 使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Data Lake Store .NET SDK 參考](https://docs.microsoft.com/dotnet/api/?view=azuremgmtdatalakestore-2.1.0-preview&term=DataLake.Store)
* [Data Lake Store REST 參考](https://msdn.microsoft.com/library/mt693424.aspx)

