---
title: "設定 Azure Active Directory 裝置型條件式存取原則 | Microsoft Docs"
description: "了解如何設定 Azure Active Directory 裝置型條件式存取原則。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 949cf120b9b0ae0815b492166ffc19cb0412efbd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="configure-azure-active-directory-device-based-conditional-access-policies"></a>設定 Azure Active Directory 裝置型條件式存取原則

透過 [Azure Active Directory (Azure AD) 條件式存取](active-directory-conditional-access-azure-portal.md)，您可以微調授權使用者如何存取您的應用程式。 例如，您可以特定資源的存取限制為受信任的裝置。 需要受信任裝置的條件式存取原則，也稱為裝置型條件式存取原則。

本主題提供有關如何為 Azure AD 連線應用程式設定裝置型條件式存取原則的資訊。 


## <a name="before-you-begin"></a>開始之前

裝置型條件式存取會將 **Azure AD 條件式存取**和 **Azure AD 裝置管理**繫結在一起。 如果您還不熟悉上述其中一種領域，您應該先閱讀下列主題：

- **[Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal.md)** - 本主題提供條件式存取和相關術語的概念性概觀。

- **[Azure Active Directory 中的裝置管理簡介](device-management-introduction.md)** - 本主題提供您可用來連線裝置與 Azure AD 之各種選項的概觀。 


## <a name="trusted-devices"></a>受信任的裝置

在行動第一、雲端第一的世界中，Azure Active Directory 可讓您從任何地方單一登入至裝置、應用程式和服務。 對於您環境中的某些資源，授與存取權給適當的使用者可能還不夠好。 除了適當的使用者，您可能也需要將受信任的裝置用來存取資源。 在您的環境中，您可以根據下列元件定義受信任的裝置：

- 裝置上的[裝置平台](active-directory-conditional-access-azure-portal.md#device-platforms)
- 裝置是否符合規範
- 裝置是否已加入網域 

[裝置平台](active-directory-conditional-access-azure-portal.md#device-platforms)的特點是您裝置執行的作業系統。 在裝置型條件式存取原則中，您可以將特定資源的存取權限制為特定裝置平台。



在裝置型條件式存取原則中，您可能需要標示為符合規範的受信任裝置。

![雲端應用程式](./media/active-directory-conditional-access-policy-connected-applications/24.png)

裝置可由下列項目在目錄中標示為符合規範：

- Intune 
- 協力廠商行動裝置受管理系統，可透過 Azure AD 整合來管理 Windows 10 裝置 
 
  

只有連線到 Azure AD 的裝置可標示為符合規範。 若要將裝置連線到 Azure Active Directory，您有下列選項： 

- 已註冊的 Azure AD
- 已聯結的 Azure AD
- 已聯結的混合式 Azure AD

    ![雲端應用程式](./media/active-directory-conditional-access-policy-connected-applications/26.png)

如果您有內部部署 Active Directory (AD) 使用量，您可以考慮未連線到 Azure AD 但已聯結至要信任之 AD 的裝置。

![雲端應用程式](./media/active-directory-conditional-access-policy-connected-applications/25.png)


## <a name="next-steps"></a>後續步驟

在您的環境中設定裝置型條件式存取原則之前，您應該閱讀 [Azure Active Directory 中條件式存取的最佳做法](active-directory-conditional-access-best-practices.md)。

