---
title: "什麼是 Azure 的自助式註冊？ | Microsoft Docs"
description: "Azure 的自助式註冊、如何管理註冊程序及如何接管 DNS 網域名稱的概觀。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: b9f01876-29d1-4ab8-8b74-04d43d532f4b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 63818878e01dd52a988e10a6e4f624638ea1cfae
ms.contentlocale: zh-tw
ms.lasthandoff: 04/27/2017

---
# <a name="what-is-self-service-signup-for-azure"></a>什麼是 Azure 的自助式註冊？
本主題說明自助式註冊程序及如何接管 DNS 網域名稱。  

## <a name="why-use-self-service-signup"></a>為何使用自助式註冊？
* 讓客戶更快取得他們想要的服務。
* 建立服務的電子郵件型供應項目。
* 建立以電子郵件為基礎的註冊流程，讓使用者使用其易記的工作電子郵件別名快速地建立身分識別。
* 未受管理的 Azure 目錄後來可以轉換成受管理的目錄，並重複用於其他服務。

## <a name="terms-and-definitions"></a>詞彙和定義
* **自助式註冊**：這是使用者用以註冊雲端服務的方法，系統會根據其電子郵件網域在 Azure Active Directory (Azure AD) 中自動建立身分識別。
* **未受管理的 Azure 目錄**：這是建立身分識別的目錄。 未受管理的目錄是沒有全域管理員的目錄。
* **電子郵件驗證的使用者**：這是 Azure AD 中的使用者帳戶類型。 在註冊自助式供應項目後自動建立身分識別的使用者，就是所謂的電子郵件驗證的使用者。 電子郵件驗證的使用者是加上 creationmethod=EmailVerified 標記之目錄的一般成員。

## <a name="user-experience"></a>使用者體驗
例如，假設電子郵件為 Dan@BellowsCollege.com 的使用者會透過電子郵件接收機密檔案。 檔案已受 Azure 版權管理 (Azure RMS) 保護。 但是 Dan 的組織 (Bellows College) 尚未註冊 Azure RMS，也未部署 Active Directory RMS。 在此情況下，Dan 可以註冊個人版 RMS 的免費訂閱，以便讀取受保護的檔案。

如果 Dan 是第一個使用 BellowsCollege.com 的電子郵件地址註冊此自助式供應項目的使用者，則 Azure AD 中會針對 BellowsCollege.com 建立未受管理的目錄。 如果來自 BellowsCollege.com 網域的其他使用者註冊此供應項目或類似的自助式供應項目，則在 Azure 中相同的未受管理目錄中，也會為他們建立經過電子郵件驗證的使用者帳戶。

## <a name="admin-experience"></a>管理員體驗
擁有未受管理 Azure 目錄之 DNS 網域名稱的管理員，可以在證明擁有權後接管或合併目錄。 下一節會更詳細地說明管理員體驗，但其摘要如下：

* 當您接管未受管理的 Azure 目錄時，您就直接變成未受管理目錄的全域管理員。 這有時候稱為內部接管。
* 當您合併未受管理的 Azure 目錄時，您會將未受管理目錄的 DNS 網域名稱新增至受管理的 Azure 目錄，而且系統會建立使用者與資源的對應，以便使用者繼續存取服務而不中斷。 這有時候稱為外部接管。

## <a name="what-gets-created-in-azure-active-directory"></a>Azure Active Directory 中建立的項目為何？
#### <a name="directory"></a>目錄
* 建立網域的 Azure Active Directory 目錄 (每個網域一個目錄)。
* Azure AD 目錄沒有全域管理員。

#### <a name="users"></a>使用者
* 針對註冊的每位使用者，Azure AD 目錄中會建立使用者物件。
* 每個使用者物件都標示為外部。
* 每位使用者都獲權存取他們註冊的服務。

### <a name="how-do-i-claim-a-self-service-azure-ad-directory-for-a-domain-i-own"></a>如何針對我擁有的網域宣告自助式 Azure AD 目錄？
您可以執行網域驗證來宣告自助式 Azure AD 目錄。 網域驗證會藉由建立 DNS 記錄來證明您擁有該網域。

有兩種方式可進行 Azure AD 目錄的 DNS 接管：

* 內部接管 (管理員會探索未受管理的 Azure 目錄，並且想要轉換成受管理的目錄)
* 外部接管 (管理員會嘗試將新的網域新增至他們管理的 Azure 目錄)

如果您在使用者執行自助式註冊後接管未受管理的目錄，或正在將新網域加入至現有的受管理目錄，您可能會想要驗證您擁有網域。 例如，您擁有名為 contoso.com 的網域，而您想要加入名為 contoso.co.uk 或 contoso.uk 的新網域。

## <a name="what-is-domain-takeover"></a>什麼是網域接管？
本節說明如何驗證您擁有的網域。

### <a name="what-is-domain-validation-and-why-is-it-used"></a>什麼是網域驗證，以及為何使用？
為了在目錄上執行作業，Azure AD 會要求您驗證 DNS 網域的擁有權。  網域驗證可讓您宣告目錄，並且將自助式目錄提升為受管理的目錄，或將自助式目錄合併到現有的受管理目錄。

## <a name="examples-of-domain-validation"></a>網域驗證範例
有兩種方式可進行目錄的 DNS 接管：

* 內部接管 (例如，系統管理員會探索未受管理的自助式目錄，並且想要轉換成受管理的目錄)
* 外部接管 (例如，管理員會嘗試將新的網域新增至受管理的目錄)

### <a name="internal-takeover---promote-a-self-service-unmanaged-directory-to-be-a-managed-directory"></a>內部接管 - 將未受管理的自助式目錄提升為受管理的目錄
當您執行內部接管時，目錄會從未受管理的目錄轉換為受管理的目錄。 您需要完成 DNS 網域名稱驗證，驗證時您會在 DNS 區域中建立 MX 記錄或 TXT 記錄。 該動作：

* 驗證您是否擁有此網域
* 將目錄變成受管理
* 讓您成為目錄的全域管理員

假設 Bellows College 的 IT 管理員發現學校的使用者已註冊自助式供應項目。 身為 DNS 名稱 BellowsCollege.com 的註冊擁有者，IT 管理員可以在 Azure 中驗證 DNS 名稱的擁有權，然後接管未受管理的目錄。 目錄接著會變成受管理的目錄，而 IT 管理員會被指派 BellowsCollege.com 目錄的全域管理員角色。

### <a name="external-takeover---merge-a-self-service-directory-into-an-existing-managed-directory"></a>外部接管 - 將自助式目錄合併到現有的受管理目錄
在外部接管中，您已有受管理的目錄，而您希望來自未受管理目錄的所有使用者和群組都加入該受管理的目錄，而非擁有兩個不同的目錄。

身為受管理目錄的管理員，您新增網域，而該網域剛好有一個相關聯的未受管理目錄。

例如，假設您是 IT 管理員，而且已有 Contoso.com (您的組織註冊的網域名稱) 的受管理目錄。 您會發現貴組織的使用者已使用電子郵件網域名稱 user@contoso.co.uk (這是貴組織擁有的另一個網域名稱) 以自助方式註冊產品方案。 這些使用者目前在 contoso.co.uk 的未受管理目錄中有帳戶。

您不想管理兩個不同的目錄，所以將 contoso.co.uk 的未受管理目錄合併到 contoso.com 的現有 IT 受管理目錄。

外部接管會遵循與內部接管相同的 DNS 驗證程序。  差異是：使用者和服務會重新對應至 IT 受管理的目錄。

#### <a name="whats-the-impact-of-performing-an-external-takeover"></a>執行外部接管的影響為何？
外部接管時會建立使用者與資源的對應，以便使用者繼續存取服務而不中斷。 許多應用程式 (包括個人的 RMS) 會妥善處理使用者與資源的對應，而使用者不需變更即可繼續存取這些服務。 如果應用程式未有效地處理使用者與資源的對應，外部接管可能會明確遭到封鎖，以免使用者發生不佳的體驗。

#### <a name="directory-takeover-support-by-service"></a>服務支援的目錄接管
目前下列服務支援接管：

* RMS

下列服務很快就會支援接管：

* PowerBI

在外部接管之後，下列項目不需要管理員採取行動來移轉使用者資料。

* SharePoint/OneDrive

## <a name="how-to-perform-a-dns-domain-name-takeover"></a>如何執行 DNS 網域名稱接管
您有幾個選項可用來執行網域驗證 (如果您想要，可執行接管)：

1. Azure 管理入口網站

   接管是由執行網域新增所觸發。  如果網域已有目錄，您將可選擇執行外部接管。

   使用您的認證登入 Azure 入口網站。  瀏覽至現有的目錄，再瀏覽至 [加入網域] 。
2. Office 365

   您可以在 Office 365 中使用 [管理網域](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/) 頁面上的選項來處理您的網域和 DNS 記錄。 請參閱 [在 Office 365 中驗證您的網域](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/)。
3. Windows PowerShell

   下列步驟是使用 Windows PowerShell 執行驗證的必要步驟。

   | 步驟 | 要使用的 Cmdlet |
   | --- | --- |
   | 建立認證物件 |Get-Credential |
   | 連接至 Azure AD |Connect-MsolService |
   | 取得網域清單 |Get-MsolDomain |
   | 建立挑戰 |Get-MsolDomainVerificationDns |
   | 建立 DNS 記錄 |在您的 DNS 伺服器上執行這項操作 |
   | 驗證挑戰 |Confirm-MsolEmailVerifiedDomain |

例如：

1. 使用用來回應自助式產品方案的認證來連接至 Azure AD：

        import-module MSOnline
        $msolcred = get-credential
        connect-msolservice -credential $msolcred
2. 取得網域清單：

    Get-MsolDomain
3. 然後執行 Get-MsolDomainVerificationDns Cmdlet 來建立挑戰：

    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord

    例如：

    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
4. 複製從此命令傳回的值 (挑戰)。

    例如：

    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
5. 在公用 DNS 命名空間中，建立 DNS txt 記錄，其中包含您在上一個步驟中複製的值。

    此記錄的名稱是父系網域的名稱，所以如果您使用 Windows Server 的 DNS 角色建立此資源記錄，請將記錄名稱空白，只要將此值貼入文字方塊即可。
6. 執行 onfirm-MsolDomain Cmdlet 來驗證挑戰：

    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*

    例如：

    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com

成功挑戰會讓您回到提示，但不會產生錯誤。

## <a name="how-do-i-control-self-service-settings"></a>如何控制自助式設定？
系統管理員目前有兩個自助式控制項。 可以控制：

* 使用者是否可以透過電子郵件加入目錄。
* 使用者是否可以自行授權應用程式和服務。

### <a name="how-can-i-control-these-capabilities"></a>如何控制這些功能？
系統管理員可以使用下列 Azure AD Cmdlet Set-MsolCompanySettings 參數設定這些功能：

* **AllowEmailVerifiedUsers** 控制使用者是否可以建立或加入未受管理的目錄。 如果您將該參數設定為 $false，則經過電子郵件驗證的使用者都無法加入目錄。
* **AllowAdHocSubscriptions** 控制使用者執行自助式註冊的能力。 如果您將該參數為 $false，則沒有任何使用者可以執行自助式註冊。

### <a name="how-do-the-controls-work-together"></a>這些控制項如何一起運作？
這兩個參數可合併使用，以定義更精確的自助式註冊控制項。 例如，下列命令可讓使用者執行自助式註冊，但僅限於在 Azure AD 中已有帳戶的使用者 (換句話說，需要建立電子郵件驗證帳戶的使用者無法執行自助式註冊)：

    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

下列流程圖說明這些參數的所有不同組合，以及針對目錄和自助式註冊造成的情況。

![][1]

如需如何使用這些參數的詳細資訊和相關範，請參閱 [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)。

## <a name="see-also"></a>另請參閱
* [如何安裝和設定 Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Cmdlet 參考](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png

