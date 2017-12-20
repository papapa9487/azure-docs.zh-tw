---
title: "如何將 Azure VM 受管理服務識別與 Azure SDK 搭配使用"
description: "將 Azure SDK 與 Azure VM MSI 搭配使用的程式碼範例。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: bryanla
ms.openlocfilehash: 222acd1624ce025985bf1a3eef40eb124ab7678e
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2017
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-with-azure-sdks"></a>如何將 Azure VM 受管理服務識別 (MSI) 與 Azure SDK 搭配使用 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)] 本文提供一份 SDK 範例清單，其中示範如何針對 MSI 使用其各自的 Azure SDK 支援。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - 本文中的所有範例程式碼/指令碼都假設用戶端在已啟用 MSI 的虛擬機器上執行。 在 Azure 入口網站中使用 VM「連線」功能，從遠端連線到您的 VM。 如需有關在虛擬機器上啟用 MSI 的詳細資訊，請參閱[使用 Azure 入口網站設定虛擬機器受管理服務識別 (MSI)](msi-qs-configure-portal-windows-vm.md)，或其中一篇變化文章 (使用 PowerShell、CLI、範本或 Azure SDK)。 

## <a name="sdk-code-samples"></a>SDK 程式碼範例

| SDK             | 程式碼範例 |
| --------------- | ----------- |
| .NET            | [使用受管理服務識別從 Windows VM 部署 Azure Resource Manager 範本](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) \(英文\) |
| .NET Core       | [使用受管理的服務識別從 Linux VM 呼叫 Azure 服務](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [使用受管理服務識別 (MSI) 來管理資源](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [使用 MSI 直接從虛擬機器內部驗證](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [從已啟用 MSI 的虛擬機器管理資源](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="related-content"></a>相關內容

- 請參閱 [Azure SDK](https://azure.microsoft.com/downloads/)，以取得 Azure SDK 資源的完整清單，包括程式庫下載、文件及更多項目。
- 若要在 Azure VM 上啟用 MSI，請參閱[使用 Azure 入口網站設定「VM 受管理的服務身分識別 (MSI)」](msi-qs-configure-portal-windows-vm.md)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。








