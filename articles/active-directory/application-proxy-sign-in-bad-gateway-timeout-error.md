---
title: "使用應用程式 Proxy 應用程式時發生「無法存取此企業應用程式」錯誤 | Microsoft Docs"
description: "如何解決使用 Azure AD 應用程式 Proxy 應用程式時常見的存取問題。"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 78ff8763a461162cbcfa04c6a86123973271928a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>使用 Application Proxy 應用程式時發生「無法存取此企業應用程式」錯誤

這篇文章可協助您為在 Azure AD Application Proxy 應用程式上看到「無法存取此企業應用程式」錯誤時所面臨的常見問題疑難排解。

## <a name="overview"></a>概觀
當您看見此錯誤時，頁面也會顯示狀態碼。 狀態碼可能代表下列其中一種狀態：

-   **閘道逾時**：Application Proxy 服務會無法連線到連接器。 這通常表示連接器指派、連接器本身，或連接器相關的網路規則有問題。

-   **不正確的閘道**︰ 連接器無法連線到後端應用程式。 這表示應用程式的設定不正確。

-   **禁止**︰使用者未獲授權存取應用程式。 當未將使用者指派至 Azure Active Directory 中的應用程式，或者使用者在後端沒有存取應用程式的權限，便會發生此狀態。

若要尋找狀態碼，請在錯誤訊息底端的文字中找到「狀態碼」欄位。 此外也可以在頁面最底端找到任何有其他秘訣的註解。

   ![閘道逾時錯誤](./media/application-proxy/connection-problem.png)

如需如何為這些錯誤的根本原因疑難排解的詳細資訊，以及建議的修正程式詳細資料，請參閱下方對應的章節。

## <a name="gateway-timeout-errors"></a>閘道逾時錯誤

當服務嘗試連線到連接器時，若無法在逾時時間範圍內連線，即發生閘道逾時。 這通常是因為指派至連接器群組的應用程式沒有運作中的連接器，或連接器所需的某些連接埠並未開啟所造成的。


## <a name="bad-gateway-errors"></a>閘道錯誤

不正確的閘道是指連接器無法連線到後端應用程式。 請確定您所發佈的是正確的應用程式。 造成此錯誤的常見過失︰

-   打字錯誤或內部 URL 不正確

-   未發佈應用程式的根目錄。 例如，發佈的是 <http://expenses/reimbursement>，但嘗試存取 <http://expenses>

-   Kerberos 限制委派 (KCD) 組態有問題

-   後端應用程式有問題

## <a name="forbidden-errors"></a>禁止錯誤

如果您看到禁止錯誤，則表示使用者尚未指派至該應用程式。 這可能發生在 Azure Active Directory 或後端應用程式。

若要了解如何將使用者指派至 Azure 中的應用程式，請參閱[組態文件](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal#add-a-test-user)。

如果您確認使用者已指派至 Azure 中的應用程式，請在後端應用程式中檢查使用者組態。 如果您使用的是 Kerberos 限制委派/整合式 Windows 驗證，您可以在我們的 [KCD 疑難排解] 頁面上看到部分指導方針。

## <a name="check-the-applications-internal-url"></a>檢查應用程式的內部 URL

第一個快速步驟就是反覆檢查內部 URL 並加以修正，做法是透過 [企業應用程式] 開啟應用程式，然後選取 [Application Proxy] 功能表。 確認內部 URL 正確無誤，亦即從您的內部網路用於存取應用程式的 URL。

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>確認應用程式已指派至運作中的連接器群組

若要確認應用程式已指派至運作中的連接器群組：

1.  移至 [Azure Active Directory]，然後依序按一下 [企業應用程式]、[所有應用程式]，以開啟入口網站中的應用程式。 開啟應用程式，然後選取左側功能表中的 [Application Proxy]。

2.  找到 [連接器群組] 欄位。 如果群組中沒有作用中的連接器，您會看到一則警告。 如果您沒有看到任何警告，請進一步「確認所有必要的連接埠皆在允許清單中」。

3.  如果這是錯誤的連接器群組，請使用下拉清單選取正確的群組，並確認未再出現任何警告。 如果這是所需的連接器群組，請按一下警告訊息以開啟含連接器管理資訊的頁面。

4.  此頁面提供數種方法可進一步向下切入：

  * 將作用中連接器移至群組中：如果您有應該屬於群組的作用中連接器，而且能直接看到目標後端應用程式，您就可以將連接器移入指派的群組。 若要這麼做，請按一下連接器。 在 [連接器群組] 欄位中，使用下拉式清單選取正確的群組，然後按一下 [儲存]。

  * 下載該群組的新連接器：此頁面上有連結可讓您[下載新連接器](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download)。 連接器必須安裝在可直接看到後端應用程式的電腦上，而且通常會位於與應用程式相同的伺服器上。 使用下載連接器連結，將連接器下載到目標電腦上。 接著按一下連接器，然後使用 [連接器群組] 下拉式清單確定它屬於正確的群組。

  * 調查非作用中連接器︰如果連接器會顯示為非作用中，則表示它無法連線到服務。 這通常是因為某些必要連接埠遭到封鎖造成的。 若要解決此問題，請進一步「確認所有必要的連接埠皆在允許清單中」。

在使用這些步驟確認應用程式已指派至有運作中連接器的群組後，請再次測試應用程式。 如果仍然無法運作，繼續進行下一節。

## <a name="check-all-required-ports-are-whitelisted"></a>檢查所有必要連接埠皆在允許清單中

若要確認所有必要連接埠皆已開啟，請參閱我們有關開啟連接埠的文件。 如果所有必要連接埠皆已開啟，請移至下一節。

## <a name="check-for-other-connector-errors"></a>檢查有無其他連接器錯誤

如果上述方法都無法解決此問題，下一步就是找出連接器本身的問題或錯誤。 您可以在[疑難排解文件](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors)中看見一些常見錯誤。 

您也可以直接查看連接器記錄檔，以找出任何錯誤。 我們的錯誤訊息中有許多能夠分享更具體的修正程式建議。 若要了解如何檢視記錄檔，請參閱[連接器文件](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors#under-the-hood)。

## <a name="additional-resolutions"></a>其他解決方式

如果上述方法皆無法修正問題，有幾個不同的可能原因。 若要找出問題︰

如果您的應用程式是設定為使用整合式 Windows 驗證 (IWA)，請在未單一登入的情況下測試應用程式。 如果不是，請移至下一段。 若要在未單一登入的情況下檢查應用程式，請透過 [企業應用程式] 開啟您的應用程式，然後移至 [單一登入] 功能表。 將下拉式清單從 [整合式 Windows 驗證] 變更為 [Azure AD single sign-on disabled (Azure AD 單一登入已停用)]。 

現在開啟瀏覽器，然後再次嘗試存取應用程式。 系統應該會提示您輸入驗證並進入應用程式。 如果可進入，則問題出在啟用單一登入的 Kerberos 限制委派 (KCD) 組態。 請參閱 [KCD 疑難排解] 頁面。

如果您繼續看到此錯誤，請移到已安裝連接器的電腦，開啟瀏覽器，並嘗試連線到應用程式使用的內部 URL。 連接器就像同一台電腦中的另一個用戶端。 如果您無法連線到應用程式，請調查為何該電腦無法連線到應用程式，或使用能夠存取應用程式之伺服器上的連接器。

如果您可以從電腦連線到應用程式，請找出連接器本身的問題或錯誤。 您可以在[疑難排解文件](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors)中看見一些常見錯誤。 您也可以直接查看連接器記錄檔，以找出任何錯誤。 我們的錯誤訊息中有許多能夠分享更具體的修正程式建議。 若要了解如何檢視記錄檔，請參閱[連接器文件](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors#under-the-hood)。

## <a name="next-steps"></a>後續步驟
[了解 Azure AD 應用程式 Proxy 連接器](application-proxy-understand-connectors.md)
