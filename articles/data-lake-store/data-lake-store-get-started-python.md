---
title: "Python：Azure Data Lake Store 的帳戶管理作業 | Microsoft Docs"
description: "了解如何利用 Python SDK 來處理 Data Lake Store 帳戶管理作業。"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: 76e84687815ca6f4b031e5f7143ba0079fb053db
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2017
---
# <a name="account-management-operations-on-azure-data-lake-store-using-python"></a>使用 Python 在 Data Lake Store 上進行帳戶管理作業
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

了解如何使用適用於 Azure Data Lake Store 的 Python SDK 來執行基本帳戶管理作業，例如建立 Data Lake Store 帳戶、列出 Data Lake Store 帳戶等。如需有關如何使用 Python 在 Data Lake Store 上執行檔案系統作業的指示，請參閱[使用 Python 在 Data Lake Store 上進行檔案系統作業](data-lake-store-data-operations-python.md)。

## <a name="prerequisites"></a>必要條件

* **Python**。 您可以從[這裡](https://www.python.org/downloads/)下載 Python。 本文使用 Python 3.6.2。

* **Azure 訂用帳戶**。 請參閱 [取得 Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

* **Azure 資源群組**。 如需指示，請參閱[建立 Azure 資源群組](../azure-resource-manager/resource-group-portal.md)。

## <a name="install-the-modules"></a>安裝模組

若要透過 Python 使用 Data Lake Store，您需要安裝三個模組。

* `azure-mgmt-resource` 模組，這包括適用於 Active Directory 等等的 Azure 模組。
* `azure-mgmt-datalake-store` 模組，這包括 Azure Data Lake Store 帳戶管理作業。 如需關於此模組的詳細資訊，請參閱 [Azure Data Lake Store Management module reference (Azure Data Lake Store 管理模組參考)](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)。
* `azure-datalake-store` 模組，這包括 Azure Data Lake Store 檔案系統作業。 如需關於此模組的詳細資訊，請參閱 [Azure Data Lake Store Filesystem module reference (Azure Data Lake Store 檔案系統模組參考)](http://azure-datalake-store.readthedocs.io/en/latest/)。

使用下列命令來安裝新模組。

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

1. 在您選定的整合式開發環境 (IDE) 中建立新的 Python 應用程式，例如 **mysample.py**。

2. 新增以下程式碼片段以匯入必要模組

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. 將變更儲存至 mysample.py。

## <a name="authentication"></a>驗證

在本節中，我們會討論向 Azure AD 進行驗證的各種方式。 可用的選項如下︰

* 如需讓應用程式進行使用者驗證，請參閱[使用 Python 向 Data Lake Store 進行使用者驗證](data-lake-store-end-user-authenticate-python.md)。
* 如需讓應用程式進行服務對服務驗證，請參閱[使用 Python 向 Data Lake Store 進行服務對服務驗證](data-lake-store-service-to-service-authenticate-python.md)。

## <a name="create-client-and-data-lake-store-account"></a>建立用戶端與 Data Lake Store 帳戶

以下程式碼片段會先建立 Data Lake Store 帳戶用戶端。 它會使用用戶端物件來建立 Data Lake Store 帳戶。 最後，程式碼片段會建立檔案系統用戶端物件。

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'

    ## Create data lake store account management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(armCreds, subscriptionId)

    ## Create a Data Lake Store account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    
## <a name="list-the-data-lake-store-accounts"></a>列出 Data Lake Store 帳戶

    ## List the existing Data Lake Store accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="delete-the-data-lake-store-account"></a>刪除 Data Lake Store 帳戶

    ## Delete the existing Data Lake Store accounts
    adlsAcctClient.account.delete(adlsAccountName)
    

## <a name="next-steps"></a>後續步驟
* [使用 Python 在 Data Lake Store 上進行檔案系統作業](data-lake-store-data-operations-python.md)。

## <a name="see-also"></a>另請參閱
* [Azure Data Lake Store Python (帳戶管理) 參考資料](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)
* [Azure Data Lake Store Python (檔案系統) 參考資料](http://azure-datalake-store.readthedocs.io/en/latest)
* [與 Azure Data Lake Store 相容的開放原始碼巨量資料應用程式](data-lake-store-compatible-oss-other-applications.md)
