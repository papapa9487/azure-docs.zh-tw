---
title: "從 Azure 存取控制服務 (ACS) 移轉"
description: "從 Azure 存取控制服務移動應用程式和服務的選項 | Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dastrock
ms.openlocfilehash: e32cac7feda929a63c4a80fc0078b221117eb2b5
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2017
---
# <a name="migrating-from-azure-access-control-service-acs"></a>從 Azure 存取控制服務 (ACS) 移轉

Microsoft Azure Active Directory 存取控制 (也稱為存取控制服務或 ACS) 將於 2018 年 11 月淘汰。  目前使用 ACS 的應用程式和服務必須在此日期之前移轉至不同的驗證機制。 本文件說明針對規劃取代使用 ACS 之目前客戶的建議。 如果您目前未使用 ACS，您不需要採取任何動作。


## <a name="brief-acs-overview"></a>簡短 ACS 概觀

ACS 是雲端驗證服務，可讓您輕鬆地對要存取您的 Web 應用程式和服務的使用者進行驗證和授權，並可讓您在程式碼中排除驗證和授權許多功能的考量。 它主要是供 .NET 用戶端、ASP.NET Web 應用程式和 WCF Web 服務的開發人員與架構設計人員使用。

ACS 的使用案例可以分成三個主要類別：

- 特定 Microsoft 雲端服務的驗證，包括 Azure 服務匯流排、Dynamics CRM 等等。 用戶端應用程式可以從 ACS 取得權杖，用來驗證這些服務以執行各種動作。
- 將驗證新增至 Web 應用程式，包括自訂建置變化和預先封裝變化 (例如 Sharepoint)。 使用 ACS「被動」驗證，Web 應用程式可以支援使用 Google、Facebook、Yahoo、Microsoft 帳戶 (Live ID)、Azure Active Directory 和 ADFS 的帳戶登入。
- 使用 ACS 簽發的權杖保護自訂建置 Web 服務。 使用「主動」驗證，Web 服務可以確保僅允許來自已使用 ACS 驗證之已知用戶端的存取。

每個使用案例及其建議的移轉策略會在下列章節中討論。 在大部分情況下，將現有的應用程式和服務移轉至較新的技術需要重大的程式碼變更。 建議您立即開始規劃及執行您的移轉，以避免任何可能的中斷或停機。

> [!WARNING]
> 在大部分情況下，將現有的應用程式和服務移轉至較新的技術需要重大的程式碼變更。 建議您立即開始規劃及執行您的移轉，以避免任何可能的中斷或停機。

在架構上，ACS 完全是由下列元件組成：

- 安全權杖服務 (STS)，接收驗證要求並且轉而簽發安全性權杖。
- 傳統 Azure 入口網站，用來建立、刪除和啟用/停用 ACS 命名空間。
- 個別 ACS 管理入口網站，用來自訂與設定 ACS 命名空間的行為。
- 管理服務，用來自動化入口網站的功能。
- 權杖轉換規則引擎，可以用來定義複雜邏輯以處理 ACS 所簽發之權杖的輸出格式。

若要使用這些元件，您必須建立一或多個 ACS **命名空間**。 命名空間是您的應用程式和服務與其通訊之 ACS 的專用執行個體，它與使用自己命名空間的所有其他 ACS 客戶隔離。 ACS 中的命名空間有專用 URL，例如：

```HTTP
https://mynamespace.accesscontrol.windows.net
```

與 STS 和管理作業的所有通訊都在此 URL 完成，不同用途有不同路徑。 若要判斷您的應用程式或服務是否使用 ACS，請監視對 `https://{namespace}.accesscontrol.windows.net` 的任何流量。  對這個 URL 的任何流量，是由 ACS 處理且必須停止的流量。  例外是對 `https://accounts.accesscontrol.windows.net` 的任何流量 -對這個 URL 的流量已由不同服務處理，且不會受到 ACS 取代的影響。  您也應該確定登入 Azure 傳統入口網站，並檢查您所擁有之訂用帳戶中的任何 ACS 命名空間。  ACS 命名空間是列在 [存取控制命名空間] 索引標籤底下的 **Active Directory** 服務。

如需有關 ACS 的詳細資訊，請參閱 [MSDN 上的這份封存文件](https://msdn.microsoft.com/library/hh147631.aspx)。

## <a name="retirement-schedule"></a>淘汰排程

到 2017 年 11 月止，所有 ACS 元件完全受到支援且可運作。 唯一的限制是[無法透過傳統 Azure 入口網站建立新的 ACS 命名空間](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)。

這些元件的取代時間軸會遵循這個排程：

- **2017 年 11 月**：傳統 Azure 入口網站中的 Azure AD 管理員體驗[已淘汰](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/)。 此時，ACS 的命名空間管理可於以下新的、專用 URL 取得：`http://manage.windowsazure.com?restoreClassic=true`。 這可讓您檢視現有的命名空間、啟用/停用它們，以及視需要完全刪除它們。
- **2018 年 4 月**：ACS 命名空間管理無法再於此專用 URL 取得。 在此時間點，您不能停用/啟用、刪除或列舉您的 ACS 命名空間。 不過，ACS 管理入口網站完全運作正常，位於 `https://{namespace}.accesscontrol.windows.net`。 ACS 的所有其他元件都會繼續正常運作。
- **2018 年 11 月**：所有 ACS 元件會永久關閉。 這包括 ACS 管理入口網站、管理服務、STS 和權杖轉換規則引擎。 此時，傳送給 ACS (位於 `{namespace}.accesscontrol.windows.net`) 的任何要求都會失敗。 您應該在此時段之前確實將所有現有的應用程式和服務移轉至其他技術。


## <a name="migration-strategies"></a>移轉策略

下列章節說明從 ACS 移轉到其他 Microsoft 技術的高階建議。

### <a name="clients-of-microsoft-cloud-services"></a>Microsoft 雲端服務的用戶端

接受 ACS 所簽發之權杖的每個 Microsoft 雲端服務，現在支援至少一個替代形式的驗證。 每個服務的正確驗證機制各有不同，因此我們建議參閱每個服務的特定文件以取得官方指引。 為了方便起見，以下提供每一組文件：

| 服務 | 指引 |
| ------- | -------- |
| Azure 服務匯流排 | [移轉至共用存取簽章](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure 轉送 | [移轉至共用存取簽章](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Azure 快取 | [移轉至 Azure Redis 快取](https://docs.microsoft.com/azure/redis-cache/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure 資料市場 | [移轉至辨識服務 API](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk 服務 | [移轉至 Azure Logic Apps](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Azure 媒體服務 | [移轉至 Azure AD 驗證](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure 備份 | [升級 Azure 備份代理程式](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: http://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->


### <a name="web-applications-using-passive-authentication"></a>使用被動驗證的 Web 應用程式

對於使用 ACS 進行使用者驗證的 Web 應用程式，ACS 會提供下列功能給 Web 應用程式的開發人員與架構設計人員：

- 與 Windows Identity Foundation (WIF) 深入整合。
- 與 Google、Facebook、Yahoo、Microsoft 帳戶 (Live ID)、Azure Active Directory 和 ADFS 同盟。
- 支援下列驗證通訊協定：OAuth 2.0 draft 13、WS-信任和 WS-同盟。
- 支援下列權杖格式：JSON Web 權杖 (JWT)、SAML 1.1、SAML 2.0 和簡單 Web 權杖 (SWT)。
- 首頁領域探索體驗，整合至 WIF，允許使用者選擇他們用來登入的帳戶類型。 這項體驗是由 Web 應用程式裝載並且可完全自訂。
- 權杖轉換，允許由 Web 應用程式從 ACS 接收之宣告的豐富自訂，包括：
    - 通過識別提供者的宣告
    - 新增額外的自訂宣告
    - 簡單的 if-then 邏輯，以在某些條件下發出宣告

很遺憾，目前沒有一個服務提供所有對等的功能。  您應該評估需要哪些 ACS 功能，然後在使用 [ **Azure Active Directory**](https://azure.microsoft.com/develop/identity/signin/) (Azure AD)、[**Azure AD B2C**](https://azure.microsoft.com/services/active-directory-b2c/)，或其他雲端驗證服務之間選擇。

#### <a name="migrating-to-azure-active-directory"></a>移轉至 Azure Active Directory

要考慮的一個路徑是將應用程式和服務直接與 Azure Active Directory 整合。 Azure AD 是 Microsoft 工作及學校帳戶的雲端型識別提供者 - 它是 Office 365、Azure 等等的識別提供者。 它會為 ACS 提供類似的同盟驗證功能，但是不支援所有 ACS 功能。 主要範例是與社交識別提供者的同盟，例如 Facebook、Google 和 Yahoo。 如果您的使用者使用這些類型的認證進行登入，則 Azure AD 不適合您。 它也不一定會支援與 ACS 完全相同的驗證通訊協定 - 例如，ACS 和 Azure AD 都支援 OAuth，需要您在移轉作業中修改程式碼的每個實作之間有些微差異。

但是，Azure AD 確實為 ACS 的客戶提供數個潛在的優點。 它原生支援裝載於雲端中的 Microsoft 工作和學校帳戶，通常是由 ACS 客戶使用。  Azure AD 租用戶也可以透過 ADFS，與內部部署 Active Directory 的一或多個執行個體同盟，讓您的應用程式驗證雲端型使用者與內部部署裝載的使用者。  它也支援 WS-同盟通訊協定，使得與使用 Windows Identity Foundation (WIF) 之 Web 應用程式的整合相對直覺。

下表比較與 Azure AD 中可用之 Web 應用程式相關的 ACS 的功能。 在高階中，**如果您只讓使用者使用其 Microsoft 工作和學校帳戶進行登入，則 Azure Active Directory 可能是移轉的適當選擇**。

| 功能 | ACS 支援 | Azure AD 支援 |
| ---------- | ----------- | ---------------- |
| **帳戶類型** | | |
| Microsoft 工作和學校帳戶 | 支援 | 支援 |
| Windows Server Active Directory 和 ADFS 的帳戶 | 透過與 Azure AD 租用戶的同盟支援 <br /> 透過與 ADFS 的直接同盟支援 | 僅透過與 Azure AD 租用戶的同盟支援 | 
| 其他企業身分識別管理系統的帳戶 | 可能透過與 Azure AD 租用戶的同盟 <br />透過直接同盟支援 | 可能透過與 Azure AD 租用戶的同盟 |
| 供個人使用的 Microsoft 帳戶 (Windows Live ID) | 支援 | 透過 Azure AD 的 v2.0 OAuth 通訊協定支援，但未透過任何其他通訊協定支援。 | 
| Facebook、Google、Yahoo 帳戶 | 支援 | 皆不支援。 |
| **通訊協定與 SDK 相容性** | | |
| Windows Identity Foundation (WIF) | 支援 | 支援，但是只有限制的指示可用。 |
| WS-同盟 | 支援 | 支援 |
| OAuth 2.0 | 支援 Draft 13 | 支援 RFC 6749，最新型的規格。 |
| WS-信任 | 支援 | 不支援 |
| **權杖格式** | | |
| Json Web 權杖 (JWT) | 在搶鮮版 (Beta) 中支援 | 支援 |
| SAML 1.1 權杖 | 支援 | 支援 |
| SAML 2.0 權杖 | 支援 | 支援 |
| 簡單 Web 權杖 (SWT) | 支援 | 不支援 |
| **自訂** | | |
| 可自訂的首頁領域探索/帳戶挑選 UI | 可下載的程式碼，可以納入應用程式 | 不支援 |
| 上傳自訂權杖簽署憑證 | 支援 | 支援 |
| 在權杖中自訂宣告 | 通過識別提供者的輸入宣告<br />取得識別提供者的存取權杖作為宣告<br />根據輸入宣告值發出輸出宣告<br />發出具有常數值的輸出宣告 | 無法通過同盟識別提供者的宣告<br />無法從識別提供者取得存取權杖作為宣告<br />無法根據輸入宣告值發出輸出宣告<br />可以發出具有常數值的輸出宣告<br />可以根據同步處理至 Azure AD 之使用者的屬性發出輸出宣告 |
| **自動化** | | |
| 自動化設定/管理工作 | 透過 ACS 管理服務支援 | 透過 Microsoft Graph 和 Azure AD Graph API 支援。 |

如果您決定 Azure AD 對於您的應用程式和服務是正確的前進方向，您應該要知道讓應用程式與 Azure AD 整合的兩種方式。

若要使用 WS-同盟/WIF 與 Azure AD 整合，我們建議以下[在本文中所述的方法](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)。 文章是針對 SAML 型單一登入設定 Azure AD，但是同樣適用於設定 WS-同盟。 遵循此方法需要 Azure AD Premium 授權，但有兩個好處：

- 您有 Azure AD 權杖自訂的完整彈性。 這可讓您自訂 Azure AD 所簽發的宣告，以符合 ACS 所簽發的那些宣告，尤其包含使用者識別碼或名稱識別碼宣告。 您必須確保 Azure AD 所簽發的使用者識別碼符合 ACS 所簽發的識別碼，讓您即使在變更技術之後，繼續收到使用者的一致使用者識別碼。
- 您可以專門為您的應用程式設定權杖簽署憑證，該應用程式的存留期在您的控制中。

<!--

Possible nameIdentifiers from ACS (via AAD or ADFS):
- ADFS - Whatever ADFS is configured to send (email, UPN, employeeID, what have you)
- Default from AAD using App Registrations, or Custom Apps before ClaimsIssuance policy: subject/persistent ID
- Default from AAD using Custom apps nowadays: UPN
- Kusto can't tell us distribution, it's redacted

-->

> [!NOTE]
> 使用這種方式需要 Azure AD Premium 授權。 如果您是 ACS 客戶，而且您需要 Premium 授權以設定應用程式的單一登入，請與我們連絡，我們很樂意提供開發人員授權讓您使用。

替代方式是依照[此程式碼範例](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation)，提供有關如何設定 WS-同盟的些微不同指示。 此程式碼範例不使用 WIF，而是使用 ASP.NET 4.5 OWIN 中介軟體。 不過，應用程式註冊的指示對於使用 WIF 的應用程式有效，而且不需要 Azure AD Premium 授權。  如果您選擇此方法，您必須了解[在 Azure AD 中簽署金鑰變換](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover)。 這個方法會使用 Azure AD 全域簽署金鑰來簽發權杖。 根據預設，WIF 不會自動重新整理簽署金鑰。 當 Azure AD 轉換其全域簽署金鑰時，您的 WIF 實作必須準備好接受變更。

如果您可以透過 OpenID Connect 或 OAuth 通訊協定與 Azure AD 整合，建議進行。  在我們的 [Azure AD 開發人員指南](http://aka.ms/aaddev)中有如何將 Azure AD 整合至您的 Web 應用程式的詳盡文件和指引。

<!-- TODO: If customers ask about authZ, let's put a blurb on role claims here -->

#### <a name="migrating-to-azure-ad-b2c"></a>移轉至 Azure AD B2C

要考量的其他移轉路徑是 Azure AD B2C。  B2C 是雲端驗證服務，類似於 ACS，可讓開發人員將其驗證和身分識別管理邏輯外包給雲端服務。  它是付費服務 (有免費和進階層)，專為面對具有多達數以百萬計作用中使用者之應用程式的取用者所設計。

就像 ACS，B2C 其中一個最具吸引力的功能是支援許多不同類型的帳戶。 您可以使用 B2C，使用其 Facebook、Google、Microsoft、LinkedIn、GitHub、Yahoo 帳戶等等來登入使用者。 B2C 還支援「本機帳戶」或使用者名稱與密碼，這是使用者特別為您的應用程式建立的。 Azure AD B2C 還提供豐富的擴充性，您可以用來自訂您的登入流程。 不過，它不支援 ACS 客戶可能需要的驗證通訊協定和權杖格式的幅度。 它也不能用來取得權杖以及從識別提供者、Microsoft 等等查詢使用者的相關其他資訊。

下表比較與 Azure AD B2C 中可用之 Web 應用程式相關的 ACS 的功能。 在高階中，**如果您的應用程式是針對取用者，或者如果它支援不同類型的帳戶，則 Azure AD B2C 對於您的移轉可能是正確的選擇。**

| 功能 | ACS 支援 | Azure AD B2C 支援 |
| ---------- | ----------- | ---------------- |
| **帳戶類型** | | |
| Microsoft 工作和學校帳戶 | 支援 | 透過自訂原則支援  |
| Windows Server Active Directory 和 ADFS 的帳戶 | 透過與 ADFS 的直接同盟支援 | 透過使用自訂原則的 SAML 同盟支援 |
| 其他企業身分識別管理系統的帳戶 | 透過使用 WS-同盟的直接同盟支援 | 透過使用自訂原則的 SAML 同盟支援 |
| 供個人使用的 Microsoft 帳戶 (Windows Live ID) | 支援 | 支援 | 
| Facebook、Google、Yahoo 帳戶 | 支援 | 原生支援 Facebook 和 Google，Yahoo 則是透過使用自訂原則的 OpenID Connect 同盟支援 |
| **通訊協定與 SDK 相容性** | | |
| Windows Identity Foundation (WIF) | 支援 | 不支援。 |
| WS-同盟 | 支援 | 不支援。 |
| OAuth 2.0 | 支援 Draft 13 | 支援 RFC 6749，最新型的規格。 |
| WS-信任 | 支援 | 不支援。 |
| **權杖格式** | | |
| Json Web 權杖 (JWT) | 在搶鮮版 (Beta) 中支援 | 支援 |
| SAML 1.1 權杖 | 支援 | 不支援 |
| SAML 2.0 權杖 | 支援 | 不支援 |
| 簡單 Web 權杖 (SWT) | 支援 | 不支援 |
| **自訂** | | |
| 可自訂的首頁領域探索/帳戶挑選 UI | 可下載的程式碼，可以納入應用程式 | 可透過自訂 CSS 完整自訂 UI。 |
| 上傳自訂權杖簽署憑證 | 支援 | 自訂簽署金鑰，不是憑證，透過自訂原則支援。 |
| 在權杖中自訂宣告 | 通過識別提供者的輸入宣告<br />取得識別提供者的存取權杖作為宣告<br />根據輸入宣告值發出輸出宣告<br />發出具有常數值的輸出宣告 | 可以通過識別提供者的宣告。 部分宣告需要自訂原則。<br />無法從識別提供者取得存取權杖作為宣告<br />可以透過自訂原則根據輸入宣告值發出輸出宣告<br />可以透過自訂原則發出具有常數值的輸出宣告 |
| **自動化** | | |
| 自動化設定/管理工作 | 透過 ACS 管理服務支援 | 允許透過 Azure AD Graph API 建立使用者。 無法以程式設計的方式建立 B2C 租用戶、應用程式或原則。 |

如果您決定 Azure AD B2C 對於您的應用程式和服務是正確的前進方向，您應該使用下列資源開始：

- [Azure AD B2C 文件](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Azure AD B2C 自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Azure AD B2C 價格](https://azure.microsoft.com/pricing/details/active-directory-b2c/)


#### <a name="other-migration-options"></a>其他移轉選項

如果 Azure AD 或 Azure AD B2C 都不符合您的 Web 應用程式需求，請與我們連絡，我們可以協助您找出最佳的前進方向。

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD nor Azure AD B2C is sufficient to replace ACS in your web applications without making major code changes.  Some common examples might include:

- web applications using WIF and/or WS-Federation for sign-in with social identity providers such as Google or Facebook.
- web applications performing direct federation to an enterprise IDP over the Ws-Federation protocol.
- web applications that require the access token issued by a social identity provider (such as Google or Facebook) as a claim in the tokens issued by ACS.
- web applications with complex token transformation rules that Azure AD or Azure AD B2C cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

<!--

## Sharepoint 2010, 2013, 2016

TODO: AAD only, use AAD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-using-active-authentication"></a>使用主動驗證的 Web 服務

針對使用 ACS 所簽發之權杖進行保護的 Web 服務，ACS 提供下列功能：

- 能夠在您的 ACS 命名空間中註冊一或多個**服務識別**，可用來要求權杖。
- 支援 OAuth WRAP 和 OAuth 2.0 Draft 13 通訊協定來要求權杖，使用下列類型的認證：
    - 為服務識別建立的簡單密碼
    - 使用對稱金鑰或 X509 憑證的已簽署 SWT
    - 由受信任的識別提供者 (通常是 ADFS 執行個體) 所簽發的 SAML 權杖
- 支援下列權杖格式：JSON Web 權杖 (JWT)、SAML 1.1、SAML 2.0 和簡單 Web 權杖 (SWT)。
- 簡單權杖轉換規則

ACS 中的服務識別通常會用於實作伺服器對伺服器 (S2S) 之類的驗證。  

#### <a name="migrating-to-azure-active-directory"></a>移轉至 Azure Active Directory

我們對於這類驗證流程的建議是移轉至 [**Azure Active Directory**](https://azure.microsoft.com/develop/identity/signin/) (Azure AD)。 Azure AD 是 Microsoft 工作及學校帳戶的雲端型識別提供者 - 它是 Office 365、Azure 等等的識別提供者。 但是，它也可以用於使用 OAuth 用戶端認證授與之 Azure AD 實作的伺服器對伺服器驗證。  下表比較 ACS 中伺服器對伺服器驗證的功能與 Azure AD 中可用的功能。

| 功能 | ACS 支援 | Azure AD 支援 |
| ---------- | ----------- | ---------------- |
| 如何註冊 Web 服務 | 在 ACS 管理入口網站中建立信賴憑證者。 | 在 Azure 入口網站中建立 Azure AD Web 應用程式。 |
| 如何註冊用戶端 | 在 ACS 管理入口網站中建立服務識別。 | 在 Azure 入口網站中建立其他 Azure AD Web 應用程式。 |
| 使用的通訊協定 | OAuth WRAP 通訊協定<br />OAuth 2.0 Draft 13 用戶端認證授與 | OAuth 2.0 用戶端認證授與 |
| 用戶端驗證方法 | 簡單密碼<br />已簽署 SWT<br />來自同盟 IDP 的 SAML 權杖 | 簡單密碼<br />已簽署 JWT |
| 權杖格式 | JWT<br />SAML 1.1<br />SAML 2.0<br />SWT<br /> | 僅限 JWT |
| 權杖轉換 | 新增自訂宣告<br />簡單 if-then 宣告發行邏輯 | 新增自訂宣告 | 
| 自動化設定/管理工作 | 透過 ACS 管理服務支援 | 透過 Microsoft Graph 和 Azure AD Graph API 支援。 |

如需實作伺服器對伺服器案例的指引，請參閱下列資源：

- [Azure AD 開發人員指南的服務對服務一節](https://aka.ms/aaddev)。
- [使用簡單密碼用戶端認證的精靈程式碼範例](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [使用憑證用戶端認證的精靈程式碼範例](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="other-migration-options"></a>其他移轉選項

如果 Azure AD 不符合您的 Web 服務需求，請留下註解，我們可協助您找出您特定案例的最佳計劃。

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD's client credentials OAuth grant implementation is not sufficient to replace ACS in your architecture without major code changes.  Some common examples might include:

- server-to-server authentication using token formats other than JWTs.
- server-to-server authentication using an input token provided by an external identity provider.
- server-to-server authentication with token transformation rules that Azure AD cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

## <a name="questions-concerns--feedback"></a>問題、考量與意見反應

我們了解許多 ACS 客戶在閱讀這篇文章之後，找不到清楚的移轉路徑，而且可能需要一些協助或指引以便判斷正確的計劃。 如果您想要討論您的移轉案例和問題，請在此分頁上留下註解。
