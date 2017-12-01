---
title: "使用 Linux VM MSI 存取 Azure Resource Manager"
description: "此教學課程引導您使用 Linux VM 受管理的服務身分識別 (MSI) 來存取 Azure Resource Manager 的程序。"
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
ms.date: 11/20/2017
ms.author: bryanla
ms.openlocfilehash: cf69470beb8e466ba0b9807d8bff5813a88b7c41
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2017
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-resource-manager"></a>使用 Linux VM 受管理的服務身分識別 (MSI) 來存取 Azure Resource Manager

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

本教學課程會示範如何為 Linux 虛擬機器啟用受管理的服務身分識別 (MSI)，並使用身分識別存取 Azure Resource Manager API。 受管理的服務身分識別由 Azure 自動管理，並可讓您驗證支援 Azure AD 驗證的服務，而不需要將認證插入程式碼中。 您會了解如何：

> [!div class="checklist"]
> * 在 Linux 虛擬機器上啟用 MSI 
> * 在 Azure Resource Manager 中將您的 VM 存取權授與資源群組 
> * 使用 VM 身分識別取得存取權杖，並使用它來呼叫 Azure Resource Manager 

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

虛擬機器 MSI 可讓您從 Azure AD 取得存取權杖，而不需要將憑證放入您的程式碼。 實際上，啟用 MSI 會執行兩項工作：在您的 VM 上安裝 MSI VM 延伸模組，並啟用 VM 的 MSI。  

1. 選取您想要在其中啟用 MSI 的 [虛擬機器]。
2. 在左側的導覽列上，按一下 [設定] 。
3. 您會看到**受管理的服務識別**。 若要註冊並啟用 MSI，請選取 [是]，如果您想要將它停用，則請選擇 [否]。
4. 按一下 [儲存] 確認儲存設定。

    ![替代映像文字](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. 如果您想要檢查哪些延伸模組會在此 **Linux VM** 上，請按一下 [延伸模組]。 如果 MSI 已啟用，則 **ManagedIdentityExtensionforLinux** 會出現在清單上。

    ![替代映像文字](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-a-resource-group-in-azure-resource-manager"></a>在 Azure Resource Manager 中將您的 VM 存取權授與資源群組 

您的程式碼可以使用 MSI 來取得存取權杖，來向支援 Azure AD 驗證的資源進行驗證。 Azure Resource Manager API 支援 Azure AD 驗證。 首先，我們需要在 Azure Resource Manager 中將這個 VM 的身分識別存取權授與資源，此情況下就是包含 VM 的資源群組。  

1. 瀏覽至**資源群組**的索引標籤。
2. 選取您先前建立的特定 [資源群組]。
3. 前往左側面板的 [存取控制 (IAM)]。
4. 按一下以 [新增] VM 的新角色指派。 選擇 [角色] 為 [讀取者]。
5. 在下一個下拉式清單中，將**存取權指派給**資源 [虛擬機器]。
6. 接下來，確認適當的訂用帳戶已列在 [訂用帳戶]下拉式清單中。 針對 [資源群組]，請選取 [所有資源群組]。
7. 最後，在 [選取] 中選擇下拉式清單中您的 Linux 虛擬機器，然後按一下 [儲存]。

    ![替代映像文字](media/msi-tutorial-linux-vm-access-arm/msi-permission-linux.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>使用 VM 身分識別取得存取權杖，並使用它來呼叫 Resource Manager 

若要完成這些步驟，您需要 SSH 用戶端。 如果您使用 Windows，您可以在[適用於 Linux 的 Windows 子系統](https://msdn.microsoft.com/commandline/wsl/about)中使用 SSH 用戶端。 如果您需要設定 SSH 用戶端金鑰的協助，請參閱[如何在 Azure 上搭配 Windows 使用 SSH 金鑰](../virtual-machines/linux/ssh-from-windows.md)，或[如何在 Azure 中建立和使用 Linux VM 的 SSH 公開和私密金鑰組](../virtual-machines/linux/mac-create-ssh-keys.md)。

1. 在入口網站中，瀏覽至 [Linux VM]，並在 [概觀] 中按一下 [連線]。  
2. 使用您所選擇的 SSH 用戶端來**連線**到 VM。 
3. 在終端機視窗中使用 CURL，向本機 MSI 端點提出要求來取得 Azure Resource Manager 的存取權杖。  
 
    存取權杖的 CURL 要求如下。  
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true   
    ```
    
    > [!NOTE]
    > 「資源」參數的值必須完全符合 Azure AD 的預期。  當使用 Resource Manager 資源 ID 時，必須在 URI 中包含結尾的斜線。 
    
    此回應包含您存取 Azure Resource Manager 所需的存取權杖。 
    
    回應：  

    ```bash
    {"access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
    ```
    
    您可以使用此存取權杖來存取 Azure Resource Manager，例如讀取您先前授與此 VM 存取的資源群組詳細資料。 將 \<SUBSCRIPTION ID\>、\<RESOURCE GROUP\> 和 \<ACCESS TOKEN\> 的值以您稍早建立的值取代。 
    
    > [!NOTE]
    > URL 區分大小寫，因此請確定您使用的是稍早在命名資源群組時所使用的相同大小寫，而且 “resourceGroup” 中的 “G” 為大寫。  
    
    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    傳回的回應含有特定的資源群組資訊： 
     
    ```bash
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}} 
    ```
     
## <a name="related-content"></a>相關內容

- 如需 MSI 的概觀，請參閱[受管理的服務識別概觀](../active-directory/msi-overview.md)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。

