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
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 78737c681a91f24f73502a9cc25a301efc9304a4
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---
# <a name="require-secure-transfer"></a>需要安全傳輸

「需要安全傳輸」選項透過只允許從安全連線對儲存體帳戶進行要求，來加強儲存體帳戶的安全性。 例如，呼叫 REST API 來存取儲存體帳戶時，您必須使用 HTTPS 連線。 啟用 [需要安全傳輸] 時，會拒絕使用 HTTP 的所有要求。

當您在啟用 [需要安全傳輸] 的情況下使用 Azure 檔案服務時，任何未加密的連線都會失敗。 這包括使用 SMB 2.1、無加密的 SMB 3.0 和某些 Linux SMB 用戶端類型的情況。 

根據預設，[需要安全傳輸] 選項是停用的。

> [!NOTE]
> 因為 Azure 儲存體不針對自訂網域名稱支援 HTTPS，使用自訂網域名稱時不會套用此選項。

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>在 Azure 入口網站中啟用 [需要安全傳輸]

不論是在 [Azure 入口網站](https://portal.azure.com)建立儲存體帳戶，或是針對現有儲存體帳戶，您都可以啟用 [需要安全傳輸] 設定。

### <a name="require-secure-transfer-when-you-create-a-storage-account"></a>當您建立儲存體帳戶時要求使用安全傳輸

1. 在 Azure 入口網站中開啟 [建立儲存體帳戶] 刀鋒視窗。
1. 在 [需要安全傳輸] 下，選取 [已啟用]。

  ![螢幕擷取畫面](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>針對現有儲存體帳戶要求使用安全傳輸

1. 在 Azure 入口網站中選取現有儲存體帳戶。
1. 在儲存體帳戶功能表刀鋒視窗的 [設定] 下選取 [組態]。
1. 在 [需要安全傳輸] 下，選取 [已啟用]。

  ![螢幕擷取畫面](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>以程式設計方式啟用 [需要安全傳輸]

設定名稱為儲存體帳戶屬性中的 _supportsHttpsTrafficOnly_。 可以透過 REST API、工具或程式庫啟用「需要安全傳輸」設定：

* **REST API** (版本：2016-12-01)：[發行套件](https://docs.microsoft.com/en-us/rest/api/storagerp/storageaccounts)
* **PowerShell** (版本：4.1.0)：[發行套件](https://docs.microsoft.com/en-us/powershell/module/azurerm.storage/set-azurermstorageaccount?view=azurermps-4.1.0)
* **CLI** (版本：2.0.11)：[發行套件](https://pypi.python.org/pypi/azure-cli-storage/2.0.11)
* **NodeJS** (版本：1.1.0)：[發行套件](https://www.npmjs.com/package/azure-arm-storage/)
* **.NET SDK** (版本：6.3.0)：[發行套件](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview)
* **Python SDK** (版本：1.1.0)：[發行套件](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0)
* **Ruby SDK** (版本：0.11.0)：[發行套件](https://rubygems.org/gems/azure_mgmt_storage)

### <a name="enable-secure-transfer-required-setting-with-rest-api"></a>透過 REST API 啟用「需要安全傳輸」設定

若要透過 REST API 簡化測試，可以使用 [ArmClient](https://github.com/projectkudu/ARMClient) 從命令列呼叫。

 您可以使用下列命令列透過 REST API 檢查設定：

```
# Login Azure and proceed with your credentials
> armclient login

> armclient GET  /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01
```

在回應中可以找到 _supportsHttpsTrafficOnly_ 設定。 範例：

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

您可以使用下列命令列透過 REST API 啟用設定：

```
# Login Azure and proceed with your credentials
> armclient login

> armclient PUT /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{accountName}?api-version=2016-12-01 < Input.json
```
Input.json 範例：
```Json
{
  "location": "westus",
  "properties": {
    "supportsHttpsTrafficOnly": true
  }
}
```

## <a name="next-steps"></a>後續步驟
Azure 儲存體提供一組完整的安全性功能，這些功能讓開發人員能夠建置安全應用程式。 如需詳細資訊，請參閱[儲存體安全性指南](storage-security-guide.md)。

