---
title: "SaaS 應用程式的 Azure 條件式存取 | Microsoft Docs"
description: "Azure AD 中的條件式存取可讓您設定每個應用程式的多重要素驗證存取規則，且能夠封鎖不在受信任網路上的使用者存取。 "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 4720d168f5ecd3a5e39cdcad812efb52755fb59a
ms.contentlocale: zh-tw
ms.lasthandoff: 12/29/2016

---
# <a name="getting-started-with-azure-active-directory-conditional-access"></a>開始使用 Azure Active Directory 條件式存取
[SaaS](https://azure.microsoft.com/overview/what-is-saas/) 應用程式及 Azure AD 連線應用程式的「Azure Active Directory 條件式存取」可讓您根據群組、位置和應用程式敏感性來設定條件式存取。 

使用以應用程式敏感性為基礎的條件式存取時，您可以設定每一應用程式的多重要素驗證 (MFA) 存取規則。 每一應用程式 MFA 會讓您能夠封鎖不在受信任網路上的使用者。 您可以將 MFA 規則套用到所有已指派給應用程式的使用者，或只套用到所指定安全性群組內的使用者。  如果使用者是從組織網路內的 IP 位址存取應用程式，則可以從 MFA 需求中排除這些使用者。

購買 Azure Active Directory Premium 授權的客戶可使用這些功能。

## <a name="scenario-prerequisites"></a>案例必要條件
* Azure Active Directory Premium 的授權
* 同盟或受管理的 Azure Active Directory 租用戶
* 同盟租用戶需要啟用多重要素驗證。

## <a name="configure-per-application-access-rules"></a>設定每個應用程式的存取規則
本節描述如何設定每個應用程式的存取規則。

1. 使用 Azure AD 全域管理員身分的帳戶登入 Azure 傳統入口網站。
2. 在左窗格中選取 [Active Directory] 。
3. 在 [目錄] 索引標籤中，選取您的目錄。
4. 選取 [應用程式]  索引標籤。
5. 選取要設定規則的應用程式。
6. 選取 [設定]  索引標籤。
7. 向下捲動至存取規則區段。 選取所需的存取規則。
8. 指定將套用此規則的使用者。
9. 將 [啟用] 設定為 [開啟] 以啟用原則。

## <a name="understanding-access-rules"></a>了解存取規則
本節提供 Azure 條件式應用程式存取中支援的存取規則的詳細描述。

### <a name="specifying-the-users-the-access-rules-apply-to"></a>指定套用存取規則的使用者
根據預設，規則會套用至所有可存取應用程式的使用者。 不過，您也可以將原則限制為指定安全性群組的成員使用者。 [加入群組]  按鈕用來從群組選取對話方塊中，選取將套用存取規則的一或多個群組。 此對話方塊也可用來移除選取的群組。 當選擇將規則套用至「群組」時，只會對屬於其中一個指定安全性群組的使用者強制執行存取規則。

您也可以選取 [例外]  選項並指定一個或多個群組，來明確地從原則中排除安全性群組。 使用者如果是 [例外]  清單中群組的成員，則即使身為套用存取規則群組的成員，也不會受到多重要素驗證需求限制。
以下顯示的存取規則會要求「管理員」群組中的所有使用者，在存取應用程式時使用多重要素驗證。

![使用 MFA 設定條件式存取規則](./media/active-directory-conditional-access-azuread-connected-apps/conditionalaccess-saas-apps.png)

## <a name="conditional-access-rules-with-mfa"></a>條件式存取規則和 MFA
如果已使用每個使用者的多重要素驗證功能來設定使用者，則使用者的這項設定將與應用程式的多重要素驗證規則相結合。 這表示已設定每個使用者多重要素驗證的使用者，即使已從應用程式多重要素驗證規則中免除，也都必須執行多重要素驗證。 深入了解多重要素驗證和每個使用者設定。

### <a name="access-rule-options"></a>存取規則選項
支援下列選項：

* **需要多重要素驗證**︰套用存取規則的使用者必須先完成多重要素驗證，才能存取套用規則的應用程式。
* **不在工作時需要多重要素驗證**︰來自受信任 IP 位址的使用者將不需要執行多重要素驗證。 可以在 [Multi-Factor Authentication 設定] 頁面上設定受信任的 IP 位址範圍。
* **不工作時封鎖存取**：將會封鎖不是來自受信任 IP 位址的使用者。 可以在 [Multi-Factor Authentication 設定] 頁面上設定受信任的 IP 位址範圍。

### <a name="setting-rule-status"></a>設定規則狀態
存取規則狀態可讓您開啟或關閉規則。 當存取規則關閉時，將不會強制執行多重要素驗證需求。

### <a name="access-rule-evaluation"></a>存取規則評估
當使用者存取使用 OAuth 2.0、OpenID Connect、SAML 或 WS-同盟的同盟應用程式時，就會評估存取規則。 此外，當 OAuth 2.0 和 OpenID Connect 使用重新整理權杖來取得存取權杖時，也會評估存取規則。 如果使用重新整理權杖時原則評估失敗，將會傳回 **invalid_grant** 錯誤，這表示使用者必須向用戶端重新進行驗證。

### <a name="configure-federation-services-to-provide-multi-factor-authentication"></a>設定同盟服務以提供多重要素驗證
就同盟的租用戶而言，MFA 可由 Azure Active Directory 或內部部署 AD FS 伺服器執行。

根據預設，MFA 會發生在 Azure Active Directory 所裝載的頁面上。 若要設定內部部署 MFA，必須使用 Windows PowerShell 的 Azure AD 模組，在 Azure Active Directory 中將 **-SupportsMFA** 屬性設定為 **true**。

下列範例示範如何在 consoso.com 租用戶上使用 [Set-MsolDomainFederationSettings Cmdlet](https://msdn.microsoft.com/library/azure/dn194088.aspx) 來啟用內部部署 MFA︰

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

除了設定這個旗標，同盟租用戶 AD FS 執行個體必須設為執行多重要素驗證。 請依照 [在內部部署環境部署 Microsoft Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-server.md)的指示操作。

## <a name="related-articles"></a>相關文章
* [保護對 Office 365 及其他連接至 Azure Active Directory 之應用程式的存取](active-directory-conditional-access.md)
* [Article Index for Application Management in Azure Active Directory (Azure Active Directory 中應用程式管理的文件索引)](active-directory-apps-index.md)


