---
title: "如何使用 Windows VM 受管理的服務識別 (MSI) 來存取 Azure Data Lake Store"
description: "本教學課程示範如何使用 Windows VM 受管理的服務識別 (MSI) 來存取 Azure Data Lake Store。"
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
ms.date: 10/31/2017
ms.author: skwan
ms.openlocfilehash: 28b3ec7640e1f126d2ef310130e94c84b68ffa75
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2017
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>使用 Windows VM 受管理的服務識別 (MSI) 來存取 Azure Data Lake Store

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

本教學課程示範如何使用 Windows 虛擬機器 (VM) 受管理的服務識別 (MSI) 來存取 Azure Data Lake Store。 受管理的服務識別由 Azure 自動管理，並可讓您驗證支援 Azure AD 驗證的服務，而不需要將認證插入程式碼中。 您會了解如何：

> [!div class="checklist"]
> * 在 Windows VM 上啟用 MSI 
> * 將您的 VM 存取權授與 Azure Data Lake Store
> * 使用 VM 身分識別取得存取權杖，並使用它存取 Azure Data Lake Store

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站，位址是 [https://portal.azure.com](https://portal.azure.com)。

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>在新的資源群組中建立 Windows 虛擬機器

此教學課程中，我們會建立新的 Windows VM。  您也可以在現有的 VM 中啟用 MSI。

1. 按一下 Azure 入口網站左上角的 [新增] 按鈕。
2. 選取 [計算]，然後選取 [Windows Server 2016 Datacenter]。 
3. 輸入虛擬機器資訊。 在此建立的**使用者名稱**和**密碼**是您登入虛擬機器要使用的認證。
4. 在下拉式清單中選擇適用於虛擬機器的適當**訂用帳戶**。
5. 若要選取要在其中建立虛擬機器的新 [資源群組]，請選擇 [新建]。 完成時，按一下 [確定]。
6. 選取 VM 的大小。 若要查看更多大小，請選取 [檢視全部] 或變更 [支援的磁碟類型] 篩選條件。 在 [設定] 頁面上，保留預設值並按一下 [確定]。

   ![替代映像文字](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>在您的 VM 上啟用 MSI 

VM MSI 可讓您從 Azure AD 取得存取權杖，而不需要將憑證放入您的程式碼。 啟用 MSI 會告訴 Azure 為您的 VM 建立受管理的身分識別。 實際上，啟用 MSI 會執行兩項工作：在您的 VM 上安裝 MSI VM 延伸模組，並在 Azure Resource Manager 中啟用 MSI。

1. 選取您想要在其中啟用 MSI 的 [虛擬機器]。  
2. 在左側的導覽列上，按一下 [設定] 。 
3. 您會看到**受管理的服務識別**。 若要註冊並啟用 MSI，請選取 [是]，如果您想要將它停用，則請選擇 [否]。 
4. 確認按一下 [儲存] 儲存設定。  
   ![替代映像文字](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. 如果您想要檢查並確認哪些延伸模組會在此 VM 上，請按一下 [延伸模組]。 如果 MSI 已啟用，則 **ManagedIdentityExtensionforWindows** 會出現在清單中。

   ![替代映像文字](media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

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

Azure Data Lake Store 原生支援 Azure AD 驗證，因此可以直接接受使用 MSI 取得的存取權杖。  為了向 Data Lake Store 檔案系統驗證，您在授權標頭中傳送由 Azure AD 所簽發的存取權杖至 Data Lake Store 檔案系統端點，其格式如下："Bearer <ACCESS_TOKEN_VALUE>"。  若要深入了解 Data Lake Store 的 Azure AD 驗證支援，請閱讀[使用 Azure Active Directory 向 Data Lake Store 進行驗證](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory)

> [!NOTE]
> Data Lake Store 檔案系統用戶端 SDK 目前尚不支援受管理的服務識別。  待 SDK 新增支援後，將會更新本教學課程。

在本教學課程中，您使用 PowerShell 向 Data Lake Store 檔案系統 REST API 驗證，以提出 REST 要求。 若要使用 VM MSI 進行驗證，您需要從 VM 提出要求。

1. 在入口網站中，瀏覽至 [虛擬機器] 並移至您的 Windows VM，在 [概觀] 中按一下 [連線]。
2. 輸入您建立 Windows VM 時新增的**使用者名稱**和**密碼**。 
3. 現在您已經建立虛擬機器的**遠端桌面連線**，請在遠端工作階段中開啟 **PowerShell**。 
4. 使用 PowerShell 的 `Invoke-WebRequest`，向本機 MSI 端點提出要求取得 Azure Data Lake Store 的存取權杖。  Data Lake Store 的資源識別項是 "https://datalake.azure.net/"。  Data Lake 會對資源識別碼執行完全相符的比對，因此結尾的斜線很重要。

   ```powershell
   $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://datalake.azure.net/"} -Headers @{Metadata="true"}
   ```
    
   將來自 JSON 物件的回應轉換為 PowerShell 物件。 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   擷取回應中的存取權杖。
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. 使用 PowerShell 的 `Invoke-WebRequest' 對您的 Data Lake Store REST 端點提出要求，以列出根資料夾中的資料夾。  這是檢查所有項目是否正確設定的簡單方式。  授權標頭中的字串 "Bearer" 必須是大寫 "B"。  您可以在 Azure 入口網站 Data Lake Store 刀鋒視窗的 [概觀] 區段中找到您的 Data Lake Store 名稱。

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   成功的回應看起來會像這樣：

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. 現在您可以嘗試將檔案上傳至您的 Data Lake Store。  首先，建立要上傳的檔案。

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. 使用 PowerShell 的 `Invoke-WebRequest` 對您的 Data Lake Store REST 端點提出要求，以將檔案上傳到您先前建立的資料夾。  此要求分為兩個步驟。  在第一個步驟中，您提出要求，並重新導向至應上傳檔案的位置。  第二個步驟中，您實際上傳檔案。  如果您使用的資料夾和檔案名稱與本教學課程中的不同，請記得適當地設定這些值。 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   如果您檢查 `$HdfsRedirectResponse` 的值，它看起來應該像下列回應：

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   將要求傳送至重新導向端點以完成上傳：

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   成功的回應看起來會像這樣：

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
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