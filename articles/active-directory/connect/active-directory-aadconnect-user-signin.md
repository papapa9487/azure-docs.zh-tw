---
title: "Azure AD Connect：使用者登入 | Microsoft Docs"
description: "適用於自訂設定的 Azure AD Connect 使用者登入。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: da517c096357bb8db4334715fa46aa209c273f22
ms.contentlocale: zh-tw
ms.lasthandoff: 08/31/2017

---
# <a name="azure-ad-connect-user-sign-in-options"></a>Azure AD Connect 使用者登入選項
Azure Active Directory (Azure AD) Connect 可讓您的使用者使用相同的密碼來登入雲端和內部部署資源。 本文說明每個身分識別模型的主要概念，以協助您選擇要用於登入 Azure AD 的身分識別。

如果您已熟悉 Azure AD 身分識別模型，而想要深入了解特定的方法，請參閱適當的連結：

* 使用[單一登入 (SSO)](active-directory-aadconnect-sso.md) 進行[密碼同步作業](#password-synchronization)
* [傳遞驗證](active-directory-aadconnect-pass-through-authentication.md)
* [同盟 SSO (搭配 Active Directory Federation Services (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>為您的組織選擇使用者登入方法
針對只想要讓使用者登入 Office 365、SaaS 應用程式及其他 Azure AD 型資源的大多數組織，建議使用預設的密碼同步處理選項。 不過，有些組織有無法使用此選項的特定原因。 它們可以選擇同盟登入選項 (例如 AD FS) 或傳遞驗證。 您可使用下表來協助您做正確的選擇。

我需要 | PS 搭配 SSO| PA 搭配 SSO| AD FS |
 --- | --- | --- | --- |
自動將內部部署 Active Directory 中的新使用者、連絡人及群組帳戶同步至雲端。|x|x|x|
設定適用於 Office 365 混合式案例的租用戶。|x|x|x|
讓我的使用者可以使用其內部部署密碼來登入及存取雲端服務。|x|x|x|
使用公司認證來實作單一登入。|x|x|x|
確定沒有任何密碼儲存在雲端。||x*|x|
啟用內部部署多重要素驗證解決方案。|||x|

*透過輕量連接器。

>[!NOTE]
> 傳遞驗證目前對於豐富型用戶端有一些限制。 如需詳細資訊，請參閱[傳遞驗證](active-directory-aadconnect-pass-through-authentication.md)。

### <a name="password-synchronization"></a>密碼同步處理
使用密碼同步處理時，可以將使用者密碼的雜湊從內部部署 Active Directory 同步至 Azure AD。 在內部部署環境中變更或重設密碼之後，新密碼會立即同步至 Azure AD，讓使用者能夠一律使用相同的密碼來存取雲端資源和內部部署資源。 這些密碼一律不會傳送至 Azure AD，也不會以純文字的形式儲存在 Azure AD 中。 您可以將密碼同步處理與密碼回寫功能搭配使用，以在 Azure AD 中啟用自助式密碼重設功能。

此外，您也可以針對公司網路中已加入網域的電腦上使用者啟用 [SSO](active-directory-aadconnect-sso.md)。 在使用單一登入的情況下，已啟用的使用者只需輸入使用者名稱，即可安全地存取雲端資源。

![密碼同步處理](./media/active-directory-aadconnect-user-signin/passwordhash.png)

如需詳細資訊，請參閱[密碼同步處理](active-directory-aadconnectsync-implement-password-synchronization.md)一文。

### <a name="pass-through-authentication"></a>傳遞驗證
使用傳遞驗證時，會向內部部署 Active Directory 控制器驗證使用者的密碼。 密碼不以任何形式存在於 Azure AD 中。 這可允許在進行雲端服務驗證的期間評估內部部署原則，例如登入時數限制。

傳遞驗證會使用內部部署環境中已加入網域的 Windows Server 2012 R2 電腦上的簡單代理程式。 此代理程式會接聽密碼驗證要求。 它不需要有任何輸入連接埠開放給網際網路。

此外，您也可以針對公司網路中已加入網域的電腦上使用者啟用單一登入。 在使用單一登入的情況下，已啟用的使用者只需輸入使用者名稱，即可安全地存取雲端資源。
![傳遞驗證](./media/active-directory-aadconnect-user-signin/pta.png)

如需詳細資訊，請參閱：
- [傳遞驗證](active-directory-aadconnect-pass-through-authentication.md)
- [單一登入](active-directory-aadconnect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>使用新的或現有伺服器陣列搭配 Windows Server 2012 R2 中的 AD FS 建立的同盟
使用同盟登入時，使用者可以使用其內部部署密碼來登入 Azure AD 型服務。 當他們在公司網路上時，甚至不需要輸入他們的密碼。 透過使用搭配 AD FS 的同盟選項，您可以部署與 Windows Server 2012 R2 中 AD FS 搭配的新的或現有伺服器陣列。 如果您選擇指定現有的伺服器陣列，Azure AD Connect 就會設定伺服器陣列與 Azure AD 之間的信任，以便讓使用者登入。

<center>![與 Windows Server 2012 R2 中的 AD FS 搭配的同盟](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>部署與 Windows Server 2012 R2 中 AD FS 搭配的同盟

如果要部署新的伺服器陣列，您需要：

* 同盟伺服器的 Windows Server 2012 R2 伺服器。
* Web 應用程式 Proxy 的 Windows Server 2012 R2 伺服器。
* 一個 .pfx 檔案，內含一個 SSL 憑證，用於預期使用的同盟服務名稱。 例如：fs.contoso.com。

如果要部署新的伺服器陣列或使用現有的伺服器陣列，您需要：

* 同盟伺服器上的本機系統管理員認證。
* 您想要部署「Web 應用程式 Proxy」角色之任何工作群組伺服器 (未加入網域) 上的本機系統管理員認證。
* 您執行精靈的電腦能夠透過「Windows 遠端管理」，連線到您要安裝 AD FS 或「Web 應用程式 Proxy」的任何其他電腦。

如需詳細資訊，請參閱[設定與 AD FS 搭配的 SSO](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)。

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>使用舊版 AD FS 或協力廠商解決方案進行登入
如果您已經使用舊版 AD FS (例如 AD FS 2.0) 或協力廠商同盟提供者來設定雲端登入，您可以選擇略過透過 Azure AD Connect 進行使用者登入設定。 這既可讓您取得最新的同步處理及其他 Azure AD Connect 功能，又可讓您仍然使用現有的解決方案進行登入。

如需詳細資訊，請參閱 [Azure AD 協力廠商同盟相容性清單](active-directory-aadconnect-federation-compatibility.md)。


## <a name="user-sign-in-and-user-principal-name"></a>使用者登入和使用者主體名稱
### <a name="understanding-user-principal-name"></a>了解使用者主體名稱
在 Active Directory 中，預設的使用者主體名稱 (UPN) 尾碼是其中建立使用者帳戶之網域的 DNS 名稱。 在大多數情況下，這是在網際網路上註冊為企業網域的網域名稱。 不過，您可以使用「Active Directory 網域及信任」來新增其他 UPN 尾碼。

使用者的 UPN 格式是 username@domain。 例如，就名為 "contoso.com" 的 Active Directory 網域而言，名為 John 的使用者可能會有 UPN "john@contoso.com"。 使用者的 UPN 是以 RFC 822 為基礎。 雖然 UPN 與電子郵件共用相同的格式，但使用者的 UPN 值不一定與使用者的電子郵件地址相同。

### <a name="user-principal-name-in-azure-ad"></a>Azure AD 中的使用者主體名稱
Azure AD Connect 精靈會使用 userPrincipalName 屬性，或讓您指定內部部署環境中要用來作為 Azure AD 中使用者主體名稱的屬性。 這是用於登入 Azure AD 的值。 如果 userPrincipalName 屬性的值未與 Azure AD 中已驗證的網域對應，Azure AD 就會以預設的 .onmicrosoft.com 值取代它。

Azure Active Directory 中的每個目錄都隨附一個內建的網域名稱，格式為 contoso.onmicrosoft.com，可讓您開始使用 Azure 或其他 Microsoft 服務。 您可以使用自訂網域來改善及簡化登入體驗。 如需有關 Azure AD 中的自訂網域名稱及如何驗證網域的資訊，請參閱[在 Azure Active Directory 中新增自訂網域名稱](../add-custom-domain.md#add-the-custom-domain-name-to-your-directory)。

## <a name="azure-ad-sign-in-configuration"></a>Azure AD 登入組態
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>使用 Azure AD Connect 設定 Azure AD 登入組態
Azure AD 登入體驗取決於 Azure AD 是否能夠將要同步處理之使用者的使用者主體名稱尾碼，對應至 Azure AD 目錄中其中一個已驗證的自訂網域。 Azure AD Connect 會在您設定 Azure AD 登入設定時提供協助，讓使用者在雲端的登入體驗與內部部署環境體驗相似。

Azure AD Connect 會列出為網域定義的 UPN 尾碼，並嘗試將它們與 Azure AD 中的自訂網域對應。 接著，它會協助您進行需要採取的適當動作。
Azure AD 登入頁面會列出為內部部署 Active directory 定義的 UPN 尾碼，並顯示每個尾碼相對應的狀態。 狀態值可以是下列其中一個︰

| State | 說明 | 需要採取的動作 |
|:--- |:--- |:--- |
| Verified |Azure AD Connect 在 Azure AD 中找到一個已驗證的相符網域。 此網域的所有使用者均可使用其內部部署認證來進行登入。 |不需要採取任何動作。 |
| 未驗證 |Azure AD Connect 在 Azure AD 中找到對應的自訂網域，但該網域未經驗證。 如果未驗證網域，此網域的使用者 UPN 尾碼將會在同步處理後變更為預設的 .onmicrosoft.com 尾碼。 | [驗證 Azure AD 中的自訂網域。](../add-custom-domain.md#verify-the-custom-domain-name-in-azure-ad) |
| 未新增 |Azure AD Connect 找不到與 UPN 尾碼對應的自訂網域。 如果未在 Azure 中新增並驗證網域，此網域的使用者 UPN 尾碼將會變更為預設的 .onmicrosoft.com 尾碼。 | [新增並驗證與 UPN 尾碼對應的自訂網域。](../add-custom-domain.md) |

Azure AD 登入頁面會列出為內部部署 Active Directory 定義的 UPN 尾碼，以及 Azure AD 中對應的自訂網域與目前的驗證狀態。 在自訂安裝中，您現在可以在 [Azure AD 登入] 頁面上選取使用者主體名稱的屬性。

![Azure AD 登入頁面](./media/active-directory-aadconnect-user-signin/custom_azure_sign_in.png)

您可以按一下 [重新整理] 按鈕，從 Azure AD 中重新擷取最新的自訂網域狀態。

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>選取 Azure AD 中使用者主體名稱的屬性
userPrincipalName 屬性是使用者登入 Azure AD 和 Office 365 時會使用的屬性。 您應該在同步處理使用者之前，先驗證在 Azure AD 中使用的網域 (也稱為 UPN 尾碼)。

強烈建議您保留 userPrincipalName 預設屬性。 如果此屬性不可路由傳送且無法驗證，則可以選取另一個屬性 (例如電子郵件) 作為保存登入識別碼的屬性。 這稱為「替代識別碼」。 「替代識別碼」屬性值必須遵守 RFC 822 標準。 您可以使用「替代識別碼」搭配密碼 SSO 和同盟 SSO 作為登入解決方案。

> [!NOTE]
> 使用替代識別碼並無法與所有的 Office 365 工作負載相容。 如需詳細資訊，請參閱[設定替代登入識別碼](https://technet.microsoft.com/library/dn659436.aspx)。
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>不同的自訂網域狀態及其對 Azure 登入體驗的影響
請務必要了解 Azure AD 目錄中的自訂網域狀態與內部部署環境中所定義 UPN 尾碼之間的關聯性。 讓我們逐一了解使用 Azure AD Connnect 來設定同步處理時，可能經歷的不同 Azure 登入體驗。

針對以下資訊，讓我們假設所關注的是 UPN 尾碼 contoso.com，這會在內部部署目錄中作為 UPN 的一部分，例如 user@contoso.com。

###### <a name="express-settingspassword-synchronization"></a>快速設定/密碼同步處理
| State | 對使用者的 Azure 登入體驗的影響 |
|:---:|:--- |
| 未新增 |在此案例中，Azure AD 目錄內並未針對 contoso.com 新增任何自訂網域。 內部部署 UPN 尾碼為 @contoso.com 的使用者將無法使用其內部部署 UPN 來登入 Azure。 他們必須改為使用 Azure AD 透過為預設 Azure AD 目錄新增尾碼來提供給他們的新 UPN。 例如，如果您要將使用者同步至 Azure AD 目錄 azurecontoso.onmicrosoft.com，則內部部署使用者 user@contoso.com 將得到的 UPN 會是 user@azurecontoso.onmicrosoft.com。 |
| 未驗證 |在此案例中，我們已在 Azure AD 目錄中新增自訂網域 contoso.com。 不過，此網域尚未經過驗證。 如果您在未驗證網域的情況下就繼續同步處理使用者，Azure AD 就會為使用者指派一個新的 UPN，就像在「未新增」案例中所做的一樣。 |
| Verified |在此案例中，我們已在 Azure AD 中為 UPN 尾碼新增並驗證自訂網域 contoso.com。 將使用者同步至 Azure AD 之後，他們將能夠使用其內部部署使用者主體名稱 (例如 user@contoso.com) 來登入 Azure。 |

###### <a name="ad-fs-federation"></a>AD FS 同盟
您無法與 Azure AD 中的預設 .onmicrosoft.com 網域或 Azure AD 中未驗證的自訂網域建立同盟。 當您執行 Azure AD Connect 精靈時，如果選取要與未驗證的網域建立同盟，則 Azure AD Connect 會在裝載該網域 DNS 的地方，提示您必須建立的記錄。 如需詳細資訊，請參閱[驗證所選取用於同盟的 Azure AD 網域](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)。

如果您選取了 [與 AD FS 同盟] 使用者登入選項，就必須擁有自訂網域，才能繼續在 Azure AD 中建立同盟。 就我們的討論而言，這意謂著我們應該在 Azure AD 目錄中新增自訂網域 contoso.com。

| State | 對使用者 Azure 登入體驗的影響 |
|:---:|:--- |
| 未新增 |在此案例中，Azure AD Connect 在 Azure AD 目錄中找不到與 UPN 尾碼 contoso.com 對應的自訂網域。 如果您需要讓使用者使用 AD FS 搭配其內部部署 UPN (例如 user@contoso.com) 來進行登入，就必須新增自訂網域 contoso.com。 |
| 未驗證 |在此案例中，Azure AD Connect 會提示您適當的詳細資料，指導您如何在稍後的階段中驗證網域。 |
| Verified |在此案例中，您可以繼續進行設定，而不需採取任何進一步的動作。 |

## <a name="changing-the-user-sign-in-method"></a>變更使用者登入方法
您可以在使用精靈完成 Azure AD Connect 的初始設定之後，使用 Azure AD Connect 中的可用工作，將使用者登入方法從同盟變更為密碼同步處理或傳遞驗證。 請再次執行 Azure AD Connect 精靈，您將會看到您可執行的工作清單。 在工作清單中選取 [變更使用者登入]  。

![變更使用者登入](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

在下一個頁面上，系統會要求您提供 Azure AD 的認證。

![連接至 Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

在 [使用者登入] 頁面上，選取所需的使用者登入。

![連接至 Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin2a.png)

> [!NOTE]
> 如果您只是要暫時切換到密碼同步處理，則請選取 [請勿轉換使用者帳戶] 核取方塊。 不勾選該選項將導致將每個使用者都轉換為同盟使用者，而這可能耗費數小時。
>
>

## <a name="next-steps"></a>後續步驟
- 深入了解[將內部部署身分識別與 Azure Active Directory 整合](active-directory-aadconnect.md)。
- 深入了解 [Azure AD Connect 設計概念](active-directory-aadconnect-design-concepts.md)。

