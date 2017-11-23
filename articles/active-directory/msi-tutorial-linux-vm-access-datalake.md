---
title: "如何使用 Linux VM 受管理的服務識別 (MSI) 來存取 Azure Data Lake Store"
description: "本教學課程示範如何使用 Linux VM 受管理的服務識別 (MSI) 來存取 Azure Data Lake Store。"
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: skwan
ms.openlocfilehash: 5c738fd8399fb555bcc72aacd278b5966a624cc7
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2017
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>使用 Linux VM 受管理的服務識別 (MSI) 來存取 Azure Data Lake Store

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

本教學課程示範如何使用 Linux 虛擬機器 (VM) 受管理的服務身分識別 (MSI) 來存取 Azure Data Lake Store。 受管理的服務識別由 Azure 自動管理，並可讓您驗證支援 Azure AD 驗證的服務，而不需要將認證插入程式碼中。 您會了解如何：

> [!div class="checklist"]
> * 在 Linux VM 上啟用 MSI 
> * 將您的 VM 存取權授與 Azure Data Lake Store
> * 使用 VM 身分識別取得存取權杖，並使用它存取 Azure Data Lake Store

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站，位址是 [https://portal.azure.com](https://portal.azure.com)。

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>在新的資源群組中建立 Linux 虛擬機器

此教學課程中，我們會建立新的 Linux VM。 您也可以在現有的 VM 中啟用 MSI。

1. 按一下 Azure 入口網站左上角的 [新增] 按鈕。
2. 選取 [計算]，然後選取 [Ubuntu Server 16.04 LTS]。
3. 輸入虛擬機器資訊。 針對 [驗證類型] 選取 [SSH 公開金鑰] 或 [密碼]。 建立的認證可讓您登入 VM。

   ![替代映像文字](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. 在下拉式清單中選擇適用於虛擬機器的**訂用帳戶**。
5. 若要選取要在其中建立虛擬機器的新 [資源群組]，請選擇 [新建]。 完成時，按一下 [確定]。
6. 選取 VM 的大小。 若要查看更多大小，請選取 [檢視全部] 或變更支援的磁碟類型篩選條件。 在 [設定] 刀鋒視窗上，保留預設值並按一下 [確定]。

## <a name="enable-msi-on-your-vm"></a>在您的 VM 上啟用 MSI

虛擬機器 MSI 可讓您從 Azure AD 取得存取權杖，而不需要將憑證放入您的程式碼。 實際上，啟用 MSI 會執行兩項工作：在您的 VM 上安裝 MSI VM 延伸模組，並在 Azure Resource Manager 中啟用 MSI。  

1. 選取您想要在其中啟用 MSI 的 [虛擬機器]。
2. 在左側的導覽列上，按一下 [設定] 。
3. 您會看到**受管理的服務識別**。 若要註冊並啟用 MSI，請選取 [是]，如果您想要將它停用，則請選擇 [否]。
4. 按一下 [儲存] 確認儲存設定。

   ![替代映像文字](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. 如果您想要檢查哪些延伸模組會在此 **Linux VM** 上，請按一下 [延伸模組]。 如果 MSI 已啟用，則 **ManagedIdentityExtensionforLinux** 會出現在清單上。

   ![替代映像文字](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>將您的 VM 存取權授與 Azure Data Lake Store

現在您可以將您的 VM 存取權授與 Azure Data Lake Store 中的檔案和資料夾。  在這個步驟，您可以使用現有的 Data Lake Store 或建立新的。  若要使用 Azure 入口網站建立新的 Data Lake Store，請遵循此 [Azure Data Lake Store 快速入門](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)。 在 [Azure Data Lake Store 文件](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview)中也有使用 Azure CLI 和 Azure PowerShell 的快速入門作法。

在 Data Lake Store 中建立新資料夾，並將在該資料夾中讀取、寫入和執行檔案的權限授與您的 VM MSI：

1. 在 Azure 入口網站中，按一下左方瀏覽列中的 [Data Lake Store]。
2. 按一下您想要使用於本教學課程中的 Data Lake Store。
3. 按一下命令列中的 [資料總管]。
4. 會選取 Data Lake Store 的根資料夾。  按一下命令列中的 [存取]。
5. 按一下 [新增] 。  在 [選取] 欄位中，輸入 VM 的名稱，例如 **DevTestVM**。  按一下以從搜尋結果中選取您的 VM，然後按一下 [選取]。
6. 按一下 [選取權限]。  選取 [讀取] 和 [執行]，加入到 [此資料夾]，然後新增為 [僅存取權限]。  按一下 [確定] 。  權限應該已成功加入。
7. 關閉 [存取] 刀鋒視窗。
8. 針對此教學課程，建立一個新資料夾。  按一下命令列中的 [新資料夾]，提供新資料夾的名稱，例如 **TestFolder**。  按一下 [確定] 。
9. 按一下您建立的資料夾，然後按一下命令列中的 [存取]。
10. 和步驟 5 相同，按一下 [新增]，在 [選取] 欄位中輸入您的 VM 名稱，選取它，然後按一下 [選取]。
11. 和步驟 6 相同，按一下 [選取權限]，選取 [讀取]、[寫入] 和 [執行]，加入到 [此資料夾]，並新增為 [存取權限項目及預設權限項目]。  按一下 [確定] 。  權限應該已成功加入。

您的 VM MSI 現在可以對您所建立資料夾中的檔案執行所有作業。  如需管理 Data Lake Store 存取權的詳細資訊，請閱讀 [Azure Data Lake Store 中的存取控制](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control)一文。

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>使用 VM MSI 取得存取權杖，並使用它呼叫 Azure Data Lake Store 檔案系統

Azure Data Lake Store 原生支援 Azure AD 驗證，因此可以直接接受使用 MSI 取得的存取權杖。  為了向 Data Lake Store 檔案系統驗證，您在授權標頭中傳送由 Azure AD 所簽發的存取權杖至 Data Lake Store 檔案系統端點，其格式如下："Bearer \<ACCESS_TOKEN_VALUE\>"。  若要深入了解 Data Lake Store 的 Azure AD 驗證支援，請閱讀[使用 Azure Active Directory 向 Data Lake Store 進行驗證](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory)

在本教學課程中，您使用 CURL 向 Data Lake Store 檔案系統 REST API 驗證，以提出 REST 要求。

> [!NOTE]
> Data Lake Store 檔案系統用戶端 SDK 目前尚不支援受管理的服務識別。  待 SDK 新增支援後，將會更新本教學課程。

若要完成這些步驟，您需要 SSH 用戶端。 如果您使用 Windows，您可以在[適用於 Linux 的 Windows 子系統](https://msdn.microsoft.com/commandline/wsl/about)中使用 SSH 用戶端。 如果您需要設定 SSH 用戶端金鑰的協助，請參閱[如何在 Azure 上搭配 Windows 使用 SSH 金鑰](../virtual-machines/linux/ssh-from-windows.md)，或[如何在 Azure 中建立和使用 Linux VM 的 SSH 公開和私密金鑰組](../virtual-machines/linux/mac-create-ssh-keys.md)。

1. 在入口網站中，瀏覽至 [Linux VM]，並在 [概觀] 中按一下 [連線]。  
2. 使用您所選擇的 SSH 用戶端來**連線**到 VM。 
3. 在終端機視窗中使用 CURL 向本機 MSI 端點提出要求，來取得 Data Lake Store 檔案系統的存取權杖。  Data Lake Store 的資源識別項是 "https://datalake.azure.net/"。  請務必包含資源識別項中結尾的斜線。
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
   ```
    
   成功的回應會傳回您向 Data Lake Store 驗證使用的存取權杖：

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. 使用 CURL 對您的 Data Lake Store 檔案系統 REST 端點提出要求，以列出根資料夾中的資料夾。  這是檢查所有項目是否正確設定的簡單方式。  複製上一個步驟中的存取權杖值。  授權標頭中的字串 "Bearer" 必須是大寫 "B"。  您可以在 Azure 入口網站 Data Lake Store 刀鋒視窗的 [概觀] 區段中找到您的 Data Lake Store 名稱。

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   成功的回應看起來會像這樣：

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. 現在您可以嘗試將檔案上傳至您的 Data Lake Store。  首先，建立要上傳的檔案。

   ```bash
   echo "Test file." > Test1.txt
   ```

6. 使用 CURL 對您的 Data Lake Store 檔案系統 REST 端點提出要求，以將檔案上傳到您先前建立的資料夾。  上傳需要重新導向，CURL 會自動遵循重新導向。 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    成功的回應看起來會像這樣：

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

您還可以使用其他 Data Lake Store 檔案系統 API，以附加至檔案及下載檔案等。

恭喜！  您已經使用 VM MSI 向 Data Lake Store 檔案系統驗證。

## <a name="related-content"></a>相關內容

- 如需 MSI 的概觀，請參閱[受管理的服務識別概觀](../active-directory/msi-overview.md)。
- Data Lake Store 使用 Azure Resource Manager 執行管理作業。  如需有關使用 VM MSI 向 Resource Manager 驗證的詳細資訊，請閱讀[使用 Linux VM 受管理的服務識別 (MSI) 存取 Resource Manager](https://docs.microsoft.com/azure/active-directory/msi-tutorial-linux-vm-access-arm)。
- 深入了解[使用 Azure Active Directory 向 Data Lake Store 進行驗證](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory)。
- 深入了解[使用 REST API 在 Azure Data Lake Store 上進行檔案系統作業](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-operations-rest-api)或 [WebHDFS 檔案系統 API](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis) \(英文\)。
- 深入了解 [Data Lake Store 中的存取控制](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。