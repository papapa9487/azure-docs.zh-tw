---
title: "管理 Azure Active Directory 中的自訂網域名稱 | Microsoft Docs"
description: "管理概念和管理 Azure Active Directory 自訂網域的做法"
services: active-directory
documentationcenter: 
author: LizCasey
manager: femila
editor: 
ms.assetid: cf3523bd-9ee0-439e-963d-ccea038867b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
robots: NOINDEX
ms.openlocfilehash: 8f5a4f8afcd5db42cd0f066602f9e2e8ee5ea940
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>管理 Azure Active Directory 中的自訂網域名稱
網域名稱可以是許多目錄資源的重要識別碼，其屬於：

* 使用者的使用者名稱或電子郵件地址
* 群組的位址
* 應用程式的應用程式識別碼 URI

Azure Active Directory (Azure AD) 中的資源可包含已驗證為目錄 (其中含有資源) 所擁有的網域名稱。 只有全域管理員可以在 Azure AD 中執行網域管理工作。

> [!IMPORTANT]
> Microsoft 建議您使用 Azure 入口網站中的 [Azure AD 系統管理中心](https://aad.portal.azure.com)來管理 Azure AD，而不要使用本文所提及的 Azure 傳統入口網站。 如需如何在 Azure AD 系統管理中心管理網域名稱的相關資訊，請參閱[在 Azure Active Directory 中管理自訂網域名稱](active-directory-domains-manage-azure-portal.md)。

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>設定 Azure AD 目錄的主要網域名稱
在您建立目錄時，初始網域名稱也是目錄的主要網域名稱，例如 'contoso.onmicrosoft.com'。 當您在 [Azure 傳統入口網站](https://manage.windowsazure.com/)或其他入口網站 (例如 Office 365 管理入口網站) 建立新的使用者時，主要網域即為新使用者的預設網域名稱。 這可以簡化系統管理員在入口網站中建立新使用者的程序。

若要變更目錄的主要網域名稱：

1. 使用屬於 Azure AD目錄全域管理員的使用者帳戶登入 [Azure 傳統入口網站](https://manage.windowsazure.com/) 。
2. 在左側的導覽列上選取 [Active Directory]  。
3. 開啟您的目錄。
4. 選取 [網域]  索引標籤。
5. 選取命令列上的 [變更主要網域]  按鈕。
6. 選取您想要設為目錄之新主要網域的網域。

您可以將目錄的主要網域名稱變更為任何已驗證的非同盟自訂網域。 變更目錄的主要網域時，並不會變更任何現有使用者的使用者名稱。

## <a name="add-custom-domain-names-to-your-azure-ad"></a>新增 Azure AD 的自訂網域名稱
您可以為每個 Azure AD 目錄新增最多 900 個自訂網域名稱。 [新增其他自訂網域名稱](active-directory-add-domain.md) 的程序與新增第一個自訂網域名稱相同。

## <a name="add-subdomains-of-a-custom-domain"></a>新增自訂網域的子網域
如果您想要將第三層網域名稱新增至您的目錄，例如 'europe.contoso.com'，則應先新增並驗證第二層網域，例如 contoso.com。Azure AD 會自動驗證子網域。 若要查看您剛才加入的子網域是否已通過驗證，請重新整理列出您目錄網域的瀏覽器頁面。

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>如果您變更您的自訂網域名稱的 DNS 註冊機構，該怎麼辦
如果您變更自訂網域名稱的 DNS 註冊機構，仍可以繼續在 Azure AD 中使用自訂網域名稱而不會中斷，且不需要其他組態工作。 如果您的自訂網域名稱搭配使用 Office 365、Intune 或仰賴 Azure AD 自訂網域名稱的其他服務，則請參閱這些服務的文件。

## <a name="delete-a-custom-domain-name"></a>刪除自訂網域名稱
如果貴組織不再使用該網域名稱，或如果您需要在另一個 Azure AD 中使用該網域名稱，您可以從您的 Azure AD 刪除自訂網域名稱。

若要刪除自訂網域名稱，您必須先確定目錄中沒有任何資源仰賴該網域名稱。 如果有下列情況，即無法刪除目錄的網域名稱：

* 有任何使用者的使用者名稱、電子郵件地址或 Proxy 位址包含該網域名稱。
* 有任何群組的電子郵件地址或 Proxy 位址包含該網域名稱。
* Azure AD 中任何應用程式的應用程式識別碼 URI 包括該網域名稱。

您必須變更或刪除 Azure AD 目錄中任何這類資源，才能刪除自訂網域名稱。

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>使用 PowerShell 或圖形 API 來管理網域名稱
在 Azure Active Directory 中，大部分的網域名稱管理工作也都可以使用 Microsoft PowerShell 來完成，或使用 Azure AD 圖形 API 以程式設計方式來完成。

* [使用 PowerShell 管理 Azure AD 中的網域名稱](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [使用圖形 API 管理 Azure AD 中的網域名稱](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>後續步驟
* [了解 Azure AD 中的網域名稱](active-directory-add-domain-concepts.md)
* [管理自訂網域名稱](active-directory-add-manage-domain-names.md)

