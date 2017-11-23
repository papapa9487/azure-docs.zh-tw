---
title: "Azure 中 IaaS 工作負載的安全性最佳作法 | Microsoft Docs"
description: " 將工作負載移轉至 Azure IaaS 可帶來重新評估設計的機會 "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: barclayn
ms.openlocfilehash: 376a3e47e5099aa4d74732e0b6ed14ed9af14091
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/22/2017
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Azure 中 IaaS 工作負載的安全性最佳作法

當您開始思考將工作負載移至 Azure 基礎結構即服務 (IaaS) 時，您可能會領悟到有些考量很熟悉。 您可能已經體驗虛擬環境保護。 當您移至 Azure IaaS 時，您可以運用您在保護虛擬環境方面的專業知識，並使用一組新選項來協助您保護資產。

我們先假設我們不應該預期以一對一的方式將內部部署資源引進 Azure。 新的挑戰與新的選項帶來了重新評估現有設計、工具和程序的機會。

您的安全性責任是根據雲端服務的類型。 下表摘要說明 Microsoft 與您所擔負之責任的平衡：


![責任範圍](./media/azure-security-iaas/sec-cloudstack-new.png)


我們將討論 Azure 中一些可用的選項，以協助您符合組織的安全性需求。 請記住，不同類型的工作負載會有不同的安全性需求。 任何其中一個最佳作法均無法獨自保護您的系統。 如同任何其他安全性項目，您必須選擇適當的選項，以及了解解決方案如何藉由滿足不足之處來彼此互補。

## <a name="use-privileged-access-workstations"></a>使用特殊權限存取工作站

組織往往因為系統管理員在使用具提高權限的帳戶時執行動作，而成為網路攻擊的受害者。 這通常不是惡意行為，而是因為現有的組態和程序允許這麼做。 大部分使用者可從概念性觀點了解這些動作的風險，但仍然選擇這麼做。

檢查電子郵件和瀏覽網際網路等作業看似無害。 但是它們可能會公開提升權限的帳戶，讓惡意執行者入侵。 瀏覽活動、特製的電子郵件或其他技術可以用來存取您的企業。 我們強烈建議使用安全管理工作站 (SAW) 來進行所有的 Azure 系統管理工作。 SAW 是減少遭到意外入侵的方式。

特殊權限存取工作站 (PAW) 提供敏感性工作的專用作業系統，此系統可免於遭受網際網路攻擊和威脅載體攻擊。 區隔每日使用工作站和裝置之間的這些機密工作和帳戶，提供增強式保護。 這個區隔會限制釣魚攻擊、應用程式和 OS 弱點、各種模擬攻擊和認證竊取攻擊的影響。 (按鍵輸入記錄、傳遞雜湊與傳遞票證)

PAW 方法是完善且建議之作法的延伸，可使用個別指派的系統管理帳戶。 系統管理帳戶與標準使用者帳戶不同。 PAW 會為這些敏感性帳戶提供可靠的工作站。

如需詳細資訊和實作指引，請參閱[特殊權限存取工作站](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations)。

## <a name="use-multi-factor-authentication"></a>使用 Multi-Factor Authentication

在過去，您的網路周邊用於控制公司資料的存取。 在雲端優先、行動優先的世界裡，身分識別是控制層︰您可使用它來控制從任何裝置存取 IaaS 服務。 您也可使用它來清楚看見及深入了解您的資料使用位置和方式。 保護 Azure 使用者的數位身分識別，就是保護訂用帳戶免於遭受身分盜用和其他網路犯罪的基石。

保護帳戶時，您可採取之最有幫助的步驟之一就是啟用雙因素驗證。 雙因素驗證是不只使用密碼的驗證方法。 它有助於緩和設法取得他人密碼的人員所造成的存取風險。

[Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) 有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。 它可以透過一些簡單的驗證選項 (例如電話、文字訊息，或行動應用程式驗證) 來提供強大的驗證功能。 使用者可選擇其慣用的方法。

使用 Multi-Factor Authentication 的最簡單方式是 Microsoft Authenticator 行動應用程式，該應用程式可用於執行 Windows、iOS 和 Android 的行動裝置。 使用最新版的 Windows 10 和內部部署 Active Directory 與 Azure Active Directory (Azure AD) 的整合，[Windows Hello 企業版](../active-directory/active-directory-azureadjoin-passport-deployment.md)即可完美地用於單一登入 Azure 資源。 在此情況下，Windows 10 裝置會作為第二個驗證因素。

對於管理 Azure 訂用帳戶的帳戶以及可以登入虛擬機器的帳戶，使用 Multi-Factor Authentication 可讓您擁有比只是使用密碼更高層級的安全性。 其他形式的雙因素驗證或許也有用，但如果這些形式還沒有在生產環境中，其部署可能會很複雜。

下列螢幕擷取畫面顯示一些適用於 Azure Multi-Factor Authentication 的選項：

![Multi-Factor Authentication 選項](./media/azure-security-iaas/mfa-options.png)

## <a name="limit-and-constrain-administrative-access"></a>限制系統管理存取權

保護可管理 Azure 訂用帳戶的帳戶極為重要。 這類帳戶若遭到入侵，就否定您為了確保資料機密性和完整性而可能採取之其他所有步驟的價值。 如 [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden) 最近所述，內部攻擊會對任何組織的整體安全性造成巨大威脅。

遵循類似下面的準則來評估個人的系統管理權限︰

- 是否正在執行需要系統管理權限的工作？
- 工作的執行頻率為何？
- 是否有另一個系統管理員無法代表他們執行工作的特定原因？

記錄所有其他授與權限的已知替代方法，以及無法接受每種方法的原因。

使用即時管理可防止在不需要提高權限的期間內，不必要存在具有提高權限的帳戶。 帳戶在有限的時間內具有提高的權限，可讓系統管理員執行其作業。 然後，在輪班結束或工作完成時移除這些權限。

您可以使用 [Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md) 來管理、監視和控制您組織中的存取權。 它可協助您隨時留意貴組織內個人所採取的動作。 它也可採用合格系統管理員的概念，將即時系統管理引進 Azure AD。 這些是具有帳戶且可能被授與系統管理員權限的人員。 這些類型的使用者可以完成啟動程序，並且在有限的時間內被授與系統管理員權限。


## <a name="use-devtest-labs"></a>使用 DevTest Labs

將 Azure 使用於實驗室和開發環境，可讓組織排除硬體採購所造成的延遲，進而獲得測試和開發靈活度。 不幸的是，不熟悉 Azure 或渴望協助促進其採用，可能會導致系統管理員的權限指派過度寬鬆。 此風險可能會不小心讓組織暴露於內部攻擊。 有些使用者可能會被授與超出其應該擁有的存取權。

[Azure DevTest Labs](../devtest-lab/devtest-lab-overview.md) 服務會使用 [Azure 角色型存取控制](../active-directory/role-based-access-control-what-is.md) (RBAC)。 透過使用 RBAC，您可以將小組內的職責區隔為各種角色，而僅授與使用者執行其作業所需的存取層級。 RBAC 隨附預先定義的角色 (擁有者、實驗室使用者和參與者)。 您甚至可以使用這些角色來指派權限給外部合作夥伴，大幅簡化共同作業。

由於 DevTest Labs 使用 RBAC，所以可能建立其他[自訂角色](../devtest-lab/devtest-lab-grant-user-permissions-to-specific-lab-policies.md)。 DevTest Labs 不僅可簡化權限管理，也能夠簡化佈建環境的程序。 它還能協助您處理小組在開發與測試環境上所面臨的其他典型挑戰。 這需要一些準備工作，但長期來看，它會讓您的小組更輕鬆地作業。

Azure DevTest Labs 的功能包括︰

- 管理控制使用者可用的選項。 系統管理員可以集中管理允許的 VM 大小、VM 數目上限，以及何時啟動和關閉 VM。
- 自動建立實驗室環境。
- 成本追蹤。
- 針對暫時性共同工作簡化 VM 的散發。
- 自助式服務可讓使用者利用範本佈建自己的實驗室。
- 管理和限制取用。

![使用 DevTest Labs 建立實驗室](./media/azure-security-iaas/devtestlabs.png)

使用 DevTest Labs 沒有額外的相關成本。 建立實驗室、原則、範本和構件是免費的。 您只需要針對在實驗室內使用到的 Azure 資源 (例如虛擬機器、儲存體帳戶和虛擬網路) 支付費用。



## <a name="control-and-limit-endpoint-access"></a>控制和限制端點存取

在 Azure 中裝載實驗室或生產系統，表示必須能夠從網際網路存取您的系統。 根據預設，新 Windows 虛擬機器的 RDP 連接埠可經由網際網路存取，而 Linux 虛擬機器已開放 SSH 連接埠。 必須採取相關步驟來限制公開的端點，以將未經授權存取的風險降至最低。

Azure 中的技術可協助您限制這些系統管理端點的存取。 在 Azure 中，您可以使用[網路安全性群組](../virtual-network/virtual-networks-nsg.md) (NSG)。 當您使用 Azure Resource Manager 進行部署時，NSG 會將來自所有網路的存取侷限於管理端點 (RDP 或 SSH)。 當您想到 NSG 時，您就會想到路由器 ACL。 您可以使用它們來嚴格控制 Azure 網路的各種區段之間的網路通訊。 這類似於在周邊網路或其他隔離的網路中建立網路。 它們不會檢查流量，但有助於與網路分割。


在 Azure 中，您可以設定從內部部署網路進行的[網站間 VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)。 網站間 VPN 會將您的內部部署網路擴充至雲端。 這會提供給您使用 NSG 的另一個機會，因為您也可以修改 NSG，不允許從區域網路以外的任何地方進行存取。 然後，您可以要求先透過 VPN 連線到 Azure 網路來進行管理。

當您在 Azure 中裝載與您的內部部署資源緊密整合的生產系統時，網站間 VPN 選項可能最具吸引力。

此外，當您想要管理不需要存取內部部署資源的系統時，可以使用[點對站](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)選項。 這些系統在自己的 Azure 虛擬網路中可能是孤立的。 系統管理員可以透過 VPN 從自己的系統管理工作站連線到 Azure 託管環境。

>[!NOTE]
>您可以使用任何一個 VPN 選項將 NSG 的 ACL 重新設定為不允許從網際網路存取管理端點。

另一個值得考慮的選項是[遠端桌面閘道](../multi-factor-authentication/multi-factor-authentication-get-started-server-rdg.md)部署。 您可以使用此部署，透過 HTTPS 安全地連線到遠端桌面伺服器，同時對這些連線套用更詳細的控制。

您可以存取的功能包括︰

- 系統管理員用來將連線限制於來自特定系統之要求的選項。
- 智慧卡驗證或 Azure Multi-Factor Authentication。
- 控制人員可以透過閘道連接到哪些系統。
- 控制裝置與磁碟重新導向。

## <a name="use-a-key-management-solution"></a>使用金鑰管理解決方案

安全的金鑰管理對於保護雲端資料十分重要。 有了 [Azure 金鑰保存庫](../key-vault/key-vault-whatis.md)，您即可安全地儲存加密金鑰和小型密碼，例如硬體安全性模組 (HSM) 中的密碼。 為了加強保證，您可以在 HSM 中匯入或產生金鑰。

Microsoft 會在進行過 FIPS 140-2 Level 2 驗證的 HSM (硬體和韌體) 中處理您的金鑰。 透過 Azure 記錄來監視及稽核金鑰的使用：將記錄傳送到 Azure 中，或您的安全性資訊及事件管理 (SIEM) 系統，以進行其他分析及威脅偵測。

只要擁有 Azure 訂用帳戶，任何人都可以建立和使用金鑰保存庫。 雖然 Key Vault 有益於開發人員和安全性系統管理員，但組織中負責管理 Azure 服務的系統管理員也可以實作並加以管理。


## <a name="encrypt-virtual-disks-and-disk-storage"></a>將虛擬磁碟和磁碟儲存體加密

[Azure 磁碟加密](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)可藉由移動磁碟，處理資料竊取的威脅或遭到未經授權的存取。 磁碟可以連結至另一個系統，藉此略過其他安全性控制。 磁碟加密使用 [BitLocker](https://technet.microsoft.com/library/hh831713) (在 Windows 中) 及 DM-Crypt (在 Linux 中) 來加密作業系統和資料磁碟機。 Azure 磁碟加密會與 Key Vault 整合以控制和管理加密金鑰。 其可用於標準 VM 和具有進階儲存體的 VM。

如需詳細資訊，請參閱 [Windows 和 Linux IaaS VM 中的 Azure 磁碟加密](azure-security-disk-encryption.md)。

[Azure 儲存體服務加密](../storage/common/storage-service-encryption.md)可協助保護待用資料。 此功能是在儲存體帳戶層級啟用。 它會在資料寫入資料中心時進行加密，而資料會在您存取時自動解密。 它支援下列案例：

- 區塊 Blob、附加 Blob 和分頁 Blob 的加密
- 從內部部署移至 Azure 的封存 VHD 和範本的加密
- 使用您的 VHD 建立的 IaaS VM 基礎 OS 和資料磁碟的加密

繼續使用 Azure 儲存體加密前，請留意兩項限制︰

- 它不適用於傳統儲存體帳戶。
- 它只會加密在啟用加密功能之後寫入的資料。

## <a name="use-a-centralized-security-management-system"></a>使用集中式安全性管理系統

您必須監視伺服器是否需要修補、設定，或是否有可能被視為安全性疑慮的事件和活動。 若要解決這些疑慮，您可以使用[資訊安全中心](https://azure.microsoft.com/services/security-center/)和 [Operations Management Suite 安全性和法規遵循](https://azure.microsoft.com/services/security-center/)。 這兩個選項都超出作業系統的設定範圍。 它們也可供監視基礎結構的組態 (例如網路組態) 和虛擬設備使用情況。

## <a name="manage-operating-systems"></a>管理作業系統

在 IaaS 部署中，您仍然負責管理您所部署的系統，就像管理您環境中的任何其他伺服器或工作站一樣。 修補、強化、權限指派以及與系統維護相關的其他活動仍是您的責任。 對於與內部部署資源緊密整合的系統，您可以使用您在內部部署使用的相同工具和程序，來處理防毒、防惡意程式、修補和備份等事項。

### <a name="harden-systems"></a>強化系統
Azure IaaS 中的所有虛擬機器都應該強化，使其只會公開已安裝的應用程式所需的服務端點。 對於 Windows 虛擬機器，請遵循 Microsoft 所發佈的建議，做為 [Security Compliance Manager](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx) 解決方案的基準。

Security Compliance Manager 是免費的工具。 它可讓您使用群組原則和 System Center Configuration Manager 快速設定及管理桌上型電腦、傳統資料中心、私人和公用雲端。

Security Compliance Manager 可提供已準備好部署的原則和經過測試的 Desired Configuration Management 設定套件。 這些基準是以 [Microsoft 安全性指南](https://technet.microsoft.com/en-us/library/cc184906.aspx)的建議和業界最佳作法為基礎。 它們可協助您管理設定飄移 (Configuration Drift)、解決相容性需求及降低安全性威脅。

您可以使用 Security Compliance Manager 以兩種不同的方法匯入目前的電腦組態。 第一種方法，您可以匯入以 Active Directory 為基礎的群組原則。 第二種方法，您可以使用 [LocalGPO 工具](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/)匯入 “Golden Master” 參考電腦的組態，以備份本機群組原則。 接著，將本機群組原則匯入 Security Compliance Manager。

比較您的標準與業界最佳作法、加以自訂，然後建立新的原則和 Desired Configuration Management 設定套件。 已針對所有支援的作業系統 (包括 Windows 10 年度更新版和 Windows Server 2016) 發佈基準。


### <a name="install-and-manage-antimalware"></a>安裝和管理反惡意程式碼

對於與您的生產環境分開裝載的環境，您可以使用反惡意程式碼擴充功能來協助保護虛擬機器和雲端服務。 該擴充功能會與 [Azure 資訊安全中心](../security-center/security-center-intro.md)整合。


[Microsoft Antimalware](azure-security-antimalware.md) 包含下列功能：即時防護、排程掃描、惡意程式碼補救、簽章更新、引擎更新、範例報告、排除事件收集和 [PowerShell 支援](https://msdn.microsoft.com/library/dn771715.aspx)。

![Azure Antimalware](./media/azure-security-iaas/azantimalware.png)

### <a name="install-the-latest-security-updates"></a>安裝最新的安全性更新
客戶移至 Azure 的第一批工作負載中包括實驗室和對外系統。 如果 Azure 託管的虛擬機器會裝載需要存取網際網路的應用程式或服務，請對修補作業保持警戒。 作業系統之外的修補。 第三方應用程式上未修補的弱點也可能造成一些問題，但只要能夠妥善管理修補程式即可避免這類問題。

### <a name="deploy-and-test-a-backup-solution"></a>部署和測試備份解決方案

如同安全性更新一樣，您必須以處理任何其他作業的相同方式來處理備份。 您生產環境中延伸至雲端的系統很適合採用這種作法。 測試和開發系統都必須遵循備份策略，而這些備份策略能夠根據使用者的內部部署環境經驗，為使用者提供他們已經習慣的類似還原功能。

可能的話，移至 Azure 的生產工作負載應該與現有的備份解決方案整合。 或者，您可以使用 [Azure 備份](../backup/backup-azure-arm-vms.md)來協助您滿足備份需求。


## <a name="monitor"></a>監視

[資訊安全中心](../security-center/security-center-intro.md)會持續評估 Azure 資源的安全性狀態，以識別潛在的安全性弱點。 建議清單會引導您完成設定所需控制項的程序。

範例包括：

- 佈建反惡意程式碼，以協助識別及移除惡意軟體。
- 設定網路安全性群組與規則，以控制對虛擬機器的流量。
- 佈建 Web 應用程式防火牆，以協助抵禦以 Web 應用程式為目標的攻擊。
- 部署遺漏的系統更新。
- 處理不符合建議基準的作業系統組態。

下圖顯示一些您可以在資訊安全中心啟用的選項。

![Azure 資訊安全中心原則](./media/azure-security-iaas/security-center-policies.png)

[Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) 是 Microsoft 雲端型 IT 管理解決方案，可協助您管理及保護內部部署和雲端基礎結構。 因為 Operations Management Suite 是以雲端型服務形式實作，所以您對基礎結構資源進行最小的投資就可以快速部署它。

新功能會自動提供，以節省持續性維護和升級成本。 Operations Management Suite 也會與 System Center Operations Manager 整合。 它有不同的元件，可協助您更妥善管理 Azure 工作負載，包括[安全性和法規遵循](../operations-management-suite/oms-security-getting-started.md)模組。

您可以使用 Operations Management Suite 中的安全性和法規遵循功能來檢視您的資源相關資訊。 該資訊分為以下四個主要類別：

- **安全性網域**︰進一步探索一段時間的安全性記錄。 存取惡意程式碼評估、更新評估、網路安全性、身分識別和存取資訊，以及具有安全性事件的電腦。 利用快速存取 Azure 資訊安全中心儀表板的功能。
- **值得注意的問題**︰快速識別作用中問題數目和這些問題的嚴重性。
- **偵測 (預覽)**︰立即將資源的安全性警示視覺化，進而識別攻擊模式。
- **威脅情報**：藉由下列方式來識別攻擊模式：視覺化呈現具有惡意輸出 IP 流量的伺服器總數、惡意威脅類型，以及顯示這些 IP 出處的地圖。
- **常見安全性查詢**︰查看最常見安全性查詢的清單，以便用來監視您的環境。 當您按一下上述其中一個查詢時，[搜尋] 刀鋒視窗即會開啟，並顯示該查詢的結果。

下列螢幕擷取畫面顯示 Operations Management Suite 可以顯示之資訊的範例。

![Operations Management Suite 安全性基準](./media/azure-security-iaas/oms-security-baseline.png)

## <a name="next-steps"></a>後續步驟

* [Azure 安全性小組部落格](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Microsoft 安全性回應中心](https://technet.microsoft.com/library/dn440717.aspx)
* [Azure 安全性最佳作法與模式](security-best-practices-and-patterns.md)
