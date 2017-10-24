---
title: "開始使用 Azure Active Directory | Microsoft Docs"
description: 
keywords: 
author: jeffgilb
manager: femila
ms.author: jeffgilb
ms.reviewer: jsnow
ms.date: 10/04/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
service: active-directory
custom: it-pro
ms.openlocfilehash: 2e9d9955810c01eb916df2580f623373aebb9d1d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-ad"></a>開始使用 Azure AD
現代的身分識別管理需要可調整、一致的可靠性，以確保只有經過驗證的使用者才可使用應用程式和服務。 為了充分支援使用者的身分識別管理需求，IT 需要設法允許存取已核准、公開的軟體即服務 (SaaS) 應用程式、設法裝載內部的企業營運應用程式，甚至設法加強內部部署應用程式開發和使用方式。 這些需求全都指出需要有一套雲端式身分識別管理解決方案。      

Azure Active Directory (Azure AD) 是 Microsoft 的多租用戶雲端型目錄和身分識別管理服務。 Azure AD 結合核心目錄服務及進階身分識別管理和應用程式存取管理。 Azure AD 經悉心設計，不但支援多租用戶、地理分布周全，更具有高可用性，表示您可以依賴 Azure AD 來應付最重要的商務需求。

Azure AD 包含一整套的身分識別管理功能，包括能夠同步處理內部部署資源資訊、可自訂的公司品牌、簡單的授權管理，甚至是自助式密碼管理。  這些輕鬆設定的功能有助於開始使用 Azure AD 來保護雲端式應用程式的安全、簡化 IT 程序、降低成本，以及協助確保達成公司合規性目標。

![Azure AD ](./media/get-started-azure-ad/Azure_Active_Directory.png)

本文其餘部分會教您如何開始使用 Azure AD。 

## <a name="sign-up-for-azure-active-directory-premium"></a>註冊 Azure Active Directory Premium
若要[開始使用 Azure Active Directory (Azure AD) Premium](active-directory-get-started-premium.md)，您可以購買授權，並將授權與您的 Azure 訂用帳戶建立關聯。 如果您建立新的 Azure 訂用帳戶，您也需要啟用授權方案及 Azure AD 服務存取。 

若要註冊 Active Directory Premium，您有數個選項︰ 

- 使用 Azure 或 Office 365 訂用帳戶
- 使用 Enterprise Mobility + Security 授權方案
- 使用 Microsoft 大量授權方案

> ### <a name="verification-step"></a>驗證步驟
> 啟用訂用帳戶之後，請確定您可以登入服務。

## <a name="add-a-custom-domain-name"></a>新增自訂網域名稱
每個 Azure AD 目錄皆隨附形式為 domainname.onmicrosoft.com 的初始網域名稱。您無法變更或刪除初始網域名稱，但您可以[將您的公司網域名稱新增至 Azure AD](add-custom-domain.md)。 例如，貴組織可能有營運所用的其他網域名稱，以及使用貴公司網域名稱登入的使用者。 將自訂網域名稱新增到 Azure AD 可讓您在目錄中指派您使用者熟悉的使用者名稱，例如 ‘alice@contoso.com’。 而不是 'alice@.onmicrosoft.com'。 程序佷簡單：

1. 在目錄中新增自訂網域名稱
2. 在網域名稱註冊機構中新增網域名稱的 DNS 項目
3. 驗證 Azure AD 中的自訂網域名稱

> ### <a name="verification-step"></a>驗證步驟
> 新增自訂網域之後，請確定它在 Azure AD 入口網站的 [網域名稱] 刀鋒視窗上顯示 [已驗證] 狀態。

## <a name="add-company-branding-to-your-sign-in-page"></a>將公司商標新增至登入頁面 
為了避免混淆，許多公司都想對其管理的所有網站和服務套用一致的外觀與風格。 Azure Active Directory (Azure AD) 提供此功能讓您[使用公司標誌和自訂色彩配置來自訂登入頁面的外觀](customize-branding.md)。 當您登入 Office 365，或其他以 Azure AD 作為識別提供者的 Web 型應用程式時，所顯示的頁面就是登入頁面。 您可以與此頁面互動來輸入您的認證。

> ### <a name="verification-step"></a>驗證步驟
> 登入 Azure 入口網站，確定您自訂的登入頁面和任何其他語言自訂都已正確設定。 

## <a name="add-new-users"></a>新增使用者
若要[將新的使用者新增至組織的 Azure AD](add-users-azure-active-directory.md)，您可以使用 Azure 入口網站來一次新增一個，也可以選擇同步處理內部部署 Windows Server AD 資源資料。 您可以直接從 Azure AD 入口網站新增雲端式使用者，或同步處理內部部署使用者資訊。

若要啟用內部部署身分識別同步處理至 Azure AD，您必須在您組織中的伺服器上安裝及設定 [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)。 此應用程式會將您現有身分識別來源的使用者和群組同步到您的 Azure AD 租用戶。

> ### <a name="verification-step"></a>驗證步驟
> 建立或同步處理新的使用者之後，請確定在 Azure AD 中可看到他們。

## <a name="assign-licenses"></a>指派授權
雖然只要取得訂用帳戶就能設定付費功能，但您仍必須為 Azure AD Premium 付費功能[指派使用者授權](license-users-groups.md)。 應存取 Azure AD 付費功能或者透過 Azure AD 付費功能管理的人員，都必須獲得授權。 授權指派是一種使用者與購買的服務 (例如 Azure AD Premium、Basic 或 Enterprise Mobility + Security) 之間的對應。

您可以使用以群組為基礎的授權指派來設定規則，如下列範例所示：

- 目錄中的所有使用者都自動取得授權
- 具有適當職稱的所有人員都取得授權
- 您可以將決定權委派給組織中的其他管理員 (利用自助群組)

> ### <a name="verification-step"></a>驗證步驟
> 在 [Azure Active Directory] > [授權] > [所有產品] 之下，檢閱已指派和可用的授權。

## <a name="configure-self-service-password-reset"></a>設定自助式密碼重設
[自助式密碼重設 (SSPR)](active-directory-passwords-getting-started.md) 提供簡單的方法，讓 IT 系統管理員允許使用者重設或解除鎖定其密碼或帳戶。 系統包含詳細的報告，以追蹤使用者何時使用系統與通知來警示您誤用或濫用。

> ### <a name="verification-step"></a>驗證步驟
> 在 [Azure Active Directory] > [密碼重設] 之下檢閱已啟用的 SSPR 屬性，確保已完成適當的使用者和群組指派。 


### <a name="learn-more"></a>詳細資訊
[Azure Active Directory 產品頁面](https://azure.microsoft.com/services/active-directory/)

[Azure Active Directory 價格資訊頁面](https://azure.microsoft.com/pricing/details/active-directory/)