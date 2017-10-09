---
title: "在 Azure 儲存體中要求使用安全傳輸 |Microsoft 文件"
description: "了解 Azure 儲存體的「需要安全傳輸」功能，以及如何啟用它。"
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft
ms.assetid: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/20/2017
ms.author: fryu
ms.translationtype: HT
ms.sourcegitcommit: 57278d02a40aa92f07d61684e3c4d74aa0ac1b5b
ms.openlocfilehash: 5ec50ca23d9f7c92365492dfab42dc14a38699e2
ms.contentlocale: zh-tw
ms.lasthandoff: 09/28/2017

---
# <a name="require-secure-transfer-in-azure-storage"></a>在 Azure 儲存體中要求使用安全傳輸

「需要安全傳輸」選項透過只允許從安全連線對帳戶進行要求，來加強儲存體帳戶的安全性。 例如，呼叫 REST API 來存取儲存體帳戶時，您必須使用 HTTPS 連線。 「需要安全傳輸」會拒絕使用 HTTP 的要求。

當您在啟用「需要安全傳輸」的情況下使用 Azure 檔案服務時，任何未加密的連線都會失敗。 這包括使用 SMB 2.1、無加密的 SMB 3.0 和某些 Linux SMB 用戶端版本的情況。 

根據預設，[需要安全傳輸] 選項是停用的。

> [!NOTE]
> 因為 Azure 儲存體針對自訂網域名稱並不支援 HTTPS，當您使用自訂網域名稱時，將不會套用此選項。

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>在 Azure 入口網站中啟用 [需要安全傳輸]

當您在 [Azure 入口網站](https://portal.azure.com)中建立儲存體帳戶時，就可以開啟 [需要安全傳輸] 設定。 您也可以為現有的儲存體帳戶啟用它。

### <a name="require-secure-transfer-for-a-new-storage-account"></a>針對新的儲存體帳戶要求使用安全傳輸

1. 在 Azure 入口網站中開啟 [建立儲存體帳戶] 窗格。
1. 在 [需要安全傳輸] 下，選取 [已啟用]。

  ![[建立儲存體帳戶] 刀鋒視窗](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>針對現有儲存體帳戶要求使用安全傳輸

1. 在 Azure 入口網站中選取現有儲存體帳戶。
1. 在 [儲存體帳戶] 功能表窗格中，選取 [設定] 底下的 [組態]。
1. 在 [需要安全傳輸] 下，選取 [已啟用]。

  ![[儲存體帳戶] 功能表窗格](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>以程式設計方式啟用 [需要安全傳輸]

若要以程式設計方式要求使用安全傳輸，請使用儲存體帳戶屬性中的設定 _supportsHttpsTrafficOnly_ 搭配 REST API、工具或程式庫：

* [REST API](https://docs.microsoft.com/en-us/rest/api/storagerp/storageaccounts) \(英文\) (版本：2016-12-01)
* [PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.storage/set-azurermstorageaccount?view=azurermps-4.1.0) \(英文\) (版本：4.1.0)
* [CLI](https://pypi.python.org/pypi/azure-cli-storage/2.0.11) \(英文\) (版本：2.0.11)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/) \(英文\) (版本：1.1.0)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview) \(英文\) (版本：6.3.0)
* [Python SDK](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0) \(英文\) (版本：1.1.0)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage) \(英文\) (版本：0.11.0)

### <a name="enable-secure-transfer-required-setting-with-rest-api"></a>透過 REST API 啟用「需要安全傳輸」設定

若要透過 REST API 簡化測試，可以使用 [ArmClient](https://github.com/projectkudu/ARMClient) \(英文\) 從命令列呼叫。

 使用下列命令列透過 REST API 檢查設定：

```
# Login Azure and proceed with your credentials
> armclient login

> armclient GET  /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01
```

在回應中找到 _supportsHttpsTrafficOnly_ 設定。 例如：

```Json

{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}",
  "kind": "Storage",
  ...
  "properties": {
    ...
    "supportsHttpsTrafficOnly": false
  },
  "type": "Microsoft.Storage/storageAccounts"
}

```

使用下列命令列透過 REST API 啟用設定：

```

# Login Azure and proceed with your credentials
> armclient login

> armclient PUT /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01 < Input.json

```

以下是 Input.json 範例：
```Json

{
  "location": "westus",
  "properties": {
    "supportsHttpsTrafficOnly": true
  }
}

```

## <a name="next-steps"></a>後續步驟
Azure 儲存體提供一組完整的安全性功能，這些功能讓開發人員能夠建置安全應用程式。 如需詳細資料，請移至[儲存體安全性指南](storage-security-guide.md)。

