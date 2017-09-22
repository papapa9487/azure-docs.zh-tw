---
title: "使用 Windows VM MSI 存取 Azure 儲存體"
description: "此教學課程引導您使用 Windows VM 受管理的服務身分識別 (MSI) 來存取 Azure 儲存體的程序。"
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
ms.openlocfilehash: 86d88e3d44f442171f69d0baea5e7d689b963277
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---

# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-storage"></a>使用 Windows VM 受管理的服務身分識別 (MSI) 來存取 Azure 儲存體

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

本教學課程會示範如何為 Linux 虛擬機器 (VM) 啟用受管理的服務身分識別 (MSI)，並使用身分識別存取儲存體金鑰。 在執行儲存作業時 (例如使用儲存體 SDK)，您可以如往常般使用儲存體金鑰。 在此教學課程中，我們將使用 Azure CLI 上傳和下載 blob。 您將了解如何：


> [!div class="checklist"]
> * 在 Windows 虛擬機器上啟用 MSI 
> * 建立新的儲存體帳戶
> * 將您的 VM 存取權授與儲存體 
> * 使用 VM 身分識別取得儲存體帳戶的存取權杖 


如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站，位址是 [https://portal.azure.com](https://portal.azure.com)。

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>在新的資源群組中建立 Windows 虛擬機器

此教學課程中，我們會建立新的 Windows VM。 您也可以在現有的 VM 中啟用 MSI。

1.  按一下 Azure 入口網站左上角的 [新增] 按鈕。
2.  選取 [計算]，然後選取 [Windows Server 2016 Datacenter]。 
3.  輸入虛擬機器資訊。 在此建立的**使用者名稱**和**密碼**是您登入虛擬機器要使用的認證。
4.  在下拉式清單中選擇適用於虛擬機器的適當**訂用帳戶**。
5.  若要選取要在其中建立虛擬機器的新 [資源群組]，請選擇 [新建]。 完成時，按一下 [確定]。
6.  選取 VM 的大小。 若要查看更多大小，請選取 [檢視全部] 或變更 [支援的磁碟類型] 篩選條件。 在 [設定] 刀鋒視窗上，保留預設值並按一下 [確定]。

    ![替代映像文字](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>在您的 VM 上啟用 MSI

虛擬機器 MSI 可讓您從 Azure AD 取得存取權杖，而不需要將憑證放入您的程式碼。 實際上，啟用 MSI 會執行兩項工作：在您的 VM 上安裝 MSI VM 延伸模組，並啟用虛擬機器的 MSI。  

1. 選取您想要在其中啟用 MSI 的**虛擬機器**。
2. 在左側的導覽列上，按一下 [設定]。
3. 您會看到**受管理的服務識別**。 若要註冊並啟用 MSI，請選取 [是]，如果您想要將它停用，則請選擇 [否]。
4. 按一下 [儲存] 確認儲存設定。

    ![替代映像文字](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. 如果您想要檢查哪些延伸模組會在此 **Windows VM** 上，請按一下 [延伸模組]。 如果 MSI 已啟用，則 **ManagedIdentityExtensionforWindows** 會出現在清單中。

    ![替代映像文字](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-new-storage-account"></a>建立新的儲存體帳戶 

在執行儲存作業時，您可以如往常般使用儲存體金鑰，在此範例中我們將著重在使用 Azure CLI 上傳和下載 blob。 

1. 瀏覽至提要欄位並選取 [儲存體]。  
2. 建立新的 [儲存體帳戶]。  
3. 在 [部署模型] 中輸入 **Resource Manager**，以及在 [帳戶類型] 中輸入**一般用途**。  
4. 請確定 [訂用帳戶] 和 [資源群組] 是您在上述步驟中建立 [Linux 虛擬機器] 時所使用的。

    ![替代映像文字](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="grant-your-vm-identity-access-to-use-storage-keys"></a>授與您 VM 的身分識別存取權以使用儲存體金鑰 

您的程式碼可以使用 MSI 來取得存取權杖，來向支援 Azure AD 驗證的資源進行驗證。   

1. 瀏覽至 [儲存體] 的索引標籤。  
2. 選取您稍早建立的特定 [儲存體帳戶]。   
3. 前往左側面板的 [存取控制 (IAM)]。  
4. 然後 [新增] 您 VM 的新角色指派，挑選 [角色] 作為 [儲存體帳戶金鑰運算子服務角色]。  
5. 在下一個下拉式清單中，將**存取權指派給**資源 [虛擬機器]。  
6. 接下來，確認適當的訂用帳戶已列在 [訂用帳戶] 下拉式清單中。 針對 [資源群組]，請選取 [所有資源群組]。  
7. 最後，在 [選取] 中選擇下拉式清單中您的 Windows 虛擬機器，然後按一下 [儲存]。 

    ![替代映像文字](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>使用 VM 身分識別取得存取權杖，並使用它來呼叫 Azure Resource Manager 

在這裡您必須使用 **PowerShell**。  如果您尚未安裝，請在[這裡](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1)下載。 

1. 在入口網站中，瀏覽至 [虛擬機器] 並移至您的 Windows 虛擬機器，在 [概觀] 中按一下 [連線]。 
2. 輸入您建立 Windows VM 時新增的**使用者名稱**和**密碼**。 
3. 現在您已經建立虛擬機器的**遠端桌面連線**，請在遠端工作階段中開啟 **PowerShell**。 
4. 使用 Powershell 的 Invoke-WebRequest，向本機 MSI 端點提出要求來取得 Azure Resource Manager 的存取權杖。

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost/50342/oauth2/token -Method GET -Body @resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > 「資源」參數的值必須完全符合 Azure AD 的預期。 當使用 Azure Resource Manager 資源 ID 時，必須在 URI 中包含結尾的斜線。
    
    接下來，擷取完整的回應，它會儲存為 $response 物件中的 JavaScript 物件標記法 (JSON) 格式字串。 
    
    ```powershell
    $content = $repsonse.Content | ConvertFrom-Json
    ```
    再來，從回應中擷取存取權杖。
    
    ```powershell
    $ArmToken = $content.access_token
    ```
    
    最後，使用存取權杖呼叫 Azure Resource Manager。 在此範例中，我們也將使用 PowerShell 的 Invoke-WebRequest 進行呼叫 Azure Resource Manager，並將存取權杖包含在授權標頭中。
    
    ```powershell
    (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $ArmToken"}).content
    ```
    > [!NOTE]
    > URL 區分大小寫，因此請確定您使用的是稍早在命名資源群組時，您所使用的相同大小寫，而且 "resourceGroup" 中的 "G" 為大寫。
    
## <a name="get-the-storage-keys-from-azure-resource-manager"></a>從 Azure Resource Manager 取得儲存體金鑰 

```powershell
PS C:\> $keysResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/97f51385-2edc-4b69-bed8-7778dd4cb761/resourceGroups/SKwan_Test/providers/Microsoft.Storage/storageAccounts/skwanteststorage/listKeys/?api-version=2016-12-01 -Method POST$ -Headers @{Authorization="Bearer $ARMToken"}
```

```powershell
PS C:\> $keysContent = $keysResponse.Content | ConvertFrom-Json
```

```powershell
PS C:\> $key = $keysContent.keys[0].value
```

**使用 Azure CLI 建立要上傳的檔案**

```bash
echo "This is a test text file." > test.txt
```

**使用 Azure CLI 上傳檔案並透過儲存體金鑰進行驗證。**

> [!NOTE]
> 首先請記得安裝 Azure commandlet “Install-Module Azure.Storage”。 

PowerShell 要求：


```powershell
PS C:\> $ctx = New-AzureStorageContext -StorageAccountName skwanteststorage -StorageAccountKey $key
PS C:\> Set-AzureStorageBlobContent -File test.txt -Container testcontainer -Blob testblob -Context $ctx
```

回應：

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

**使用 Azure CLI 下載檔案並透過儲存體金鑰進行驗證。**

PowerShell 要求：

```powershell
PS C:\> Get-AzureStorageBlobContent -Blob <blob name> -Container <container name> -Destination <file> -Context $ctx
```

回應：

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/13/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```





