---
title: "將自訂網域新增到 Azure AD | Microsoft Docs"
description: "說明如何在 Azure Active Directory 中新增自訂網域。"
services: active-directory
author: jeffgilb
manager: femila
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: jeffgilb
ms.reviewer: jsnow
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 4848130601ffa18ed1565e79cb0f0db3274e950f
ms.contentlocale: zh-tw
ms.lasthandoff: 08/11/2017

---
# <a name="quickstart-add-a-custom-domain-name-to-azure-active-directory"></a>快速入門：將自訂網域名稱新增到 Azure Active Directory

每個 Azure AD 目錄皆隨附形式為 domainname.onmicrosoft.com 的初始網域名稱。 初始網域名稱無法變更或刪除，但是您也可以將您的公司網域名稱新增至 Azure AD。 例如，貴組織可能有營運所用的其他網域名稱，以及使用貴公司網域名稱登入的使用者。 將自訂網域名稱新增到 Azure AD 可讓您在目錄中指派您使用者熟悉的使用者名稱，例如 ‘alice@contoso.com’。 而不是 'alice@*<domain name>*.onmicrosoft.com'。 程序佷簡單：

1. 在目錄中新增自訂網域名稱
2. 在網域名稱註冊機構中新增網域名稱的 DNS 項目
3. 驗證 Azure AD 中的自訂網域名稱

## <a name="add-your-custom-domain"></a>新增自訂網域
1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com) 。
2. 選取 [更多服務]，在文字方塊中輸入 **Azure Active Directory**，然後選取 **Enter**。
   
   ![開啟使用者管理](./media/active-directory-domains-add-azure-portal/user-management.png)
3. 在 [directory-name] 刀鋒視窗上，選取 [網域名稱]。
4. 在 [*directory-name* - 網域名稱] 刀鋒視窗上，選取 [新增] 命令。
   
   ![選取 [新增] 命令](./media/active-directory-domains-add-azure-portal/add-command.png)
5. 在 [網域名稱] 刀鋒視窗上，於方塊中輸入您的自訂網域名稱 (例如 'contoso.com')，然後選取 [新增網域]。 請務必包含 .com、.net 或其他最上層的擴充。
6. 在 [網域名稱] 刀鋒視窗 (標題中含有自訂網域名稱) 上，取得用來驗證貴組織是否擁有自訂網域名稱的 DNS 項目資訊。
   
   ![取得 DNS 項目資訊](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

> [!TIP]
> 如果您計劃讓內部部署 Windows Server AD 與 Azure AD 同盟，當您執行 Azure AD Connect 工具以同步處理您的目錄時，您必須選取 [我計劃將這個網域設定為可使用我的本機 Active Directory 進行單一登入] 核取方塊。 您也需要註冊相同的網域名稱，您選取該名稱以與精靈中 **Azure AD 網域** 步驟中的內部部署目錄同盟。 您可以[在這些指示中](./connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)看到精靈中那些步驟看起來如何。 如果您沒有 Azure AD Connect 工具，您可以 [在這裡下載](http://go.microsoft.com/fwlink/?LinkId=615771)。

既然您已新增網域名稱，Azure AD 必須確認您的組織擁有該網域名稱。 您必須先在 DNS 區域檔案中新增該網域名稱的 DNS 項目，Azure AD 才可以進行此確認。 這項工作是在該網域名稱的網域名稱註冊機構網站上進行。

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>在網域名稱註冊機構中新增網域的 DNS 項目
利用 Azure AD 使用您自訂網域名稱的下一個步驟，就是更新網域的 DNS 區域檔案。 然後 Azure AD 可以確認您的組織擁有該自訂網域名稱。

1. 登入網域的網域名稱註冊機構。 如果您無法存取以更新 DNS 項目，請要求具有此存取權的人員或小組完成步驟 2 並在完成時通知您。
2. 透過新增 Azure AD 提供給您的 DNS 項目來更新網域的 DNS 區域檔案。 此 DNS 項目可讓 Azure AD 確認您擁有網域。 DNS 項目不會變更任何行為，例如郵件路由或 Web 裝載。

如需新增此 DNS 項目的說明，請參閱 [在常用 DNS 註冊機構新增 DNS 項目的指示](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>使用 Azure AD 確認網域名稱
一旦新增了 DNS 項目，您就可以使用 Azure AD 確認網域名稱。

只有在 DNS 記錄傳播完成之後，才能驗證網域名稱。 通常此傳播只需要幾秒鐘，但有時可能需要一個小時以上。 如果驗證第一次不成功，請稍後再試。

1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com) 。
2. 選取 [瀏覽]，在文字方塊中輸入「使用者管理」，然後選取 **Enter**。
   
   ![開啟使用者管理](./media/active-directory-domains-add-azure-portal/user-management.png)
3. 在 [使用者管理 - 網域名稱]  刀鋒視窗上，選取您想要驗證的未驗證網域名稱。
4. 在 [網域名稱] 刀鋒視窗 (也就是所開啟且標題中含有新網域名稱的刀鋒視窗) 上，選取 [驗證] 來完成驗證。

> [!TIP]
> 您可以新增多達 900 個自訂網域名稱，但只能有一個[設為 Azure AD 目錄的主要網域名稱](active-directory-domains-manage-azure-portal.md#set-the-primary-domain-name-for-your-azure-ad-directory)，在您建立新帳戶時作為預設值。

現在您可以建立雲端式使用者帳戶，或者使用您的自訂網域名稱，更新先前已同步處理的內部部署使用者帳戶資訊。 您也可以使用 [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) 或[圖形 API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)，修改先前同步處理的使用者帳戶網域尾碼資訊。

## <a name="troubleshooting"></a>疑難排解
如果無法確認自訂網域名稱，請嘗試下列疑難排解步驟：

1. **等候一小時**。 DNS 記錄必須在 Azure AD 確認網域之後傳播。 這可能需要一個小時以上。
2. **確定已輸入正確的 DNS 記錄**。 請在該網域的網域名稱註冊機構網站上完成這個步驟。 如果 DNS 項目不在 DNS 區域檔案中，或如果與 Azure AD 提供您的 DNS 項目不完全相符，則 Azure AD 無法確認網域名稱。 如果您無法在網域名稱註冊機構上存取以更新網域的 DNS 記錄，請與組織內具有此存取權的個人或團隊共用 DNS 項目，並請他們新增 DNS 項目。
3. **從 Azure AD 中的另一個目錄刪除網域名稱**。 網域名稱只能在單一目錄中確認。 如果網域名稱先前在另一個目錄中確認過，則必須先在那裡將其刪除後，才可在新的目錄中確認。 若要了解如何刪除網域名稱，請參閱 [管理自訂網域名稱](active-directory-domains-manage-azure-portal.md)。    

## <a name="add-more-custom-domain-names"></a>新增更多的自訂網域名稱
如果您的組織使用多個自訂網域名稱，例如 'contoso.com' 和 'contosobank.com'，您可以新增多達 900 以上的網域名稱，方法是針對每個網域名稱重複這篇文章中的步驟。

### <a name="learn-more"></a>詳細資訊
[Azure AD 中自訂網域名稱的概念式概觀](active-directory-add-domain-concepts.md)

[管理自訂網域名稱](active-directory-domains-manage-azure-portal.md)


## <a name="next-steps"></a>後續步驟
在此快速入門中，您學到如何將自訂網域新增至 Azure AD。 

您可以使用下列連結，從 Azure 入口網站在 Azure AD 中新增新的自訂網域。

> [!div class="nextstepaction"]
> [新增自訂網域](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/QuickStart) 
