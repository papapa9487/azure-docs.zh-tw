---
title: "使用 Azure CLI 1.0 來加密 Linux VM 上的磁碟 | Microsoft Docs"
description: "如何使用 Azure CLI 1.0 和 Resource Manager 部署模型來加密 Linux VM 上的磁碟"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2017
ms.author: iainfou
ms.openlocfilehash: b436f2d43c41000f4385889edb3fa3983d4a8c66
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli-10"></a>使用 Azure CLI 1.0 來加密 Linux VM 上的磁碟
如需強化虛擬機器 (VM) 安全性與法規遵循，可以將 Azure 中的待用虛擬磁碟加密。 磁碟會使用 Azure 金鑰保存庫中受保護的密碼編譯金鑰進行加密。 您可控制這些密碼編譯金鑰，並可稽核其使用情況。 本文詳細說明如何使用 Azure CLI 1.0 和 Resource Manager 部署模型來加密 Linux VM 上的虛擬磁碟。

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本
您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0](#quick-commands) – 適用於傳統和資源管理部署模型的 CLI (本文章)
- [Azure CLI 2.0](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 適用於資源管理部署模型的新一代 CLI

## <a name="quick-commands"></a>快速命令
如果您需要快速完成工作，下列章節詳細說明將 VM 上的虛擬磁碟加密的基本命令。 每個步驟的詳細資訊和內容可在文件其他地方找到，[從這裡開始](#overview-of-disk-encryption)。

您需要安裝[最新的 Azure CLI 1.0](../../xplat-cli-install.md) 並使用 Resource Manager 模式來登入，如下：

```azurecli
azure config mode arm
```

在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含 `myResourceGroup`、`myKeyVault` 和 `myVM`。

首先，啟用您的 Azure 訂用帳戶中的 Azure 金鑰保存庫提供者，以及建立資源群組。 下列範例會在 `WestUS` 位置建立名為 `myResourceGroup` 的資源群組：

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

建立 Azure 金鑰保存庫。 下列範例會建立名為 `myKeyVault` 的金鑰保存庫：

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

在您的金鑰保存庫中建立密碼編譯金鑰，並針對磁碟加密加以啟用。 下列範例會建立名為 `myKey` 的金鑰：

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

使用 Azure Active Directory 建立端點，以便處理金鑰保存庫中密碼編譯金鑰的驗證和交換。 `--home-page` 和 `--identifier-uris` 不必是實際的可路由傳送位址。 如需最高等級的安全性，則應使用用戶端密碼 (Secret) 而非使用密碼 (Password)。 Azure CLI 目前無法產生用戶端密碼。 用戶端密碼只能在 Azure 入口網站中產生。 下列範例會建立名為 `myAADApp` 的 Azure Active Directory 端點，並使用 `myPassword` 的密碼。 指定您自己的密碼，如下所示︰

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

請記下先前命令的輸出中顯示的 `applicationId`。 此應用程式識別碼會使用於下列步驟：

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

將資料磁碟新增至現有的 VM。 下列範例會將資料磁碟新增至名為 `myVM` 的 VM：

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

檢閱您的金鑰保存庫和您所建立的金鑰詳細資料。 您需要最後一個步驟中的金鑰保存庫識別碼、URI 和金鑰 URL。 下列範例會檢閱名為 `myKeyVault` 的金鑰保存庫和名為 `myKey` 的金鑰詳細資料：

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

如下所示，始終輸入您自己的參數名稱，將您的磁碟加密︰

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Azure CLI 不會在加密過程中提供詳細資訊錯誤。 如需其他疑難排解資訊，請檢閱 `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`。 由於上述命令有許多變數，而且您可能未得到很多有關程序為何失敗的指示，其完整命令範例如下所示︰

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

最後，再次檢閱加密狀態，確認您的虛擬磁碟現在已加密。 下列範例會檢查 `myResourceGroup` 資源群組中名為 `myVM` 的 VM 狀態：

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>磁碟加密概觀
Linux VM 上的待用虛擬磁碟會使用 [dm-crypt](https://wikipedia.org/wiki/Dm-crypt) 進行加密。 將 Azure 中的虛擬磁碟加密完全免費。 密碼編譯金鑰會使用軟體保護功能儲存在 Azure 金鑰保存庫中，或者您可以在 FIPS 140-2 第 2 級標準認證的硬體安全性模組 (HSM) 中匯入或產生金鑰。 您可保留這些密碼編譯金鑰的控制權，並可稽核其使用情況。 這些密碼編譯金鑰用來加密及解密連接到 VM 的虛擬磁碟。 Azure Active Directory 端點提供一個安全機制，以便在 VM 開機或關機時發出這些密碼編譯金鑰。

將 VM 加密的程序如下所示：

1. 在 Azure 金鑰保存庫中建立密碼編譯金鑰。
2. 將密碼編譯金鑰設定為可用於磁碟加密。
3. 若要讀取 Azure 金鑰保存庫中的密碼編譯金鑰，請使用 Azure Active Directory 並以適當權限建立端點。
4. 發出命令將您的虛擬磁碟加密，並指定要使用的 Azure Active Directory 端點和適當密碼編譯金鑰。
5. Azure Active Directory 端點會向 Azure 金鑰保存庫要求所需的密碼編譯金鑰。
6. 虛擬磁碟會使用所提供的密碼編譯金鑰進行加密。

## <a name="supporting-services-and-encryption-process"></a>支援服務和加密程序
磁碟加密依賴下列其他元件︰

* **Azure 金鑰保存庫** - 用來保護磁碟加密/解密程序所使用的密碼編譯金鑰和密碼。
  * 如果有的話，您可以使用現有的 Azure 金鑰保存庫。 您沒有專門用來加密磁碟的金鑰保存庫。
  * 若要區分系統管理界限和金鑰可視性，您可以建立專用的金鑰保存庫。
* **Azure Active Directory** - 處理必要密碼編譯金鑰的安全交換以及所要求動作的驗證。
  * 您通常使用現有的 Azure Active Directory 執行個體來裝載您的應用程式。
  * 對金鑰保存庫和虛擬機器服務而言，此應用程式不僅只是個端點，還可要求和取得已發出的適當密碼編譯金鑰。 您並未開發與 Azure Active Directory 整合的實際應用程式。

## <a name="requirements-and-limitations"></a>需求和限制
磁碟加密支援的案例和需求：

* 下列 Linux 伺服器 SKU - Ubuntu、CentOS、SUSE、SUSE Linux Enterprise Server (SLES) 和 Red Hat Enterprise Linux。
* 所有資源 (例如金鑰保存庫、儲存體帳戶、VM) 必須位於相同的 Azure 區域和訂用帳戶。
* 標準 A、D、DS、G 和 GS 系列 VM。

下列案例目前不支援磁碟加密︰

* 基本層 VM。
* 使用傳統部署模型建立的 VM。
* 在 Linux VM 上停用作業系統磁碟加密。
* 在已經加密的 Linux VM 上更新密碼編譯金鑰。

## <a name="create-the-azure-key-vault-and-keys"></a>建立 Azure 金鑰保存庫和金鑰
若要完成本指南的其餘部分，您需要安裝[最新的 Azure CLI 1.0](../../xplat-cli-install.md) 並使用 Resource Manager 模式來登入，如下：

```azurecli
azure config mode arm
```

在整個命令範例中，以您自己的名稱、位置和金鑰值取代所有的範例參數。 下列範例使用 `myResourceGroup`、`myKeyVault`、`myAADApp` 等的慣例。

建立 Azure 金鑰保存庫的第一個步驟是儲存您的密碼編譯金鑰。 Azure 金鑰保存庫儲存可讓您安全地在應用程式和服務中實作的金鑰和密碼 (Secret 或 Password)。 如需虛擬磁碟加密，您可使用金鑰保存庫來儲存用來加密或解密虛擬磁碟的密碼編譯金鑰。

啟用您的 Azure 訂用帳戶中的 Azure 金鑰保存庫提供者，然後建立資源群組。 下列範例會在 `WestUS` 位置建立名為 `myResourceGroup` 的資源群組：

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

包含密碼編譯金鑰和相關聯計算資源 (例如儲存體和 VM) 的 Azure 金鑰保存庫本身必須位於相同的區域中。 下列範例會建立名為 `myKeyVault` 的 Azure 金鑰保存庫：

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

您可以使用軟體或硬體安全性模型 (HSM) 保護功能來儲存密碼編譯金鑰。 使用 HSM 時需要進階金鑰保存庫。 建立進階金鑰保存庫 (而非用來儲存軟體保護金鑰的標準金鑰保存庫) 會有額外的成本。 若要建立進階金鑰保存庫，在前一個步驟中將 `--sku Premium` 新增至命令。 下列範例會使用軟體保護的金鑰，因為我們建立了標準金鑰保存庫。

在兩種保護模型中，Azure 平台都必須獲得存取權，才能在 VM 開機時要求密碼編譯金鑰來將虛擬磁碟解密。 在您的金鑰保存庫中建立加密金鑰，然後加以啟用以便用於虛擬磁碟加密。 下列範例會建立名為 `myKey` 的金鑰，然後加以啟用以便用於磁碟加密︰

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>建立 Azure Active Directory 應用程式
加密或解密虛擬磁碟時，您可以使用端點來處理金鑰保存庫中密碼編譯金鑰的驗證和交換。 此端點 (Azure Active Directory 應用程式) 可讓 Azure 平台代表 VM 要求適當的密碼編譯金鑰。 雖然許多組織都有專用的 Azure Active Directory 目錄，但您的訂用帳戶中會有預設 Azure Active Directory 執行個體。

因為您不想建立完整的 Azure Active Directory 應用程式，所以下列範例中的 `--home-page` 和 `--identifier-uris` 參數不必是實際可路由傳送的位址。 下列範例也會指定以密碼 (password) 為基礎的密碼 (secret)，而不是在 Azure 入口網站中產生金鑰。 此時，無法從 Azure CLI 產生金鑰。

建立 Azure Active Directory 應用程式。 下列範例會建立名為 `myAADApp` 的應用程式，並使用 `myPassword` 的密碼。 指定您自己的密碼，如下所示︰

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

請記下先前命令的輸出中傳回的 `applicationId`。 此應用程式識別碼會使用於剩餘的某些步驟。 接下來，建立服務主體名稱 (SPN)，以便在您的環境中存取應用程式。 若要成功加密或解密虛擬磁碟，金鑰保存庫中所儲存之密碼編譯金鑰的權限必須設定為允許 Azure Active Directory 應用程式讀取機碼。

建立 SPN 並設定適當的權限，如下所示︰

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>新增虛擬磁碟並檢閱加密狀態
若要實際加密某些虛擬磁碟，請將磁碟新增至現有的 VM。 將 5Gb 資料磁碟新增至現有的 VM，如下所示︰

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

虛擬磁碟目前並未加密。 檢閱 VM 的目前加密狀態，如下所示︰

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>將虛擬磁碟加密
若要現在將虛擬磁碟加密，您可結合所有先前的元件︰

1. 指定 Azure Active Directory 應用程式和密碼。
2. 指定金鑰保存庫以儲存已加密磁碟的中繼資料。
3. 指定要用於實際加密和解密的密碼編譯金鑰。
4. 指定是否要將作業系統磁碟、資料磁碟或所有磁碟加密。

請檢閱 Azure 金鑰保存庫的詳細資料和您建立的金鑰，因為您在最後一個步驟中需要金鑰保存庫識別碼、URI 和 URL︰

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

使用 `azure keyvault show` 和 `azure keyvault key show` 命令的輸出將您的虛擬磁碟加密，如下所示︰

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

由於上述命令有許多變數，下列範例是供您參考的完整命令︰

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Azure CLI 不會在加密過程中提供詳細資訊錯誤。 如需其他疑難排解資訊，請檢閱您所加密之 VM 上的 `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`。

最後，再次檢閱加密狀態，確認您的虛擬磁碟現在已加密：

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>新增其他資料磁碟
一旦將資料磁碟加密，您稍後即可將其他虛擬磁碟新增至您的 VM，而且予以加密。 當您執行 `azure vm enable-disk-encryption` 命令時，請使用 `--sequence-version` 參數遞增順序版本。 此順序版本參數可讓您在相同的 VM 上執行重複的作業。

例如，將第二個虛擬磁碟新增至您的 VM，如下所示︰

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

重新執行命令來加密虛擬磁碟，這次新增 `--sequence-version` 參數，並從第一次執行遞增此值，如下所示︰

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>後續步驟
* 如需有關管理 Azure 金鑰保存庫 (包括刪除密碼編譯金鑰和保存庫) 的詳細資訊，請參閱[使用 CLI 管理金鑰保存庫](../../key-vault/key-vault-manage-with-cli2.md)。
* 如需有關磁碟加密 (例如準備加密的自訂 VM 以上傳至 Azure) 的詳細資訊，請參閱 [Azure 磁碟加密](../../security/azure-security-disk-encryption.md)。
