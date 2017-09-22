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
ms.date: 09/14/2017
ms.author: elkuzmen
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 9fec51528d1203a7107558408ced42fa2b2b884a
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---


# <a name="use-managed-service-identity-msi-with-a-linux-vm-to-access-storage-credentials"></a>使用 Linux VM 的受管理服務身分識別 (MSI) 來存取儲存體認證

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

本教學課程會示範如何為 Linux 虛擬機器 (VM) 啟用受管理的服務身分識別 (MSI)，並使用身分識別存取儲存體金鑰。 在執行儲存作業時 (例如使用儲存體 SDK)，您可以如往常般使用儲存體金鑰。 在此教學課程中，我們將使用 Azure CLI 上傳和下載 blob。 您將了解如何：


> [!div class="checklist"]
> * 在 Linux 虛擬機器上啟用 MSI 
> * 建立新的儲存體帳戶
> * 將您的 VM 存取權授與儲存體
> * 使用 VM 身分識別取得儲存體帳戶的存取權杖來存取儲存體金鑰 


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
6. 選取 VM 的大小。 若要查看更多大小，請選取 [檢視全部] 或變更 [支援的磁碟類型] 篩選條件。 在 [設定] 刀鋒視窗上，保留預設值並按一下 [確定]。

## <a name="enable-msi-on-your-vm"></a>在您的 VM 上啟用 MSI

虛擬機器 MSI 可讓您從 Azure AD 取得存取權杖，而不需要將憑證放入您的程式碼。 實際上，啟用 MSI 會執行兩項工作：在您的 VM 上安裝 MSI VM 延伸模組，並啟用 VM 的受管理服務識別。  

1. 選取您想要在其中啟用 MSI 的 [虛擬機器]。
2. 在左側的導覽列上，按一下 [設定] 。
3. 您會看到**受管理的服務識別**。 若要註冊並啟用 MSI，請選取 [是]，如果您想要將它停用，則請選擇 [否]。
4. 確認按一下 [儲存] 儲存設定。

    ![替代映像文字](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. 如果您想要檢查哪些延伸模組會在此 **Linux VM** 上，請按一下 [延伸模組]。 如果 MSI 已啟用，則 **ManagedIdentityExtensionforLinux** 會出現在清單中。

    ![替代映像文字](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)


## <a name="create-a-new-storage-account"></a>建立新的儲存體帳戶 

在執行儲存作業時，您可以如往常般使用儲存體金鑰，在此範例中我們將著重在使用 Azure CLI 上傳和下載 blob。 

1. 瀏覽至提要欄位並選取 [儲存體]。  
2. 建立新的 [儲存體帳戶]。  
3. 在 [部署模型] 中，輸入 [Resource Manager] 和 [帳戶類型]，並附帶 [一般用途]。  
4. 請確定 [訂用帳戶] 和 [資源群組] 是您在上述步驟中建立 [Linux 虛擬機器] 時所使用的。

    ![替代映像文字](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="grant-your-vm-identity-access-to-use-storage-keys"></a>授與您 VM 的身分識別存取權以使用儲存體金鑰 

您的程式碼可以使用 MSI 來取得存取權杖，來向支援 Azure AD 驗證的資源進行驗證。   

1. 瀏覽至 [儲存體] 的索引標籤。  
2. 選取您稍早建立的特定 [儲存體帳戶]。   
3. 前往左側面板的 [存取控制 (IAM)]。  
4. 然後 [新增] 您 VM 的新角色指派，挑選 [角色] 為 [儲存體帳戶金鑰運算子服務角色]。  
5. 在下一個下拉式清單中，將**存取權指派給**資源 [虛擬機器]。  
6. 接下來，確認適當的訂用帳戶已列在 [訂用帳戶]下拉式清單中。 針對 [資源群組]，請選取 [所有資源群組]。  
7. 最後，在 [選取] 中選擇下拉式清單中您的 Linux 虛擬機器，然後按一下 [儲存]。 
    ![替代映像文字](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>使用 VM 身分識別取得存取權杖，並使用它來呼叫 Azure Resource Manager

您必須使用 Bash 終端機，在[這裡](https://msdn.microsoft.com/commandline/wsl/install_guide)挑選並下載您的 Linux 散發套件。


1. 在入口網站中，瀏覽至 [Linux 虛擬機器]，並在 [概觀]中按一下 [連線]。 系統會提示您使用 Bash，請記下警示中的 SSH 和 VM IP 。 
2. 開啟並連線到 Bash。  
3. 在終端機中，輸入您想要連線的 [SSH] 和 [VM]，例如 "ssh admin@12.61.219.35"。  
4. 接下來，系統會提示您輸入建立 Linux VM 時新增的 [密碼]。 您應該可以順利登入。  
5. 然後，您可以使用 CURL 提出要求，取得已登入的 Linux VM 的授權權杖。 **Azure Resource Manager** 端點是 https://management.azure.com。  

    存取權杖的 CURL 要求如下：
    
    ```bash
    curl --data "authority= https://login.microsoftonline.com/<TENANT ID>&&resource=https://management.azure.com/"  -H Metadata:true http://localhost:50432/oauth2/token   
    ```
    
    > [!NOTE]
    > 請確認您嘗試要求存取權限的資源 URL 包含正確的格式，如結尾的斜線 'https:<RESOURCE>/'
    
    ```powershell
    {"access_token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyIsImtpZCI6IkhIQnlLVS0wRHFBcU1aaDZaRlBkMlZXYU90ZyJ9.eyJhdWQiOiJodHRwczovL21hbmFnZW1lbnQuYXp1cmUuY29tIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsImlhdCI6MTUwNDEyNjYyNywibmJmIjoxNTA0MTI2NjI3LCJleHAiOjE1MDQxMzA1MjcsImFpbyI6IlkyRmdZTGg2dENWSzRkSDlGWGtuZzgyQ21ZNVdBZ0E9IiwiYXBwaWQiOiI2ZjJmNmU2OS04MGExLTQ3NmEtOGRjZi1mOTgzZDZkMjUxYjgiLCJhcHBpZGFjciI6IjIiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwib2lkIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwic3ViIjoiMTEyODJiZDgtMDNlMi00NGVhLTlmYjctZTQ1YjVmM2JmNzJlIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoib0U5T3JVZFJMMHVKSEw4UFdvOEJBQSIsInZlciI6IjEuMCJ9.J6KS7b9kFgDkegJ-Vfff19LMnu3Cfps4dL2uNGucb5M76rgDM5f73VO-19wZSRhQPxWmZLETzN3SljnIMQMkYWncp79MVdBud_xqXYyLdQpGkNinpKVJhTo1j1dY27U_Cjl4yvvpBTrtH3OX9gG0GtQs7PBFTTLznqcH3JR9f-bTSEN4wUhalaIPHPciVDtJI9I24_vvMfVqxkXOo6gkL0mEP"}
     ```
    
## <a name="the-curl-request-to-get-storage-keys-from-azure-resource-manager"></a>從 Azure Resource Manager 取得儲存體金鑰的 CURL 要求  

> [!NOTE]
> 在 URL 中的文字區分大小寫，因此請確定您使用的資源群組大小寫有正確相應。 此外，務必知道這是 POST 要求而不是 GET 要求，並確定您傳遞的值符合 -d 的長度限制，且 -d 可能是 NULL。  

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 –request POST -d"" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

CURL 回應提供索引鍵的清單：  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNtXcluBiFSV2Fs+JTj6c+VK5wWB4RWUVYw2Y5BkRQu21cRpAztt3S19bAowhGPqJrAfMr3gn89Ui9VPbtA=="},{"keyName":"key2","permissions":"Full","value":"U+uI0B1Iygw1bDK6Ic1VWKMMY77ac0VV3kqYaCW7FcUFiuw4UkLfEKbnF2sZpIfFR9zUWniLBhlt+eUn/TeSJg=="}]} 
```


建立要上傳檔案，這將會是範例 blob 檔案，您可以在您建立的儲存體帳戶容器內透過儲存體金鑰上傳。 

在 Linux VM 上，您可以使用下列命令來執行此操作。 

```bash
echo "This is a test file." > test.txt
```
 接下來，您可以使用 Azure CLI 上傳檔案並透過儲存體金鑰進行驗證。
 

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


