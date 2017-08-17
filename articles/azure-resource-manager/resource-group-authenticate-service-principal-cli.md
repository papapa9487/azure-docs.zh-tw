---
title: "使用 Azure CLI 建立 Azure App 的身分識別 | Microsoft Docs"
description: "描述如何使用 Azure CLI 建立 Azure Active Directory 應用程式和服務主體，並透過角色型存取控制將存取權授與資源。 它示範如何使用密碼或憑證來驗證應用程式。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c224a189-dd28-4801-b3e3-26991b0eb24d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/15/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: 3c5826d58887ff1af4df8e66999d9c1a1643bcc7
ms.contentlocale: zh-tw
ms.lasthandoff: 07/21/2017

---
# <a name="use-azure-cli-to-create-a-service-principal-to-access-resources"></a>使用 Azure CLI 建立用來存取資源的服務主體

當您有 App 或指令碼需要存取資源時，可以設定 App 的身分識別，並使用 App 自己的認證進行驗證。 此身分識別就是所謂的服務主體。 這種方法可讓您︰

* 將權限指派給不同於您自己權限的 App 身分識別。 一般而言，這些權限只會限制為 App 必須執行的確切權限。
* 使用憑證在執行無人看管的指令碼時進行驗證。

本文說明如何使用 [Azure CLI 1.0](../cli-install-nodejs.md) 來設定應用程式，讓它利用自己的認證和身分識別來執行。 安裝最新版的 [Azure CLI 1.0](../cli-install-nodejs.md)，以確定您的環境符合本文中的範例。

## <a name="required-permissions"></a>所需的權限
若要完成本主題，您必須在 Azure Active Directory 和您的 Azure 訂用帳戶中有足夠的權限。 具體來說，您必須能夠在 Azure Active Directory 中建立應用程式，並將服務主體指派給角色。 

檢查您的帳戶是否具有足夠的權限，最簡單的方式是透過入口網站。 請參閱[在入口網站中檢查必要的權限](resource-group-create-service-principal-portal.md#required-permissions)。

現在，繼續進行[密碼](#create-service-principal-with-password)或[憑證](#create-service-principal-with-certificate)驗證的章節。

## <a name="create-service-principal-with-password"></a>使用密碼建立服務主體
在本節中，您可以執行步驟來使用密碼建立 AD 應用程式，並將讀取者角色指派給服務主體。

1. 登入您的帳戶。
   
   ```azurecli
   azure login
   ```
2. 若要建立應用程式身分識別，請提供 App 名稱和密碼，如下列命令中所示︰
     
   ```azurecli
   azure ad sp create -n exampleapp -p {your-password}
   ```
     
   傳回新的服務主體。 授與權限時，需要物件識別碼。 在登入時，則需要隨服務主體名稱列出的 GUID。 此 GUID 是和應用程式識別碼相同的值。 在範例應用程式中，這個值稱為 `Client ID`。 
     
   ```azurecli
   info:    Executing command ad sp create
     
   Creating application exampleapp
     / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
     data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
     data:    Display Name:            exampleapp
     data:    Service Principal Names:
     data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
     data:                             https://www.contoso.org/example
     info:    ad sp create command OK
   ```

3. 授與服務主體對您訂用帳戶的權限。 在此範例中，您會將服務主體新增至「讀取者」角色，以授與讀取訂用帳戶中所有資源的權限。 若為其他角色，請參閱 [RBAC︰內建角色](../active-directory/role-based-access-built-in-roles.md)。 針對 objectid 參數，提供您在建立應用程式時所使用的物件識別碼。 執行此命令之前，您必須允許一些時間讓新的服務主體在整個 Azure Active Directory 中傳播。 當您手動執行這些命令時，通常工作與工作之間經過的時間已足夠。 您應該在指令碼中的命令之間加入睡眠步驟 (例如`sleep 15`)。 如果您看到主體不存在於目錄中的錯誤訊息，請重新執行命令。
   
   ```azurecli
   azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/
   ```
   
就這麼簡單！ 您的 AD 應用程式和服務主體已設定好。 下一節會說明如何透過 Azure CLI 以認證來登入。 如果您想要在您的程式碼應用程式中使用認證，則不需要繼續進行本主題。 您可以跳到 [範例應用程式](#sample-applications) ，以查看使用應用程式識別碼和密碼來登入的範例。 

### <a name="provide-credentials-through-azure-cli"></a>透過 Azure CLI 提供認證
現在，您需要以應用程式的形式登入以執行作業。

1. 每當您以服務主體的形式登入時，都需要提供 AD 應用程式目錄的租用戶識別碼。 租用戶是 Azure Active Directory 的執行個體。 若要擷取目前已驗證訂用帳戶的租用戶識別碼，請使用︰
   
   ```azurecli
   azure account show
   ```
   
   它會傳回：
   
   ```azurecli
   info:    Executing command account show
   data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
   data:    ID                          : {guid}
   data:    State                       : Enabled
   data:    Tenant ID                   : {guid}
   data:    Is Default                  : true
   ...
   ```
   
     如果您需要取得其他訂用帳戶的租用戶識別碼，請使用下列命令︰
   
   ```azurecli
   azure account show -s {subscription-id}
   ```
2. 如果您需要擷取用戶端識別碼以便用於登入，請使用︰
   
   ```azurecli
   azure ad sp show -c exampleapp --json
   ```
   
     要用於登入的值是服務主體名稱中所列出的 GUID。
   
   ```azurecli
   [
     {
       "objectId": "ff863613-e5e2-4a6b-af07-fff6f2de3f4e",
       "objectType": "ServicePrincipal",
       "displayName": "exampleapp",
       "appId": "7132aca4-1bdb-4238-ad81-996ff91d8db4",
       "servicePrincipalNames": [
         "https://www.contoso.org/example",
         "7132aca4-1bdb-4238-ad81-996ff91d8db4"
       ]
     }
   ]
   ```
3. 以服務主體的形式登入。
   
   ```azurecli
   azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant {tenant-id}
   ```
   
    系統會提示您輸入密碼。 提供您建立 AD 應用程式時指定的密碼。
   
   ```azurecli
   info:    Executing command login
   Password: ********
   ```

您現在已驗證為您所建立之服務主體的服務主體。

或者，您可以從命令列叫用 REST 作業來登入。 從驗證回應，您可以擷取存取權杖以用於其他作業。 如需叫用 REST 作業擷取存取權杖的範例，請參閱[產生存取權杖](resource-manager-rest-api.md#generating-an-access-token)。

## <a name="create-service-principal-with-certificate"></a>使用憑證建立服務主體
在本節中，您會執行下列步驟：

* 建立自我簽署憑證
* 建立 AD 應用程式 (包含憑證) 和服務主體
* 將 [讀取者角色] 指派給服務主體

若要完成這些步驟，您必須安裝 [OpenSSL](http://www.openssl.org/) 。

1. 建立自我簽署憑證。
   
   ```
   openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'
   ```

2. 上一個步驟建立了兩個檔案 - privkey.pem 和 cert.pem。 將公開和私密金鑰結合為單一檔案。

   ```
   cat privkey.pem cert.pem > examplecert.pem
   ```

3. 開啟 **examplecert.pem** 檔案並尋找在 **-----BEGIN CERTIFICATE-----** 與 **-----END CERTIFICATE-----** 之間的一長串字元。 複製憑證資料。 您會在建立服務主體時將此資料當作參數傳遞。

4. 登入您的帳戶。

   ```azurecli
   azure login
   ```
5. 若要建立服務主體，請提供應用程式名稱和憑證資料，如下列命令所示︰
     
   ```azurecli
   azure ad sp create -n exampleapp --cert-value {certificate data}
   ```
     
   傳回新的服務主體。 授與權限時，需要物件識別碼。 在登入時，則需要隨服務主體名稱列出的 GUID。 此 GUID 是和應用程式識別碼相同的值。 在範例應用程式中，這個值稱為用戶端識別碼。 
     
   ```azurecli
   info:    Executing command ad sp create
     
   Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
     data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
     data:    Display Name:     exampleapp
     data:    Service Principal Names:
     data:                      4fd39843-c338-417d-b549-a545f584a745
     data:                      https://www.contoso.org/example
     info:    ad sp create command OK
   ```
6. 授與服務主體對您訂用帳戶的權限。 在此範例中，您會將服務主體新增至「讀取者」角色，以授與讀取訂用帳戶中所有資源的權限。 若為其他角色，請參閱 [RBAC︰內建角色](../active-directory/role-based-access-built-in-roles.md)。 針對 objectid 參數，提供您在建立應用程式時所使用的物件識別碼。 執行此命令之前，您必須允許一些時間讓新的服務主體在整個 Azure Active Directory 中傳播。 當您手動執行這些命令時，通常工作與工作之間經過的時間已足夠。 您應該在指令碼中的命令之間加入睡眠步驟 (例如`sleep 15`)。 如果您看到主體不存在於目錄中的錯誤訊息，請重新執行命令。
   
   ```azurecli
   azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/
   ```
  
### <a name="provide-certificate-through-automated-azure-cli-script"></a>透過自動化的 Azure CLI 指令碼提供憑證
現在，您需要以應用程式的形式登入以執行作業。

1. 每當您以服務主體的形式登入時，都需要提供 AD 應用程式目錄的租用戶識別碼。 租用戶是 Azure Active Directory 的執行個體。 若要擷取目前已驗證訂用帳戶的租用戶識別碼，請使用︰
   
   ```azurecli
   azure account show
   ```
   
   它會傳回：
   
   ```azurecli
   info:    Executing command account show
   data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
   data:    ID                          : {guid}
   data:    State                       : Enabled
   data:    Tenant ID                   : {guid}
   data:    Is Default                  : true
   ...
   ```
   
   如果您需要取得其他訂用帳戶的租用戶識別碼，請使用下列命令︰
   
   ```azurecli
   azure account show -s {subscription-id}
   ```
2. 若要擷取憑證指紋，然後移除不必要的字元，請使用：
   
   ```
   openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
   ```
   
   這會傳回指紋值，類似：
   
   ```
   30996D9CE48A0B6E0CD49DBB9A48059BF9355851
   ```
3. 如果您需要擷取用戶端識別碼以便用於登入，請使用︰
   
   ```azurecli
   azure ad sp show -c exampleapp
   ```
   
   要用於登入的值是服務主體名稱中所列出的 GUID。
     
   ```azurecli
   [
     {
       "objectId": "7dbc8265-51ed-4038-8e13-31948c7f4ce7",
       "objectType": "ServicePrincipal",
       "displayName": "exampleapp",
       "appId": "4fd39843-c338-417d-b549-a545f584a745",
       "servicePrincipalNames": [
         "https://www.contoso.org/example",
         "4fd39843-c338-417d-b549-a545f584a745"
       ]
     }
   ]
   ```
4. 以服務主體的形式登入。
   
   ```azurecli
   azure login --service-principal --tenant {tenant-id} -u 4fd39843-c338-417d-b549-a545f584a745 --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}
   ```

您現在驗證為您所建立之 Azure Active Directory 應用程式的服務主體。

## <a name="change-credentials"></a>管理認證

若要變更 AD 應用程式認證，不管是因為安全性危害或認證過期，請使用`azure ad app set`。

若要變更密碼，使用：

```azurecli
azure ad app set --applicationId 4fd39843-c338-417d-b549-a545f584a745 --password p@ssword
```

若要變更憑證值，使用︰

```azurecli
azure ad app set --applicationId 4fd39843-c338-417d-b549-a545f584a745 --cert-value {certificate data}
```

## <a name="debug"></a>偵錯

建立服務主體時，您可能會遇到下列錯誤︰

* **Authentication_Unauthorized」**或**「在內容中找不到訂用帳戶。」** - 當您的帳戶在 Azure Active Directory 上未具備註冊應用程式的[必要權限](#required-permissions)時，您就會看到此錯誤。 通常，只有 Azure Active Directory 中的管理使用者可以註冊應用程式，且您的帳戶不是系統管理員時，就會看到此錯誤。 要求系統管理員將您指派給系統管理員角色，或是讓使用者註冊應用程式。

* 您的帳戶**「沒有在範圍 '/subscriptions/{guid}' 中執行 'Microsoft.Authorization/roleAssignments/write' 動作的權限。」** - 當您的帳戶沒有足夠權限可將角色指派給身分識別時，您就會看到此錯誤。 要求訂用帳戶管理員將您新增至「使用者存取系統管理員」角色。

## <a name="sample-applications"></a>範例應用程式
如需透過不同平台以應用程式身分登入的資訊，請參閱：

* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.js](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>後續步驟
* 如需有關將應用程式整合至 Azure 來管理資源的詳細步驟，請參閱 [利用 Azure Resource Manager API 進行授權的開發人員指南](resource-manager-api-authentication.md)。
* 如需使用憑證和 Azure CLI 的詳細資訊，請參閱 [從 Linux 命令列以憑證方式驗證 Azure 服務主體](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx)。 
* 如需可授與或拒絕使用者的可用動作清單，請參閱 [Azure Resource Manager 資源提供者作業](../active-directory/role-based-access-control-resource-provider-operations.md)。

