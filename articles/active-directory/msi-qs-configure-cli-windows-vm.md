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
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: fe276fe802eceb1f062ed8bda685dd44a1e3d175
ms.contentlocale: zh-tw
ms.lasthandoff: 09/26/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-azure-cli"></a>使用 Azure CLI 設定「VM 受管理的服務身分識別 (MSI)」

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

在 Azure Active Directory 中，「受管理的服務身分識別」會提供自動受管理的身分給 Azure 服務。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您將了解如何使用 Azure CLI 啟用和移除 Azure VM 的 MSI。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

若要執行 CLI 指令碼範例，您有三個選項：

- 從 Azure 入口網站使用 [Azure Cloud Shell](../cloud-shell/overview.md) (請參閱下一節)。
- 請透過每個程式碼區塊右上角的 [立即試用] 按鈕，使用內嵌的 Azure Cloud Shell。
- 如果您想要使用本機的 CLI 主控台，請[安裝最新版的 CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 或更新版本)。 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>在建立 Azure VM 時啟用 MSI

若要建立啟用 MSI 的虛擬機器：

1. 如果您要在本機主控台中使用 Azure CLI，請先使用 [az login](/cli/azure/#login) 登入 Azure。 使用您想部署 VM 且已與 Azure 訂用帳戶相關聯的帳戶：

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

1. 如果您要在本機主控台中使用 Azure CLI，請先使用 [az login](/cli/azure/#login) 登入 Azure。 使用與包含虛擬機器的 Azure 訂用帳戶相關聯的帳戶。 此外也請確定您的帳戶屬於在 VM 上具有寫入權限的角色，例如「虛擬機器參與者」：

   ```azurecli-interactive
   az login
   ```

2. 使用 [az vm assign-identity](/cli/azure/vm/#az_vm_assign_identity)和 `--assign-identity` 將 MSI 新增至現有 VM：

   ```azurecli-interactive
   az vm assign-identity -g myResourceGroup -n myVm
   ```

## <a name="remove-msi-from-an-azure-vm"></a>從 Azure VM 移除 MSI

如果您的虛擬機器不再需要 MSI：

1. 如果您要在本機主控台中使用 Azure CLI，請先使用 [az login](/cli/azure/#login) 登入 Azure。 使用與包含虛擬機器的 Azure 訂用帳戶相關聯的帳戶。 此外也請確定您的帳戶屬於在 VM 上具有寫入權限的角色，例如「虛擬機器參與者」：

   ```azurecli-interactive
   az login
   ```

2. 使用包含 [az vm extension delete](https://docs.microsoft.com/cli/azure/vm/#assign-identity) 的 `-n ManagedIdentityExtensionForWindows` 或 `-n ManagedIdentityExtensionForLinux` 切換 (取決於 VM 的類型)，來移除 MSI：

   ```azurecli-interactive
   az vm extension delete --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
   ```

## <a name="related-content"></a>相關內容

- [受管理的服務識別概觀](msi-overview.md)
- 如需完整的 Azure VM 建立快速入門，請參閱： 

  - [使用 CLI 建立 Windows 虛擬機器](../virtual-machines/windows/quick-create-cli.md)  
  - [使用 CLI 建立 Linux 虛擬機器](../virtual-machines/linux/quick-create-cli.md) 

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。

















