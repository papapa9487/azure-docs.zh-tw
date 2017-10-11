---
title: "金鑰保存庫 .NET 2.x API 版本資訊 | Microsoft Docs"
description: ".NET 開發人員會使用這個 API 來編寫 Azure 金鑰保存庫的程式碼"
services: key-vault
author: BrucePerlerMS
manager: mbaldwin
editor: bruceper
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2017
ms.author: bruceper
ms.openlocfilehash: c5b5fd7f16faf17d16ecc82269fb1264adf4dd06
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure 金鑰保存庫 .NET 2.0 - 版本資訊和移轉指南
下列資訊和指引適用於使用 Azure Key Vault .NET/C# 程式庫的開發人員。 從 1.0 版轉換為 2.0 版時，進行的數個更新需要程式碼中的移轉工作，您才能受益於功能改進和功能新增 (例如 **Key Vault 憑證**支援)。

## <a name="key-vault-certificates"></a>Key Vault 憑證

金鑰保存庫憑證支援提供管理 x509 憑證和下列行為︰  

* 允許憑證擁有者透過金鑰保存庫建立程序或匯入現有憑證來建立憑證。 這同時包含自我簽署和憑證授權單位所產生的憑證。
* 允許金鑰保存庫憑證擁有者實作 X509 憑證的安全儲存和管理，而不需要與私密金鑰資料互動。  
* 允許憑證擁有者建立原則，以指示金鑰保存庫管理憑證的生命週期。  
* 允許憑證擁有者提供有關憑證到期和更新生命週期事件的通知的連絡資訊。  
* 支援與所選簽發者的自動更新 - 金鑰保存庫夥伴 X509 憑證提供者/憑證授權單位。
  
  * 請注意 - 也允許無合作關係的提供者/授權單位，但不支援自動更新功能。

## <a name="net-support"></a>.NET 支援

* Azure 金鑰保存庫 .NET/C# 程式庫 2.0 版不支援 **.NET 4.0**
* Azure 金鑰保存庫 .NET/C# 程式庫 2.0 版支援 **.NET Core**

## <a name="namespaces"></a>命名空間

* **模型**的命名空間會從 **Microsoft.Azure.KeyVault** 變更為 **Microsoft.Azure.KeyVault.Models**。
* 卸除 **Microsoft.Azure.KeyVault.Internal** 命名空間。
* Azure SDK 相依性命名空間會從 **Hyak.Common** 和 **Hyak.Common.Internals** 變更為 **Microsoft.Rest** 和 **Microsoft.Rest.Serialization**

## <a name="type-changes"></a>類型變更

* *Secret* 已變更為 *SecretBundle*
* *Dictionary* 已變更為 *IDictionary*
* *List<T>, string []* 已變更為 *IList<T>*
* *NextList* 已變更為 *NextPageLink*

## <a name="return-types"></a>傳回類型

* **KeyList** 和 **SecretList** 將傳回 *IPage<T>*，而非 *ListKeysResponseMessage*
* 產生的 **BackupKeyAsync** 將會傳回 *BackupKeyResult*，其包含 *Value* (備份 Blob)。 包裝方法之前，只會傳回值。

## <a name="exceptions"></a>例外狀況

* *KeyVaultClientException* 變更為 *KeyVaultErrorException*
* 服務錯誤會從 *exception.Error* 變更為 *exception.Body.Error.Message*。
* 已移除 **[JsonExtensionData]** 的錯誤訊息中的其他資訊。

## <a name="constructors"></a>建構函式

* 建構函式只會接受 *HttpClientHandler* 或 *DelegatingHandler[]*，而不是接受 *HttpClient* 做為建構函式引數。

## <a name="downloaded-packages"></a>下載的套件

用戶端正在處理與金鑰保存庫的相依性時，會下載下列項目：

### <a name="previous-package-list"></a>先前的套件清單

* package id="Hyak.Common" version="1.0.2" targetFramework="net45"
* package id="Microsoft.Azure.Common" version="2.0.4" targetFramework="net45"
* package id="Microsoft.Azure.Common.Dependencies" version="1.0.0" targetFramework="net45"
* package id="Microsoft.Azure.KeyVault" version="1.0.0" targetFramework="net45"
* package id="Microsoft.Bcl" version="1.1.9" targetFramework="net45"
* package id="Microsoft.Bcl.Async" version="1.0.168" targetFramework="net45"
* package id="Microsoft.Bcl.Build" version="1.0.14" targetFramework="net45"
* package id="Microsoft.Net.Http" version="2.2.22" targetFramework="net45"

### <a name="current-package-list"></a>目前套件清單

* package id="Microsoft.Azure.KeyVault" version="2.0.0-preview" targetFramework="net45"
* package id="Microsoft.Rest.ClientRuntime" version="2.2.0" targetFramework="net45"
* package id="Microsoft.Rest.ClientRuntime.Azure" version="3.2.0" targetFramework="net45"

## <a name="class-changes"></a>類別變更

* 已移除 **UnixEpoch** 類別
* **Base64UrlConverter** 類別已重新命名為 **Base64UrlJsonConverter**

## <a name="other-changes"></a>其他變更

* 這個版本的 API 已新增暫時性失敗的 KV 作業重試原則組態支援。

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* 針對傳回 *vault* 的作業，傳回類型是包含 Vault 屬性的類別。 傳回類型現在是 *Vault*。
* *PermissionsToKeys* 和 *PermissionsToSecrets* 現在是 *Permissions.Keys* 和 *Permissions.Secrets*
* 部分傳回類型變更也會套用至控制平面。

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* 套件最多分成 **Microsoft.Azure.KeyVault.Extensions** 和 **Microsoft.Azure.KeyVault.Cryptography** 來進行密碼編譯作業。

