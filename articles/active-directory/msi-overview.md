---
title: "受管理的服務識別 (MSI) (適用於 Azure Active Directory)"
description: "受管理的服務識別 (適用於 Azure 資源) 概觀。"
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 09/14/2017
ms.author: skwan
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: 891a895419a4fe882e01495231f33a1d79eb42a9
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---

#  <a name="managed-service-identity-msi-for-azure-resources"></a>受管理的服務識別 (MSI) (適用於 Azure 資源)

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

建置雲端應用程式常見的難題是如何管理程式碼中必須存在的認證，以向雲端服務進行驗證。 保護好這些認證是相當重要的工作。 在理想情況下，這些認證永不會出現在開發人員工作站或簽入原始程式碼控制。 Azure Key Vault 可安全地儲存認證和其他金鑰及密碼，但是您的程式碼必須向 Key Vault 進行驗證，才可取得這些項目。 受管理的服務身分識別 (MSI) 可以輕易地解決此問題，因為 MSI 可在 Azure Active Directory (Azure AD) 中提供自動受管理的身分識別給 Azure 服務。 您可以使用此身分識別來完成任何支援 Azure AD 驗證的服務驗證 (包括 Key Vault)，不需要任何您程式碼中的認證。

## <a name="how-does-it-work"></a>運作方式

當您在 Azure 服務上啟用受管理的服務身分識別時，Azure 會自動為 Azure AD 租用戶 (由您的 Azure 訂用帳戶所使用) 中的服務執行個體建立身分識別。  實際上，Azure 會將身分識別認證佈建到服務執行個體。  您的程式碼可以執行索取存取權杖的本機要求，來存取支援 Azure AD 驗證的服務。  Azure 會負責更新服務個體使用的認證。  如果服務執行個體已刪除，則 Azure 會自動清除 Azure AD 中的認證和身分識別。

以下是受管理的服務身分識別與 Azure 虛擬機器的運作方式範例。

![虛擬機器 MSI 範例](./media/msi-vm-example.png)

1. Azure Resource Manager 收到訊息，啟用 VM 上的 MSI。
2. Azure Resource Manager 在 Azure AD 中建立服務主體，代表 VM 的身分識別。 服務主體會建立在此訂用帳戶信任的 Azure AD 租用戶中。
3. Azure Resource Manager 會在 VM 的 MSI VM 延伸模組中設定服務主體詳細資料。  此步驟包括設定延伸模組用來從 Azure AD 取得存取權杖的用戶端識別碼和認證。
4. 現在我們已知道 VM 的服務主體身分識別，可以將 Azure 資源的存取權授與此身分識別。  例如，您的程式碼需要呼叫 Azure Resource Manager，您會使用 Azure AD 中的角色型存取控制 (RBAC)，指派適當角色給 VM 的服務主體。  如果您的程式碼需要呼叫 Key Vault，您會將存取權授與程式碼，以取得 Key Vault 中特定的密碼或金鑰。
5. 執行於 VM 上的程式碼會從本機端點要求由 MSI VM 延伸模組：http://localhost:50342/oauth2/token 託管的權杖。  資源參數會指定權杖該傳送到哪個服務。 例如，如果您希望程式碼向 Azure Resource Manager 進行驗證，您會使用resource=https://management.azure.com/。
6. MSI VM 延伸模組會使用其設定的用戶端識別碼和認證，來向 Azure AD 要求存取權杖。  Azure AD 會傳回 JSON Web 權杖 (JWT) 存取權杖。
7. 您的程式碼會在呼叫上傳送存取權杖給支援 Azure AD 驗證的服務。

每個支援「受管理的服務識別身分」的 Azure 服務都自有一套方法，讓您的程式碼取得存取權杖。 請查看每個服務的教學課程，找出取得權杖的特定方法。

## <a name="which-azure-services-support-managed-service-identity"></a>哪些 Azure 服務支援受管理的服務身分識別？

支援「受管理的服務身分識別」的 Azure 服務若支援 Azure AD 驗證，則可使用 MSI 向服務進行驗證。  我們正在努力整合 MSI 和整個 Azure 的 Azure AD 驗證。  請經常回來檢查更新。

### <a name="azure-services-that-support-managed-service-identity"></a>支援「受管理的服務身分識別」的 Azure 服務

下列 Azure 服務支援受管理的服務身分識別。

| 服務 | 狀態 | Date |
| --- | --- | --- |
| Azure 虛擬機器 | 預覽 | 2017 年 9 月 |
| Azure 虛擬機器擴展集 | 預覽 | 2017 年 9 月 |
| Azure App Service | 預覽 | 2017 年 9 月 |
| Azure Functions | 預覽 | 2017 年 9 月 |

### <a name="azure-services-that-support-azure-ad-authentication"></a>支援 Azure AD 驗證的 Azure 服務

下列服務支援 Azure AD 驗證，並已經過使用「受管理的服務身分識別」的用戶端服務測試。

| 服務 | 資源識別碼 | 狀態 | Date |
| --- | --- | --- | --- |
| Azure Resource Manager | https://management.azure.com/ | 可用 | 2017 年 9 月 |
| Azure 金鑰保存庫 | https://vault.azure.net/ | 可用 | 2017 年 9 月 |
| Azure SQL | https://database.windows.net/ | 可用 | 2017 年 9 月 |
| Azure Data Lake | https://datalake.azure.net/ | 可用 | 2017 年 9 月 |

## <a name="how-much-does-managed-service-identity-cost"></a>「受管理的服務身分識別」的售價如何？

「受管理的服務身分識別」隨附於 Azure Active Directory Free，這是 Azure 訂用帳戶的預設功能。  無須針對「受管理的服務身分識別」支付其他費用。

## <a name="support-and-feedback"></a>支援與意見反應

希望能夠聽到您的意見！

* 請在有 [azure msi](http://stackoverflow.com/questions/tagged/azure-msi) 標記的 Stack Overflow 上詢問作法問題。
* 請在[適用於開發人員的 Azure AD 意見反應論壇](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences)中索取功能或給予意見反應。

## <a name="try-managed-service-identity"></a>試用受管理的服務身分識別

現在您已了解基礎概念，請透過受管理的服務身分識別教學課程，來了解如何存取不同 Azure 資源。

- [使用 Windows VM 受管理的服務身分識別存取 Azure Resource Manager](msi-tutorial-windows-vm-access-arm.md)
- [使用 Linux VM 受管理的服務身分識別存取 Azure Resource Manager](msi-tutorial-linux-vm-access-arm.md)
- [使用 Windows VM 受管理的服務身分識別存取 Azure 儲存體](msi-tutorial-windows-vm-access-storage.md)
- [使用 Linux VM 受管理的服務身分識別存取 Azure 儲存體](msi-tutorial-linux-vm-access-storage.md)
- [使用 Windows VM 受管理的服務身分識別存取非 Azure AD 資源](msi-tutorial-windows-vm-access-nonaad.md)
- [使用 Linux VM 受管理的服務身分識別存取非 Azure AD 資源](msi-tutorial-linux-vm-access-nonaad.md)
- [使用者受管理的服務身分識別與 Azure App Service 或 Azure Functions](/azure/app-service/app-service-managed-service-identity)


















