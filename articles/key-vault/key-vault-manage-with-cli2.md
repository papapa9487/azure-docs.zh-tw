---
title: "使用 CLI 管理 Azure Key Vault | Microsoft Docs"
description: "透過本教學課程，使用 CLI 2.0 來自動化 Key Vault 中的一般工作"
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: barclayn
ms.openlocfilehash: eaeb50ca8a83fcfee6689acf549f20ba5d44c51d
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/28/2017
---
# <a name="manage-key-vault-using-cli-20"></a>使用 CLI 2.0 管理 Key Vault

本文說明如何透過 Azure CLI 2.0 開始使用 Azure Key Vault。 請查看下列資訊：
- 如何在 Azure 中建立強行寫入的容器 (保存庫)
- 如何儲存和管理密碼編譯金鑰和 Azure 中的祕密。 
- 流程：使用 Azure 跨平台命令列介面，建立一個包含金鑰或密碼的保存庫，稍後再於 Azure 應用程式中使用此金鑰或密碼。 
- 應用程式可以如何使用該金鑰或密碼。

大部分地區均提供 Azure 金鑰保存庫。 如需詳細資訊，請參閱 [金鑰保存庫價格頁面](https://azure.microsoft.com/pricing/details/key-vault/)。

**預估完成時間：** 20 分鐘

> [!NOTE]
> 本教學課程沒有說明如何撰寫其中一個步驟所包含的 Azure 應用程式，但會示範如何授權應用程式使用金鑰保存庫中的金鑰或密碼。
>

如需 Azure Key Vault 的概觀，請參閱[什麼是 Azure Key Vault？](key-vault-whatis.md)

## <a name="prerequisites"></a>必要條件
若要完成本教學課程，您必須具備下列項目：

* Microsoft Azure 訂用帳戶。 如果您沒有訂用帳戶，您可以註冊 [免費試用](https://azure.microsoft.com/pricing/free-trial)。
* 命令列介面 2.0 版或更新版本。 若要安裝最新版本，並連線至 Azure 訂用帳戶，請參閱[安裝與設定 Azure 跨平台命令列介面 2.0](/cli/azure/install-azure-cli)。
* 可設定使用您在本教學課程中所建立之金鑰或密碼的應用程式。 您可以在 [Microsoft 下載中心](http://www.microsoft.com/download/details.aspx?id=45343)找到範例應用程式。 如需相關指示，請參閱隨附的讀我檔案。

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>取得使用 Azure 跨平台命令列介面的說明
本教學課程假設您熟悉命令列介面 (Bash、終端機、命令提示字元)

--help 或 -h 參數可用於檢視特定命令的說明。 或者，您也可使用 azure help [command] [options] 格式傳回相同資訊。 例如，以下命令全部都會傳回相同的資訊：

```azurecli-interactive
az account set --help
az account set -h
```

不確定命令所需的參數時，請使用 --help、-h 或 az help [command] 來查閱說明。

您也可以閱讀以下教學課程，以熟悉 Azure 跨平台命令列介面中的 Azure 資源管理員：

* [安裝 Azure CLI](/cli/azure/install-azure-cli)
* [開始使用 Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)

## <a name="connect-to-your-subscriptions"></a>連線到您的訂閱
若要使用組織帳戶登入，請使用下列命令：

```azurecli-interactive
az login -u username@domain.com -p password
```

或者，如果您想要以互動方式輸入來登入

```azurecli-interactive
az login
```

如果您有多個訂用帳戶，並想要指定其中一個訂用帳戶供 Azure 金鑰保存庫使用，請輸入下列內容以查看您帳戶的訂用帳戶：

```azurecli-interactive
az account list
```

接著，若要指定要使用的訂用帳戶，請輸入：

```azurecli-interactive
az account set --subscription <subscription name or ID>
```

如需設定 Azure 跨平台命令列介面的詳細資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-a-new-resource-group"></a>建立新的資源群組
使用 Azure 資源管理員時，會在資源群組內建立所有相關資源。 在本教學課程中，我們將建立新的資源群組 'ContosoResourceGroup'。

```azurecli-interactive
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

第一個參數是資源群組名稱，而第二個參數是位置。 若要取得所有可能位置類型的清單：

```azurecli-interactive
az account list-locations
``` 

如果您需要更多資訊，請輸入： 

```azurecli-interactive
az account list-locations -h
```

## <a name="register-the-key-vault-resource-provider"></a>註冊金鑰保存庫資源提供者
當您嘗試建立新的 Key Vault 時，可能會看到「訂用帳戶未登錄要使用命名空間 'Microsoft.KeyVault'」錯誤。 如果出現該訊息，請確定是否已在訂用帳戶中註冊 Key Vault 資源提供者：

```azurecli-interactive
az provider register -n Microsoft.KeyVault
```

>[!NOTE]
每個訂用帳戶只需要執行這項作業一次。

## <a name="create-a-key-vault"></a>建立金鑰保存庫
使用 `az keyvault create` 命令來建立金鑰保存庫。 這個指令碼包含三個必要參數：資源群組名稱、金鑰保存庫名稱和地理位置。

例如：

- 如果您使用保存庫名稱 **ContosoKeyVault**
- 資源群組名稱 **ContosoResourceGroup**
- **東亞**的位置

您要輸入：

```azurecli-interactive
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

此命令的輸出會顯示您剛剛建立的金鑰保存庫屬性。 兩個最重要屬性是：

* **name**：在此範例中，這是 ContosoKeyVault。 您將在其他 Key Vault 命令中使用此名稱。
* **vaultUri**：在此範例中，這是 https://contosokeyvault.vault.azure.net。 透過其 REST API 使用保存庫的應用程式必須使用此 URI。

您的 Azure 帳戶現已取得在此金鑰保存庫上執行任何作業的授權。 而且，沒有其他人有此授權。

## <a name="add-a-key-or-secret-to-the-key-vault"></a>將金鑰或密碼加入至金鑰保存庫

如果您想讓 Azure 金鑰保存庫為您建立一個軟體防護金鑰，請使用 `az key create` 命令，並輸入下列內容：
```azurecli-interactive
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```
不過，如果您在 .pem 檔案中有現有金鑰，而該檔案已另存為本機檔案 (在名為 softkey.pem 的檔案中)，且您想要將該金鑰上傳至 Azure 金鑰保存庫，請輸入下列命令以從 .PEM 檔案匯入金鑰 (這樣便可透過金鑰保存庫服務中的軟體來保護金鑰)：

```azurecli-interactive
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'PaSSWORD' --protection software
```

您現在可以參照您所建立或上傳至 Azure 金鑰保存庫的金鑰 (藉由使用其 URI)。 使用 **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** 一律可取得目前的版本，而使用 **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** 可取得此特定版本。

若要將名為 SQLPassword 且其 Azure 金鑰保存庫的值為 Pa$$w0rd 的密碼新增至保存庫，請輸入下列內容：

```azurecli-interactive
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```

透過使用其 URI，您現在可以參照您新增至 Azure 金鑰保存庫的密碼。 使用 **https://ContosoVault.vault.azure.net/secrets/SQLPassword** 一律可取得目前的版本，而使用 **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** 可取得此特定版本。

讓我們來檢視剛剛建立的金鑰或密碼：

* 若要檢視您的金鑰，請輸入： 

```azurecli-interactive
az keyvault key list --vault-name 'ContosoKeyVault'
```

* 若要檢視您的密碼，請輸入： 

```azurecli-interactive
az keyvault secret list --vault-name 'ContosoKeyVault'
```

## <a name="register-an-application-with-azure-active-directory"></a>向 Azure Active Directory 註冊應用程式
這步驟通常會由開發人員在個別電腦上完成。 這並非 Azure Key Vault 的特有狀況，在此列出是為了提醒注意。

> [!IMPORTANT]
> 若要完成本教學課程，您的帳戶、保存庫及將在本步驟中註冊的應用程式全都必須位於相同的 Azure 目錄中。
>
>

使用金鑰保存庫的應用程式必須使用 Azure Active Directory 的權杖進行驗證。 若要達到此目的，應用程式擁有者首先必須在其 Azure Active Directory 中註冊該應用程式。 註冊結束時，應用程式擁有者會取得下列值：

- **應用程式識別碼** 
- **驗證金鑰**(也稱為共用祕密)。 

應用程式必須向 Azure Active Directory 出示這兩個值才能取得權杖。 如何設定應用程式執行此作業會取決於應用程式。 在 [Key Vault 範例應用程式](https://www.microsoft.com/download/details.aspx?id=45343)中，應用程式擁有者會在 app.config 檔案中設定這些值。

如需使用 Azure Active Directory 註冊應用程式的詳細步驟，您應檢閱標題為[整合應用程式與 Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md)或[使用入口網站來建立可存取資源的 Active Directory 應用程式和服務主體](../azure-resource-manager/resource-group-create-service-principal-portal.md)。若要在 Azure Active Directory 中註冊應用程式：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左側按一下 [應用程式註冊]。 如果您沒有看到應用程式註冊，請按一下 [更多服務] 並在該處找到它。  
[!NOTE]
您必須選取您用來建立金鑰保存庫的 Azure 訂用帳戶所在的同一個目錄。 
3. 按一下 [新增應用程式註冊]。
4. 在 [建立] 刀鋒視窗上提供您的應用程式名稱，然後選取 [WEB 應用程式和/或 WEB API] (預設值)，並指定 Web 應用程式的 [登入 URL]。 如果您目前沒有這項資訊，可在此步驟中假設一個 (例如，您可以指定 http://test1.contoso.com)。 這些網站是否存在並不重要。 

    ![新增應用程式註冊](./media/key-vault-manage-with-cli2/new-application-registration.png)
    >[!WARNING]
    請確定您已選擇 [Web 應用程式和/或 WEB API]，否則將不會看到設定下的 [金鑰] 選項。

5. 按一下 [ **建立** ] 按鈕。
6. 完成應用程式註冊時，可以看到已註冊應用程式的清單。 尋找您剛註冊的應用程式，並按一下它。
7. 按一下 [註冊的應用程式] 刀鋒視窗並複製 [應用程式識別碼]
8. 按一下 [所有設定]
9. 在 [設定] 刀鋒視窗上，按一下 [金鑰]
9. 在 [金鑰描述] 方塊中輸入描述並選取持續時間，然後按一下 [儲存]。 頁面會重新整理，並顯示金鑰值。 
10. 您將在下一個步驟中使用 [應用程式識別碼] 和 [金鑰] 資訊來設定您保存庫的權限。


## <a name="authorize-the-application-to-use-the-key-or-secret"></a>授權應用程式使用金鑰或密碼
若要授權應用程式存取保存庫中的金鑰或密碼，請使用 `az keyvault set-policy` 命令。

例如，如果您的保存庫名稱是 ContosoKeyVault，且您要授權的應用程式具有 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed 的用戶端識別碼，您想要授權應用程式使用保存庫中的金鑰來進行解密並簽署，則請執行下列作業：

```azurecli-interactive
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

如果您想要授權該相同的應用程式讀取您保存庫中的機密資料，請執行以下命令：

```azurecli-interactive
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```
## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>如果想要使用硬體安全模組 (HSM)
為了加強保證，您可以在硬體安全模組 (HSM) 中匯入或產生無需離開 HSM 界限的金鑰。 HSM 已通過 FIPS 140-2 Level 2 驗證。 如果此需求對您不適用，請略過本節並移至 [刪除金鑰保存庫及相關聯的金鑰和密碼](#delete-the-key-vault-and-associated-keys-and-secrets)。

若要建立這些受 HSM 保護的金鑰，您必須具備支援受 HSM 保護之金鑰的保存庫訂閱。

建立金鑰保存庫時，請新增 'sku' 參數：

```azurecli-interactive
az keyvault create --name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```
您可以將軟體防護金鑰 (如稍早所示) 和受 HSM 保護的金鑰新增至此保存庫。 若要建立受 HSM 保護的金鑰，請將 [目的地] 參數設為 'HSM'：

```azurecli-interactive
az keyvault key create --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --protection 'hsm'
```

您可以使用下列命令，從電腦上的 .pem 檔案匯入金鑰。 此命令會將金鑰匯入金鑰保存庫服務中的 HSM：

```azurecli-interactive
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --protection 'hsm' --pem-password 'PaSSWORD'
```

下一個命令會匯入「自備金鑰」(BYOK) 封包。 這可讓您在您的本機 HSM 中產生金鑰，且在金鑰無需離開 HSM 界限的情況下，即可將它傳輸到金鑰保存庫服務中的 HSM：

```azurecli-interactive
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --protection 'hsm'
```
如需有關如何產生此 BYOK 封包的詳細指示，請參閱 [如何使用 Azure 金鑰保存庫中受 HSM 保護的金鑰](key-vault-hsm-protected-keys.md)。

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>刪除金鑰保存庫及相關聯的金鑰和密碼
如果您不再需要金鑰保存庫及其所包含的金鑰或祕密，可以使用 `az keyvault delete` 命令來刪除金鑰保存庫：

```azurecli-interactive
az keyvault delete --name 'ContosoKeyVault'
```

或者，您可以刪除整個 Azure 資源群組，其中包括金鑰保存庫和您加入該群組的任何其他資源：

```azurecli-interactive
az group delete --name 'ContosoResourceGroup'
```

## <a name="other-azure-cross-platform-command-line-interface-commands"></a>其他 Azure 跨平台命令列介面命令
可能有助於管理 Azure 金鑰保存庫的其他命令。

此命令會列出以表格形式顯示的所有金鑰和所選屬性：

```azurecli-interactive
az keyvault key list --vault-name 'ContosoKeyVault'
```

此命令會顯示指定金鑰的完整屬性清單：

```azurecli-interactive
az keyvault key show --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'
```

此命令會列出以表格形式顯示的所有密碼名稱和所選屬性：

```azurecli-interactive
az keyvault secret list --vault-name 'ContosoKeyVault'
```

以下是如何移除特定金鑰的範例：

```azurecli-interactive
az keyvault key delete --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'
```

以下是如何移除特定密碼的範例：

```azurecli-interactive
az keyvault secret delete --vault-name 'ContosoKeyVault' --name 'SQLPassword'
```

## <a name="next-steps"></a>後續步驟

- 如需金鑰保存庫命令的完整 Azure CLI 參考，請參閱 [Key Vault CLI 參考](/cli/azure/keyvault)。

- 如需程式設計參考，請參閱 [Azure 金鑰保存庫開發人員指南](key-vault-developers-guide.md)。

- 如需 Azure 金鑰保存庫和 HSM 的相關資訊，請參閱[如何使用 Azure 金鑰保存庫中受 HSM 保護的金鑰](key-vault-hsm-protected-keys.md)。
