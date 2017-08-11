---
title: "將您的自訂網域名稱新增並設定同盟登入至 Azure Active Directory | Microsoft Docs"
description: "如何將您公司的網域名稱新增至 Azure Active Directory，以在 Azure Active Directory 與內部部署同盟方案之間設定同盟登入"
services: active-directory
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 27126c7e-e6d6-4ef3-a4fb-f5f0706e749d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 7f257dff6fdd423b89d1c52a84b64951dcf5915f
ms.contentlocale: zh-tw
ms.lasthandoff: 05/17/2017

---
# <a name="add-your-custom-domain-name-to-azure-active-directory"></a>將您的自訂網域名稱新增至 Azure Active Directory
您可以設定自訂網域名稱，例如 'contoso.com'，如此 contoso.com 中的使用者可以從您的公司網路進行同盟單一登入。 如果您已經在公司網路上執行 Active Directory 同盟服務 (AD FS) 或不同的同盟伺服器，可以使用 Azure AD Connect 工具設定 Azure AD 使用您的自訂網域名稱。 您也可以使用 Azure AD Connect 部署新的 AD FS 環境，並為其設定同盟單一登入 Azure AD。

如果您不需要且並不打算部署 AD FS 或另一部同盟伺服器，請遵循下列指示︰ [將自訂網域名稱新增至 Azure Active Directory](active-directory-add-domain.md)。

## <a name="add-a-custom-domain-name-to-your-directory"></a>在目錄中新增自訂網域名稱
1. 使用屬於 Azure AD目錄全域管理員的使用者帳戶登入 [Azure 傳統入口網站](https://manage.windowsazure.com/) 。
2. 在 [Active Directory] 中開啟您的目錄，然後選取 [網域] 索引標籤。
3. 在命令列上選取 [新增] ，然後輸入自訂網域的名稱，例如 'contoso.com'。 請務必包含 .com、.net 或其他最上層的擴充。
4. 選取 [我計劃將這個網域設定為可使用我的本機 Active Directory 進行單一登入]  核取方塊。
5. 選取 [新增] 。

執行 Azure AD Connect 工具以取得 Azure AD 用來確認網域的 DNS 項目。 您會在精靈中看到 **Azure AD 網域** 步驟中的 DNS 項目。 您可以[在這些指示中](connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)看到精靈中那些步驟看起來如何。 如果您沒有 Azure AD Connect 工具，您可以 [在這裡下載](http://go.microsoft.com/fwlink/?LinkId=615771)。

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>在網域名稱註冊機構中新增網域的 DNS 項目
利用 Azure AD 使用您自訂網域名稱的下一個步驟，就是更新網域的 DNS 區域檔案。 這可讓 Azure AD 確認您的組織擁有該自訂網域名稱。

1. 登入您網域名稱的網域名稱註冊機構網站。 如果您無法存取以進行此舉，請要求組織中具有此存取權的人員或小組完成步驟 2 並在完成時通知您。
2. 透過新增 Azure AD 提供給您的 DNS 項目來更新網域的 DNS 區域檔案。 此 DNS 項目可讓 Azure AD 確認您擁有網域。 DNS 項目不會變更任何行為，例如郵件路由或 Web 裝載。

如需此步驟的說明，請參閱 [在常用 DNS 註冊機構新增 DNS 項目的指示](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>使用 Azure AD 確認網域名稱
一旦新增了 DNS 項目，您就可以使用 Azure AD 確認網域名稱。

若要確認網域，請選取 Azure AD Connect 精靈上 [Azure AD 網域] 步驟的 [下一步]。 Azure AD 會在 DNS 區域檔案中尋找網域的 DNS 項目。 只有在 DNS 記錄傳播完成之後，Azure AD 才可以確認網域名稱。 通常傳播只需要幾秒鐘，但有時可能需要一個小時以上。 如果驗證第一次不成功，請稍後再試。

然後，繼續 Azure AD Connect 精靈的其餘步驟。 這會將使用者從您的 Windows Server AD 同步至 Azure AD。 您針對同盟所設定的網域中同步處理的使用者，可以體驗到由公司網路同盟單一登入到 Azure AD。

## <a name="troubleshooting"></a>疑難排解
如果無法確認自訂網域名稱，請嘗試下列方法。 我們會從最常見的原因來開始逐一介紹到最不常見的原因。

1. **等候一小時**。 DNS 記錄必須在 Azure AD 確認網域之後傳播。 這可能需要一個小時以上。
2. **確定已輸入正確的 DNS 記錄**。 請在該網域的網域名稱註冊機構網站上完成這個步驟。 如果 DNS 項目不在 DNS 區域檔案中，或如果與 Azure AD 提供您的 DNS 項目不完全相符，則 Azure AD 無法確認網域名稱。 如果您無法在網域名稱註冊機構上存取以更新網域的 DNS 記錄，請與組織內具有此存取權的個人或團隊共用 DNS 項目，並請他們新增 DNS 項目。
3. **從 Azure AD 中的另一個目錄刪除網域名稱**。 網域名稱只能在單一目錄中確認。 如果網域名稱先前在另一個目錄中確認過，則必須先在那裡將其刪除後，才可在新的目錄中確認。 若要了解如何刪除網域名稱，請參閱 [管理自訂網域名稱](active-directory-add-manage-domain-names.md)。

## <a name="add-more-custom-domain-names"></a>新增更多的自訂網域名稱
如果您的組織使用多個自訂網域名稱，例如 ‘contoso.com’ 和 ‘contosobank.com’，您最多可以新增 900 個網域名稱。 請使用本文中的相同步驟來新增每個網域名稱。

## <a name="next-steps"></a>後續步驟
* [管理自訂網域名稱](active-directory-add-manage-domain-names.md)
* [了解 Azure AD 中的網域管理概念](active-directory-add-domain-concepts.md)
* [在您的使用者登入時顯示公司的商標](active-directory-add-company-branding.md)
* [使用 PowerShell 管理 Azure AD 中的網域名稱](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)


