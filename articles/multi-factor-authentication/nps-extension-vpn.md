---
title: "使用 NPS 擴充功能來整合 VPN 與 Azure MFA | Microsoft Docs"
description: "本文將探討如何使用 Microsoft Azure 的網路原則伺服器 (NPS) 擴充功能來整合您的 VPN 基礎結構與 Azure MFA。"
services: active-directory
keywords: "Azure MFA, 整合 VPN, Azure Active Directory, 網路原則伺服器擴充功能"
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: kgremban
ms.reviewer: jsnow
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: 3dfcf25856ede50266336c2ebb057dd3f7b8897e
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---

# <a name="integrate-your-vpn-infrastructure-with-azure-multi-factor-authentication-mfa-using-the-network-policy-server-nps-extension-for-azure"></a>使用 Azure 的網路原則伺服器 (NPS) 擴充功能來整合您的 VPN 基礎結構與 Azure Multi-Factor Authentication (MFA)

## <a name="overview"></a>概觀

Azure 的網路原則服務 (NPS) 擴充功能可讓組織使用以雲端為基礎的 [Azure Multi-Factor Authentication (MFA)](multi-factor-authentication-get-started-server-rdg.md) (可提供雙步驟驗證) 來保護遠端驗證撥入使用者服務 (RADIUS) 用戶端驗證。

本文會提供指示，讓您知道如何使用 Azure 的 NPS 擴充功能來整合 NPS 基礎結構與 Azure MFA，以便為嘗試使用 VPN 連線到您的網路的使用者能夠安全地進行雙步驟驗證。 

網路原則與存取服務 (NPS) 能為組織提供下列功能：

* 指定中央位置以供管理和控制網路要求，以指出誰可以連線、每天的哪些時段允許連線、連線的持續時間，以及用戶端在連線時必須使用的安全性層級等等。 組織不必在每個 VPN 或遠端桌面 (RD) 閘道伺服器上指定這些原則，而是在中央位置一次指定這些原則。 系統會使用 RADIUS 通訊協定來提供集中式的驗證、授權和計量 (AAA)。 
* 建立並強制執行網路存取保護 (NAP) 用戶端健康原則，以判斷要讓裝置不受限制還是受限制地存取網路資源。
* 提供強制驗證和授權的方法，以供存取具有 802.1x 功能的無線存取點及乙太網路交換器。    

如需詳細資訊，請參閱[網路原則伺服器 (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)。 

為了加強安全性並提供高水準的合規性，組織可以整合 NPS 與 Azure MFA 來確保使用雙步驟驗證的使用者能夠連線到 VPN 伺服器上的虛擬連接埠。 使用者若要獲得存取權，就必須提供其使用者名稱/密碼的組合以及使用者所掌握的資訊。 這項資訊必須能夠讓人信任且無法輕易複製，例如行動電話號碼、室內電話號碼、行動裝置上的應用程式等等。

在 Azure 的 NPS 擴充功能推出前，想要對整合式的 NPS 與 Azure MFA 環境實作雙步驟驗證的客戶，必須在內部部署環境中另外設定及維護一個 MFA Server，如＜使用 RADIUS 的遠端桌面閘道和 Azure Multi-Factor Authentication Server＞所述。

隨著 Azure 的 NPS 擴充功能推出，組織現在可以選擇要部署內部部署型 MFA 解決方案還是雲端型 MFA 解決方案，以保護 RADIUS 用戶端驗證。
 
## <a name="authentication-flow"></a>驗證流程
使用者在連線到 VPN 伺服器上的虛擬連接埠時，必須先使用不同的通訊協定來進行驗證，而組織可以合併使用使用者名稱/密碼和憑證型驗證方法來建立此驗證機制。 

除了驗證和確認身分識別外，使用者還必須擁有適當的撥入權限。 在簡單的實作中，這些可提供存取權的撥入權限會直接設定於 Active Directory 使用者物件上。 

 ![使用者屬性](./media/nps-extension-vpn/image1.png)

在簡單的實作中，每一部 VPN 伺服器都會根據每個本機 VPN 伺服器上所定義的原則來授與或拒絕存取權。

而在擴充性更高的較大型實作中，負責授與或拒絕 VPN 存取權的原則則集中在 RADIUS 伺服器上。 在此情況下，VPN 伺服器會擔任存取伺服器 (RADIUS 用戶端)，以將連線要求和帳戶訊息轉寄給 RADIUS 伺服器。 若要連線到 VPN 伺服器上的虛擬連接埠，使用者必須先通過驗證，並符合 RADIUS 伺服器上所集中定義的條件。 

當 Azure 的 NPS 擴充功能與 NPS 整合時，成功的驗證流程如下所示：

1. VPN 伺服器收到來自 VPN 使用者的驗證要求，其中包含用來連線到某項資源 (例如，遠端桌面工作階段) 的使用者名稱和密碼。 
2. VPN 伺服器作為 RADIUS 用戶端，以將要求轉換為 RADIUS Access-Request 訊息，並將此訊息 (密碼會經過加密) 傳送至 NPS 擴充功能安裝所在的 RADIUS (NPS) 伺服器。 
3. 使用者名稱和密碼組合在 Active Directory 中進行驗證。 如果使用者名稱/密碼不正確，RADIUS 伺服器會傳送 Access-Reject 訊息。 
4. 如果 NPS 連線要求與網路原則中所指定的所有條件 (例如，一天當中的時間或群組成員資格限制) 皆能符合，NPS 擴充功能就會觸發要求，以便使用 Azure MFA 進行第二項驗證。 
5. Azure MFA 會與 Azure Active Directory 通訊、擷取使用者的詳細資料，並使用使用者所設定的方法執行第二項驗證 (簡訊、行動裝置應用程式等等)。 
6. 成功通過 MFA 挑戰後，Azure MFA 會將結果告知 NPS 擴充功能。
7. 當連線嘗試通過驗證並獲得授權後，擴充功能安裝所在的 NPS 伺服器就會傳送 RADIUS Access-Accept 訊息給 VPN 伺服器 (RADIUS 用戶端)。
8. 使用者會獲得 VPN 伺服器上之虛擬連接埠的存取權，並建立加密的 VPN 通道。

## <a name="prerequisites"></a>必要條件
本節會詳述在整合 Azure MFA 與遠端桌面閘道之前所需具備的必要條件。 開始之前，您必須先具備下列必要條件。

* VPN 基礎結構
* 網路原則與存取服務 (NPS) 角色
* Azure MFA 授權
* Windows Server 軟體
* 程式庫
* 與內部部署 AD 同步的 Azure AD 
* Azure Active Directory GUID 識別碼

### <a name="vpn-infrastructure"></a>VPN 基礎結構
本文假設您已有使用 Microsoft Windows Server 2016 的運作中 VPN 基礎結構，而且 VPN 伺服器目前尚未設定為將連線要求轉寄給 RADIUS 伺服器。 在本指南中，您要將 VPN 基礎結構設定為使用中央 RADIUS 伺服器。

如果您沒有已在運作的基礎結構，您可以按照眾多 VPN 設定教學課程 (可於 Microsoft 和第三方廠商的網站上找到) 所提供的指導方針快速建立這個基礎結構。 

### <a name="network-policy-and-access-services-nps-role"></a>網路原則與存取服務 (NPS) 角色

NPS 角色服務可提供 RADIUS 伺服器和用戶端功能。 本文假設您已在環境中的成員伺服器或網域控制站上安裝 NPS 角色。 在本指南中，您要設定 RADIUS 以進行 VPN 設定。 在與您的 VPN 伺服器不同的「其他」伺服器上安裝 NPS 角色。

如需有關安裝 NPS 角色服務 Windows Server 2012 或更新版本的資訊，請參閱[安裝 NAP 健康原則伺服器](https://technet.microsoft.com/library/dd296890.aspx)。 Windows Server 2016 已淘汰網路存取原則 (NAP)。 如需 NPS 最佳做法的說明 (包括在網域控制站上安裝 NPS 的建議)，請參閱 [NPS 的最佳做法](https://technet.microsoft.com/library/cc771746)。

### <a name="licenses"></a>授權

需要 Azure MFA 的授權，此授權可透過 Azure AD Premium、Enterprise Mobility plus Security (EMS) 或 MFA 訂用帳戶來獲得。 如需詳細資訊，請參閱[如何取得 Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md)。 若要進行測試，您可以使用試用版訂用帳戶。

### <a name="software"></a>軟體

NPS 擴充功能需要安裝了 NPS 角色服務的 Windows Server 2008 R2 SP1 或更新版本。 本指南中的所有步驟皆是使用 Windows Server 2016 來執行的。

### <a name="libraries"></a>程式庫

需要有下列兩個程式庫：

* [適用於 Visual Studio 2013 的 Visual C++ 可轉散發套件 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
* 適用於 Windows PowerShell 1.1.166.0 版或更新版本的 Microsoft Azure Active Directory 模組。 如需最新版本和安裝的指示，請參閱 [Microsoft Azure Active Directory PowerShell 模組版本發行歷程記錄](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx)。

儘管現有文件另有說明，但這些程式庫並未封裝在 NPS 擴充功能安裝程式檔案 (0.9.1.2 版) 中。 您最少必須安裝適用於 Visual Studio 2013 的 Visual C++ 可轉散發套件。 如果您還沒有適用於 Windows PowerShell 的 Microsoft Azure Active Directory 模組，系統會透過您在安裝過程中執行的設定指令碼來加以安裝。 因此，如果您尚未安裝此模組，就不必事先安裝。

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>與內部部署 Active Directory 同步的 Azure Active Directory 

若要使用 NPS 擴充功能，內部部署使用者必須與 Azure Active Directory 保持同步並啟用 Multi-Factor Authentication。 本指南假設內部部署使用者已使用 AD Connect 來與 Azure Active Directory 保持同步。 下面會提供為使用者啟用 MFA 的指示。
如需 Azure AD Connect 的相關資訊，請參閱[整合您的內部部署目錄與 Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md)。 

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID 識別碼 
若要安裝 NPS，您必須知道 Azure Active Directory 的 GUID。 下一節會提供尋找 Azure Active Directory GUID 的指示。

## <a name="configure-radius-for-vpn-connections"></a>設定 RADIUS 的 VPN 連線

如果您已在成員伺服器上安裝 NPS 伺服器角色，您必須進行設定以驗證和授權提出 VPN 連線要求的 VPN 用戶端。 

本節假設您已安裝網路原則伺服器角色，但尚未將它設定用於您的基礎結構中。

>[!NOTE]
>如果您已擁有運作中的 VPN 伺服器，且使用集中式的 RADIUS 伺服器來進行驗證，則可以略過本節。
>

### <a name="register-server-in-active-directory"></a>在 Active Directory 中註冊伺服器
若要讓 NPS 伺服器在本案例中正常運作，您必須在 Active Directory 中加以註冊。

1. 開啟 [伺服器管理員]。
2. 在 [伺服器管理員] 中按一下 [工具]，然後按一下 [網路原則伺服器]。 
3. 在 [網路原則伺服器] 主控台中，以滑鼠右鍵按一下 [NPS (本機)]，然後按一下 [在 Active Directory 中註冊伺服器]。 按 [確定] 兩次。

 ![網路原則伺服器](./media/nps-extension-vpn/image2.png)

4. 讓主控台保持開啟以供下一個程序使用。

### <a name="use-wizard-to-configure-radius-server"></a>使用精靈來設定 RADIUS 伺服器
您可以使用標準 (精靈式) 或進階的設定選項來設定 RADIUS 伺服器。 本節假設您使用精靈式的標準設定選項。

1. 在 [網路原則伺服器] 主控台中，按一下 [NPS (本機)]。
2. 選取 [標準設定] 底下的 [撥號或 VPN 連線的 RADIUS 伺服器]，然後按一下 [設定 VPN 或撥號]。

 ![設定 VPN](./media/nps-extension-vpn/image3.png)

3. 在 [選取撥號或虛擬私人網路連線類型] 頁面上選取 [虛擬私人網路連線]，然後按 [下一步]。

 ![虛擬私人網路](./media/nps-extension-vpn/image4.png)

4. 在 [指定撥號或 VPN 伺服器] 頁面上按一下 [新增]。
5. 在 [新增 RADIUS 用戶端] 對話方塊中，提供好記的名稱，輸入可解析的 VPN 伺服器名稱或 IP 位址，並輸入共用祕密。 請為此共用祕密使用較長且複雜的形式。 記錄此密碼，因為您需要在下一節的步驟中用到。

 ![新增 RADIUS 用戶端](./media/nps-extension-vpn/image5.png)

6. 按一下 [確定]，然後按 [下一步]。
7. 在 [設定驗證方法] 頁面上，接受預設的選取項目 (Microsoft 加密驗證第 2 版 (MS-CHAPv2) 或選擇另一個選項，然後按 [下一步]。

  >[!NOTE]
  >如果您設定可延伸的驗證通訊協定 (EAP)，則必須使用 MS CHAPv2 或 PEAP。 其他 EAP 皆不受支援。
 
8. 在 [指定使用者群組] 頁面上按一下 [新增]，然後選取適當的群組 (如果有的話)。 否則，請保持空白選取狀態，以對所有使用者授與存取權。

 ![指定使用者群組](./media/nps-extension-vpn/image7.png)

9. 按一下 [下一步] 。
10. 在 [指定 IP 篩選器] 頁面上按 [下一步]。
11. 在 [指定加密設定] 頁面上接受預設設定，然後按 [下一步]。

 ![指定加密](./media/nps-extension-vpn/image8.png)

12. 在 [指定領域名稱] 上讓名稱保持空白，接受預設設定，然後按 [下一步]。

 ![指定領域名稱](./media/nps-extension-vpn/image9.png)

13. 在 [完成新的撥號或虛擬私人網路連線和 RADIUS 用戶端] 頁面上，按一下 [完成]。

 ![完成連線](./media/nps-extension-vpn/image10.png)

### <a name="verify-radius-configuration"></a>確認 RADIUS 設定
本節會詳述您使用精靈所建立的設定。

1. 在 NPS 伺服器的 [NPS (本機)] 主控台中展開 [RADIUS 用戶端]，然後選取 [RADIUS 用戶端]。
2. 在詳細資料窗格中，以滑鼠右鍵按一下您使用精靈所建立的 RADIUS 用戶端，然後按一下 [屬性]。 RADIUS 用戶端 (VPN 伺服器) 的屬性應該類似如下所示。

 ![VPN 屬性](./media/nps-extension-vpn/image11.png)

3. 按一下 [取消]。
4. 在 NPS 伺服器的 [NPS (本機)] 主控台中展開 [原則]，然後選取 [連線要求原則]。 您應該會看到類似下圖的 VPN 連線原則。

 ![連線要求](./media/nps-extension-vpn/image12.png)

5. 在 [原則] 底下選取 [網路原則]。 您應該會看到類似下圖的虛擬私人網路 (VPN) 連線原則。

 ![網路屬性](./media/nps-extension-vpn/image13.png)

## <a name="configure-vpn-server-to-use-radius-authentication"></a>將 VPN 伺服器設定為使用 RADIUS 驗證
在本節中，您要將 VPN 伺服器設定為使用 RADIUS 驗證。 本節假設您已有運作中的 VPN 伺服器設定，但尚未將 VPN 伺服器設定為使用 RADIUS 驗證。 在設定 VPN 伺服器之後，您要確認您的設定是否如預期般運作。

>[!NOTE]
>如果您已擁有運作中的 VPN 伺服器設定，且該設定使用 RADIUS 驗證，則可以略過本節。
>

### <a name="configure-authentication-provider"></a>設定驗證提供者
1. 在 VPN 伺服器上，開啟 [伺服器管理員]。
2. 在 [伺服器管理員] 中按一下 [工具]，然後按一下 [路由及遠端存取]。
3. 在 [路由及遠端存取] 主控台中，以滑鼠右鍵按一下 [\[伺服器名稱\] (本機)]，然後按一下 [屬性]。

 ![路由及遠端存取](./media/nps-extension-vpn/image14.png)
 
4. 在 [伺服器名稱} (本機) 屬性] 對話方塊中，按一下 [安全性] 索引標籤。 
5. 在 [安全性] 索引標籤的 [驗證提供者] 底下按一下 [RADIUS 驗證]，然後按一下 [設定]。

 ![RADIUS 驗證](./media/nps-extension-vpn/image15.png)
 
6. 在 [RADIUS 驗證] 對話方塊中按一下 [新增]。
7. 在 [新增 RADIUS 伺服器] 的 [伺服器名稱] 中新增您在上一節所設定之 RADIUS 伺服器的名稱或 IP 位址。
8. 在 [共用祕密] 中按一下 [變更]，然後新增您稍早所建立並記錄下來的共用祕密。
9. 將 [逾時 (秒)] 中的值變更為 **30** 到 **60** 之間的數字。 您必須這樣設定才能讓第二個驗證要素有足夠的時間來完成。
 
 ![新增 RADIUS 伺服器](./media/nps-extension-vpn/image16.png)
 
10. 持續按 [確定]直到您徹底關閉所有對話方塊。

### <a name="test-vpn-connectivity"></a>測試 VPN 連線能力
在本節中，您要確認在您嘗試連線到 VPN 虛擬連接埠時，VPN 用戶端已通過 RADIUS 伺服器的驗證並獲得授權。 本節假設您使用 Windows 10 來作為 VPN 用戶端。 

>[!NOTE]
>如果您已將 VPN 用戶端設定為連線到 VPN 伺服器並已儲存各項設定，您可以略過與設定和儲存 VPN 連線物件有關的步驟。
>

1. 在 VPN 用戶端電腦上按一下 [啟動]，然後按一下 [設定] \(齒輪圖示)。
2. 在 [視窗設定] 中按一下 [網路和網際網路]。
3. 按一下 [VPN]。
4. 按一下 [新增 VPN 連線]。
5. 在 [新增 VPN 連線] 中指定以 [Windows (內建)] 作為 [VPN 提供者]，接著視情況完成剩餘欄位，然後按一下 [儲存]。 

 ![新增 VPN 連線](./media/nps-extension-vpn/image17.png)
 
6. 在 [控制台] 中開啟 [網路和共用中心]。
7. 按一下 [變更介面卡設定]。

 ![變更介面卡設定](./media/nps-extension-vpn/image18.png)

8. 以滑鼠右鍵按一下 VPN 網路連線，然後按一下 [屬性]。 

 ![VPN 網路屬性](./media/nps-extension-vpn/image19.png)

9. 在 [VPN 屬性] 對話方塊中按一下 [安全性] 索引標籤。 
10. 確定 [安全性] 索引標籤上只選取了 [Microsoft CHAP 第 2 版 (MS-CHAP v2)]，然後按一下 [確定]。

 ![允許通訊協定](./media/nps-extension-vpn/image20.png)

11. 以滑鼠右鍵按一下 VPN 連線，然後按一下 [連線]。
12. 在 [設定] 頁面上按一下 [連線]。

如下所示，在 RADIUS 伺服器的安全性記錄中，事件識別碼 6272 會顯示連線成功。

 ![事件屬性](./media/nps-extension-vpn/image21.png)

## <a name="troubleshoot-guide"></a>疑難排解指南
假設在您將 VPN 伺服器設定為使用集中式的 RADIUS 伺服器來進行驗證和授權之前，您的 VPN 設定就已在運作。 在此情況下，問題很可能是 RADIUS 伺服器的設定不正確或使用了無效的使用者名稱或密碼所導致。 例如，如果您在使用者名稱中使用替代的 UPN 尾碼，則登入嘗試可能會失敗 (您應該使用相同的帳戶名稱，以便獲得最佳結果)。 

若要針對這些問題進行疑難排解，最好是從檢查 RADIUS 伺服器上的安全性事件記錄來著手。 若要節省花在搜尋事件的時間，您可以在事件檢視器中使用以角色為基礎的 [網路原則與存取伺服器] 自訂檢視，如下所示。 事件識別碼 6273 指出網路原則伺服器拒絕使用者存取的事件。 

 ![事件檢視器](./media/nps-extension-vpn/image22.png)
 
## <a name="configure-multi-factor-authentication"></a>設定 Multi-Factor Authentication
本節提供為使用者啟用 MFA 以及為帳戶設定雙步驟驗證的指示。 

### <a name="enable-multi-factor-authentication"></a>啟用 Multi-Factor Authentication
在本節中，您將為 Azure AD 帳戶啟用 MFA。 使用**傳統入口網站**來為使用者啟用 MFA。 

1. 開啟瀏覽器，並瀏覽至 [https://manage.windowsazure.com](https://manage.windowsazure.com)。 
2. 以系統管理員身分登入。
3. 在入口網站中，按一下左方瀏覽窗格的 [ACTIVE DIRECTORY]。

 ![預設目錄](./media/nps-extension-vpn/image23.png)

4. 在 [名稱] 資料行中按一下 [預設目錄] \(或其他目錄，視情況而定)。
5. 在 [快速入門] 頁面上，按一下 [設定]。

 ![設定預設值](./media/nps-extension-vpn/image24.png)

6. 在 [設定] 頁面上向下捲動，然後在 [多重要素驗證] 區段中，按一下 [管理服務設定]。

 ![管理 MFA 設定](./media/nps-extension-vpn/image25.png)
 
7. 在 [多重要素驗證] 頁面上檢閱預設的服務設定，然後按一下 [使用者]。 

 ![MFA 使用者](./media/nps-extension-vpn/image26.png)
 
8. 在 [使用者] 頁面上選取您想要啟用 MFA 的使用者，然後按一下 [啟用]。

 ![屬性](./media/nps-extension-vpn/image27.png)
 
9. 出現提示時，按一下 [啟用多重要素驗證]。

 ![啟用 MFA](./media/nps-extension-vpn/image28.png)
 
10. 按一下 [關閉] 。 
11. 重新整理頁面。 MFA 狀態就會變更為 [已啟用]。

如需如何為使用者啟用 Multi-Factor Authentication 的相關資訊，請參閱[開始在雲端中使用 Azure Multi-Factor Authentication](multi-factor-authentication-get-started-cloud.md)。 

### <a name="configure-accounts-for-two-step-verification"></a>為帳戶設定雙步驟驗證
在為帳戶啟用 MFA 之後，使用者就無法登入 MFA 原則所控管的資源，除非他們能成功地設定受信任的裝置來作為使用雙步驟驗證的第二個驗證要素。

在本節中，您要設定受信任的裝置以供雙步驟驗證使用。 有數個選項可供您進行這些設定，包括下列選項：

* **行動裝置應用程式**。 您要在 Windows Phone、Android 或 iOS 裝置上安裝 Microsoft Authenticator 應用程式。 根據您組織的原則，您必須以兩種模式的其中一種來使用此應用程式：接收驗證通知 (系統會對您的裝置推送通知) 或使用驗證碼 (您必須輸入驗證碼，此驗證碼每隔 30 秒就會更新)。 
* **行動電話通話或文字**。 您會收到自動發出的來電或簡訊。 若是來電選項，您要接聽電話並按 # 字號來進行驗證。 若是簡訊選項，您可以回覆簡訊或在登入介面中輸入驗證碼。
* **辦公室電話通話**。 此程序和上述的自動來電程序的說明相同。

請按照這些指示來設定裝置，以使用行動裝置應用程式來接收用於驗證的推播通知。

1. 登入 [https://aka.ms/mfasetup](https://aka.ms/mfasetup) 或任何必須使用具有 MFA 功能之認證來進行驗證的網站 (例如 [https://portal.azure.com](https://portal.azure.com))。 
2. 在以使用者名稱和密碼登入時，系統會顯示畫面來提示您為帳戶設定其他安全性驗證。

 ![額外的安全性](./media/nps-extension-vpn/image29.png)

3. 按一下 [立即設定]。
4. 在 [其他安全性驗證] 頁面中選取連絡類型 (驗證電話、辦公室電話或行動裝置應用程式)。 然後選取國家或地區並選取方法。 方法會因您所選取的連絡類型而異。 例如，如果您選擇行動裝置應用程式，您可以選取要接收驗證通知還是要使用驗證碼。 以下步驟假設您選擇 [行動裝置應用程式] 來作為連絡類型。

 ![電話驗證](./media/nps-extension-vpn/image30.png)

5. 選取 [行動裝置應用程式]，按一下 [接收驗證通知]，然後按一下 [設定]。 

 ![行動裝置應用程式驗證](./media/nps-extension-vpn/image31.png)
 
6. 如果您尚未在裝置上安裝 Authenticator 行動裝置應用程式，請予以安裝。 
7. 按照行動裝置應用程式中的指示來掃描顯示的條碼或手動輸入資訊，然後按一下 [完成]。

 ![設定行動裝置應用程式](./media/nps-extension-vpn/image32.png)

8. 在 [其他安全性驗證] 頁面上按一下 [與我連絡]，然後回覆裝置所收到的通知。
9. 如果您失去行動裝置應用程式的存取權，請在 [其他安全性驗證] 頁面上輸入連絡電話，然後按 [下一步]。

 ![行動電話號碼](./media/nps-extension-vpn/image33.png)
 
10. 在 [其他安全性驗證] 上按一下 [完成]。

裝置現已設定為提供第二種驗證方法。 如需為帳戶設定雙步驟驗證的相關資訊，請參閱[對我的帳戶進行雙步驟驗證設定](./end-user/multi-factor-authentication-end-user-first-time.md)。

## <a name="install-and-configure-nps-extension"></a>安裝和設定 NPS 擴充功能

本節會提供指示，以引導您將 VPN 設定為使用 Azure MFA 讓用戶端向 VPN 伺服器進行驗證。

在安裝並設定了 NPS 擴充功能後，由此伺服器負責處理的所有 RADIUS 型用戶端驗證都必須使用 Azure MFA。 如果您並未在 Azure MFA 中為您的所有 VPN 使用者完成註冊，您可以另外設定一個 RADIUS 伺服器來對未設定為使用 MFA 的使用者提供驗證程序。 或者，您也可以建立登錄項目，來允許受到挑戰的使用者提供第二個驗證要素，但前提是他們必須已在 MFA 中註冊。 

在 HKLM\SOFTWARE\Microsoft\AzureMfa 中建立名為「REQUIRE_USER_MATCH」的新字串值，然後將該值設為 TRUE 或 FALSE。 

 ![需要使用者比對](./media/nps-extension-vpn/image34.png)
 
如果值設為 TRUE 或未進行設定，則所有驗證要求都由 MFA 挑戰來決定。 如果值設為 FALSE，則只有已在 MFA 中註冊的使用者會收到 MFA 挑戰。 在測試或生產環境中，請只在上架期間使用 FALSE 設定。

### <a name="acquire-azure-active-directory-guid-id"></a>取得 Azure Active Directory GUID 識別碼

在設定 NPS 擴充功能期間，您必須為 Azure AD 租用戶提供管理員認證和 Azure Active Directory 識別碼。 下列步驟會說明如何取得租用戶識別碼。

1. 以 Azure 租用戶的全域管理員身分登入 Azure 入口網站 [https://portal.azure.com](https://portal.azure.com)。
2. 在左方瀏覽窗格中，按一下 [Azure Active Directory] 圖示。
3. 按一下 [內容] 。
4. 若要將目錄識別碼複製到剪貼簿，請選取 [複製] 圖示。
 
 ![目錄識別碼](./media/nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>安裝 NPS 擴充功能
NPS 擴充功能必須安裝於已安裝「網路原則與存取服務 (NPS)」角色，且依設計要作為 RADIUS 伺服器的伺服器上。 請勿將 NPS 擴充功能安裝在您的遠端桌面伺服器上。

1. 從 [https://aka.ms/npsmfa](https://aka.ms/npsmfa) 下載 NPS 擴充功能。 
2. 將安裝程式可執行檔 (NpsExtnForAzureMfaInstaller.exe) 複製到 NPS 伺服器。
3. 在 NPS 伺服器上按兩下 **NpsExtnForAzureMfaInstaller.exe**。 出現提示時，按一下 [執行]。
4. 檢閱 [Azure MFA 的 NPS 擴充功能] 對話方塊中的軟體授權條款，勾選 [我同意授權條款和條件]，然後按一下 [安裝]。

 ![NPS 擴充功能](./media/nps-extension-vpn/image36.png)
 
5. 在 [Azure MFA 的 NPS 擴充功能] 對話方塊中按一下 [關閉]。  

 ![設定成功](./media/nps-extension-vpn/image37.png) 
 
### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>使用 PowerShell 指令碼來設定要用於 NPS 擴充功能的憑證
為確保通訊安全並提供保證，您必須設定要供 NPS 擴充功能使用的憑證。 NPS 元件納入了 Windows PowerShell 指令碼，以設定要用於 NPS 的自我簽署憑證。 

此指令碼會執行下列動作：

* 建立自我簽署憑證
* 讓憑證的公開金鑰與 Azure AD 的服務主體產生關聯
* 將憑證儲存在本機電腦的存放區
* 將憑證的私密金鑰存取權授與給網路使用者
* 重新啟動網路原則伺服器服務

如果您想要使用您自己的憑證，您必須讓該憑證的公開金鑰與 Azure AD 的服務主體產生關聯，依此類推。
若要使用此指令碼，請為擴充功能提供您的 Azure Active Directory 系統管理認證以及您之前複製的 Azure Active Directory 租用戶識別碼。 在安裝了 NPS 擴充功能的每個 NPS 伺服器上執行此指令碼。

1. 開啟系統管理 Windows PowerShell 提示字元。
2. 在 PowerShell 提示字元中輸入 _cd ‘c:\Program Files\Microsoft\AzureMfa\Config’_，然後按 **ENTER**。
3. 輸入 _.\AzureMfsNpsExtnConfigSetup.ps1_，然後按 **ENTER**。 
 * 此指令碼會檢查您是否已安裝 Azure Active Directory PowerShell 模組。 如果尚未安裝此模組，指令碼就會為您安裝。
 
 ![PowerShell](./media/nps-extension-vpn/image38.png)
 
4. 在指令碼確認您已安裝 PowerShell 模組後，它會顯示 [Azure Active Directory PowerShell 模組] 對話方塊。 在對話方塊中，輸入您的 Azure 系統管理認證和密碼，然後按一下 [登入]。 
 
 ![PowerShell 登入](./media/nps-extension-vpn/image39.png)
 
5. 出現提示時，貼上您之前複製到剪貼簿的租用戶識別碼，然後按 **ENTER**。 

 ![租用戶識別碼](./media/nps-extension-vpn/image40.png)

6. 此指令碼會建立自我簽署憑證，並進行其他的設定變更。 其輸出類似下圖所示。

 ![自我簽署憑證](./media/nps-extension-vpn/image41.png)

7. 重新啟動伺服器。
 
### <a name="verify-configuration"></a>驗證組態
若要驗證設定，您必須對 VPN 伺服器建立新的 VPN 連線。 在主要驗證中成功輸入認證時，VPN 連線會先等候次要驗證成功再建立連線，如下所示。 

 ![驗證設定](./media/nps-extension-vpn/image42.png)

如果您已成功地使用您先前在 Azure MFA 中設定的次要驗證方法來進行驗證，您就會連線到資源。 不過，如果次要驗證失敗，系統就會拒絕讓您存取資源。 

在下列範例中，我們使用了 Windows Phone 上的 Authenticator 應用程式來提供次要驗證。

 ![驗證帳戶](./media/nps-extension-vpn/image43.png)

在成功地使用第二種方法進行驗證之後，您就能存取 VPN 伺服器上的虛擬連接埠。 不過，因為您必須使用受信任裝置上的行動裝置應用程式來使用次要驗證方法，登入程序會比僅使用使用者名稱/密碼組合來得更加安全。

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>檢視事件檢視器記錄來找到成功的登入事件
若要檢視 Windows 事件檢視器記錄中的成功登入事件，您可以發出下列 Windows PowerShell 命令來查詢 NPS 伺服器上的 Windows 安全性記錄。

若要查詢安全性事件檢視器記錄中的成功登入事件，請使用下列命令，
* _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL 

 ![安全性事件檢視器](./media/nps-extension-vpn/image44.png)
 
您也可以檢視安全性記錄或網路原則與存取服務自訂檢視，如下所示：

 ![網路原則存取](./media/nps-extension-vpn/image45.png)

在安裝了 Azure MFA NPS 擴充功能的伺服器上，您可以在 **Application and Services Logs\Microsoft\AzureMfa** 找到擴充功能專屬的事件檢視器應用程式記錄。 

* _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL

 ![事件數目](./media/nps-extension-vpn/image46.png)

## <a name="troubleshoot-guide"></a>疑難排解指南
如果設定未如預期般運作，確認使用者是否已設為使用 Azure MFA 是不錯的疑難排解起點。 讓使用者連線到 [https://portal.azure.com](https://portal.azure.com)。如果他們收到次要驗證提示而且可以成功地完成驗證，您就可以排除 Azure MFA 設定不正確的可能性。

如果 Azure MFA 對使用者有效，請檢閱相關的事件記錄。 這些記錄包括安全性事件記錄、閘道運作記錄，以及上一節中討論的 Azure MFA 記錄。 

以下是安全性記錄的輸出範例，其中顯示了失敗登入事件 (事件識別碼 6273)：

 ![安全性記錄](./media/nps-extension-vpn/image47.png)

以下是來自 Azure MFA 記錄的相關事件：

 ![Azure MFA 記錄](./media/nps-extension-vpn/image48.png)

若要執行進階的疑難排解選項，請參閱安裝了 NPS 服務的 NPS 資料庫格式記錄檔。 這些記錄檔會建立於 %SystemRoot%\System32\Logs 資料夾，並以逗號分隔文字檔的形式存在。 如需這些記錄檔的說明，請參閱[解譯 NPS 資料庫格式記錄檔](https://technet.microsoft.com/library/cc771748.aspx)。 

這些記錄檔中的項目若不匯入到試算表或資料庫將難以解譯。 您可以在線上找到許多 IAS 剖析器來協助您解譯記錄檔。 以下是其中一個這類可下載[共享軟體應用程式](http://www.deepsoftware.com/iasviewer)的輸出： 

 ![共享軟體應用程式](./media/nps-extension-vpn/image49.png)

最後，如需其他疑難排解選項，您可以使用通訊協定分析器，例如 Wireshark 或 [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)。 下圖來自 Wireshark，會顯示 VPN 伺服器與 NPS 伺服器之間的 RADIUS 訊息。

 ![Microsoft Message Analyzer](./media/nps-extension-vpn/image50.png)

如需詳細資訊，請參閱[將現有的 NPS 基礎結構與 Azure Multi-Factor Authentication 整合](multi-factor-authentication-nps-extension.md)。  

## <a name="next-steps"></a>後續步驟
[如何取得 Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md)

[使用 RADIUS 的遠端桌面閘道和 Azure Multi-Factor Authentication Server](multi-factor-authentication-get-started-server-rdg.md)

[整合您的內部部署目錄與 Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md)


