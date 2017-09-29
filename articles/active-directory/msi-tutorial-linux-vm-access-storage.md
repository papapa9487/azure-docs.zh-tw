---
title: "使用 Linux VM MSI 存取 Azure 儲存體"
description: "此教學課程引導您使用 Linux VM 受管理的服務身分識別 (MSI) 來存取 Azure 儲存體的程序。"
services: active-directory
documentationcenter: 
author: elkuzmen
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: elkuzmen
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 8e4b3466143dc8b19df399913baca864b0af9bc0
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---


# <a name="use-a-linux-vm-managed-service-identity-to-access-azure-storage"></a>使用 Linux VM 受管理的服務身分識別存取 Azure 儲存體

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

本教學課程會示範如何為 Linux 虛擬機器 (VM) 啟用受管理的服務身分識別 (MSI)，並使用身分識別存取儲存體金鑰。 在執行儲存作業時 (例如使用儲存體 SDK)，您可以如往常般使用儲存體金鑰。 在此教學課程中，我們將使用 Azure CLI 上傳和下載 Blob。 您將了解如何：


> [!div class="checklist"]
> * 在 Linux 虛擬機器上啟用 MSI 
> * 在 Resource Manager 中將您的 VM 存取權授與儲存體金鑰
> * 使用 VM 身分識別取得存取權杖，並使用它從 Resource Manager 擷取儲存體金鑰 


如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

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

虛擬機器 MSI 可讓您從 Azure AD 取得存取權杖，而不需要將憑證放入您的程式碼。 實際上，啟用 MSI 會執行兩項工作：在您的 VM 上安裝 MSI VM 延伸模組，並啟用 VM 的受管理服務識別。  

1. 巡覽 至新虛擬機器的資源群組，並選取您在上一個步驟中建立的虛擬機器。
2. 在左側的 [VM 設定] 下，按一下 [組態]。
3. 若要註冊並啟用 MSI，請選取 [是]，如果您想要將它停用，則請選擇 [否]。
4. 按一下 [儲存] 確認儲存設定。

    ![替代映像文字](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. 如果您想要檢查哪些延伸模組會在此 VM 上，請按一下 [延伸模組]。 如果 MSI 已啟用，則 **ManagedIdentityExtensionforLinux** 會出現在清單中。

    ![替代映像文字](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>建立儲存體帳戶 

如果您還沒有帳戶，您現在將建立一個儲存體帳戶。  您也可以略過此步驟，並將您的 VM MSI 存取權授與現有儲存體帳戶的金鑰。 

1. 按一下 Azure 入口網站左上角的 [新增] 按鈕。
2. 按一下 [儲存體]，然後按一下 [儲存體帳戶]，就會顯示新的 [建立儲存體帳戶] 面板。
3. 輸入儲存體帳戶的 [名稱]，您稍後將會使用它。  
4. [部署模型] 和 [帳戶類型] 應該分別設定為「資源管理員」和「一般用途」。 
5. 確定 [訂用帳戶] 和 [資源群組] 符合您在上一個步驟中建立 VM 時指定的值。
6. 按一下 [建立] 。

    ![建立新的儲存體帳戶](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>在儲存體帳戶中建立 Blob 容器

稍後我們將上傳和下載檔案到新的儲存體帳戶。 由於檔案需要 Blob 儲存體，我們需要建立 Blob 容器，用來儲存檔案。

1. 巡覽回到您新建立的儲存體帳戶。
2. 按一下左側導覽列中 [Blob 服務] 下的 [容器] 連結。
3. 按一下頁面上方的 [+ 容器]，[新的容器] 面板隨即會滑出。
4. 指定容器的名稱，選取存取層級，然後按一下 [確定]。 稍後在教學課程中將會用到您指定的名稱。 

    ![建立儲存體容器](media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-identity-access-to-use-storage-keys"></a>授與您 VM 的身分識別存取權以使用儲存體金鑰 

Azure 儲存體原生並不支援 Azure AD 驗證。  不過，您可以使用 MSI 從 Resource Manager 擷取儲存體金鑰，並使用這些金鑰來存取儲存體。  在此步驟中，您會將您的 VM MSI 存取權授與儲存體帳戶的金鑰。   

1. 瀏覽至 [儲存體] 的索引標籤。  
2. 選取您稍早建立的特定 [儲存體帳戶]。   
3. 前往左側面板的 [存取控制 (IAM)]。  
4. 然後 [新增] 您 VM 的新角色指派，挑選 [角色] 作為 [儲存體帳戶金鑰運算子服務角色]。  
5. 在下一個下拉式清單中，將**存取權指派給**資源 [虛擬機器]。  
6. 接下來，確認適當的訂用帳戶已列在 [訂用帳戶] 下拉式清單中。 針對 [資源群組]，請選取 [所有資源群組]。  
7. 最後，在 [選取] 中選擇下拉式清單中您的 Linux 虛擬機器，然後按一下 [儲存]。 
    ![替代映像文字](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>使用 VM 的身分識別取得存取權杖，並使用它來呼叫 Azure Resource Manager

若要完成這些步驟，您需要 SSH 用戶端。 如果您使用 Windows，您可以在[適用於 Linux 的 Windows 子系統](https://msdn.microsoft.com/commandline/wsl/install_guide)中使用 SSH 用戶端。

1. 在入口網站中，瀏覽至 [Linux 虛擬機器]，並在 [概觀]中按一下 [連線]。 系統會提示您使用 Bash，請記下警示中的 SSH 和 VM IP 。 
2. 開啟並連線到 Bash。  
3. 在終端機中，輸入您想要連線的 [SSH] 和 [VM]，例如 "ssh admin@12.61.219.35"。  
4. 接下來，系統會提示您輸入建立 Linux VM 時新增的 [密碼]。 您應該可以順利登入。  
5. 使用 CURL 取得 Azure Resource Manager 的存取權杖。  

    存取權杖的 CURL 要求如下：
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true    
    ```
    
    > [!NOTE]
    > 「資源」參數的值必須完全符合 Azure AD 的預期。 當使用 Azure Resource Manager 資源 ID 時，必須在 URI 中包含結尾的斜線。
    
    ```bash
    {"access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyIsImtpZCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsImlhdCI6MTUwNDEyNjYyNywibmJmIjoxNTA0MTI2NjI3LCJleHAiOjE1MDQxMzA1MjcsImFpbyI6IlkyRmdZTGg2dENWSzRkSDlGWGtuZzgyQ21ZNVdBZ0E9IiwiYXBwaWQiOiI2ZjJmNmU2OS04MGExLTQ3NmEtOGRjZi1mOTgzZDZkMjUxYjgiLCJhcHBpZGFjciI6IjIiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwib2lkIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwic3ViIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoib0U5T3JVZFJMMHVKSEw4UFdvOEJBQSIsInZlciI6IjEuMCJ9.J6KS7b9kFgDkegJ-Vfff19LMnu3Cfps4dL2uNGucb5M76rgDM5f73VO-19wZSRhQPxWmZLETzN3SljnIMQMkYWncp79MVdBud_xqXYyLdQpGkNinpKVJhTo1j1dY27U_Cjl4yvvpBTrtH3OX9gG0GtQs7PBFTTLznqcH3JR9f-bTSEN4wUhalaIPHPciVDtJI9I24_vvMfVqxkXOo6gkL0mEP"}
     ```
    
## <a name="use-curl-to-get-storage-keys-from-azure-resource-manager"></a>使用 CURL 從 Azure Resource Manager 取得儲存體金鑰  

現在我們將利用在上一節中擷取的存取權杖，使用 CURL 對 Resource Manager 進行呼叫，以擷取儲存體存取金鑰。 一旦有了儲存體存取金鑰，我們便可呼叫儲存體進行上傳/下載作業。

> [!NOTE]
> 在 URL 中的文字區分大小寫，因此請確定您使用的資源群組大小寫有正確相應。 此外，務必知道這是 POST 要求而不是 GET 要求，並確定您傳遞的值符合 -d 的長度限制，且 -d 可能是 NULL。  

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 –request POST -d"" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

CURL 回應提供索引鍵的清單：  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNtXcluBiFSV2Fs+JTj6c+VK5wWB4RWUVYw2Y5BkRQu21cRpAztt3S19bAowhGPqJrAfMr3gn89Ui9VPbtA=="},{"keyName":"key2","permissions":"Full","value":"U+uI0B1Iygw1bDK6Ic1VWKMMY77ac0VV3kqYaCW7FcUFiuw4UkLfEKbnF2sZpIfFR9zUWniLBhlt+eUn/TeSJg=="}]} 
```


建立範例 Blob 檔案以上傳至您的 Blob 儲存體容器。 在 Linux VM 上，您可以使用下列命令來執行此操作。 

```bash
echo "This is a test file." > test.txt
```

接下來，使用 Azure CLI 上傳檔案並透過儲存體金鑰進行驗證。
 

```azurecli-interactive
 az storage blob upload --container-name 
                        --file 
                        --name 
                        [--account-name] 
                        [--account-key] 
```

回應： 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

此外，您可以使用 Azure CLI 下載檔案並透過儲存體金鑰進行驗證。 

要求： 

```azurecli-interactive
az storage blob download --container-name
                         --file 
                         --name 
                         [--account-name]
                         [--account-key]  
```

回應： 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "testblob",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 16,
    "contentRange": "bytes 0-15/16",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "Aryr///Rb+D8JQ8IytleDA==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D4F9929765C139\"",
    "lastModified": "2017-09-12T03:58:56+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="related-content"></a>相關內容

如需 MSI 的概觀，請參閱[受管理的服務識別概觀](../active-directory/msi-overview.md)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。


