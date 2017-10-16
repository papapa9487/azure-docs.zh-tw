---
title: "如何使用 Azure 入口網站設定 Azure VM 上的 MSI"
description: "使用 Azure 入口網站在 Azure VM 上設定「受管理的服務身分識別 (MSI)」的逐步指示。"
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
ms.date: 09/19/2017
ms.author: bryanla
ms.openlocfilehash: 169417530da21e0c8c58cbf770fd1d26660387f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>使用 Azure 入口網站設定「VM 受管理的服務身分識別 (MSI)」

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

在 Azure Active Directory 中，「受管理的服務身分識別」會提供自動受管理的身分給 Azure 服務。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您將了解如何使用 Azure 入口網站啟用和移除 Azure VM 的 MSI。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>在建立 Azure VM 時啟用 MSI

從本文編寫完成後，不支援在 Azure 入口網站中建立 VM 期間啟用 MSI。 因此，請參閱下列 VM 建立快速入門文章之一，先建立 VM：

- [使用 Azure 入口網站建立 Windows 虛擬機器](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [使用 Azure 入口網站建立 Linux 虛擬機器](../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

然後進入下一節，取得在虛擬機器上啟用 MSI 的詳細資料。

## <a name="enable-msi-on-an-existing-azure-vm"></a>在現有的 Azure VM 上啟用 MSI

如果您的 VM 原本沒有佈建MSI：

1. 使用與包含虛擬機器的 Azure 訂用帳戶相關聯的帳戶，登入 [Azure 入口網站](https://portal.azure.com)。 此外也請確定您的帳戶屬於在 VM 上具有寫入權限的角色，例如「虛擬機器參與者」。

2. 瀏覽到需要的虛擬機器。

2. 按一下 [組態] 頁面，並選取「受管理的服務身分識別」下的 [是] 來啟用 VM 上的 MSI，然後按一下 [儲存] 。 此作業可能需要 60 秒以上才能完成：

   ![組態頁面螢幕擷取畫面](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-vm"></a>從 Azure VM 移除 MSI

如果您的虛擬機器不再需要 MSI：

1. 使用與包含虛擬機器的 Azure 訂用帳戶相關聯的帳戶，登入 [Azure 入口網站](https://portal.azure.com)。 此外也請確定您的帳戶屬於在 VM 上具有寫入權限的角色，例如「虛擬機器參與者」。

2. 瀏覽到需要的虛擬機器。

3. 按一下 [組態] 頁面，並選取「受管理的服務身分識別」下的 [否] 來移除 VM 上的 MSI，然後按一下 [儲存] 。 此作業可能需要 60 秒以上才能完成：

   ![組態頁面螢幕擷取畫面](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>相關內容

- 如需 MSI 的概觀，請參閱[受管理的服務識別概觀](msi-overview.md)。

## <a name="next-steps"></a>後續步驟

- 使用 Azure 入口網站，提供 Azure VM 的 MSI[ 存取權給另一個 Azure 資源](msi-howto-assign-access-portal.md)。

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。
