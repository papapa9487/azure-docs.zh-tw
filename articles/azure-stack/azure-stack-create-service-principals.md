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
ms.date: 10/17/2017
ms.author: helaw
ms.openlocfilehash: 96d5cdfc28759fd516eab5fd97c6cf444af08cf6
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2017
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
在本節中，您會在 Azure AD 中建立一個應用程式 (服務主體) 來代表您的應用程式。

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

指令碼是從 ERCS 虛擬機器上的特殊權限端點執行。


需求：
- 需要認證。

**參數**

需要下列資訊，作為自動化參數的輸入：


|參數|說明|範例|
|---------|---------|---------|
|名稱|SPN 的帳戶名稱|MyAPP|
|ClientCertificates|憑證物件的陣列|X509 憑證|
|ClientRedirectUris<br>(選用)|應用程式重新導向 URI|         |

**範例**

1. 開啟提升權限的 Windows PowerShell 工作階段，並執行下列命令：

   > [!NOTE]
   > 此範例會建立自我簽署憑證。 當您在生產環境部署中執行這些命令時，使用 Get-Certificate 擷取您想要使用之憑證的憑證物件。

   ```
   $creds = Get-Credential

   $session = New-PSSession -ComputerName <IP Address of ECRS> -ConfigurationName PrivilegedEndpoint -Credential $creds

   $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=testspn2" -KeySpec KeyExchange

   Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name 'MyApp' -ClientCertificates $using:cert}

   $session|remove-pssession

   ```

2. 自動化完成之後，它會顯示使用 SPN 的必要詳細資料。 

   例如：

   ```
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
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