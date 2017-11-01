---
title: "使用 Windows VM MSI 存取 Azure Key Vault"
description: "此教學課程引導您使用 Windows VM 受管理的服務身分識別 (MSI) 來存取 Azure Key Vault 的程序。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2017
ms.author: elkuzmen
ms.openlocfilehash: e3f9fa3e543851e79d9aed9c80ae4a8d2dd3420d
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>使用 Windows VM 受管理的服務身分識別 (MSI) 來存取 Azure Key Vault 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

本教學課程會示範如何為 Windows 虛擬機器啟用受管理的服務識別 (MSI)，然後使用該識別來存取 Azure Key Vault。 作為啟動程序，金鑰保存庫可讓您的用戶端應用程式，接著使用密碼存取未受 Azure Active Directory (AD) 保護的資源。 受管理的服務識別由 Azure 自動管理，並可讓您驗證支援 Azure AD 驗證的服務，而不需要將認證插入程式碼中。 

您會了解如何：


> [!div class="checklist"]
> * 在 Windows 虛擬機器上啟用受管理的服務身分識別 
> * 將您的 VM 存取權授與 Key Vault 中儲存的祕密 
> * 使用 VM 身分識別取得存取權杖，並使用它來擷取 Key Vault 的祕密 


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

虛擬機器 MSI 可讓您從 Azure AD 取得存取權杖，而不需要將憑證放入您的程式碼。 啟用 MSI 會告訴 Azure 為您的虛擬機器建立受管理的身分識別。 實際上，啟用 MSI 會執行兩項工作：在您的 VM 上安裝 MSI VM 延伸模組，並在 Azure Resource Manager 中啟用 MSI。

1.  選取您想要在其中啟用 MSI 的 [虛擬機器]。  
2.  在左側的導覽列上，按一下 [設定] 。 
3.  您會看到**受管理的服務識別**。 若要註冊並啟用 MSI，請選取 [是]，如果您想要將它停用，則請選擇 [否]。 
4.  按一下 [儲存] 確認儲存設定。  

    ![替代映像文字](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. 如果您想要檢查並確認哪些延伸模組會在此 VM 上，請按一下 [延伸模組]。 如果 MSI 已啟用，則 **ManagedIdentityExtensionforWindows** 會出現在清單中。

    ![替代映像文字](media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>授與 VM 存取權以取得 Key Vault 中的密碼 
 
您的程式碼可以使用 MSI 來取得存取權杖，來向支援 Azure AD 驗證的資源進行驗證。  但是，並非所有 Azure 服務都支援 Azure AD 驗證。 若要使用 MSI 搭配那些服務，請將服務認證儲存在 Azure Key Vault 中，並使用 MSI 存取 Key Vault 來擷取認證。 

首先，我們需要建立 Key Vault 並將 VM 的身分識別存取權授與 Key Vault。   

1. 在左側導覽列的頂端，選取 [+ 新增][安全性 + 身分識別] 然後 [Key Vault]。  
2. 提供新 Key Vault 的**名稱**。 
3. 在與 VM (稍早建立) 相同的訂用帳戶和資源群組中找到 Key Vault。 
4. 選取 [存取原則]，然後按一下 [新增]。 
5. 在 [從範本設定] 中，選取 [密碼管理]。 
6. 選擇 [選取主體]，並在 [搜尋] 欄位中輸入您稍早建立的 VM 名稱。  在結果清單中選取 VM，然後按一下 [選取]。 
7. 按一下 [確定] 來完成新增存取原則，和 [確定] 來完成存取原則選取。 
8. 按一下 [建立] 即可完成建立 Key Vault。 

    ![替代映像文字](media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


接下來，將密碼新增至 Key Vault，好讓您稍後可以使用在 VM 中執行的程式碼來擷取密碼： 

1. 選取 [所有資源]，然後尋找並選取您所建立的 Key Vault。 
2. 選取 [密碼]，然後按一下 [新增]。 
3. 從 [上傳選項] 中選取 [手動]。 
4. 輸入密碼的名稱和值。  值可以是任何您想要的項目。 
5. 將啟動日期和到期日期保留空白，並將 [啟用] 設定為 [是]。 
6. 按一下 [建立] 來建立密碼。 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>使用 VM 身分識別取得存取權杖，並使用它來擷取 Key Vault 的密碼  

如果您未安裝 PowerShell 4.3.1 或更新版本，則必須[下載並安裝最新版](https://docs.microsoft.com/powershell/azure/overview)。

首先，我們會使用 VM 的 MSI 來取得存取權杖來驗證 Key Vault：
 
1. 在入口網站中，瀏覽至 [虛擬機器] 並移至您的 Windows 虛擬機器，在 [概觀] 中按一下 [連線]。
2. 輸入您建立 **Windows VM** 時新增的**使用者名稱**和**密碼**。  
3. 現在您已經建立虛擬機器的**遠端桌面連線**，請在遠端工作階段中開啟 PowerShell。  
4. 在 PowerShell 中，叫用租用戶上的 Web 要求，以在 VM 的特定連接埠中取得本機主機的權杖。  

    PowerShell 要求：
    
    ```powershell
    PS C:\> $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://vault.azure.net"} -Headers @{Metadata="true"} 
    ```
    
    接下來，擷取完整的回應，它會儲存為 $response 物件中的 JavaScript 物件標記法 (JSON) 格式字串。  
    
    ```powershell
    PS C:\> $content = $response.Content | ConvertFrom-Json 
    ```
    
    再來，從回應中擷取存取權杖。  
    
    ```powershell
    PS C:\> $KeyVaultToken = $content.access_token 
    ```
    
    最後，使用 PowerShell 的 Invoke-WebRequest 命令來擷取您稍早在 Key Vault 中建立的祕密，在授權標頭中傳遞存取權杖。  您會需要 Key Vault 的 URL，其位於 Key Vault [概觀] 頁面的 [基本資訊]區段。  
    
    ```powershell
    PS C:\> (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    回應如下所示： 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
一旦您已從 Key Vault 擷取密碼，您便可以將其用來向需要名稱和密碼的服務進行驗證。 

## <a name="related-content"></a>相關內容

- 如需 MSI 的概觀，請參閱[受管理的服務識別概觀](../active-directory/msi-overview.md)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。
