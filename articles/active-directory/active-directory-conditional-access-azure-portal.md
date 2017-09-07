---
title: "Azure Active Directory 條件式存取 | Microsoft Docs"
description: "使用條件式存取控制，Azure Active Directory 會在驗證應用程式的存取權時，檢查特定的條件。"
services: active-directory
keywords: "應用程式的條件式存取, Azure AD 條件式存取, 安全存取公司資源, 條件式存取原則"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/24/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: c97f05caec4c302c847e2297d136c6614e82fd93
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---
# <a name="conditional-access-in-azure-active-directory"></a>Azure Active Directory 中的條件式存取

在行動第一、雲端第一的世界中，Azure Active Directory 可讓您從任何地方單一登入至裝置、應用程式和服務。 隨著裝置 (包括 BYOD)、在公司網路外部作業，以及協力廠商 SaaS 應用程式的激增，IT 專業人員面臨到兩個相對的目標︰

- 讓使用者隨時隨地都具有生產力
- 隨時保護公司資產

為了改善生產力，Azure Active Directory 為使用者提供廣泛的選項，以便存取您的公司資產。 透過應用程式存取管理，Azure Active Directory 可讓您確保只有「適當人員」可以存取您的應用程式。 如果您想要更充分掌控適當人員在某些情況下如何存取您的資源，該怎麼辦？ 如果您甚至有想要封鎖「適當人員」存取某些應用程式的條件，該怎麼辦？ 例如，您同意適當的人員從信任的網路存取某些應用程式；不過，您可能不希望他們從您不信任的網路存取這些應用程式。 您可以使用條件式存取解決這些問題。

條件式存取是 Azure Active Directory 的功能，可讓您根據特定條件，強制控制您環境中的應用程式存取。 利用控制項，您可以將額外需求繫結到存取，也可以封鎖存取。 條件式存取的實作是以原則為基礎。 以原則為基礎的方法可簡化您的設定經驗，因為它會依循您對於存取需求的想法。  

您通常會使用以下列模式為基礎的陳述式，定義您的存取需求︰

![控制](./media/active-directory-conditional-access-azure-portal/10.png)

當您以真實世界的資訊取代出現兩次的 “this”，您會有可能看似熟悉的原則陳述式範例︰

*When contractors are trying to access our cloud apps from networks that are not trusted, then block access.*

上述的原則陳述式凸顯了條件式存取的功效。 您雖然可以讓約聘人員基本上存取您的雲端應用程式 (**誰**)，但利用條件式存取，您也可以定義在哪些條件下可能存取 (**如何**)。

在 Azure Active Directory 條件式存取的內容中：

- "**When this happens**" 稱為**條件陳述式**
- "**Then do this**" 成為**控制項**

![控制](./media/active-directory-conditional-access-azure-portal/11.png)

條件陳述式與控制項的組合代表條件式存取原則。

![控制](./media/active-directory-conditional-access-azure-portal/12.png)


## <a name="controls"></a>控制

在條件式存取原則中，控制項定義滿足條件陳述式時所應發生的狀況。  
利用控制項，您可以封鎖存取，或允許有額外需求的存取。
當您設定可允許存取的原則時，您必須選取至少一個需求。   

### <a name="grant-controls"></a>授與控制
目前的 Azure Active Directory 實作可讓您設定下列授與控制權需求︰

![控制](./media/active-directory-conditional-access-azure-portal/05.png)

- **Multi-Factor Authentication** - 您可以透過 Multi-Factor Authentication 來要求增強式驗證。 身為提供者，您可以使用 Azure Multi-Factor Authentication，或使用結合 Active Directory Federation Services (AD FS) 的內部部署多重要素驗證提供者。 對於未獲授權但已取得有效使用者之認證存取權的使用者，使用 Multi-Factor Authentication 有助於防止其存取資源。

- **符合規範的裝置** - 您可以設定以裝置作為基礎的條件式存取原則。 以裝置作為基礎的條件式存取原則目標在於只從信任的裝置授與已設定資源的存取權。 要定義受信任裝置的選項之一，是要求符合規範的裝置。 如需詳細資訊，請參閱[設定 Azure Active Directory 裝置型條件式存取原則](active-directory-conditional-access-policy-connected-applications.md)。

- **已加入網域的裝置** – 設定以裝置作為基礎的條件式存取原則的另一個選項，是要求已加入網域的裝置。 這項需求是指加入內部部署 Active Directory 的 Windows 桌上型電腦、膝上型電腦和企業平板電腦。 如需詳細資訊，請參閱[設定 Azure Active Directory 裝置型條件式存取原則](active-directory-conditional-access-policy-connected-applications.md)。

如果您選取多個控制項，也可以設定在處理您的原則時是否全都為必要。

![控制](./media/active-directory-conditional-access-azure-portal/06.png)

### <a name="session-controls"></a>工作階段控制項
工作階段控制項可讓您限制雲端應用程式內的體驗。 工作階段控制項是由雲端應用程式強制執行，並依賴 Azure AD 對應用程式提供有關工作階段的其他資訊。

![控制](./media/active-directory-conditional-access-azure-portal/31.png)

#### <a name="use-app-enforced-restrictions"></a>使用應用程式強制執行限制
您可以使用這個控制項，要求 Azure AD 將裝置資訊傳遞至雲端應用程式。 這有助於雲端應用程式了解使用者是否來自符合規範的裝置或加入網域的裝置。 此控制項目前僅支援使用 SharePoint 作為雲端應用程式。 視裝置狀態而定，SharePoint 會使用裝置資訊來提供使用者有限或完整的經驗。
若要深入了解如何要求 SharePoint 的有限存取，請參閱[從未受管理的裝置控制存取](https://aka.ms/spolimitedaccessdocs)。

## <a name="condition-statement"></a>條件陳述式

上一節介紹了以控制項形式封鎖或限制資源存取的支援選項。 在條件式存取原則中，您可以定義必須符合才能以條件陳述式的形式套用您的控制項的準則。  

您可以將下列指派包含在您的條件陳述式中︰

![控制](./media/active-directory-conditional-access-azure-portal/07.png)


### <a name="who"></a>何人？

當您設定條件式存取原則時，必須選取原則所適用的使用者或群組。 在許多情況下，您需要讓控制項套用至一組特定的使用者。 在條件陳述式中，您可以選取原則所適用的必要使用者或群組，以定義這組使用者。 如有必要，您也可以明確豁免一組使用者，進行從您的原則中排除這些使用者。  

![控制](./media/active-directory-conditional-access-azure-portal/08.png)



### <a name="what"></a>何事？

當您設定條件式存取原則時，必須選取原則所適用的雲端應用程式。
從保護的觀點而言，有些在您環境中的應用程式通常需要更多關注 (相較於其他應用程式)。 例如，這會影響有權存取敏感性資料的應用程式。
選取雲端應用程式，您可以定義您的原則套用至的雲端應用程式範圍。 如有必要，您也可以從您的原則中明確排除一組應用程式。

![控制](./media/active-directory-conditional-access-azure-portal/09.png)

如需可在條件式存取原則中使用的雲端應用程式的完整清單，請參閱 [Azure Active Directory 條件式存取的技術參考](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments)。

### <a name="how"></a>方式：

只要您的應用程式存取是在可控制的條件下執行，就可能不需要對使用者存取您雲端應用程式的方式強加其他控制項。 不過，如果從不受信任的網路或不符合規範的裝置執行您的雲端應用程式存取，情況可能會有所不同。 在條件陳述式中，您可以定義某些存取條件，其對應用程式存取的執行方式有額外需求。

![條件](./media/active-directory-conditional-access-azure-portal/21.png)


## <a name="conditions"></a>條件

在目前的 Azure Active Directory 實作中，您可以定義下列領域的條件︰

- 登入風險
- 裝置平台
- 位置
- 用戶端應用程式

![條件](./media/active-directory-conditional-access-azure-portal/21.png)

### <a name="sign-in-risk"></a>登入風險

登入風險是一種物件，供 Azure Active Directory 用於追蹤非由使用者帳戶合法擁有者執行登入的可能性。 在此物件中，可能性 (高、中或低) 會以名為[登入風險層級](active-directory-reporting-risk-events.md#risk-level)屬性形式儲存。 如果 Azure Active Directory 偵測到登入風險，則會在使用者登入期間產生此物件。 如需詳細資訊，請參閱[有風險的登入](active-directory-identityprotection.md#risky-sign-ins)。  
您可以使用計算出的登入風險層級作為條件式存取原則中的條件。 

![條件](./media/active-directory-conditional-access-azure-portal/22.png)

### <a name="device-platforms"></a>裝置平台

裝置平台的特點是您裝置執行的作業系統：

- Android
- iOS
- Windows Phone
- Windows
- macOS (預覽)。 

![條件](./media/active-directory-conditional-access-azure-portal/02.png)

您可以定義包含的裝置平台以及從原則中豁免的裝置平台。  
若要使用原則中的裝置平台，請先將 [設定] 切換為 [是]，然後選取原則套用至的所有或個別裝置平台。 如果您選取個別的裝置平台，原則就只會影響這些平台。 在此情況下，其他支援平台的登入不受此原則影響。


### <a name="locations"></a>位置

您用來連線到 Azure Active Directory 之用戶端 IP 位址所識別的位置。 此條件要求您需熟悉**具名位置**和 **MFA 信任 IP**。  

**具名位置**是 Azure Active Directory 的一個功能，可讓您標記組織中信任的 IP 位址範圍。 在您的環境中，您可以在[風險事件](active-directory-reporting-risk-events.md)偵測內容中使用具名位置以及條件式存取。 如需有關在 Azure Active Directory 中設定具名位置的詳細資訊，請參閱 [Azure Active Directory 中的具名位置](active-directory-named-locations.md)。

您可以設定的位置數目受到 Azure AD 中相關物件大小的限制。 您可以設定：
 
 - 一個具名位置最多有 500 個 IP 範圍
 - 最多 60 個具名位置 (預覽)，每個位置皆指派一個 IP 範圍 


**MFA 信任的 IP** 是 Multi-Factor Authentication 的功能，可讓您定義信任的 IP 位址範圍，以代表您組織的區域內部網路。 當您設定位置條件時，信任的 IP 可讓您區分從您組織的網路與所有其他位置進行的連線。 如需詳細資訊，請參閱[信任的 IP](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips)。  



您可以包含所有位置或所有信任的 IP，也可以排除所有信任的 IP。

![條件](./media/active-directory-conditional-access-azure-portal/03.png)


### <a name="client-apps"></a>用戶端應用程式

用戶端應用程式可以是您用來連線到 Azure Active Directory 的一般層級應用程式 (網頁瀏覽器、行動裝置應用程式、桌面用戶端)，或者您可以特別選取 Exchange Active Sync。  
舊式驗證是指使用基本驗證的用戶端，例如，未使用新式驗證的舊版 Office 用戶端。 舊式驗證目前不支援條件式存取。

![條件](./media/active-directory-conditional-access-azure-portal/04.png)


如需可在條件式存取原則中使用的用戶端應用程式完整清單，請參閱 [Azure Active Directory 條件式存取的技術參考](active-directory-conditional-access-technical-reference.md#client-apps-conditions)。



## <a name="common-scenarios"></a>常見案例

### <a name="requiring-multi-factor-authentication-for-apps"></a>應用程式需要 Multi-Factor Authentication

許多環境中的應用程式需要比其他應用程式更高層級的保護。
例如，有權存取敏感性資料的應用程式。
如果您想要對這些應用程式新增另一層的保護，您可以設定當使用者存取這些應用程式時需要 Multi-Factor Authentication 的條件式存取原則。


### <a name="requiring-multi-factor-authentication-for-access-from-networks-that-are-not-trusted"></a>從不受信任的網路存取時需要 Multi-Factor Authentication

此案例類似於前一個案例，因為這會新增 Multi-Factor Authentication 的需求。
不過，主要差異在於這項需求的條件。  
雖然前一個案例的焦點在於有權存取敏感性資料的應用程式，而此案例的焦點則在於信任的位置。  
換句話說，如果使用者從您不信任的網路存取應用程式，您可能需要 Multi-Factor Authentication。


### <a name="only-trusted-devices-can-access-office-365-services"></a>只有受信任的裝置可以存取 Office 365 服務

如果您在自己的環境中使用 Intune，您可以在 Azure 主控台中立即開始使用條件式存取原則介面。

許多 Intune 客戶都使用條件式存取來確保只有受信任的裝置可以存取 Office 365 服務。 這表示行動裝置已向 Intune 進行註冊並符合合規性原則需求，而且 Windows 電腦已加入內部部署網域。 您不需要為每項 Office 365 服務設定相同的原則，是一項重大改進。  當您建立新原則時，請設定雲端應用程式包含您想要使用條件式存取保護的每個 O365 應用程式。

## <a name="next-steps"></a>後續步驟

- 如果您想要知道如何設定條件式存取原則，請參閱[開始使用 Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal-get-started.md)。

- 如果您已準備好設定您環境的條件式存取原則，請參閱 [Azure Active Directory 中條件式存取的最佳做法](active-directory-conditional-access-best-practices.md)。 
