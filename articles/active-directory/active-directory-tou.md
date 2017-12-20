---
title: "Azure Active Directory 使用規定 | Microsoft Docs"
description: "Azure AD 使用規定可讓您和貴公司提供 Azure AD 服務的使用規定。"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/17/2017
ms.author: billmath
ms.openlocfilehash: a935c3a7a5eeead8eaac5d8d0980c289b17f3289
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-terms-of-use-feature-preview"></a>Azure Active Directory 使用規定特性 (預覽)
Azure AD 使用規定提供一種簡單的方法，組織可用來將資訊呈現給終端使用者。  這確保使用者看到合法或合規性需求的相關免責聲明。

Azure AD 使用規定使用 pdf 格式來呈現內容。   此 pdf 可以是任何內容 (例如現有的合約文件) 可讓您在使用者登入期間收集終端使用者合約。  您可以對應用程式、使用者群組，或基於不同用途 (若您有多個使用規定的話) 使用此使用規定。

本文件的其餘內容說明如何開始使用 Azure AD 使用規定。  

## <a name="why-use-azure-ad-terms-of-use"></a>為何使用 Azure AD 使用規定
發現在取得存取權之前，很難讓員工或來賓同意您的使用規定嗎？ 需要協助以瞭解誰已同意或不同意貴公司的使用規定嗎？  Azure AD 使用規定提供一種簡單的方法，組織可用來將資訊呈現給終端使用者。  這確保他們看到合法或合規性需求的相關免責聲明。

Azure AD 使用規定可以用於下列案例：
-   用於貴組織中所有使用者的一般使用規定。
-   以使用者屬性為基礎的特定使用規定 (例如， 醫生與護士或國內員工與國際員工，由[動態群組](https://azure.microsoft.com/updates/azure-active-directory-dynamic-membership-for-groups)完成)。
-   以存取高商業影響應用程式 (例如 Salesforce) 為基礎的特定使用規定。


## <a name="prerequisites"></a>必要條件
請使用下列步驟來設定 Azure AD 使用規定：

1. 對於您要設定 Azure AD 使用規定的目錄，使用全域管理員、安全性系統管理員或條件式存取系統管理員，登入 Azure AD。
2. 確定該目錄具有 Azure AD Premium P1、P2、EMS E3 或 EMS E5 訂用帳戶。  如果未[取得 Azure AD Premium](active-directory-get-started-premium.md) 或[開始試用](https://azure.microsoft.com/trial/get-started-active-directory/)，
3. 檢視 Azure AD 使用規定儀表板，網址為 [https://aka.ms/catou](https://aka.ms/catou)。

>[!IMPORTANT]
>條件式存取原則控制項 (包括使用條款) 不支援服務帳戶的強制執行。  建議您排除條件式存取原則中的所有服務帳戶。

## <a name="add-company-terms-of-use"></a>新增公司使用規定
一旦完成了您的使用規定，請使用下列程序來新增它。

### <a name="to-add-terms-of-use"></a>新增使用規定
1. 瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 中的儀表板
2. 按一下 [新增]。</br>
![新增 TOU](media/active-directory-tou/tou2.png)
3. 輸入使用規定的**名稱**
4. 輸入**顯示名稱**。  使用者在登入時會看到此標頭。
5. **瀏覽**至您已完成的使用規定 pdf 並加以選取。  建議使用的字型大小是 24。
6. 您可以使用範本或自訂的條件式存取原則，**強制執行**上傳的使用規定。  自訂的條件式存取原則可讓細微使用條款下降到特定雲端應用程式或使用者群組。  如需詳細資訊，請參閱[設定條件式存取原則](active-directory-conditional-access-best-practices.md)
7. 按一下 [建立] 。
8. 如果您已選取自訂的條件式存取範本，則新的畫面會出現，可讓您自訂 CA 原則。
7. 您現在應該看到新的使用規定。</br>

![新增 TOU](media/active-directory-tou/tou3.png)

## <a name="delete-terms-of-use"></a>刪除使用規定
您可以使用下列程序，移除或刪除舊的使用規定：

### <a name="to-delete-terms-of-use"></a>刪除使用規定
1. 瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 中的儀表板
2. 選取您想要移除的使用規定。
3. 按一下 [刪除] 。
4. 您應該再也看不到新的使用規定。


## <a name="audit-terms-of-use"></a>稽核使用規定
Azure AD 使用規定提供了易於使用的稽核，讓您能夠查看誰已接受，以及他們何時接受您的使用條款。  若要開始使用稽核，請使用下列程序：

### <a name="to-audit-terms-of-use"></a>稽核使用規定
1. 瀏覽至 [https://aka.ms/catou](https://aka.ms/catou) 中的儀表板
2. 按一下 [稽核事件]。</br>
![稽核事件](media/active-directory-tou/tou8.png)
3.  在 Azure AD 稽核記錄畫面上，您可以使用提供的下拉式清單，將特定的稽核記錄資訊設為目標來篩選資訊。
![稽核事件](media/active-directory-tou/tou9.png)
4.  您也可以將資訊下載成.csv 檔案，以在本機中使用。

## <a name="what-users-see"></a>使用者看到的內容
一旦建立並強制執行使用規定，範圍內的使用者將會看到下列內容。  他們將在登入期間看到這些畫面。
-   最佳作法是在 PDF 內使用大小為 24 的字型。
![稽核事件](media/active-directory-tou/tou10.png)
-   此畫面是它在行動裝置上的顯示方式</br></br>
![稽核事件](media/active-directory-tou/tou11.png)

## <a name="additional-information"></a>其他資訊
下列是需要留意並可協助使用此使用條款的資訊。

如果情況如下，範圍中的使用者必須登出並登入，才能符合新的原則：
 - 對使用規定啟用條件式存取原則
 - 或建立第二個使用條款

原因是條件式存取原則會立即生效。 當此情況發生時，系統管理員便會開始看到 「悲傷雲端」或「Azure AD 權杖問題」。 系統管理員必須登出並再次登入，才能符合新的原則。





## <a name="frequently-asked-questions"></a>常見問題集

**問：如何查看使用者何時或是否已接受使用規定？**</br>
答：接受使用規定的使用者會寫入至稽核記錄。 您可以搜尋 Azure AD 稽核記錄來查看結果。  

**問：如果變更使用規定，是否需要使用者重新接受？**</br>
答：是，系統管理員可以變更使用規定，而且其需要重新接受新規定。

**問：使用規定是否支援多種語言？**</br>
答：否，目前單一使用規定中不能具有多種語言。  不過，您可以設定群組的範圍 (例如，法國的使用規定與英國的使用條款不同)。 

**問：何時觸發使用規定？**</br>
答：登入體驗期間即會觸發使用規定。

**問：哪些應用程式也可以將使用規定設為目標？**</br>
答：您可以使用新式驗證，在企業應用程式上建立條件式存取原則。  如需詳細資訊，請參閱[企業應用程式](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-view-azure-portal)。

**問：是否可以將多個使用規定新增至指定的使用者或應用程式？**</br>
答：是，方法為建立多個條件式存取原則，將群組或應用程式設為目標。 如果使用者落在多個使用條款的範圍內，則他們一次同意一個使用規定。
 
**問：如果使用者拒絕使用規定，會發生什麼事？**</br>
答：使用者會遭封鎖而無法存取應用程式。 使用者必須再次登入並同意規定，才能取得存取權。