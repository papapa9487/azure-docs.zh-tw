---
title: "在 Azure Active Directory 中自訂登入頁面 | Microsoft Docs"
description: "了解如何將公司商標新增到 Azure 登入頁面"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: curtand
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: 6bfce3d4ec243779229cc4f39e1c22149229a66a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>快速入門：在 Azure AD 中將公司商標新增至登入頁面
為了避免混淆，許多公司都想對其管理的所有網站和服務套用一致的外觀與風格。 Azure Active Directory (Azure AD) 是透過讓您利用公司標誌和自訂色彩配置來自訂登入頁面外觀的方式，提供這項功能。 登入頁面是當您登入 Office 365 或其他使用 Azure AD 作為識別提供者的 Web 型應用程式時，所顯示的頁面。 您可以與此頁面互動來輸入您的認證。

> [!NOTE]
> * 公司商標僅在您升級至「進階」或「基本」版本的 Azure AD 後，或擁有 Office 365 授權時才能使用。 若要了解您的授權類型是否支援某功能，請查看 [Azure Active Directory 價格資訊頁面](https://azure.microsoft.com/pricing/details/active-directory/)。
> 
> * Azure Active Directory Premium 和 Basic 版本適用於使用全球 Azure Active Directory 執行個體的中國客戶。 由 21Vianet 在中國提供的 Microsoft Azure 服務目前不支援 Azure Active Directory Premium 和 Basic 版本。 如需詳細資訊，請透過 [Azure Active Directory 論壇](https://feedback.azure.com/forums/169401-azure-active-directory/)與我們連絡。

## <a name="customizing-the-sign-in-page"></a>自訂登入頁面

<!--You can customize the following elements on the sign-in page: <attach image>-->

當使用者存取租用戶特定 URL (例如 [*https://outlook.com/contoso.com*](https://outlook.com/contoso.com)) 時，公司商標自訂會出現在 Azure AD 登入頁面上。

當使用者造訪一般 URL (例如 www.office.com) 時，因為系統不知道使用者是誰，登入頁面不包含公司商標自訂。 使用者輸入其使用者識別碼或選取使用者圖格之後，就會顯示公司商標。

> [!NOTE]
> * 在您已設定商標之 Azure 入口網站的 [網域]  部分，您的網域名稱必須顯示為 [作用中]。 如需詳細資訊，請參閱[新增自訂網域名稱](add-custom-domain.md)。
> * 登入頁面商標不會延續到個人 Microsoft 帳戶的登入頁面。 如果您的員工或商務來賓使用個人 Microsoft 帳戶登入，其登入頁面不會反映貴組織的商標。


### <a name="banner-logo"></a>橫幅標誌 

說明 | 條件約束 | 建議
------- | ------- | ----------
橫幅標誌會顯示在 [登入] 和 [存取面板] 頁面上。<br>在 [登入] 頁面上，標誌在使用者組織確定後才會顯示，這通常是在使用者名稱輸入後。  | 透明 JPG 或 PNG<br>最大高度：36 像素<br>最大寬度：245 像素 | 在這裡使用您組織的標誌。<br>使用透明的映像。 請勿假設背景為白色。<br>請勿在映像中的標誌周圍加上邊框間距，否則您的標誌會看起來不成比例。

### <a name="username-hint"></a>使用者名稱提示   
說明 | 條件約束 | 建議
------- | ------- | ----------
這可自訂使用者名稱欄位中的提示文字。 | Unicode 文字最多 64 個字元<br>僅限純文字 | 如果您希望貴組織以外的來賓使用者登入您的應用程式，建議您不要設定此項目。
            
### <a name="sign-in-page-text"></a>登入頁面文字   
說明 | 條件約束 | 建議
------- | ------- | ----------
這會出現在登入表單底部，可用來提供其他資訊 (例如技術支援中心的電話號碼)，或顯示法律聲明。 | Unicode 文字最多 256 個字元<br>僅純文字 (沒有連結或 HTML 標記)   

### <a name="sign-in-page-image"></a>登入頁面映像  
說明 | 條件約束 | 建議
------- | ------- | ----------
這會出現在登入頁面的背景，錨定至可檢視空間的中心，並且會調整並裁切以填滿瀏覽器視窗。    <br>在手機這類螢幕狹窄的裝置上，不會顯示此映像。<br>載入頁面時，會根據我們的程式碼套用 0.55 不透明度的黑色遮罩至此映像。 | JPG 或 PNG<br>映像尺寸：1920 x 1080 像素<br>檔案大小：&lt; 300 KB | <br>使用沒有強烈主題焦點的映像。 不透明的登入表單隨即出現在映像中央，並且可根據瀏覽器視窗的大小來覆蓋映像的任何部分。<br>檔案大小保持越小越好，以確保快速載入時間。 

### <a name="background-color"></a>背景色彩
說明 | 條件約束 | 建議
------- | ------- | ----------
在低頻寬連線時，此顏色會用來取代背景映像。 | 十六進位格式的 RGB 色彩 (範例：#FFFFFF | 建議使用橫幅標誌的主要顏色或貴組織的代表顏色。

### <a name="show-option-to-remain-signed-in"></a>顯示保持登入的選項
說明 | 條件約束 | 建議
------- | ------- | ----------
Azure AD 登入可讓使用者選擇在關閉並重新開啟其瀏覽器時保持登入。 使用此項目來隱藏該選項。<br>將此設為 [否]，以向您的使用者隱藏此選項。 | &nbsp; | 這不會影響工作階段存留期。<br>SharePoint Online 和 Office 2010 的某些功能取決於能夠選擇此選項以保持登入的使用者。 如果您將此設為隱藏，使用者可能會在登入時看見其他和非預期的提示。

> [!NOTE]
> 所有元素都是選用的。 例如，如果您指定的橫幅標誌沒有任何背景映像，登入頁面會針對目的地網站 (例如，Office 365) 顯示您的標誌和背景映像。

## <a name="add-company-branding-to-your-directory"></a>將公司商標新增到您的目錄

1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [更多服務]，在文字方塊中輸入「使用者和群組」，然後選取 **Enter**。

   ![開啟使用者管理](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)
3. 在 [使用者和群組] 刀鋒視窗上，選取 [公司商標]。
4. 在 [使用者和群組 - 公司商標] 刀鋒視窗上，選取 [編輯] 命令。

    ![編輯自訂商標](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)
5. 修改您想要自訂的元素。 所有元素都是選用的。
6. 按一下 [儲存] 。

您對登入頁面商標所做的任何變更可能最多需要一個小時才會顯示。

## <a name="add-language-specific-company-branding-to-your-directory"></a>將特定語言公司商標新增到您的目錄

1. 使用具備目錄全域管理員身分的帳戶來登入 [Azure AD 管理中心](https://aad.portal.azure.com)。
2. 在文字方塊中選取 [使用者和群組]，然後選取 [Enter]。

   ![開啟使用者管理](./media/active-directory-branding-localize-azure-portal/user-management.png)
3. 在 [使用者和群組] 刀鋒視窗上，選取 [公司商標]。
4. 在 [使用者和群組 - 公司商標] 刀鋒視窗上，選取 [新增語言] 命令。

    ![新增語言特定商標元素](./media/active-directory-branding-localize-azure-portal/add-language.png)
5. 修改您想要自訂的元素。 所有元素都是選用的。
6. 按一下 [儲存] 。

您對登入頁面商標所做的任何變更可能最多需要一個小時才會顯示。

## <a name="next-steps"></a>後續步驟
本快速入門中，您會了解如何將公司商標新增到您的 Azure AD 目錄。 

您可以從 Azure 入口網站使用下列連結在 Azure AD 中設定公司商標。

> [!div class="nextstepaction"]
> [設定公司商標](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 