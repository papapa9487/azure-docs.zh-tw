---
title: "將您的自訂網域名稱新增至 Azure Active Directory | Microsoft Docs"
description: "如何在 Azure Active Directory 中新增您公司的網域名稱，以及如何確認網域名稱。"
services: active-directory
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 35a6e20a-9907-432b-9d36-16b916a5c249
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
robots: NOINDEX
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 0228bf7ce261cfccdec9431d3c9fb67f461eefbd
ms.contentlocale: zh-tw
ms.lasthandoff: 07/25/2017

---
# <a name="add-a-custom-domain-name-to-azure-active-directory"></a>將自訂網域名稱新增至 Azure Active Directory
> [!div class="op_single_selector"]
> * [Azure 入口網站](active-directory-domains-add-azure-portal.md)
> * [Azure 傳統入口網站](active-directory-add-domain.md)
> 
> 

您已有一或多個您的組織用來經營業務的網域名稱，而且您的使用者使用您公司的網域名稱來登入公司網路。 既然您將要使用 Azure Active Directory (Azure AD)，您也可以將您的公司網域名稱加入 Azure AD。 這可讓您在目錄中指派您的使用者熟悉的使用者名稱，例如 ‘alice@contoso.com’。 程序佷簡單：

1. 在目錄中新增自訂網域名稱
2. 在網域名稱註冊機構中新增網域名稱的 DNS 項目
3. 驗證 Azure AD 中的自訂網域名稱

> [!IMPORTANT]
> Microsoft 建議您使用 Azure 入口網站中的 [Azure AD 系統管理中心](https://aad.portal.azure.com)來管理 Azure AD，而不要使用本文所提及的 Azure 傳統入口網站。 如需如何在 Azure AD 系統管理中心新增公司網域名稱的相關資訊，請參閱[在 Azure Active Directory 中指派系統管理員角色](active-directory-domains-add-azure-portal.md)。

如果您打算設定自訂網域名稱以搭配 Active Directory 同盟服務 (AD FS) 或公司網路上不同的 Security Token Service (STS) 使用，請依照下列的指示進行： [新增和設定同盟的網域來搭配 Azure Active Directory](active-directory-add-domain-federated.md)。 如果您打算要將公司目錄的使用者同步處理至 Azure AD，並且 [密碼雜湊同步](active-directory-aadconnectsync-implement-password-synchronization.md) 不符合您的需求時，這將十分有幫助。

## <a name="add-a-custom-domain-name-to-your-directory"></a>在目錄中新增自訂網域名稱
1. 使用屬於 Azure AD目錄全域管理員的使用者帳戶登入 [Azure 傳統入口網站](https://manage.windowsazure.com/) 。
2. 在 [Active Directory] 中開啟您的目錄，然後選取 [網域] 索引標籤。
3. 在命令列上選取 [新增] 。 輸入您的自訂網域名稱，例如 'contoso.com'。 請務必包含 .com、.net 或其他最上層的擴充功能，並清除「單一登入」(同盟) 的核取方塊。
4. 選取 [新增] 。
5. 在「新增網域」精靈的第二頁中，取得 Azure AD 將用來驗證您的組織是否擁有自訂網域名稱的 DNS 項目。

既然您已新增網域名稱，Azure AD 必須確認您的組織擁有該網域名稱。 您必須先在 DNS 區域檔案中新增該網域名稱的 DNS 項目，Azure AD 才可以進行此確認。 這項工作是在該網域名稱的網域名稱註冊機構網站上進行。

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>在網域名稱註冊機構中新增網域的 DNS 項目
利用 Azure AD 使用您自訂網域名稱的下一個步驟，就是更新網域的 DNS 區域檔案。 這可讓 Azure AD 確認您的組織擁有該自訂網域名稱。

1. 登入網域的網域名稱註冊機構。 如果您無法存取以更新 DNS 項目，請要求具有此存取權的人員或小組完成步驟 2 並在完成時通知您。
2. 透過新增 Azure AD 提供給您的 DNS 項目來更新網域的 DNS 區域檔案。 此 DNS 項目可讓 Azure AD 確認您擁有網域。 DNS 項目不會變更任何行為，例如郵件路由或 Web 裝載。

如需新增此 DNS 項目的說明，請參閱 [在常用 DNS 註冊機構新增 DNS 項目的指示](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>使用 Azure AD 確認網域名稱
一旦新增了 DNS 項目，您就可以使用 Azure AD 確認網域名稱。

如果您的 [新增網域] 精靈仍保持開啟，請選取精靈第三頁上的 [確認]。 當您選取 [確認] ，Azure AD 會在 DNS 區域檔案中尋找網域的 DNS 項目。 只有在 DNS 記錄傳播完成之後，Azure AD 才可以確認網域名稱。 通常此傳播只需要幾秒鐘，但有時可能需要一個小時以上。 如果驗證第一次不成功，請稍後再試。

如果 [新增網域]  精靈未保持開啟，您可以在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中確認網域：

1. 使用屬於 Azure AD 目錄全域管理員的使用者帳戶進行登入。
2. 開啟您的目錄，然後選取 [網域]  索引標籤。
3. 選取您想要確認的網域名稱，然後在命令列上選取 [確認]  。
4. 在對話方塊中選取 [確認]  以完成驗證。

現在您可以 [指派包含自訂網域名稱的使用者名稱](active-directory-add-domain-add-users.md)。

## <a name="troubleshooting"></a>疑難排解
如果無法確認自訂網域名稱，請嘗試下列方法。 我們會從最常見的原因來開始逐一介紹到最不常見的原因。

1. **等候一小時**。 DNS 記錄必須在 Azure AD 確認網域之後傳播。 這可能需要一個小時以上。
2. **確定已輸入正確的 DNS 記錄**。 請在該網域的網域名稱註冊機構網站上完成這個步驟。 如果 DNS 項目不在 DNS 區域檔案中，或如果與 Azure AD 提供您的 DNS 項目不完全相符，則 Azure AD 無法確認網域名稱。 如果您無法在網域名稱註冊機構上存取以更新網域的 DNS 記錄，請與組織內具有此存取權的個人或團隊共用 DNS 項目，並請他們新增 DNS 項目。
3. **從 Azure AD 中的另一個目錄刪除網域名稱**。 網域名稱只能在單一目錄中確認。 如果網域名稱先前在另一個目錄中確認過，則必須先在那裡將其刪除後，才可在新的目錄中確認。 若要了解如何刪除網域名稱，請參閱 [管理自訂網域名稱](active-directory-add-manage-domain-names.md)。

## <a name="add-more-custom-domain-names"></a>新增更多的自訂網域名稱
如果您的組織使用多個自訂網域名稱，例如 ‘contoso.com’ 和 ‘contosobank.com’，您最多可以新增 900 個網域名稱。 請使用本文中的相同步驟來新增每個網域名稱。

## <a name="next-steps"></a>後續步驟
* [指派包含自訂網域名稱的使用者名稱](active-directory-add-domain-add-users.md)
* [管理自訂網域名稱](active-directory-add-manage-domain-names.md)
* [了解 Azure AD 中的網域管理概念](active-directory-add-domain-concepts.md)
* [在您的使用者登入時顯示公司的商標](active-directory-add-company-branding.md)
* [使用 PowerShell 管理 Azure AD 中的網域名稱](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)


