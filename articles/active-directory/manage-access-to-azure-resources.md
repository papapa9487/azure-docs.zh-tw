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
ms.date: 09/25/2017
ms.author: skwan
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 17879d69b95186848a81e4ff4afdf8fc0d1a511e
ms.contentlocale: zh-tw
ms.lasthandoff: 09/26/2017

---

# <a name="manage-access-to-azure-resources-with-azure-active-directory"></a>使用 Azure Active Directory 管理對 Azure 資源的存取

對於使用雲端的任何組織而言，針對雲端資源的身分識別和存取管理是非常重要的功能。  Azure Active Directory (Azure AD) 是 Microsoft Azure 的身分識別和存取系統。  

Azure AD 提供下列功能來管理對 Azure 資源的存取：

|||
|---|---|
| [Azure AD 租用戶與訂用帳戶之間的關聯性](active-directory-understanding-resource-access.md) | 了解 Azure AD 如何作為 Azure 訂用帳戶之使用者和群組的受信任來源。 |
| [角色型存取控制 (RBAC)](role-based-access-control-what-is.md) | 透過指派給使用者、群組或服務主體的角色，提供更細緻的存取管理。 |
| [RBAC 的 Privileged Identity Management](pim-azure-resource.md) | 及時指派特殊權限角色，控制高度特殊存取權。 |
| [Azure 管理的條件式存取](conditional-access-azure-management.md) | 設定條件式存取原則，以允許或封鎖對 Azure 管理端點的存取。 |
| [受管理服務識別 (MSI)](msi-overview.md) | 為 Azure 資源 (例如虛擬機器) 提供自己的識別身分，讓您不需要將認證放在程式碼中。 |

 
