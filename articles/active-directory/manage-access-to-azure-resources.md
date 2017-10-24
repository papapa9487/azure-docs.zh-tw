---
title: "使用 Azure Active Directory 管理對 Azure 資源的存取"
description: "了解如何使用 Azure Active Directory 的不同功能管理對 Azure 資源的存取。"
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: bryanla
ms.assetid: f66abf54-3809-436c-92b6-018e1179780e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2017
ms.author: skwan
ms.openlocfilehash: e42bbed0a9e37a6632b2a71d2b3546fcbc38cdc7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-access-to-azure-resources-with-azure-active-directory"></a>使用 Azure Active Directory 管理對 Azure 資源的存取

對於使用雲端的任何組織而言，針對雲端資源的身分識別和存取管理是非常重要的功能。 Azure Active Directory (Azure AD) 是 Microsoft Azure 的身分識別和存取系統。  

在探索 Azure AD 支援的功能範圍之前，請觀看下列影片：「使用 SSO、角色型存取控制和條件式存取來鎖定 Azure 雲端的存取。」 這部影片可讓您了解：

- 搭配內部部署 Active Directory 來設定 Azure 入口網站單一登入的最佳作法。
- 使用 Azure RBAC 對訂用帳戶中的資源採取更細緻的存取控制。
- 使用 Azure AD 條件式存取來強制執行強式驗證規則。
- 管理服務識別的概念，其中 Azure 資源可以自動向 Azure 服務驗證，而不需要開發人員處理 API 金鑰或密碼。

> [!VIDEO https://www.youtube.com/embed/FKBoWWKRnvI]

## <a name="feature-areas"></a>功能範圍
Azure AD 提供下列功能來管理對 Azure 資源的存取：

|||
|---|---|
| [Azure AD 租用戶與訂用帳戶之間的關聯性](active-directory-understanding-resource-access.md) | 了解 Azure AD 如何作為 Azure 訂用帳戶之使用者和群組的受信任來源。 |
| [角色型存取控制 (RBAC)](role-based-access-control-what-is.md) | 透過指派給使用者、群組或服務主體的角色，提供更細緻的存取管理。 |
| [RBAC 的 Privileged Identity Management](pim-azure-resource.md) | 及時指派特殊權限角色，控制高度特殊存取權。 |
| [Azure 管理的條件式存取](conditional-access-azure-management.md) | 設定條件式存取原則，以允許或封鎖對 Azure 管理端點的存取。 |
| [受管理服務識別 (MSI)](msi-overview.md) | 為 Azure 資源 (例如虛擬機器) 提供自己的識別身分，讓您不需要將認證放在程式碼中。 |

 