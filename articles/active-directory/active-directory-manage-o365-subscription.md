---
title: "在 Azure 中管理 Office 365 訂用帳戶的目錄 | Microsoft Docs"
description: "使用 Azure Active Directory 和 Azure 傳統入口網站管理 Office 365 訂用帳戶目錄"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 746987b7-2dfd-4b35-819d-37c1b65c5c81
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/25/2017
ms.author: curtand
ms.custom: oldportal;it-pro;
ms.openlocfilehash: b520a5e96417fb766a757fabc384a1fc4eb0f14e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="manage-the-directory-for-your-office-365-subscription-in-azure"></a>在 Azure 中管理 Office 365 訂用帳戶的目錄
本文說明如何使用 Azure 傳統入口網站，管理針對 Office 365 訂用帳戶建立的目錄。 您必須是服務管理員或 Azure 訂用帳戶的共同管理員，才能登入 Azure 傳統入口網站。 如果您尚未擁有 Azure 訂用帳戶，您可以立即註冊 [免費 30 天試用版](https://azure.microsoft.com/trial/get-started-active-directory/) ，並使用此連結在 5 分鐘內部署第一個雲端解決方案。 請確認使用您用來登入 Office 365 的工作或學校帳戶來註冊。

> [!IMPORTANT]
> Microsoft 建議您使用 Azure 入口網站中的 [Azure AD 系統管理中心](https://aad.portal.azure.com)來管理 Azure AD，而不要使用本文所提及的 Azure 傳統入口網站。

完成 Azure 訂用帳戶之後，您便可以登入 Azure 傳統入口網站並存取 Azure 服務。 按一下 Active Directory 擴充模組，以便管理用來驗證您 Office 365 使用者的相同目錄。

如果您已經擁有 Azure 訂用帳戶，則管理其他目錄的程序也很簡單。 在此範例中，Michael Smith 可能擁有適用於 Contoso.com 的 Office 365 訂用帳戶。 他也擁有使用其 Microsoft 帳戶 msmith@hotmail.com 所註冊的 Azure 訂用帳戶。 在此情況下，他要管理兩個目錄。

| 訂用帳戶 | Office 365 | Azure |
| --- | --- | --- |
|   顯示名稱 |Contoso |預設 Azure Active Directory (Azure AD) 目錄 |
|   網域名稱 |contoso.com |msmithhotmail.onmicrosoft.com |

他想要在使用 Microsoft 帳戶登入 Azure 時，管理 Contoso 目錄中的使用者身分識別，如此就能啟用 Azure AD 功能，例如多重要素驗證 。 下圖可能有助於說明此程序。

![兩個獨立目錄的管理圖表](./media/active-directory-manage-o365-subscription/AAD_O365_03.png)

在此情況下，這兩個目錄是彼此獨立的。

## <a name="to-manage-two-independent-directories"></a>管理兩個獨立的目錄
為了讓 Michael Smith 能夠在使用 msmith@hotmail.com 登入 Azure 時管理這兩個目錄，他必須完成下列步驟：

> [!NOTE]
> 只有當使用者使用 Microsoft 帳戶登入時，才能完成下列步驟。 如果使用者使用工作或學校帳戶登入，就無法使用 [使用現有的目錄]  選項。 工作或學校帳戶只能透過其主目錄 (也就是儲存工作或學校帳戶，且由公司或學校所擁有的目錄) 進行驗證。
>
>

1. 以 msmith@hotmail.com 身分登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 按一下 [新增]  >  [應用程式服務]  >  [Active Directory]  >  [目錄]  >  [自訂建立]。
3. 按一下 [使用現有的目錄]，然後選取 [我現在已經可以登出]  核取方塊。
4. 以 Contoso.onmicrosoft.com 的全域管理員身分登入 Azure 傳統入口網站 (例如 msmith@contoso.com)。
5. 當系統出現 [搭配使用 Contoso 目錄和 Azure？] 提示時，按一下 [繼續]。
6. 按一下 [ **立即登出**]。
7. 以 msmith@hotmail.com 身分登入 Azure 傳統入口網站。 Contoso 目錄和預設目錄會出現在 Active Directory 延伸模組中。

完成這些步驟之後，msmith@hotmail.com 會成為 Contoso 目錄中的全域管理員。

## <a name="to-administer-resources-as-the-global-admin"></a>以全域管理員身分管理資源
現在我們假設 Jane Doe 需要管理與 msmith@hotmail.com 的 Azure 訂用帳戶相關聯的網站和資料庫資源。 在這麼做之前，Michael Smith 必須先完成下列額外步驟：

1. 使用 Azure 訂用帳戶的服務管理員帳戶登入 [Azure 傳統入口網站](https://manage.windowsazure.com) (在此範例中為 msmith@hotmail.com)。
2. 將訂用帳戶移轉至 Contoso 目錄：按一下 [設定]  >  [訂用帳戶] > 選取訂用帳戶 > [編輯目錄] > 選取 [Contoso (Contoso.com)]。 在移轉過程中，如果有工作或學校帳戶是訂用帳戶的共同管理員，則會移除這類帳戶。
3. 新增 Jane Doe 做為訂用帳戶的共同管理員：按一下 [設定]  >  [系統管理員] > 選取訂用帳戶 > [新增] > 輸入 **JohnDoe@Contoso.com**。

## <a name="next-steps"></a>後續步驟
如需訂用帳戶與目錄間的關聯性詳細資訊，請參閱 [如何將訂用帳戶如何關聯至目錄](active-directory-how-subscriptions-associated-directory.md)。
