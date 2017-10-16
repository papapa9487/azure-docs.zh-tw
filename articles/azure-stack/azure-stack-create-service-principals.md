---
title: "為 Azure Stack 建立服務主體 | Microsoft Docs"
description: "描述如何建立可以與 Azure Resource Manager 中的角色型存取控制搭配使用來管理資源存取權的新服務主體。"
services: azure-resource-manager
documentationcenter: na
author: heathl17
manager: byronr
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 5787b25fb1dd7331e561798152678ed187e24d54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="provide-applications-access-to-azure-stack"></a>為 Azure Stack 提供應用程式存取

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

當應用程式需要存取權透過 Azure Resource Manager 在 Azure Stack 中部署或設定資源時，您會建立服務主體，這是您的應用程式的認證。  然後，您可以只對該服務主體委派必要權限。  

舉例來說，您可能有使用 Azure Resource Manager 來清查 Azure 資源的組態管理工具。  在此案例中，您可以建立服務主體、為該服務主體授與讀取者角色，以及將組態管理工具限制在唯讀存取權。 

以您自己的認證執行應用程式最好是使用服務主體，因為：

* 您可以對服務主體指派不同於您自己帳戶權限的權限。 一般而言，這些權限只會限制為 App 必須執行的確切權限。
* 如果您的職責變更，就不需要變更 App 的認證。
* 您可以使用憑證在執行自動指令碼時自動進行驗證。  

## <a name="getting-started"></a>開始使用

根據您部署 Azure Stack 的方式，您會從建立服務主體開始。  此文件會引導您進行為 [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad) 和 [Active Directory Federation Services (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs) 建立服務主體的程序。  一旦您已建立服務主體，會使用 AD FS 與 Azure Active Directory 的一組共通步驟來對角色[委派權限](azure-stack-create-service-principals.md#assign-role-to-service-principal)。     

## <a name="create-service-principal-for-azure-ad"></a>為 Azure AD 建立服務主體

如果您使用 Azure AD 做為身分識別存放區部署了 Azure Stack，則可以如同對 Azure 般建立服務主體。  本節說明如何透過入口網站執行這些步驟。  開始之前，請確認您有[必要的 Azure AD 權限](../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions)。

### <a name="create-service-principal"></a>建立服務主體
在本節中，您會在 Azure AD 中建立將代表您的應用程式的應用程式 (服務主體)。

1. 透過 [Azure 入口網站](https://portal.azure.com)登入 Azure 帳戶。
2. 選取 [Azure Active Directory] > [應用程式註冊] > [新增]   
3. 提供應用程式的名稱和 URL。 針對您想要建立的應用程式類型，選取 [Web 應用程式/API] 或 [原生]。 設定值之後，選取 [建立]。

您已建立應用程式的服務主體。

### <a name="get-credentials"></a>取得認證
以程式設計方式登入時，您會使用應用程式的識別碼和驗證金鑰。 若要取得這些值，請使用下列步驟︰

1. 在 Active Directory 中，從 [應用程式註冊]選取您的應用程式。

2. 複製 [應用程式識別碼] 並儲存在您的應用程式碼中。 [範例應用程式](#sample-applications) 區段中的應用程式會參考此值作為用戶端識別碼。

     ![用戶端識別碼](./media/azure-stack-create-service-principal/image12.png)
3. 若要產生驗證金鑰，請選取 [金鑰]。

4. 提供金鑰的描述和金鑰的持續時間。 完成時，選取 [儲存]。

儲存金鑰之後會顯示金鑰的值。 請複製此值，因為您之後就無法擷取金鑰。 您提供金鑰值和應用程式識別碼，以應用程式身分簽署。 將金鑰值儲存在應用程式可擷取的地方。

![儲存的金鑰](./media/azure-stack-create-service-principal/image15.png)


完成後，繼續進行[將您的應用程式指派至角色](azure-stack-create-service-principals.md#assign-role-to-service-principal)。

## <a name="create-service-principal-for-ad-fs"></a>為 AD FS 建立服務主體
如果您是使用 Azure Stack 部署 AD FS，則可以使用 PowerShell 來建立服務主體、指派用於存取的角色，以及從 PowerShell 使用該身分識別登入。

### <a name="before-you-begin"></a>開始之前

[下載與 Azure Stack 搭配運作所需的工具到您的本機電腦。](azure-stack-powershell-download.md)

### <a name="import-the-identity-powershell-module"></a>匯入身分識別 PowerShell 模組
下載工具之後，瀏覽至 [下載] 資料夾，並使用下列命令來匯入身分識別 PowerShell 模組：

```PowerShell
Import-Module .\Identity\AzureStack.Identity.psm1
```

匯入模組時，您可能會收到錯誤指出「AzureStack.Connect.psm1 未經過數位簽署。 這個指令碼將不會在系統上執行」。 若要解決此問題，您可以在提升權限的 PowerShell 工作階段中使用下列命令來設定執行原則，以允許執行指令碼：

```PowerShell
Set-ExecutionPolicy Unrestricted
```

### <a name="create-the-service-principal"></a>建立服務主體
您可以藉由執行下列命令來建立服務主體，請務必更新 *DisplayName* 參數：
```powershell
$servicePrincipal = New-AzSADGraphServicePrincipal `
 -DisplayName "<YourServicePrincipalName>" `
 -AdminCredential $(Get-Credential) `
 -AdfsMachineName "AZS-ADFS01" `
 -Verbose
```
### <a name="assign-a-role"></a>指派角色
一旦建立服務主體，您必須[將它指派至角色](azure-stack-create-service-principals.md#assign-role-to-service-principal)

### <a name="sign-in-through-powershell"></a>透過 PowerShell 登入
一旦已指派角色，您可以使用服務主體搭配下列命令登入 Azure Stack：

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ApplicationId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>指派角色給服務主體
若要存取您的訂用帳戶中的資源，您必須將應用程式指派給角色。 決定哪個角色代表應用程式的正確權限。 若要深入了解可用的角色，請參閱 [RBAC：內建角色](../active-directory/role-based-access-built-in-roles.md)。

您可以針對訂用帳戶、資源群組或資源的層級設定範圍。 較低的範圍層級會繼承較高層級的權限。 例如，為資源群組的讀取者角色新增應用程式，代表該角色可以讀取資源群組及其所包含的任何資源。

1. 在 Azure Stack 入口網站中，瀏覽至您想要指派應用程式的範圍層級。 例如，若要在訂用帳戶範圍指派角色，請選取 [訂用帳戶]。 您可以改為選取資源群組或資源。

2. 選取特定訂用帳戶作為指派應用程式的對象 (資源群組或資源)。

     ![選取要指派的訂用帳戶](./media/azure-stack-create-service-principal/image16.png)

3. 選取 [存取控制 (IAM)]。

     ![選取存取](./media/azure-stack-create-service-principal/image17.png)

4. 選取 [新增] 。

5. 選取您想要將應用程式指派給哪個角色。

6. 搜尋並選取您的應用程式。

7. 選取 [確定] 以完成角色指派。 您在使用者清單中看到應用程式已指派給該範圍的角色。

既然您已建立服務主體並指派角色，您可以開始在應用程式內使用它來存取 Azure Stack 資源。  

## <a name="next-steps"></a>後續步驟

[為 ADFS 新增使用者](azure-stack-add-users-adfs.md)
[管理使用者權限](azure-stack-manage-permissions.md)