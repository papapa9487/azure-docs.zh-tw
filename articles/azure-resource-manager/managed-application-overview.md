---
title: "Azure 受管理的應用程式概觀 | Microsoft Docs"
description: "說明 Azure 受管理應用程式的概念"
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 09/19/2017
ms.author: gauravbh
ms.openlocfilehash: 96b07bb3b923a5120e3d43c3fa60b3b1308010b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-managed-applications-overview"></a>Azure 受管理的應用程式概觀

使用 Azure 的廠商可為世界各地的客戶提供解決方案。 Azure Marketplace 是一個資源庫，包含第一方和第三方廠商的數百個複雜、多資源範本。 客戶可以在幾分鐘內部署及啟動平台即服務 (PaaS) 和軟體即服務 (SaaS) 應用程式。 

雖然 Marketplace 為客戶提供能快速部署產品的絕佳方式，但客戶仍必須負責維護和更新解決方案。 除了虛擬機器映像計費，廠商無法向客戶收取應用程式使用費用。 此外，廠商無法防止客戶修改重要的應用程式資源。 供應商也無法阻止存取組成應用程式的智慧屬性。 Azure 受管理的應用程式針對這些問題提供了一些解決方案。 

受管理的應用程式與 Marketplace 中的解決方案範本類似，只有一個主要差異。 在受管理的應用程式中，資源會佈建到廠商所管理的資源群組。 資源群組存在於客戶的訂用帳戶，但廠商租用戶中的身分識別可以存取資源群組。

## <a name="advantages-of-managed-applications"></a>受管理應用程式的優點

受管理的服務提供者 (MSP)、ISV 和公司中央 IT 小組可以使用受管理的應用程式，透過 Marketplace 或 Service Catalog 提供解決方案。 雖然客戶在其訂用帳戶中部署這些受管理的應用程式，但他們不需要維護、更新或服務它們。 因為廠商會管理和支援應用程式，所以客戶不需要開發應用程式特定領域知識來管理這些應用程式。 客戶可以自動取得應用程式更新，而不必擔心應用程式的疑難排解和診斷問題。

對於廠商及提供者，受管理的應用程式會建立一個管道，以透過 Marketplace 銷售基礎結構和軟體。 受管理的應用程式也提供將服務和操作支援附加至 Azure 客戶的方式。 供應商可以使用 Azure 計費系統向客戶開立帳單。 他們可以使用範本來管理已部署的應用程式生命週期。 這些解決方案都各自獨立且客戶不會知曉，因此廠商可以提供高品質的服務。 這種方式有益於 PaaS 和 SaaS 供應商。 此方法讓公司中央平台小組和想要封裝並轉售其解決方案的系統整合業者也都能有所受益。

## <a name="managed-application-types"></a>受管理的應用程式類型
Azure 受管理的應用程式以兩種類型提供 - Service Catalog 和 Marketplace。
 
### <a name="service-catalog"></a>Service Catalog  

透過 Service Catalog，客戶可以建立已核准解決方案的目錄，讓該組織中的人員使用 Azure。 維護這類解決方案的目錄對於企業的中央 IT 小組很有幫助。 他們可以使用目錄來確保符合特定組織標準，同時為其組織提供一些解決方案。 他們可以控制、更新和維護這些應用程式。 員工可以使用目錄輕易探索其 IT 部門所建議和核准的豐富應用程式集合。 客戶會看到他們所建立的 Service Catalog 受管理應用程式。 他們也會看到組織中的其他人所共用的受管理應用程式。
 
如需發佈 Service Catalog 受管理應用程式的資訊，請參閱[建立和發佈 Service Catalog 受管理的應用程式](managed-application-publishing.md)。
 
如需使用 Service Catalog 受管理應用程式的詳細資訊，請參閱[使用 Service Catalog 受管理的應用程式](managed-application-consumption.md)。
 
### <a name="marketplace"></a>Marketplace

可透過 Azure 入口網站的 Marketplace 取得的受管理應用程式。 在廠商發佈應用程式之後，這些應用程式便可供組織內外的所有人取用。 透過這種方法，MSP、ISV 和 SI 能將其解決方案提供給所有的 Azure 客戶。 客戶可獲得使用這些複雜解決方案的好處，而不需要費心了解和維護解決方案。 

目前，發行者可以讓其供應項目提供為受管理的應用程式，或是不受管理的解決方案範本。 發佈受管理應用程式的主要元件包括範本檔案和 UI 定義檔。 範本檔案描述已佈建的資源。 UI 定義檔描述用於佈建這些資源的必要輸入在入口網站中的顯示方式。 必要的檔案會封裝為 .zip 檔案，並透過發行入口網站上傳。
 
如需在 Marketplace 上發佈受管理應用程式的詳細資訊，請參閱 [Marketplace 中 Azure 受管理的應用程式](managed-application-author-marketplace.md)。

如需從 Marketplace 使用受管理應用程式的詳細資訊，請參閱[在 Marketplace 中使用 Azure 受管理的應用程式](managed-application-consume-marketplace.md)。

## <a name="key-concepts"></a>重要概念

### <a name="managed-resource-group"></a>受管理的資源群組
受管理的資源群組是範本中佈建的所有 Azure 資源的建立位置。 例如，如果應用程式用來建立儲存體帳戶，此資源群組就會包含儲存體帳戶資源。 它不包含應用程式資源。

### <a name="application-package"></a>應用程式套件
發行者建立的套件中，會包含範本檔案和 createUIDefinition 檔案。 具體來說，它包含下列檔案：

- **mainTemplate.json**：此範本檔案定義由應用程式佈建的所有資源。 這個檔案是用來建立資源的一般範本檔案。

- **createUIDefinition.json**：這個檔案會描述範本中定義的參數所需之 UI 的呈現方式。

### <a name="authorization"></a>Authorization
發行者必須指定廠商所需的權限，才能代表客戶管理資源。 此權限會套用至受管理的資源群組。 設定下列值：

- **PrincipalID**：使用者、群組或應用程式的 Azure Active Directory (Azure AD) 識別碼，可用來授與受管理資源群組的存取權。 這個識別碼是屬於發行者的租用戶。

- **RoleDefinitionID**：指派給先前主體識別碼之角色的 Azure AD 識別碼。 可能是發行者租用戶中的任何內建角色型存取控制角色。 如需詳細資訊，請參閱 [Azure 角色型存取控制的內建角色](../active-directory/role-based-access-built-in-roles.md)。

## <a name="next-steps"></a>後續步驟

* 如需發佈受管理的應用程式到 Marketplace 的資訊，請參閱 [Marketplace 中 Azure 受管理的應用程式](managed-application-author-marketplace.md)。
* 如需從 Marketplace 使用受管理應用程式的詳細資訊，請參閱[在 Marketplace 中使用 Azure 受管理的應用程式](managed-application-consume-marketplace.md)。
* 如需發佈 Service Catalog 受管理應用程式的資訊，請參閱[建立和發佈 Service Catalog 受管理的應用程式](managed-application-publishing.md)。
* 如需使用 Service Catalog 受管理應用程式的詳細資訊，請參閱[使用 Service Catalog 受管理的應用程式](managed-application-consumption.md)。
* 若要建立 UI 定義檔，請參閱[開始使用 CreateUiDefinition](managed-application-createuidefinition-overview.md)。
