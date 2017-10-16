---
title: "如何為自訂開發的應用程式變更權杖存留期預設值 | Microsoft Docs"
description: "如何為針對 Azure AD 開發的應用程式更新權杖存留期原則"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: a28eacd820ed28a6470992ce86b060e886c00bcb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>如何為自訂開發的應用程式變更權杖存留期預設值

Azure AD Premium 可讓應用程式開發人員與租用戶系統管理員為針對非機密用戶端簽發的權杖設定存留期。 權杖存留期原則是以整個租用戶為基礎所設定，或針對要存取的資源所設定。

 * 若要設定權杖存留期原則，您必須下載 [Azure AD PowerShell 模組 (英文)](https://www.powershellgallery.com/packages/AzureADPreview)。

 * 執行 **Connect-AzureAD -Confirm** 命令。

 * 下列範例原則會設定單一要素重新整理權杖存留期上限。 建立原則：```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * 請參閱[設定權杖存留期](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes)一文，以了解如何建立其他自訂。

## <a name="next-steps"></a>後續步驟
[設定權杖存留期](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Azure AD 權杖參考](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-token-and-claims)

