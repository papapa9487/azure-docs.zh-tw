---
title: "將自訂網域新增到 Azure AD | Microsoft Docs"
description: "說明如何在 Azure Active Directory 中新增自訂網域。"
services: active-directory
author: curtand
manager: michael.tillman
ms.assetid: 0a90c3c5-4e0e-43bd-a606-6ee00f163038
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 27e7449f039da8f7661d113999e1c4e5d76c3cf6
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2017
---
# <a name="quickstart-add-a-custom-domain-name-to-azure-active-directory"></a>快速入門：將自訂網域名稱新增到 Azure Active Directory

每個 Azure AD 目錄皆隨附形式為 domainname.onmicrosoft.com 的初始網域名稱。初始網域名稱無法變更或刪除，但是您也可以將您的公司網域名稱新增至 Azure AD。 例如，貴組織可能有營運所用的其他網域名稱，以及使用貴公司網域名稱登入的使用者。 將自訂網域名稱新增到 Azure AD 可讓您在目錄中指派您使用者熟悉的使用者名稱，例如 ‘alice@contoso.com’。 而不是 'alice@*domain name*.onmicrosoft.com'。 程序佷簡單：

1. 在目錄中新增自訂網域名稱
2. 在網域名稱註冊機構中新增網域名稱的 DNS 項目
3. 驗證 Azure AD 中的自訂網域名稱

## <a name="add-the-custom-domain-name-to-your-directory"></a>在目錄中新增自訂網域名稱
1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) 。
2. 在左側選取 [自訂網域名稱]。
3. 選取 [新增自訂網域]。
   
   ![選取 [新增] 命令](./media/add-custom-domain/add-custom-domain.png)
5. 在 [自訂網域名稱] 上，於方塊中輸入您的自訂網域名稱 (例如「contoso.com」)，然後選取 [新增網域]。 請務必包含 .com、.net 或其他最上層的擴充。
6. 在 [domainname] 上 (也就是新網域名稱為標題之處)，收集稍後用於驗證 Azure AD 中自訂網域名稱的 DNS 項目資訊。
   
   ![取得 DNS 項目資訊](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

> [!TIP]
> 如果您計劃讓內部部署 Windows Server AD 與 Azure AD 同盟，當您執行 Azure AD Connect 工具以同步處理您的目錄時，您必須選取 [我計劃將這個網域設定為可使用我的本機 Active Directory 進行單一登入] 核取方塊。 您也需要註冊相同的網域名稱，您選取該名稱以與精靈中 **Azure AD 網域** 步驟中的內部部署目錄同盟。 您可以[在這些指示中](./connect/active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation)看到精靈中那些步驟看起來如何。 如果您沒有 Azure AD Connect 工具，您可以 [在這裡下載](http://go.microsoft.com/fwlink/?LinkId=615771)。

## <a name="add-a-dns-entry-for-the-domain-name-at-the-domain-name-registrar"></a>在網域名稱註冊機構中新增網域名稱的 DNS 項目
利用 Azure AD 使用您自訂網域名稱的下一個步驟，就是更新網域的 DNS 區域檔案。 然後 Azure AD 可以確認您的組織擁有該自訂網域名稱。 您可以對於 Azure 中的 Azure/Office 365/外部 DNS 記錄使用 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal)，也可以在[不同的 DNS 註冊機構](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)新增 DNS 項目。

1. 登入網域的網域名稱註冊機構。 如果您無法存取以更新 DNS 項目，請要求具有此存取權的人員或小組完成步驟 2 並在完成時通知您。
2. 透過新增 Azure AD 提供給您的 DNS 項目來更新網域的 DNS 區域檔案。 DNS 項目不會變更任何行為，例如郵件路由或 Web 裝載。

## <a name="verify-the-custom-domain-name-in-azure-ad"></a>驗證 Azure AD 中的自訂網域名稱
一旦新增了 DNS 項目，您就可以使用 Azure AD 確認網域名稱。 只有在 DNS 記錄傳播完成之後，才能驗證網域名稱。 通常此傳播只需要幾秒鐘，但有時可能需要一個小時以上。 如果驗證第一次不成功，請稍後再試。

1. 使用具備租用戶全域管理員身分的帳戶來登入 [Azure AD](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)。
2. 選取 [自訂網域名稱]。
3. 選取您想要驗證的未驗證網域名稱。
4. 請檢查您的項目，然後選取 [確認] 以完成驗證。

現在您可以[指派包含自訂網域名稱的使用者名稱](active-directory-users-create-azure-portal.md)。 您可以建立雲端式使用者帳戶，也可以使用您的自訂網域名稱，更新先前已同步處理的內部部署使用者帳戶資訊。 您也可以使用 [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) 或[圖形 API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)，變更同步處理的使用者帳戶網域尾碼資訊。

> [!TIP]
> 您最多可以新增 900 個受管理的網域名稱。 如果您使用內部部署 Active Directory 設定同盟的所有網域，您最多可以在每個目錄中新增 450 個網域名稱。 如需詳細資訊，請參閱[同盟和受管理的網域名稱](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain-concepts#federated-and-managed-domain-names)。

## <a name="troubleshooting"></a>疑難排解
如果無法確認自訂網域名稱，請嘗試下列疑難排解步驟：

1. **等候一小時**。 DNS 記錄必須在 Azure AD 驗證網域之後傳播。 此流程可能需要一個小時以上。
2. **確定已輸入正確的 DNS 記錄**。 請在該網域的網域名稱註冊機構網站上完成這個步驟。 在下列情況下，Azure AD 無法驗證網域名稱： 
  * DNS 項目不存在於 DNS 區域檔案中
  * 不完全符合 Azure AD 提供您的 DNS 項目。 
  
  如果您無法在網域名稱註冊機構上存取以更新網域的 DNS 記錄，請與組織內具有此存取權的個人或團隊共用 DNS 項目，並請他們新增 DNS 項目。
3. **從 Azure AD 中的另一個目錄刪除網域名稱**。 網域名稱只能在單一目錄中確認。 如果目前是在不同的目錄中驗證網域名稱，則在刪除另一個目錄上的網域名稱之前，無法驗證新目錄中的網域名稱。 若要了解如何刪除網域名稱，請參閱 [管理自訂網域名稱](active-directory-domains-manage-azure-portal.md)。    

重複本文中的步驟來新增每個網域名稱。

## <a name="learn-more"></a>詳細資訊
[Azure AD 中自訂網域名稱的概念式概觀](active-directory-domains-manage-azure-portal.md)

[管理自訂網域名稱](active-directory-domains-manage-azure-portal.md)

## <a name="next-steps"></a>後續步驟
在此快速入門中，您學到如何將自訂網域新增至 Azure AD。 

您可以使用下列連結，從 Azure 入口網站在 Azure AD 中新增新的自訂網域。

> [!div class="nextstepaction"]
> [新增自訂網域](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/QuickStart) 