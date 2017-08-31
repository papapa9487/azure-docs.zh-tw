---
title: "解決現有 Azure AD Domain Services 受管理的網域的不相符目錄錯誤 | Microsoft Docs"
description: "了解並解決現有 Azure AD Domain Services 受管理的網域的不相符目錄錯誤"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: ca9ff29f5f91b8d796a29706ab49a82e417d1ecd
ms.contentlocale: zh-tw
ms.lasthandoff: 07/08/2017

---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>解決現有 Azure AD Domain Services 受管理的網域的不相符目錄錯誤
您有使用 Azure 傳統入口網站啟用的現有受管理的網域。 當您瀏覽至新的 Azure 入口網站並檢視受管理的網域時，您會看到下列錯誤訊息：

![不相符目錄錯誤](.\media\getting-started\mismatched-tenant-error.png)

在解決錯誤之前，您無法管理此受管理的網域。


## <a name="whats-causing-this-error"></a>造成這個錯誤的原因是什麼？
這個錯誤是在受管理的網域和在其中啟用的虛擬網路分別屬於兩個不同 Azure AD 租用戶時造成的。 例如，您有名為 'contoso.com' 的受管理的網域，它針對 Contoso 的 Azure AD 租用戶啟用。 不過，在其中啟用受管理的網域的 Azure 虛擬網路屬於 Fabrikam - 不同的 Azure AD 租用戶。

新的 Azure 入口網站 (特別是 Azure AD Domain Services 延擴充) 是建置在 Azure Resource Manager。 在現代的 Azure Resource Manager 環境中，會強制執行特定限制，為資源提供更高的安全性和角色型存取控制 (RBAC)。 為 Azure AD 租用戶啟用 Azure AD Domain Services 是敏感的作業，因為它會導致認證雜湊同步處理至受管理的網域。 這項作業需要您是目錄的租用戶管理員。 此外，您必須具有要在其中啟用受管理的網域之虛擬網路的系統管理權限。 為了讓 RBAC 檢查能夠順利運作，受管理的網域和虛擬網路應該屬於相同 Azure AD 租用戶。

簡單來說，您無法在屬於另一個 Azure AD 租用戶 'fabrikam.com' 擁有之 Azure 訂用帳戶的虛擬網路中，針對 Azure AD 租用戶 'contoso.com' 啟用受管理的網域。 Azure 傳統入口網站未建置在資源管理員平台上，並且不會強制這類限制。

**有效設定**：在此案例中，Contoso 受管理的網域是針對 Contoso Azure AD 租用戶啟用。 受管理的網域會在屬於 Contoso Azure AD 租用戶擁有之 Azure 訂用帳戶的虛擬網路中公開。 因此，受管理的網域以及虛擬網路屬於相同的 Azure AD 租用戶。 此設定有效且完全受到支援。

![有效租用戶設定](./media/getting-started/valid-tenant-config.png)

**不相符租用戶設定**：在此案例中，Contoso 受管理的網域是針對 Contoso Azure AD 租用戶啟用。 但是，受管理的網域會在屬於 Fabrikam Azure AD 租用戶擁有之 Azure 訂用帳戶的虛擬網路中公開。 因此，受管理的網域和虛擬網路分別屬於兩個不同的 Azure AD 租用戶。 此設定是不相符租用戶設定，不受到支援。 虛擬網路必須移至與受管理的網域相同的 Azure AD 租用戶 (也就是 Contoso)。 如需詳細資訊，請參閱[解決方案](#resolution)一節。

![不相符租用戶設定](./media/getting-started/mismatched-tenant-config.png)

因此，在受管理的網域和在其中啟用的虛擬網路分別屬於兩個不同 Azure AD 租用戶的案例中，您會看到這個錯誤。

下列規則適用於資源管理員環境：
- Azure AD 目錄有多個 Azure 訂用帳戶。
- Azure 訂用帳戶有多個資源，例如虛擬網路。
- Azure AD 目錄已啟用單一 Azure AD Domain Services 受管理的網域。
- Azure AD Domain Services 受管理的網域可以在屬於相同 Azure AD 租用戶內之任何 Azure 訂用帳戶的虛擬網路上啟用。


## <a name="resolution"></a>解決方案
您有兩個選項可以用來解決不相符目錄錯誤。 您可以：

- 按一下 [刪除] 按鈕，刪除現有的受管理的網域。 使用 [Azure 入口網站](https://portal.azure.com)重新建立，讓受管理的網域和可用的虛擬網路屬於 Azure AD 目錄。 您必須重新加入新建立的受管理網域，先前的所有機器加入已刪除網域。

- 請連絡 Azure 支援中心，將包含虛擬網路的 Azure 訂用帳戶移至受管理的網域所屬的 Azure AD 目錄。 按一下 [新增支援要求] 並且在支援要求的 [詳細資料] 區段中指定**不相符目錄**。 包含錯誤訊息中提供的資訊作為支援要求的一部分。


## <a name="related-content"></a>相關內容
* [Azure AD Domain Services - 入門指南](active-directory-ds-getting-started.md)
* [疑難排解指南 - Azure AD Domain Services](active-directory-ds-troubleshooting.md)

