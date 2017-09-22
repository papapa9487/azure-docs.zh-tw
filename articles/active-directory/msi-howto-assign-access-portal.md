---
title: "如何使用 Azure 入口網站將 MSI 存取權指派給 Azure 資源"
description: "使用 Azure 入口網站在一個資源上指派 MSI，並存取另一個資源的逐步解說指示。"
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
ms.openlocfilehash: 9dd02c8d7580cd9233e192f807686b7857ccf696
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---

# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-the-azure-portal"></a>使用 Azure 入口網站將受管理的服務識別 (MSI) 存取權指派給資源

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

一旦已透過 MSI 設定 Azure 資源，您便可以將 MSI 存取權提供給另一個資源，就像任何安全性主體。 此範例將示範如何使用 Azure 入口網站將 Azure 虛擬機器的 MSI 存取權提供給 Azure 儲存體帳戶。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>使用 RBAC 將 MSI 存取權指派給另一個資源

您在 Azure 資源 ([例如 Azure VM](msi-qs-configure-portal-windows-vm.md)) 上啟用 MSI 之後：

1. 使用您已設定 MSI 且與 Azure 訂用帳戶相關聯的帳戶，登入 [Azure 入口網站](https://portal.azure.com)。

2. 瀏覽至您要修改其存取控制的資源。 在此範例中，因為我們會將 Azure VM 存取權給予儲存體帳戶，所以瀏覽至儲存體帳戶。

3. 按一下資源的 [存取控制 (IAM)] 頁面，然後按一下 [+ 新增]。 然後指定角色、指派權存權給「虛擬機器」，以及指定資源所在的對應訂用帳戶和資源群組。 在搜尋條件區域中，您應該會看到顯示的資源。 選取資源，然後點擊 [儲存]： 

   ![存取控制 (IAM) 螢幕擷取畫面](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  

4. 回到主要的 [存取控制 (IAM)] 頁面，您會看到其中已有資源的 MSI 新項目。 在此範例中，示範資源群組的 "SimpleWinVM" VM 已取得儲存體帳戶的「參與者」存取權：

   ![存取控制 (IAM) 螢幕擷取畫面](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

## <a name="troubleshooting"></a>疑難排解

如果資源的 MSI 沒有顯示在可用的身分識別清單中，請確認已正確啟用 MSI。 在我們的案例中，我們可以返回 Azure VM，並且：

- 查看 [組態] 頁面，並確定已啟用的 MSI = "Yes"。
- 查看 [延伸模組] 頁面，並確定已成功部署 MSI 延伸模組。

如果其中一者不正確，您可能需要再次在資源上重新部署 MSI，或針對部署失敗進行疑難排解。

## <a name="related-content"></a>相關內容

- 如需 MSI 的概觀，請參閱[受管理的服務識別概觀](msi-overview.md)。
- 若要在 Azure VM 上啟用 MSI，請參閱[使用 Azure 入口網站設定 Azure VM 受管理的服務身分識別 (MSI)](msi-qs-configure-portal-windows-vm.md)。



