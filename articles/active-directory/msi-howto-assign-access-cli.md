---
title: "如何使用 Azure CLI 將 MSI 存取權指派給 Azure 資源"
description: "使用 Azure CLI 在一個資源上指派 MSI，並存取另一個資源的逐步解說指示。"
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
ms.date: 09/25/2017
ms.author: bryanla
ms.openlocfilehash: e77915c1d982ccf6262ffcbc09dc91dfd986dac5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>使用 Azure CLI 將受管理的服務識別 (MSI) 存取權指派給資源

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

一旦已透過 MSI 設定 Azure 資源，您便可以將 MSI 存取權提供給另一個資源，就像任何安全性主體。 此範例將示範如何使用 Azure CLI 將 Azure 虛擬機器的 MSI 存取權提供給 Azure 儲存體帳戶。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

若要執行 CLI 指令碼範例，您有三個選項：

- 從 Azure 入口網站使用 [Azure Cloud Shell](../cloud-shell/overview.md) (請參閱下一節)。
- 請透過每個程式碼區塊右上角的 [立即試用] 按鈕，使用內嵌的 Azure Cloud Shell。
- 如果您想要使用本機的 CLI 主控台，請[安裝最新版的 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 或更新版本)。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>使用 RBAC 將 MSI 存取權指派給另一個資源

您在 Azure 資源 ([例如 Azure VM](msi-qs-configure-cli-windows-vm.md)) 上啟用 MSI 之後： 

1. 如果您要在本機主控台中使用 Azure CLI，請先使用 [az login](/cli/azure/#login) 登入 Azure。 使用您想部署 VM 且已與 Azure 訂用帳戶相關聯的帳戶：

   ```azurecli-interactive
   az login
   ```

2. 在此範例中，我們會將 Azure VM 存取權給予儲存體帳戶。 首先我們使用 [az 資源清單](/cli/azure/resource/#list)取得 VM 名為 "myVM" 的服務主體，這是我們在 VM 上啟用 MSI 時所建立的：

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```

3. 一旦我們有服務主體 ID 後，使用 [az 角色指派建立](/cli/azure/role/assignment#az_role_assignment_create)將 VM「讀取」存取權提供給稱為 "myStorageAcct" 的儲存體帳戶：

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>疑難排解

如果資源的 MSI 沒有顯示在可用的身分識別清單中，請確認已正確啟用 MSI。 在我們的案例中，我們可以返回 [Azure 入口網站](https://portal.azure.com)中的 Azure VM，並且：

- 查看 [設定] 頁面，並確定已啟用的 MSI = "Yes"。
- 查看 [延伸模組] 頁面，並確定已成功部署 MSI 延伸模組。

如果其中一者不正確，您可能需要再次在資源上重新部署 MSI，或針對部署失敗進行疑難排解。

## <a name="related-content"></a>相關內容

- 如需 MSI 的概觀，請參閱[受管理的服務識別概觀](msi-overview.md)。
- 若要在 Azure VM 上啟用 MSI，請參閱[使用 Azure CLI 設定 Azure VM 受管理的服務身分識別 (MSI)](msi-qs-configure-cli-windows-vm.md)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。

