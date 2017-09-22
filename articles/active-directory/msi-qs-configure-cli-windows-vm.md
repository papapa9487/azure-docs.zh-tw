---
title: "如何使用 Azure CLI 設定 Azure VM 上的 MSI"
description: "使用 Azure CLI 在 Azure VM 上設定「受管理的服務身分識別 (MSI)」的逐步指示。"
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
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 78a6164e76f6ceab936874e68bd38bb4eb387e00
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-azure-cli"></a>使用 Azure CLI 設定「VM 受管理的服務身分識別 (MSI)」

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

在 Azure Active Directory 中，「受管理的服務身分識別」會提供自動受管理的身分給 Azure 服務。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您將了解如何使用 Azure CLI 啟用和移除 Azure Windows VM 的 MSI，。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

若要執行 CLI 指令碼範例，您有三個選項：

- 從 Azure 入口網站使用 [Azure Cloud Shell](../cloud-shell/overview.md) (請參閱下一節)。
- 請透過每個程式碼區塊右上角的 [立即試用] 按鈕，使用內嵌的 Azure Cloud Shell。
- 如果您想要使用本機的 CLI 主控台，請[安裝最新版的 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 或更新版本)。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>在建立 Azure VM 時啟用 MSI

使用指定的組態參數，在新資源群組中新建已啟用 MSI 的 Windows 虛擬機器資源。 請注意，其中多個函式可能會執行數秒/分鐘才回傳。

1. 如果您不是從 Azure 入口網站中使用 Azure Cloud Shell，初次登入 Azure 請使用 [az 登入](/cli/azure/#login)。 使用您想部署 VM 且已與 Azure 訂用帳戶相關聯的帳戶：

   ```azurecli-interactive
   az login
   ```

2. 使用 [az group create](/cli/azure/group/#create)，為您的 VM 和其相關資源建立[資源群組](../azure-resource-manager/resource-group-overview.md#terminology)。 如果您已經有想要使用的資源群組，您可以略過此步驟：

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. 使用 [az vm create](/cli/azure/vm/#create) 建立 VM。 下列範例會依要求使用 `--assign-identity` 參數，建立具有 MSI 且名為 *myVM* 的 VM。 `--admin-username` 和 `--admin-password` 參數會指定登入虛擬機器的系統管理使用者名稱和密碼帳戶。 請針對您的環境適當地更新這些值： 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>在現有的 Azure VM 上啟用 MSI

如果您要在現有的虛擬機器上啟用 MSI：

1. 如果您不是從 Azure 入口網站中使用 Azure Cloud Shell，初次登入 Azure 請使用 [az 登入](/cli/azure/#login)。 使用您想部署 VM 且已與 Azure 訂用帳戶相關聯的帳戶：

   ```azurecli-interactive
   az login
   ```

2. 使用 [az vm assign-identity](/cli/azure/vm/#az_vm_assign_identity)和 `--assign-identity` 將 MSI 新增至現有 VM：

   ```azurecli-interactive
   az vm assign-identity -g myResourceGroup -n myVm
   ```

## <a name="remove-msi-from-an-azure-vm"></a>從 Azure VM 移除 MSI

如果您的虛擬機器不再需要 MSI：

1. 如果您不是從 Azure 入口網站中使用 Azure Cloud Shell，初次登入 Azure 請使用 [az 登入](/cli/azure/#login)。 使用您想部署 VM 且已與 Azure 訂用帳戶相關聯的帳戶：

   ```azurecli-interactive
   az login
   ```

2. 使用 `-n ManagedIdentityExtensionForWindows` 參數和 [az vm extension delete](https://docs.microsoft.com/cli/azure/vm/#assign-identity) 移除 MSI：

   ```azurecli-interactive
   az vm extension delete --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
   ```

## <a name="related-content"></a>相關內容

- [受管理的服務識別概觀](msi-overview.md)
- 本文改編自[使用 CLI 建立 Windows 虛擬機器](../virtual-machines/windows/quick-create-cli.md)快速入門，為包含 MSI 特定指示而修改。 

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。

















