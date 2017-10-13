---
title: "使用 Windows VM MSI 存取 Azure Resource Manager"
description: "此教學課程引導您使用 Windows VM 受管理的服務身分識別 (MSI) 來存取 Azure Resource Manager 的程序。"
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
ms.date: 09/15/2017
ms.author: elkuzmen
ms.openlocfilehash: 064843d3118c36c63c3024f5cd5d08ba4d65eb7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-resource-manager"></a>使用 Windows VM 受管理的服務身分識別 (MSI) 來存取 Azure Resource Manager

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

本教學課程會示範如何為 Windows 虛擬機器 (VM) 啟用受管理的服務身分識別 (MSI)。 接著，您便可以使用該身分識別來存取 Azure Resource Manager API。 受管理的服務身分識別由 Azure 自動管理，並可讓您驗證支援 Azure AD 驗證的服務，而不需要將認證插入程式碼中。 您會了解如何：

> [!div class="checklist"]
> * 在 Windows VM 上啟用 MSI 
> * 在 Azure Resource Manager 中將您的 VM 存取權授與資源群組 
> * 使用 VM 身分識別取得存取權杖，並使用它來呼叫 Azure Resource Manager


如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="sign-in-to-azure"></a>登入 Azure
登入 Azure 入口網站，位址是 [https://portal.azure.com](https://portal.azure.com)。

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>在新的資源群組中建立 Windows 虛擬機器

此教學課程中，我們會建立新的 Windows VM。  您也可以在現有的 VM 中啟用 MSI。

1.  按一下 Azure 入口網站左上角的 [新增] 按鈕。
2.  選取 [計算]，然後選取 [Windows Server 2016 Datacenter]。 
3.  輸入虛擬機器資訊。 在此建立的**使用者名稱**和**密碼**是您登入虛擬機器要使用的認證。
4.  在下拉式清單中選擇適用於虛擬機器的適當**訂用帳戶**。
5.  若要選取要在其中建立虛擬機器的新 [資源群組]，請選擇 [新建]。 完成時，按一下 [確定]。
6.  選取 VM 的大小。 若要查看更多大小，請選取 [檢視全部] 或變更 [支援的磁碟類型] 篩選條件。 在 [設定] 頁面上，保留預設值並按一下 [確定]。

    ![替代映像文字](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>在您的 VM 上啟用 MSI 

VM MSI 可讓您從 Azure AD 取得存取權杖，而不需要將憑證放入您的程式碼。 啟用 MSI 會告訴 Azure 為您的 VM 建立受管理的身分識別。 實際上，啟用 MSI 會執行兩項工作：在您的 VM 上安裝 MSI VM 延伸模組，並在 Azure Resource Manager 中啟用 MSI。

1.  選取您想要在其中啟用 MSI 的 [虛擬機器]。  
2.  在左側的導覽列上，按一下 [設定] 。 
3.  您會看到**受管理的服務識別**。 若要註冊並啟用 MSI，請選取 [是]，如果您想要將它停用，則請選擇 [否]。 
4.  確認按一下 [儲存] 儲存設定。  
    ![替代映像文字](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. 如果您想要檢查並確認哪些延伸模組會在此 VM 上，請按一下 [延伸模組]。 如果 MSI 已啟用，則 **ManagedIdentityExtensionforWindows** 會出現在清單中。

    ![替代映像文字](media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-resource-group-in-resource-manager"></a>在 Resource Manager 中將您的 VM 存取權授與資源群組
您的程式碼可以使用 MSI 來取得存取權杖，來向支援 Azure AD 驗證的資源進行驗證。  Azure Resource Manager 支援 Azure AD 驗證。  首先，我們需要在 Resource Manager 中將這個 VM 的身分識別存取權授與資源，此情況下就是包含 VM 的資源群組。  

1.  瀏覽至**資源群組**的索引標籤。 
2.  選取您為 **Windows VM**所建立的特定 [資源群組]。 
3.  前往左側面板的 [存取控制 (IAM)]。 
4.  然後 [新增] **Windows VM**的新角色指派。  選擇 [角色] 為 [讀取者]。 
5.  在下一個下拉式清單中，將**存取權指派給**資源 [虛擬機器]。 
6.  接下來，確認適當的訂用帳戶已列在 [訂用帳戶]下拉式清單中。 針對 [資源群組]，請選取 [所有資源群組]。 
7.  最後，在 [選取] 中選擇下拉式清單中您的 Windows VM，然後按一下 [儲存]。

    ![替代映像文字](media/msi-tutorial-windows-vm-access-arm/msi-windows-permissions.png)

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-resource-manager"></a>使用 VM 身分識別取得存取權杖，並使用它來呼叫 Azure Resource Manager 

在這裡您必須使用 **PowerShell**。  如果您尚未安裝，請在[這裡](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1)下載。 

1.  在入口網站中，瀏覽至 [虛擬機器] 並移至您的 Windows 虛擬機器，在 [概觀] 中按一下 [連線]。 
2.  輸入您建立 Windows VM 時新增的**使用者名稱**和**密碼**。 
3.  現在您已經建立虛擬機器的**遠端桌面連線**，請在遠端工作階段中開啟 **PowerShell**。 
4.  使用 Powershell 的 Invoke-WebRequest，向本機 MSI 端點提出要求來取得 Azure Resource Manager 的存取權杖。

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > 「資源」參數的值必須完全符合 Azure AD 的預期。 當使用 Azure Resource Manager 資源 ID 時，必須在 URI 中包含結尾的斜線。
    
    接下來，擷取完整的回應，它會儲存為 $response 物件中的 JavaScript 物件標記法 (JSON) 格式字串。 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
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
    > URL 區分大小寫，因此請確定您使用的是稍早在命名資源群組時，您所使用的相同大小寫，而且 "resourceGroups" 中的 "G" 為大寫。
        
    下列命令會傳回資源群組的詳細資料：

    ```powershell
    {"id":"/subscriptions/98f51385-2edc-4b79-bed9-7718de4cb861/resourceGroups/DevTest","name":"DevTest","location":"westus","properties":{"provisioningState":"Succeeded"}}
    ```

## <a name="related-content"></a>相關內容

- 如需 MSI 的概觀，請參閱[受管理的服務識別概觀](../active-directory/msi-overview.md)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。

