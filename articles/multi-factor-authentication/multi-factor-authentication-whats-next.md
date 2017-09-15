---
title: "設定 Azure MFA | Microsoft Docs"
description: "這是描述 Azure Multi-factor Authentication 後續步驟的 MFA 頁面。  其內容包括報告、詐騙警示、一次性略過、自訂語音訊息、快取、信任的 IP 及應用程式密碼。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: yossib
ms.assetid: 75af734e-4b12-40de-aba4-b68d91064ae8
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2017
ms.author: joflore
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: a470a8bc70365a1891fc57efc6d2d4391162fd17
ms.contentlocale: zh-tw
ms.lasthandoff: 08/24/2017

---
# <a name="configure-azure-multi-factor-authentication-settings"></a>設定 Azure Multi-Factor Authentication 設定
既然您已啟動並執行 Azure Multi-Factor Authentication，本文將協助您進行管理。  本文涵蓋各種主題，協助您充分發揮 Azure Multi-Factor Authentication 的功效。  並非所有版本的 Azure Multi-Factor Authentication 均提供所有這些功能。

| 功能 | 說明 | 
|:--- |:--- |
| [詐騙警示](#fraud-alert) |您可以設定詐騙警示，讓使用者得以針對存取其資源的詐騙嘗試提出報告。 |
| [一次性略過](#one-time-bypass) |一次性略過可讓使用者「略過」Multi-Factor Authentication 來通過驗證一次。 |
| [自訂語音訊息](#custom-voice-messages) |自訂語音訊息可讓您將自己的錄音或問候語用於 Multi-Factor Authentication。 |
| [快取](#caching-in-azure-multi-factor-authentication) |您可以利用快取來設定一段特定的時間，讓後續的驗證嘗試自動成功。 |
| [信任的 IP](#trusted-ips) |受管理或同盟租用戶的系統管理員可以使用「信任的 IP」，讓從公司近端內部網路登入的使用者略過雙步驟驗證。 |
| [應用程式密碼](#app-passwords) |應用程式密碼允許非 MFA 感知應用程式略過 Multi-Factor Authentication 並繼續運作。 |
| [針對已記住的裝置和瀏覽器，記住其 Multi-Factor Authentication](#remember-multi-factor-authentication-for-devices-that-users-trust) |可讓您在使用者使用 MFA 成功登入後的設定天數內記住裝置。 |
| [可選取的驗證方法](#selectable-verification-methods) |可讓您選擇可供使用者使用的驗證方法。 |

## <a name="access-the-azure-mfa-management-portal"></a>存取 Azure MFA 管理入口網站

本文提及的功能是在 Azure Multi-Factor Authentication 管理入口網站中設定。 有兩種方式可透過 Azure 傳統入口網站存取 MFA 管理入口網站。 第一種方式是藉由管理 Multi-Factor Auth Provider。 第二種方式是透過 MFA 服務設定。 

### <a name="use-an-auth-provider"></a>使用驗證提供者

如果您針對以使用情況為基礎的 MFA 使用 Multi-Factor Auth Provider，請採用此方法存取管理入口網站。

若要透過 Azure Multi-Factor Auth Provider 存取 MFA 管理入口網站，請以系統管理員身分登入 Azure 傳統入口網站，並選取 [Active Directory] 選項。 按一下 [多因素驗證提供者] 索引標籤，並選取您的目錄，然後按一下底部的 [管理] 按鈕。

### <a name="use-the-mfa-service-settings-page"></a>使用 [MFA 服務設定] 頁面 

如果您有 Multi-Factor Auth Provider，或有 Azure MFA、Azure AD Premium 或 Enterprise Mobility + Security 授權，請使用此方法存取 [MFA 服務設定] 頁面。

若要透過 [MFA 服務設定] 頁面存取 MFA 管理入口網站，請以系統管理員身分登入 Azure 傳統入口網站，並選取 [Active Directory] 選項。 按一下您的目錄，然後按一下 [設定]  索引標籤。在 [Multi-Factor Authentication] 區段底下，選取 [管理服務設定] 。 在 [MFA 服務設定] 頁面底部，按一下 [移至入口網站]  連結。


## <a name="fraud-alert"></a>詐騙警示
您可以設定詐騙警示，讓使用者得以針對存取其資源的詐騙嘗試提出報告。  使用者可以透過行動應用程式或電話來報告詐騙活動。

### <a name="set-up-fraud-alert"></a>設定詐騙警示
1. 依照此頁面最上方的指示，瀏覽至 MFA 管理入口網站。
2. 在 Azure Multi-Factor Authentication 管理入口網站中，按一下 [設定] 區段底下的 [設定]。
3. 在 [設定] 頁面的 [詐騙警示] 區段底下，勾選 [允許使用者提交詐騙警示] 核取方塊。
4. 選取 [儲存] 以套用變更。 

### <a name="configuration-options"></a>組態選項

- **回報詐騙時封鎖使用者** - 如果使用者報告詐騙，將會封鎖其帳戶。
- **初始問候語期間以代碼報告詐騙** - 使用者通常會按 # 來確認雙步驟驗證。 如果他們想要報告詐騙，則需要在按 # 之前輸入代碼。 根據預設，此代碼是 **0**，但您可以自訂。

> [!NOTE]
> Microsoft 的預設語音問候語會指示使用者按 0# 以提交詐騙警示。 如果您要使用 0 以外的代碼，則應該記錄下來，並上傳您自己的自訂語音問候語與適當的指示。

![詐騙警示選項 - 螢幕擷取畫面](./media/multi-factor-authentication-whats-next/fraud.png)

### <a name="how-users-report-fraud"></a>使用者如何報告詐騙 
提報詐騙活動的方法有兩種。  使用者可以透過或行動應用程式或電話來提報。  

#### <a name="report-fraud-with-the-mobile-app"></a>利用行動裝置應用程式報告詐騙
1. 系統將驗證傳送到電話時，請選取它來開啟 Microsoft Authenticator 應用程式。
2. 在通知上選取 [拒絕]。 
3. 選取 [回報詐騙]。
4. 關閉應用程式。

#### <a name="report-fraud-with-a-phone"></a>使用電話報告詐騙
1. 當電話響起驗證來電時，請逕行接聽。  
2. 若要報告詐騙，請輸入詐騙代碼 (預設值為 0)，然後按 # 符號。 系統會通知您詐騙警示已提交。
3. 結束通話。

### <a name="view-fraud-reports"></a>檢視詐騙報告
1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 在左側選取 [Active Directory]。
3. 在頂端選取 [Multi-Factor Auth Provider]。 這會顯示您的多因素驗證提供者清單。
4. 選取您的 Multi-Factor Auth Provider，然後按一下頁面底部的 [管理]。 這時會開啟 Azure Multi-Factor Authentication 管理入口網站。
5. 在 Azure Multi-Factor Authentication 管理入口網站的 [檢視報告] 下方，按一下 [詐騙警示]。
6. 在報告中指定要檢視的日期範圍。 您也可以指定使用者名稱、電話號碼和使用者狀態。
7. 按一下 **[執行]**。 這樣會產生詐騙警示報告。 如果您想要匯出報告，請按一下 [匯出至 CSV]。

## <a name="one-time-bypass"></a>一次性略過
「單次許可」可讓使用者只驗證一次，不需要執行雙步驟驗證。 許可只是暫時性，經過指定的秒數之後就會到期。 在行動裝置應用程式或電話沒有收到通知或來電的情況下，您可以啟用單次許可，讓使用者能夠存取所需的資源。

### <a name="create-a-one-time-bypass"></a>建立單次許可
1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 依照此頁面最上方的指示，瀏覽至 MFA 管理入口網站。
3. 在 Azure Multi-Factor Authentication 管理入口網站中，如果在左側看到您的租用戶或 Azure MFA 提供者的名稱旁邊有一個 **++，按一下**  可查看不同的 MFA 伺服器複寫群組和 Azure 預設群組。 選取適當的群組。
4. 在 [使用者管理] 底下，選取 [單次許可] 。
5. 在 [單次許可] 頁面中，按一下 [新增單次許可] 。

  ![雲端](./media/multi-factor-authentication-whats-next/create1.png)

6. 輸入使用者名稱、許可的持續秒數及許可的理由。 按一下 [許可]。
7. 時間限制立即生效，因此，使用者必須在單次許可到期之前登入。 

### <a name="view-the-one-time-bypass-report"></a>檢視單次許可報告
1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 在左側選取 [Active Directory]。
3. 在頂端選取 [Multi-Factor Auth Provider]。 這會顯示您的多因素驗證提供者清單。
4. 選取您的 Multi-Factor Auth Provider，然後按一下頁面底部的 [管理]。 這時會開啟 Azure Multi-Factor Authentication 管理入口網站。
5. 在 Azure Multi-Factor Authentication 管理入口網站的左側，按一下 [檢視報告] 下方的 [單性許可]。
6. 在報告中指定要檢視的日期範圍。 您也可以指定使用者名稱、電話號碼和使用者狀態。
7. 按一下 **[執行]**。 這樣會產生許可報告。 如果您想要匯出報告，請按一下 [匯出至 CSV]。

## <a name="custom-voice-messages"></a>自訂語音訊息
自訂語音訊息可讓您將自己的錄音或問候語用於雙步驟驗證。 您可以在 Microsoft 錄音之外額外使用自訂語音訊息，也可以取代 Microsoft 的錄音。

在開始之前，請注意下列項目：

* 支援的檔案格式為 .wav 和 .mp3。
* 檔案大小限制為 5 MB。
* 驗證訊息應該少於 20 秒。 超過這個秒數的任何訊息會導致驗證失敗，因為使用者在訊息結束之前可能沒有回應，造成驗證逾時。

### <a name="set-up-a-custom-message"></a>設定自訂訊息

建立自訂訊息分成兩個部分。 首先，您上傳訊息，然後播放給使用者聽。

若要上傳自訂訊息︰

1. 使用其中一種支援的檔案格式建立自訂語音訊息。
2. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
3. 依照此頁面最上方的指示，瀏覽至 MFA 管理入口網站。
4. 在 Azure Multi-Factor Authentication 管理入口網站中，按一下 [設定] 區段底下的 [語音訊息]。
5. 在 [設定：語音訊息] 頁面上，按一下 [新增語音訊息]。
   ![雲端](./media/multi-factor-authentication-whats-next/custom1.png)
6. 在 [設定：新增語音訊息] 頁面上，按一下 [管理聲音檔] 。
   ![雲端](./media/multi-factor-authentication-whats-next/custom2.png)
7. 在 [設定：聲音檔] 頁面上，按一下 [上傳聲音檔] 。
   ![雲端](./media/multi-factor-authentication-whats-next/custom3.png)
8. 在 [設定：上傳聲音檔] 上，按一下 [瀏覽] 以瀏覽至您的語音訊息，然後按一下 [開啟]。
9. 新增 [描述]，然後按一下 [上傳]。
10. 這項作業完成之後，會出現一則訊息，確認已成功上傳檔案。

若要播放訊息給使用者聽︰

1. 在左側按一下 [語音訊息]。
2. 在 [語音訊息] 區段下方，按一下 [新增語音訊息] 。
3. 從 [語言] 下拉式清單選取語言。
4. 如果此訊息僅適用於特定應用程式，請在 [應用程式] 方塊中指定。
5. 從 [訊息類型] 下拉式清單，選取要以新的自訂訊息覆寫的訊息類型。
6. 從 [音效檔] 下拉式清單，選取您在第一個部分上傳的音效檔。
7. 按一下 [建立] 。 即會出現一則訊息，確認已成功建立語音訊息。
    ![雲端](./media/multi-factor-authentication-whats-next/custom5.png)</center>

## <a name="caching-in-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 中的快取
您可以利用快取來設定一段特定的時間，讓後續不超過這段時間的驗證嘗試自動成功。 這主要是在內部部署系統 (例如 VPN) 於第一個要求仍在進行中的同時傳送多個驗證要求時使用。 如此，在使用者成功完成進行中的第一次驗證後，後續要求會自動成功。 

快取並非用於登入 Azure AD。

### <a name="set-up-caching"></a>設定快取 
1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 依照此頁面最上方的指示，瀏覽至 MFA 管理入口網站。
3. 在 Azure Multi-Factor Authentication 管理入口網站中，按一下 [設定] 區段底下的 [快取]。
4. 在 [設定快取] 頁面上，按一下 [新增快取]。
5. 選取快取類型和快取秒數。 按一下 [建立] 。

<center>![雲端](./media/multi-factor-authentication-whats-next/cache.png)</center>

## <a name="trusted-ips"></a>信任的 IP
「信任的 IP」是 Azure MFA 的一項功能，受管理或同盟租用戶的管理員可以利用此功能，讓從公司近端內部網路登入的使用者略過雙步驟驗證。 完整版 Azure Multi-Factor Authentication 提供此功能給管理員使用，免費版不提供。 如需有關如何取得完整版 Azure Multi-Factor Authentication 的詳細資訊，請參閱[ Azure Multi-Factor Authentication](multi-factor-authentication.md)。

| Azure AD 租用戶類型 | 可用的信任 IP 選項 |
|:--- |:--- |
| 受管理 |<li>特定 IP 位址範圍 - 對於從公司內部網路登入的使用者，管理員可以指定允許略過雙步驟驗證的 IP 位址範圍。</li> |
| 同盟 |<li>所有同盟使用者 - 所有從組織內登入的同盟使用者，都使用 AD FS 所發行的宣告來略過雙步驟驗證。</li><br><li>特定 IP 位址範圍 - 對於從公司內部網路登入的使用者，管理員可以指定允許略過雙步驟驗證的 IP 位址範圍。 |

此略過機制只適用於公司內部網路。 例如，如果您選取所有同盟使用者，當使用者從公司的內部網路之外登入時，即使該使用者出示 AD FS 宣告，仍必須使用雙步驟驗證來進行驗證。 

**公司網路內部的使用者體驗︰**

停用「信任的 IP」時，瀏覽器流程需要雙步驟驗證，較舊的豐富型用戶端應用程式需要應用程式密碼。 

啟用「信任的 IP」時，瀏覽器流程「不」需要雙驟驗證，較舊的豐富型用戶端應用程式「不」需要應用程式密碼，前提是使用者尚未建立應用程式密碼。 應用程式密碼一旦使用，以後就都需要使用。 

**公司網路外部的使用者體驗︰**

不論啟用或停用「信任的 IP」，瀏覽器流程需要雙步驟驗證，較舊的豐富型用戶端應用程式需要應用程式密碼。 

### <a name="to-enable-trusted-ips"></a>啟用信任的 IP
1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 依照本文開頭的指示，瀏覽至 [MFA 服務設定] 頁面。
3. 在 [服務設定] 頁面之 [信任的 IP] 下方，您有兩個選擇：
   
   * **適用於從我的內部網路產生的同盟使用者提出的要求** – 勾選此方塊。 所有從公司網路登入的同盟使用者，將會使用 AD FS 所發行的宣告來略過雙步驟驗證。
   * **適用於從特定公用 IP 範圍提出的要求**– 在提供的文字方塊中，使用 CIDR 表示法輸入 IP 位址。 例如：xxx.xxx.xxx.0/24 代表介於 xxx.xxx.xxx.1 – xxx.xxx.xxx.254 之範圍內的 IP 位址；xxx.xxx.xxx.xxx/32 代表一個 IP 位址。 您最多可以輸入 50 個 IP 位址範圍。 從這些 IP 位址登入的使用者會略過雙步驟驗證。
4. 按一下 [儲存] 。
5. 套用更新之後，按一下 [關閉]。

![信任的 IP](./media/multi-factor-authentication-whats-next/trustedips3.png)

## <a name="app-passwords"></a>應用程式密碼
某些應用程式 (例如 Office 2010 或更舊版本和 Apple Mail) 不支援雙步驟驗證。 它們未設定為接受第二次驗證。 若要使用這些應用程式，您需要使用「應用程式密碼」來取代傳統的密碼。 應用程式密碼可讓應用程式略過雙步驟驗證並繼續運作。

> [!NOTE]
> 適用於 Office 2013 用戶端的新式驗證
> 
> Office 2013 用戶端 (包括 Outlook) 和更新版本支援新式驗證通訊協定，而且可搭配雙步驟驗證。 一旦啟用，這些用戶端應用程式就不需要應用程式密碼。  如需詳細資訊，請參閱[發表的 Office 2013 新式驗證公開預覽](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。

### <a name="important-things-to-know-about-app-passwords"></a>應用程式密碼重要須知
以下清單是應用程式密碼的重要須知。

* 每個應用程式應該只需要輸入一次應用程式密碼。 使用者不需要追蹤應用程式密碼，也不需要每次輸入。
* 實際的密碼會自動產生，而不是由使用者提供。 這是因為攻擊者比較難以猜中自動產生的密碼，所以比較安全。
* 每位使用者的密碼以 40 組為限。 
* 在內部部署案例中會快取密碼並予以使用的應用程式可能會開始失敗，因為應用程式密碼無法在組織識別碼之外辨識。將 Exchange 電子郵件存放在內部部署設施，但是將封存郵件存放在雲端即是一例。 同樣的密碼無法適用於兩者。
* 一旦使用者的帳戶啟用 Multi-Factor Authentication，應用程式密碼即可用於大部分的非瀏覽器用戶端 (例如 Outlook 和 Lync)，但是透過非瀏覽器應用程式 (例如 Windows PowerShell) 無法使用應用程式密碼執行系統管理動作，即使具備系統管理員帳戶也一樣。  請確保您建立的服務帳戶是使用強式密碼來執行 PowerShell 指令碼，並且請勿將帳戶用於雙步驟驗證。

> [!WARNING]
> 應用程式密碼無法在用戶端會同時與內部部署及雲端自動探索端點通訊的混合環境作用。 這是因為需要網域密碼才能在內部部署進行驗證，而且需要應用程式密碼才能向雲端進行驗證。

### <a name="naming-guidance-for-app-passwords"></a>應用程式密碼的命名指引
應用程式密碼名稱應該反映出它們用在哪個裝置。 例如，如果膝上型電腦有 Outlook、Word 及 Excel 之類的非瀏覽器應用程式，請建立一個名為 Laptop 的應用程式密碼，並將該應用程式密碼用於這些應用程式中。 接著，在桌上型電腦上，為同樣這些應用程式建立另一個應用程式密碼，名為 Desktop。 

Microsoft 建議為每個裝置建立一個應用程式密碼，而不是為每個應用程式建立一個應用程式密碼。

### <a name="federated-sso-app-passwords"></a>同盟 (SSO) 應用程式密碼
Azure AD 支援與內部部署 Windows Server Active Directory Domain Services (AD DS) 同盟 (單一登入)。 如果您的組織與 Azure AD 同盟，而且您想要使用 Azure Multi-Factor Authentication，則以下有關應用程式密碼的資訊就很重要。 本節僅適用於同盟 (SSO) 客戶。

* 應用程式密碼由 Azure AD 驗證，因此會略過同盟。 唯有在設定應用程式密碼時才會主動使用同盟。
* 對於同盟 (SSO) 使用者，我們不會像被動流程一樣尋求識別提供者 (IdP)。 密碼會儲存在組織識別碼中。如果使用者離開公司，這些資訊必須使用 DirSync 即時流向組織識別碼。 帳戶停用/刪除最長可能需要三個小時才能完成同步處理，導致 Azure AD 中的應用程式密碼停用/刪除延遲。
* 應用程式密碼不會遵守內部部署用戶端存取控制設定。
* 應用程式密碼不適用內部部署驗證記錄/稽核功能。
* 某些進階架構設計在使用雙步驟驗證時，可能需要有組織使用者名稱和密碼及應用程式密碼的組合，需視驗證的位置而定。 對於根據內部部署基礎結構進行驗證的用戶端，您可以使用組織使用者名稱和密碼。 對於根據 Azure AD 進行驗證的用戶端，您需要使用應用程式密碼。

  例如，假設您的架構由以下項目組成：

  * 您要讓內部部署 Active Directory 執行個體與 Azure AD 同盟
  * 您正在使用 Exchange Online
  * 您只在內部使用 Lync
  * 您正在使用 Azure Multi-Factor Authentication

  ![Proofup](./media/multi-factor-authentication-whats-next/federated.png)

  在這些情況下，您必須執行下列作業：

  * 在登入 Lync 時，使用組織使用者名稱和密碼。
  * 在嘗試透過連接 Exchange online 的 Outlook 用戶端存取通訊錄時，使用應用程式密碼。

### <a name="allow-app-password-creation"></a>允許建立應用程式密碼
根據預設，使用者無法建立應用程式密碼。 這項功能必須經過啟用。 若要允許使用者建立應用程式密碼，請使用下列程序：

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 依照本文開頭的指示，瀏覽至 [MFA 服務設定] 頁面。
3. 選取 [允許使用者建立應用程式密碼以登入非瀏覽器應用程式] 旁的選項按鈕。

![建立應用程式密碼](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="create-app-passwords"></a>建立應用程式密碼
使用者可以在一開始的註冊期間建立應用程式密碼。 註冊程序的最後可讓他們選擇建立應用程式密碼。

在註冊之後，使用者也可以在 Azure 入口網站或 Office 365 入口網站中變更設定，以建立應用程式密碼。 如需使用者的詳細資訊和詳細步驟，請參閱[什麼是 Azure Multi-Factor Authentication 中的應用程式密碼](./end-user/multi-factor-authentication-end-user-app-passwords.md)。

## <a name="remember-multi-factor-authentication-for-devices-that-users-trust"></a>針對使用者信任的裝置，記住 Multi-Factor Authentication
記住裝置和瀏覽器的 Multi-Factor Authentication，使用者信任是供給所有 MFA 使用者的免費功能。 它可讓您在使用者使用 MFA 成功登入後的設定天數內有略過 MFA 的選項。 這會減少使用者在相同裝置上執行雙步驟驗證的次數，可提高可用性。

不過，若帳戶或裝置遭到入侵，則記住受信任裝置的 MFA 可能會影響安全性。 如果公司帳戶遭入侵或信任的裝置遺失或遭竊，您應該[在所有裝置上還原 Multi-Factor Authentication](multi-factor-authentication-manage-users-and-devices.md#restore-mfa-on-all-remembered-devices-for-a-user)。 此動作會從所有裝置撤銷受信任的狀態，使用者需要再次執行雙步驟驗證。 您也可以使用[管理雙步驟驗證的設定](./end-user/multi-factor-authentication-end-user-manage-settings.md#require-two-step-verification-again-on-a-device-youve-marked-as-trusted)中的指示，指示使用者在自己的裝置上還原 MFA

### <a name="how-it-works"></a>運作方式

當使用者在登入時勾選「**X** 天內不要再問我」的方塊時，記住 Multi-Factor Authentication 的運作方式是在瀏覽器上設定永續性 cookie。 系統不會再次提示使用者從該瀏覽器輸入 MFA，直到 cookie 到期為止。 如果使用者在相同的裝置上開啟不同的瀏覽器或清除其 cookie，系統會提示他們再次確認。 

非瀏覽器應用程式上不會顯示「**X**天內不要再問我」核取方塊，不論是否支援數據機驗證。 這些應用程式使用每隔一小時會提供新存取權杖的重新整理權杖。 當已驗證重新整理權杖時，Azure AD 會檢查最後一次執行雙步驟驗證是否在已設定天數內。 

因此，在受信任的裝置上記住 MFA 會減少 web 應用程式 (通常每一次都會提示) 的驗證次數，但是會增加現代化驗證用戶端 (通常每 90 天會提示) 的驗證次數。

> [!NOTE]
>當使用者透過 Azure MFA Server 或第三方 MFA 解決方案執行雙步驟驗證時，這項功能無法與 AD FS 的「讓我保持登入」功能相容。 如果您的使用者選取 AD FS 上的「讓我保持登入」，並將其裝置標示為受 MFA 信任，他們在「記住 MFA」天數到期後將無法確認。 Azure AD 要求重新整理雙步驟驗證，但是 AD FS 會傳回包含原始 MFA 宣告及日期的權杖，而不是再次執行雙步驟驗證。 這會啟動 Azure AD 與 AD FS 之間的驗證迴圈。 

### <a name="enable-remember-multi-factor-authentication"></a>啟用記住 Multi-Factor Authentication
1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 依照本文開頭的指示，瀏覽至 [MFA 服務設定] 頁面。
3. 在 [服務設定] 頁面的管理使用者裝置設定下方，勾選 [允許使用者在其信任的裝置記住多重要素驗證]。
   ![記住裝置](./media/multi-factor-authentication-whats-next/remember.png)
4. 設定您想要允許受信任的裝置略過雙步驟驗證的天數。 預設值為 14 天。
5. 按一下 [儲存] 。
6. 按一下 [關閉] 。

### <a name="mark-a-device-as-trusted"></a>將裝置標示為受信任

一旦您啟用此功能，使用者可以在登入時勾選 [不再詢問]，將裝置標示為受信任。

![不再詢問 - 螢幕擷取畫面](./media/multi-factor-authentication-whats-next/trusted.png)

## <a name="selectable-verification-methods"></a>可選取的驗證方法
您可以選擇可供使用者使用的驗證方法。 下表提供每種方法的簡短概觀。

使用者將其帳戶註冊進行 MFA 時，會在您已啟用的選項中選擇慣用驗證方法。 [對我的帳戶進行雙步驟驗證設定](multi-factor-authentication-end-user-first-time.md)涵蓋其註冊程序的指引。

| 方法 | 說明 |
|:--- |:--- |
| 電話通話 |撥打自動語音電話。 使用者可接聽電話並按電話鍵盤上的 # 進行驗證。 此電話號碼將不會同步到內部部署 Active Directory。 |
| 電話簡訊 |傳送包含驗證碼的簡訊。 系統會提示使用者使用驗證碼來回覆簡訊，或在登入介面中輸入驗證碼。 |
| 行動應用程式的通知 |將推播通知傳送至您的電話或已註冊的裝置。 使用者會看到通知，然後選取 [驗證] 來完成驗證。 <br>Microsoft 驗證器應用程式適用於 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072) 和 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073)。 |
| 行動應用程式傳回的驗證碼 |Microsoft Authenticator 應用程式每隔 30 秒會產生新的 OATH 驗證碼。 使用者會在登入介面中輸入這個驗證碼。<br>Microsoft 驗證器應用程式適用於 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072) 和 [IOS](http://go.microsoft.com/fwlink/?Linkid=825073)。 |

### <a name="how-to-enabledisable-authentication-methods"></a>如何啟用/停用驗證方法
1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 依照本文開頭的指示，瀏覽至 [MFA 服務設定] 頁面。
3. 在 [服務設定] 頁面的驗證選項下，選取/取消選取您想要使用的選項。
   ![驗證選項](./media/multi-factor-authentication-whats-next/authmethods.png)
4. 按一下 [儲存] 。
5. 按一下 [關閉] 。


