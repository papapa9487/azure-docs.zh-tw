---
title: "使用 Privileged Identity Management (PIM) 管理 Azure 資源的存取權"
description: "了解如何使用 PIM 中的角色型存取管理來存取 Azure 資源。"
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: bryanla
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/22/2017
ms.author: skwan
ms.openlocfilehash: 4245d0dd9798046674f9ae3b197cec6b9b5d2545
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-access-to-azure-resources-with-privileged-identity-management-preview"></a>使用 Privileged Identity Management (預覽) 管理 Azure 資源的存取權

若要保護具特殊權限的帳戶免受惡意網路攻擊的威脅，您可以使用 Azure Active Directory Privileged Identity Management (PIM) 來降低權限的曝光時間，並透過報告和警示提升您對其使用情況的可見度。 PIM 會限制使用者只能在 Just-In-Time (JIT) 狀態下行使其權限，或指派持續時間較短的權限，並在時間到達之後自動撤銷該權限，以達成上述目的。 

您現在可以使用 PIM 與 Azure 角色型存取控制 (RBAC) 來管理、控制和監視 Azure 資源的存取權。 PIM 可以管理內建和自訂角色的成員資格，以助您： 

- 視需要啟用 Just-In-Time 存取 Azure 資源。
- 讓已指派使用者和群組的資源存取權自動過期。
- 針對快速工作和值勤排程指派暫時的 Azure 資源存取權。
- 在新使用者或群組獲指派資源存取權，以及啟動適用指派時，取得警示。

如需詳細資訊，請參閱 [Azure PIM 中的角色型存取控制概觀](privileged-identity-management/azure-pim-resource-rbac.md)。