---
title: "使用 RBAC 管理存取權與權限 - Azure RBAC | Microsoft Docs"
description: "在 Azure 入口網站中使用 Azure 角色型存取控制開始進行存取管理。 使用角色指派在您的目錄中指派權限。"
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
ms.assetid: 8f8aadeb-45c9-4d0e-af87-f1f79373e039
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: 68213a40755ae7dc675556c595da032b408be261
ms.contentlocale: zh-tw
ms.lasthandoff: 07/20/2017

---
# <a name="get-started-with-role-based-access-control-in-the-azure-portal"></a>在 Azure 入口網站中開始使用角色型存取控制
安全性導向公司應該將焦點放在提供員工所需的確切權限。 權限太多可能會讓帳戶暴露在攻擊者的威脅下。 權限太少則會讓員工無法有效率地完成工作。 Azure「角色型存取控制」(RBAC) 可以為 Azure 提供更細緻的存取管理來協助解決這個問題。

RBAC 可讓您區隔小組內的職責，而僅授與使用者執行作業所需的存取權。 您可以不授與每個人 Azure 訂用帳戶或資源中無限制的權限，而是只允許執行特定的動作。 例如，使用 RBAC 讓一位員工管理某個訂用帳戶中的虛擬機器，而讓另一位員工管理相同訂用帳戶中的 SQL 資料庫。

## <a name="basics-of-access-management-in-azure"></a>Azure 存取權管理的基礎
每個 Azure 訂用帳戶都會與一個 Azure Active Directory (AD) 目錄相關聯。 該目錄中的使用者、群組和應用程式可以管理 Azure 訂用帳戶中的資源。 請使用 Azure 入口網站、Azure 命令列工具或「Azure 管理」API 來指派這些存取權限。

您可以藉由在特定範圍將適當的 RBAC 角色指派給使用者、群組及應用程式，來授與存取權。 角色指派的範圍可以是訂用帳戶、資源群組或單一資源。 在父範圍指派的角色也會授與其內含子系的存取權。 例如，具有資源群組存取權的使用者可以管理其內含的所有資源，例如網站、虛擬機器和子網路。

![Azure Active Directory 元素之間的關聯性 - 圖表](./media/role-based-access-control-what-is/rbac_aad.png)

您指派的 RBAC 角色，指定使用者、群組或應用程式可以在該範圍內管理的資源。

## <a name="built-in-roles"></a>內建角色
Azure RBAC 有適用於所有資源類型的三個基本角色：

* **擁有者** 具有所有資源的完整存取權，包括將存取權委派給其他人的權限。
* **參與者** 可以建立和管理所有類型的 Azure 資源，但是不能將存取權授與其他人。
* **讀者** 可以檢視現有的 Azure 資源。

Azure 中其餘的 RBAC 角色可以管理特定 Azure 資源。 例如，「虛擬機器參與者」角色可讓使用者建立和管理虛擬機器。 但不會授予他們存取虛擬機器所連接的虛擬網路或子網路的存取權。 

[RBAC 內建角色](role-based-access-built-in-roles.md) 會列出 Azure 中可用的角色。 它會指定每個內建角色授與使用者的作業和範圍。 如果您想要定義自己的角色，獲得更進一步控制，請參閱如何建立 [Azure RBAC 中的自訂角色](role-based-access-control-custom-roles.md)。

## <a name="resource-hierarchy-and-access-inheritance"></a>資源階層和存取繼承
* Azure 中的每個 **訂用帳戶** 只屬於一個目錄。 (但每個目錄可以有多個訂用帳戶)。
* 每個 **資源群組** 只屬於一個訂用帳戶。
* 每個 **資源** 只屬於一個資源群組。

您在父範圍授與的存取權會在子範圍繼承。 例如：

* 您可將讀者角色指派給訂用帳戶範圍內的 Azure AD 群組。 該群組的成員可以檢視訂用帳戶中的每個資源群組和資源。
* 您可將參與者角色指派給資源群組範圍內的應用程式。 它可以管理該資源群組中所有類型的資源，但是無法管理訂用帳戶中的其他資源群組。

## <a name="azure-rbac-vs-classic-subscription-administrators"></a>Azure RBAC 與傳統訂用帳戶系統管理員
傳統訂用帳戶系統管理員和共同管理員具有 Azure 訂用帳戶的完整存取權。 他們可以使用 [Azure 入口網站](https://portal.azure.com)搭配 Azure Resource Manager API，或是使用 [Azure 傳統入口網站](https://manage.windowsazure.com)和 Azure 傳統部署模型，來管理資源。 在 RBAC 模型中，傳統系統管理員會獲指派訂用帳戶範圍的擁有者角色。

只有 Azure 入口網站和新的 Azure Resource Manager API 支援 Azure RBAC。 獲指派 RBAC 角色的使用者和應用程式無法使用傳統管理入口網站和 Azure 傳統部署模型。

## <a name="authorization-for-management-vs-data-operations"></a>管理與資料作業的授權
Azure RBAC 僅支援在 Azure 入口網站和 Azure Resource Manager API 中的 Azure 資源管理作業。 它無法授權 Azure 資源的所有資料層級作業。 例如，您可以授權某個人管理「儲存體帳戶」，但無法授權他管理「儲存體帳戶」內的 blob 或資料表。 同樣地，可以管理 SQL Database，但無法管理其中的資料表。

## <a name="next-steps"></a>後續步驟
* 在 Azure 入口網站中開始使用 [Azure 角色型存取控制](role-based-access-control-configure.md)。
* 參閱 [RBAC 內建角色](role-based-access-built-in-roles.md)
* 定義您自己的 [Azure RBAC 中的自訂角色](role-based-access-control-custom-roles.md)

