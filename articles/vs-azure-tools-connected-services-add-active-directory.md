---
title: "使用 Visual Studio 的已連接服務加入 Azure Active Directory | Microsoft Docs"
description: "使用 Visual Studio 的 [加入已連接服務] 對話方塊加入 Azure Active Directory"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.service: active-directory
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: kraigb
ms.translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 5ff7c4ee612fd1261d85870be4d6fcbd66b64735
ms.contentlocale: zh-tw
ms.lasthandoff: 03/27/2017

---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>在 Visual Studio 中使用已連接服務加入 Azure Active Directory
藉由使用 Azure Active Directory (Azure AD)，您便可以針對 ASP.NET MVC Web 應用程式支援「單一登入」(SSO)，或在 Web API 服務中支援「Active Directory 驗證」。 在使用「Azure Active Directory 驗證」的情況下，您的使用者可以從 Azure Active Directory 使用其帳戶來連接到您的 Web 應用程式。 「Azure Active Directory 驗證」搭配 Web API 的優點包括在從 Web 應用程式公開 API 時，可增強資料安全性。 有了 Azure AD，您不必以各自的帳戶和使用者管理作業來管理個別的驗證系統。

## <a name="prerequisites"></a>必要條件
- Azure 帳戶 - 如果您沒有 Azure 帳戶，您可以[申請免費試用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)，或是[啟用您的 Visual Studio 訂閱者權益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)。

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>使用 [已連接服務] 對話方塊來連接到 Azure Active Directory
1. 在 Visual Studio 中，建立或開啟 ASP.NET MVC 專案或 ASP.NET Web API 專案。

1. 從 [方案總管] 中，在 [已連接服務] 節點上按一下滑鼠右鍵，然後從操作功能表中選取 [加入已連接服務]。

1. 在 [已連接服務] 頁面上，選取 [使用 Azure Active Directory 進行驗證]。
   
    ![[已連接服務] 頁面](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. 在 [設定 Azure AD 驗證] 精靈的 [簡介] 頁面上，選取 [下一步]。
   
    ![[簡介] 頁面](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1. 在 [設定 Azure AD 驗證] 精靈的 [單一登入] 頁面上，從 [網域] 下拉式清單中選取一個網域。 網域清單包含 [帳戶設定] 對話方塊中所列的帳戶可存取的所有網域。 或者，如果您找不到所要尋找的網域 (例如 `mydomain.onmicrosoft.com`)，則可以輸入網域名稱。 您可以選擇可建立 Azure Active Directory 應用程式的選項，或是使用來自現有 Azure Active Directory 應用程式的設定。 完成時，選取 [下一步]。
   
    ![[單一登入] 頁面](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)

1. 在 [設定 Azure AD 驗證] 精靈的 [目錄存取] 頁面上，確定已核取 [讀取目錄資料] 選項。 
   
    ![[目錄存取] 頁面](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. 選取 [結束] 來新增必要的組態程式碼和參考，以讓您的專案能夠進行 Azure AD 驗證。 您可以在 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)上看到 Active Directory 網域。

1. Visual Studio 將會顯示一篇[發生什麼事](#how-your-project-is-modified)文章，來說明已如何修改您的專案。 如果您想要檢查是否一切正常，請開啟其中一個已修改的組態檔，然後確認其中包含文章中所提到的設定。 

## <a name="how-your-project-is-modified"></a>您的專案修改方式
當您執行精靈時，Visual Studio 會將 Azure Active Directory 和關聯的參考新增到您的專案。 您專案中的組態檔和程式碼也會進行修改，以加入 Azure AD 支援。 Visual Studio 所做的特定修改視專案類型而定。 如需深入了解 ASP.NET MVC 專案的修改方式，請參閱 [發生什麼事：MVC 專案](http://go.microsoft.com/fwlink/p/?LinkID=513809)。 而對於 Web API 專案，請參閱 [發生什麼事：Web API 專案](http://go.microsoft.com/fwlink/p/?LinkId=513810)。

## <a name="next-steps"></a>後續步驟
* [MSDN 的 Azure Active Directory 論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)
* [Azure Active Directory 文件](https://azure.microsoft.com/documentation/services/active-directory/)
* [部落格文章：Azure Active Directory 簡介 (英文)](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)


