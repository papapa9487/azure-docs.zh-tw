---
title: "使用 Azure 身分識別和存取控制來保護個人資料 | Microsoft Docs"
description: "使用 Azure 身分識別和存取控制來協助您保護您的個人資料"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 908bf4c34295168024afd11d24c7a3d2044ff518
ms.contentlocale: zh-tw
ms.lasthandoff: 08/30/2017

---
# <a name="azure-active-directory-and-multi-factor-authentication-protect-personal-data-with-identity-and-access-controls"></a>Azure Active Directory 和 Multi-factor Authentication：使用身分識別和存取控制來保護個人資料

本文提供的資訊和程序，可供您使用 Azure Active Directory 和 Multi-factor Authentication 安全性功能和服務來保護個人資料。

## <a name="scenario"></a>案例

總部設於美國的大型郵輪公司將擴展其營運，以提供地中海、亞得里亞海、波羅的海和不列顛群島的行程。 為了支援這些工作，它收購了以義大利、德國、丹麥和英國為據點的數個較小型郵輪公司。 

此公司使用 Microsoft Azure 在雲端儲存公司資料。 其中包含其全球客戶群的名稱、地址、電話號碼和信用卡資訊等個人識別資訊。 它也會包含傳統人力資源資訊，例如地址、電話號碼、稅務識別碼，以及有關各地公司員工的其他資訊。 此郵輪公司也會維護獎勵和忠誠度方案會員的大型資料庫，其中包含的個人資訊可追蹤與目前和過去客戶的關係。

公司員工會從公司的遠端辦公室存取網路，而位於全球的旅行社會存取某些公司資源。

## <a name="problem-statement"></a>問題陳述

公司必須保護客戶和員工個人資料的隱私權，防止攻擊者試圖使用遭到入侵的身分識別來取得存取權。 他們也必須確保合法使用者的個人資料存取權，僅限於需要該資料才能執行其作業的人員。

## <a name="company-goal"></a>公司目標

公司的目標是為了確保個人資料的存取權受到嚴格控管。 請務必以強式驗證保護具備個人資料存取權之使用者的身分識別。 必須強制執行 [最小權限] 的原則 (https://en.wikipedia.org/wiki/Principle_of_least_privilege)，讓合法使用者只能擁有他們所需的存取層級。

## <a name="solutions"></a>解決方案

Microsoft Azure 提供身分識別和存取管理工具，協助公司控管誰具有資源 (包含個人資料) 的存取權。

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (AAD) 可管理身分識別和控制 Azure 及其他內部部署和其他雲端資源、資料和應用程式的存取。 [Azure Active Directory Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access) 可協助 Azure 系統管理員將可存取特定資訊 (例如個人資料) 的人數降至最低。 它可讓系統管理員探索、限制和監視特殊權限的身分識別及其資源存取權，並且將暫時性 Just-In-Time (JIT) 系統管理權限指派給合格的使用者。 它也可供深入了解具有 AAD 系統管理員權限的人員。

使用 AAD PIM 時所涉及的活動包括：

- 為您的目錄啟用 Privileged Identity Management

- 使用 Privileged Identity Management 管理儀表板來速覽重要資訊

- 藉由新增或移除每個角色的永久或合格系統管理員，管理特殊權限的身分識別 (系統管理員)

- 設定角色啟用設定

- 啟用角色

- 檢閱角色活動

#### <a name="how-do-i-enable-aad-pim"></a>如何啟用 ADD PIM？

若要針對您的目錄開始使用 PIM，執行下列作業：

1. 以目錄的全域系統管理員身分登入 Azure 入口網站。

2. 如果貴組織有多個目錄，請選取 Azure 入口網站右上角的使用者名稱。 選取您將在其中使用 Azure AD Privileged Identity Management 的目錄。

3. 選取 [更多服務] 並使用 [篩選器] 文字方塊來搜尋 [Azure AD Privileged Identity Management]。

4. 選取 [釘選到儀表板]，然後按一下 [建立]。 Privileged Identity Management 應用程式隨即開啟。

Azure AD Privileged Identity Management 設定完畢後，您會在每次開啟應用程式時看到導覽刀鋒視窗。

![](media/protect-personal-data-identity-access-controls/azure-pim.png)

如需開始使用 AAD PIM 的詳細資訊和指示，請參閱[開始使用 Azure AD Privileged Identity Management](https://docs.microsoft.com/active-directory/active-directory-privileged-identity-management-getting-started)。

### <a name="azure-role-based-access-control"></a>Azure 角色型存取控制

[Azure 角色型存取控制](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) (RBAC) 能夠根據使用者的指派角色授與存取權限，協助 Azure 系統管理員來管理 Azure 資源的存取權。 您可以區隔小組內的職責，僅授與使用者、群組和應用程式執行其作業所需的存取量。

使用 Azure 入口網站、Azure 命令列工具或 Azure 管理 API 的使用者，可以獲授與角色型存取權。

如需 Azure RBAC 基本概念的詳細資訊，請參閱[在 Azure 入口網站中開始使用角色型存取控制](https://docs.microsoft.com/active-directory/role-based-access-control-what-is)。

#### <a name="how-do-i-manage-azure-rbac-with-powershell"></a>如何使用 PowerShell 管理 Azure RBAC？

您可以使用 PowerShell Cmdlet 來管理 Azure RBAC，包括下列管理工作：

- 列出角色

- 查看誰具有存取權

- 授與存取權

- 移除存取

- 建立自訂角色

- 取得資源提供者的動作

- 修改自訂角色

- 刪除自訂角色

- 列出自訂角色

如需有關如何使用 PowerShell 管理 Azure RBAC 的指示，請參閱[使用 Azure PowerShell 管理角色型存取](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell)。

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

[Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/) (MFA) 是雙步驟的驗證解決方案，有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。 它可以透過一些驗證方法 (包括電話、文字訊息，或行動應用程式驗證) 來提供強大的驗證功能。

若要在 Azure 雲端部署 MFA，您必須先啟用它，然後再為使用者開啟雙步驟驗證。

#### <a name="how-do-i-enable-azure-to-use-mfa"></a>如何讓 Azure 能夠使用 MFA？

如果您的使用者具有包含 Azure Multi-Factor Authentication 的授權，您就不需要手動開啟 Azure MFA。 若非如此，您需要在目錄中建立 Multi-Factor Auth Provider。 若要這樣做，請遵循下列步驟：

1. 在 Azure 傳統入口網站中選取 [Active Directory] (以系統管理員身分登入)。

2. 選取 [Multi-Factor Authentication Provider]。

3. 選取 [新增]，然後在 [應用程式服務] 之下，選取 [Multi-Factor Auth Provider]。

4. 選取 [快速建立]。

5. 填寫名稱欄位，然後選取使用量模型 (根據驗證或根據啟用的使用者)。

6. 指定與 MFA Provider 相關聯的目錄。

7. 按一下 [ **建立** ] 按鈕。

![](media/protect-personal-data-identity-access-controls/quick-create.png)

如需如何管理 Multi-Factor Auth Provider 的詳細指示，請參閱[開始使用 Azure Multi-Factor Auth Provider](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-auth-provider)。

#### <a name="how-do-i-turn-on-two-step-verification-for-users"></a>如何對使用者開啟雙步驟驗證？

您可以對所有登入強制執行雙步驟驗證，也可以建立條件式存取原則，要求只有在適用特定條件時使用雙步驟驗證。

藉由變更使用者狀態來啟用 Azure MFA 是用來要求使用雙步驟驗證的傳統方法。 您所啟用的所有使用者都會有相同的需求，也就是在每次登入時執行雙步驟驗證。 啟用使用者的效力會凌駕任何可能影響該使用者的條件式存取原則。

使用條件式存取原則來啟用 Azure MFA 是用來要求使用雙步驟驗證的更彈性方法。 您可以建立適用於群組以及個別使用者的條件式存取原則。 您可以對高風險群組施加比低風險群組更多的限制，或者，也可以只針對高風險雲端應用程式要求雙步驟驗證，至於低風險的雲端應用程式則略過雙步驟驗證。 不過，條件式存取是 Azure Active Directory 的付費功能。

若要藉由變更使用者狀態來啟用 MFA，請執行下列作業：

1. 以系統管理員身分登入 Azure 入口網站。
2. 移至 [Azure Active Directory] \> [使用者和群組] \> [所有使用者]。
3. 選取 [多重要素驗證]。
4. 尋找您想要針對 Azure MFA 啟用的使用者。 您可能需要在頂端變更檢視。
5. 勾選使用者名稱旁邊的方塊。
6. 在右邊的快速步驟底下，選擇 [啟用]。

   ![](media/protect-personal-data-identity-access-controls/quick-create.png)

7. 在開啟的快顯視窗中確認您的選取項目。  系統會要求已啟用 MFA 的使用者會下一次登入時進行註冊。

若要使用條件式存取原則來啟用 Azure MFA，請執行下列作業：

1. 以系統管理員身分登入 Azure 入口網站。

2. 移至 [Azure Active Directory] \> [條件式存取]。

3. 選取 [新增原則]。

4. 在 [指派] 底下選取 [使用者和群組]。 使用 [包含] 和 [排除] 索引標籤，指定要由原則管理的使用者和群組。

5. 在 [指派] 底下選取 [雲端應用程式]。 選擇要 [包含所有雲端應用程式]。
6.  在 [存取控制] 底下選取 [授與]。 選擇 [需要多重要素驗證]。
7.  將 [啟用原則] 轉為 [開啟]，然後選取 [儲存]。

如需如何進行 Azure MFA 設定，以設定詐騙警示、建立一次性略過、使用自訂語音訊息、設定快取，指定受信任的 IP、建立應用程式密碼、啟用記住使用者信任之裝置的 MFA，以及選取驗證方法，請參閱[進行 Azure Multi-factor Authentication 設定](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next)。

## <a name="next-steps"></a>後續步驟

- [保護 Azure AD 中的特殊權限存取](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)

- [與 Azure Multi-Factor Authentication 相關的常見問題](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-faq)

- [角色型存取控制疑難排解](https://docs.microsoft.com/azure/active-directory/role-based-access-control-troubleshooting)

- [Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

