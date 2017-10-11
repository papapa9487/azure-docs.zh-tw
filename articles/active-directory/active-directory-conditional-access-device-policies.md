---
title: "Office 365 服務的 Azure Active Directory 條件式存取裝置原則 | Microsoft Docs"
description: "了解如何佈建條件式存取裝置原則，以協助讓公司資源更安全，同時保有使用者合規性和服務的存取權。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8664c0bb-bba1-4012-b321-e9c8363080a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 10a4a23af08c31ea107e196ae441fefd39c7cabc
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="active-directory-conditional-access-device-policies-for-office-365-services"></a>適用於 Office 365 服務的 Active Directory 條件式存取裝置原則

條件式存取需要多個部分才能運作。 其中包含經過多重要素驗證的使用者、已驗證的裝置及符合規範的裝置等因素。 本文著重於組織可用的裝置型條件，以協助您控制 Office 365 服務的存取。 

公司使用者想要在公司或學校從其個人裝置存取 Office 365 服務，例如 Exchange 和 SharePoint Online。 您想要安全地存取。 您可以佈建條件式存取裝置原則，以協助讓公司資源更安全，同時為使用符合規範之裝置的使用者授與服務的存取權。 您可以在 Microsoft Intune 條件式存取入口網站中設定 Office 365 的條件式存取原則。

Azure Active Directory (Azure AD) 會強制執行條件式存取原則，以協助保護 Office 365 服務的存取安全。 您可以建立條件式存取原則，以封鎖使用不符合規範之裝置的使用者存取 Office 365 服務。 使用者必須先符合公司的裝置原則，才能獲得服務的存取權。 或者，您可以建立原則，要求使用者註冊其裝置以取得 Office 365 服務的存取權。 原則可以套用至組織中的所有使用者，或是僅限於少數的目標群組。 隨著時間推移，您可以在原則中加入更多目標群組。

強制執行裝置原則的必要條件是使用者必須使用 Azure AD 裝置註冊服務註冊其裝置。 針對使用 Azure AD 裝置註冊服務註冊的裝置，您可以選擇開啟多重要素驗證。 建議針對 Azure Active Directory 裝置註冊服務使用多重要素驗證。 開啟多重要素驗證時，使用 Azure AD 裝置註冊服務註冊其裝置的使用者需經過第二個要素驗證。

## <a name="how-does-a-conditional-access-policy-work"></a>條件式存取原則如何運作？

當使用者要求從支援的裝置平台存取 Office 365 服務時，Azure AD 會驗證使用者和裝置。 唯有當使用者符合為服務設定的原則時，Azure AD 才會授與服務的存取權。 系統會提供指示給未註冊裝置上的使用者，告知如何註冊及符合規範以存取公司 Office 365 服務。 iOS 和 Android 裝置上的使用者必須使用 Intune 公司入口網站應用程式來註冊其裝置。 當使用者註冊裝置時，該裝置會向 Azure AD 登錄，並針對裝置管理和合規性進行註冊。 您必須使用 Azure AD 裝置註冊服務搭配 Microsoft Intune，以進行 Office 365 服務的行動裝置管理。 若已強制執行裝置原則，使用者需要進行裝置註冊，才能存取 Office 365 服務。

當使用者成功註冊裝置時，該裝置就會成為受信任的。 Azure AD 會為已驗證的使用者提供公司應用程式的單一登入存取權。 Azure AD 不只會在使用者第一次要求存取權時，而且會在每次使用者更新要求以進行存取時，強制執行條件式存取原則來授與服務的存取權。 當登入認證變更、裝置遺失或遭竊，或在要求更新時不符合原則的條件時，使用者將被拒絕存取服務。

## <a name="deployment-considerations"></a>部署考量

您必須使用 Azure AD 裝置註冊服務來註冊裝置。

當內部部署使用者正準備進行驗證時，必須具備 Active Directory 同盟服務 (AD FS) (1.0 版和更新版本)。 當識別提供者不能進行多重要素驗證時，適用於 Workplace Join 的多重要素驗證將會失敗。 例如，您無法搭配 AD FS 2.0 使用多重要素驗證。 在您針對 Azure AD 裝置註冊服務開啟多重要素驗證之前，請先確認內部部署 AD FS 能與多重要素驗證搭配使用，且有效的多重要素驗證方法已就緒。 例如，Windows Server 2012 R2 上的 AD FS 具有多重要素驗證功能。 您也必須先在 AD FS 伺服器上設定其他有效的驗證 (多重要素驗證) 方法，才能針對 Azure AD 裝置註冊服務開啟多重要素驗證。 如需 AD FS 中支援的多重要素驗證方法詳細資訊，請參閱[設定 AD FS 的其他驗證方法](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)。

## <a name="next-steps"></a>後續步驟

*   如需常見問題的解答，請參閱 [Azure Active Directory 條件式存取常見問題集](active-directory-conditional-faqs.md)。
