---
title: "如何使用範本設定 Azure VM 上的 MSI"
description: "使用 Azure Resource Manager 範本在 Azure VM 上設定「受管理的服務身分識別 (MSI)」的逐步指示。"
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
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: 8b599c3e0e7d4fa3ae5bdb156191bff0553249ee
ms.contentlocale: zh-tw
ms.lasthandoff: 09/23/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-a-template"></a>使用範本設定「VM 受管理的服務身分識別 (MSI)」

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

在 Azure Active Directory 中，「受管理的服務身分識別」會提供自動受管理的身分給 Azure 服務。 您可以使用此身分識別來向任何支援 Azure AD 驗證的服務進行驗證，不需要任何您程式碼中的認證。 

在本文中，您將了解如何使用 Azure Resource Manager 部署範本啟用和移除 Azure VM 的 MSI。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>在建立 Azure VM 時，或在現有 VM 上啟用 MSI

如同 Azure 入口網站和指令碼，Azure Resource Manager 範本提供部署由 Azure 資源群組定義之新/修改資源的能力。 針對範本編輯和部署有許多選項可用，包括本機以及入口網站/網頁。 其中包括：

   - 使用 [Azure Marketplace 所提供的自訂範本](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)，可讓您從頭開始建立範本，或以現有的一般或[快速入門範本](https://azure.microsoft.com/documentation/templates/)為基礎。
   - 衍生自現有的資源群組，方法是從[原始部署](../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history)，或從[部署的目前狀態](../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group)匯出範本。
   - 使用本機 [JSON 編輯器 (例如 VS 程式碼)](../azure-resource-manager/resource-manager-create-first-template.md)，然後使用 PowerShell 或 CLI 上傳/部署。
   - 使用 Visual Studio 的 [Azure Resource Group 專案](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)來建立和部署範本。  

不論您採取何種路徑，在初始部署和重新部署期間範本語法都是相同的，使得在新的或現有 VM 上啟用 MSI 都可以相同方式完成。 此外，根據預設 Azure Resource Manager 會[累加式更新](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)部署：

1. 無論您是在本機登入 Azure 或透過 Azure 入口網站登入，都請使用與包含虛擬機器的 Azure 訂用帳戶相關聯的帳戶。 您也需要確定您的帳戶屬於在 VM 上具有寫入權限的角色，例如「虛擬機器參與者」。

2. 將範本載入編輯器中之後，找出＜`resources`一節中您感興趣的 `Microsoft.Compute/virtualMachines` 資源。 根據您所使用的編輯器，以及您正在編輯新的部署或現有部署的範本，您的畫面與這個螢幕擷取畫面可能看起來稍有不同：

   >[!NOTE] 
   > 此範例假設在範本中已定義了 `vmName`、`storageAccountName` 和 `nicName` 這類變數。
   >

   ![螢幕擷取畫面之前的範本 - 找出 VM](./media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. 使用下列語法，以與 `"type": "Microsoft.Compute/virtualMachines"` 屬性相同的層級新增 `"identity"` 屬性：

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. 然後使用下列語法將 VM MSI 延伸模組新增為 `resources` 元素：

   >[!NOTE] 
   > 下列範例假設 Windows VM 延伸模組 (`ManagedIdentityExtensionForWindows`) 正在部署。 針對 `"name"` 和 `"type"` 元素，您也可改用 `ManagedIdentityExtensionForLinux` 來設定 Linux。
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2016-03-30",
       "location": "[resourceGroup().location]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.ManagedIdentity",
           "type": "ManagedIdentityExtensionForWindows",
           "typeHandlerVersion": "1.0",
           "autoUpgradeMinorVersion": true,
           "settings": {
               "port": 50342
           },
           "protectedSettings": {}
       }
   }
   ```

5. 完成之後，您的範本看起來應該類似下列範例：

   ![擷取畫面之後的範本](./media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>從 Azure VM 移除 MSI

如果您的虛擬機器不再需要 MSI：

1. 無論您是在本機登入 Azure 或透過 Azure 入口網站登入，都請使用與包含虛擬機器的 Azure 訂用帳戶相關聯的帳戶。 此外也請確定您的帳戶屬於在 VM 上具有寫入權限的角色，例如「虛擬機器參與者」。

2. 移除在前一節中所新增的兩個元素：虛擬機器 的 `"identity"` 屬性和 `"Microsoft.Compute/virtualMachines/extensions"` 資源。

## <a name="related-content"></a>相關內容

- [受管理的服務識別概觀](msi-overview.md)

使用下列意見區段來提供意見反應，並協助我們改善及設計我們的內容。


