---
title: "Azure Active Directory 行動裝置應用程式管理的條件式存取 | Microsoft Docs"
description: "了解 Azure Active Directory 行動裝置應用程式管理的條件式存取如何運作。"
services: active-directory
keywords: "應用程式的條件式存取, Azure AD 條件式存取, 安全存取公司資源, 條件式存取原則"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: spunukol
ms.translationtype: HT
ms.sourcegitcommit: ce0189706a3493908422df948c4fe5329ea61a32
ms.openlocfilehash: c6bc39dc151c80ffe1306464da60a029e54cc6b1
ms.contentlocale: zh-tw
ms.lasthandoff: 09/05/2017

---
# <a name="conditional-access-with-mobile-app-management-in-azure-active-directory"></a>Azure Active Directory 行動裝置應用程式管理的條件式存取  

Azure 入口網站的 Azure Active Directory (Azure AD) 應用程式型條件式存取結合了 Intune 應用程式防護原則，有助於將雲端應用程式的存取權侷限於可支援 Intune 應用程式保護的行動裝置應用程式，例如將 Exchange Online 的存取權侷限於 Outlook 應用程式。 這項支援讓未申請由 Intune MDM 管理的裝置仍然能夠保護公司資料。   

行動裝置應用程式管理條件式存取可和其他原則結合，裝置型條件式存取原則即是一例，如此可讓保護個人和公司裝置資料的方式更具彈性。 

##<a name="before-you-begin"></a>開始之前

本主題假設您已熟悉：

- [Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal.md)的基本概念。

- 如何[設定條件式存取原則](active-directory-conditional-access-azure-portal-get-started.md)。


此外，建議您查看一下 [Azure Active Directory 中條件式存取最佳做法](active-directory-conditional-access-best-practices.md)。  



## <a name="prerequisites"></a>必要條件

1.  建立應用程式型條件式存取原則之前，您必須擁有 Enterprise Mobility + Security 或 Azure Active Directory Premium 訂用帳戶，而且使用者必須獲得 EMS 或 Azure AD 的授權。 
2.  您使用行動裝置應用程式管理原則建立新的條件式存取之前，需先檢閱情節和移轉考量

## <a name="supported-platforms"></a>支援的平台

-   iOS

-   Android

## <a name="approved-client-applications"></a>已核准的用戶端應用程式 

- Microsoft Outlook

- Microsoft SharePoint

- Microsoft OneDrive

- Microsoft Teams

- Microsoft Word

- Microsoft Excel

- Microsoft PowerPoint


## <a name="exchange-online-policy"></a>Exchange Online 原則 

以下情節為使用受核准的應用程式以採取行動裝置應用程式管理原則的條件式存取來存取 Exchange Online。


### <a name="scenario-playbook"></a>情節腳本

此情節假設使用者：

- 將使用 iOS 或 Android 原生郵件應用程式的電子郵件設定為連線到 Exchange

- 收到一封電子郵件，郵件顯示僅能使用 Outlook 應用程式存取

- 透過連結下載應用程式

- 開啟 Outlook 應用程式並使用 Azure AD 認證登入

- 系統提示安裝 Authenticor (iOS) 或公司入口網站 (Android) 以繼續進行

- 安裝應用程式，而且可以返回 Outlook 應用程式繼續進行

- 系統提示註冊裝置

- 能夠存取電子郵件

任何 Intune 應用程式防護原則都會在存取公司資料時啟動，而且可能會提示使用者重新啟動應用程式、使用其他 PIN 碼等 (若有針對應用程式和平台設定)。

### <a name="configuration"></a>組態 

**步驟 1 - 設定 Exchange Online 的 Azure AD 條件式存取原則**

對於此步驟中的條件式存取原則，您需要設定下列元件：

![條件式存取](./media/active-directory-conditional-access-mam/01.png)

1. 條件式存取原則的**名稱**。

2. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。

3. **雲端應用程式：**您必須選取 [Office 365 Exchange Online] 做為雲端應用程式。

    ![條件式存取](./media/active-directory-conditional-access-mam/07.png)

4. **條件：**[條件] 需要設定 [裝置平台] 和 [用戶端應用程式]：

    a. [裝置平台] 請選取 [Android] 和 [iOS]。

    ![條件式存取](./media/active-directory-conditional-access-mam/03.png)

    b. [用戶端應用程式] 需選取 [行動裝置應用程式和桌面應用程式]。

    ![條件式存取](./media/active-directory-conditional-access-mam/04.png)

5. [存取控制項] 必須選取 [需要核准的用戶端應用程式 \(預覽\)\]。

    ![條件式存取](./media/active-directory-conditional-access-mam/05.png)
 

**步驟 2 - 使用 Active Sync (EAS) 設定 Exchange Online 的 Azure AD 條件式存取原則**

對於此步驟中的條件式存取原則，您需要設定下列元件：

![條件式存取](./media/active-directory-conditional-access-mam/06.png)

1. 條件式存取原則的**名稱**。

2. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。


3. **雲端應用程式：**您必須選取 [Office 365 Exchange Online] 做為雲端應用程式。

    ![條件式存取](./media/active-directory-conditional-access-mam/07.png)

4. **條件：**[條件] 需要設定 [用戶端應用程式]。 

    a. [用戶端應用程式] 請選取 [Exchange Active Sync]。

    ![條件式存取](./media/active-directory-conditional-access-mam/08.png)

    b. [存取控制項] 必須選取 [需要核准的用戶端應用程式 \(預覽\)\]。

    ![條件式存取](./media/active-directory-conditional-access-mam/05.png)


**步驟 3 - 設定 iOS 和 Android 用戶端應用程式的 Intune 應用程式防護原則**


![條件式存取](./media/active-directory-conditional-access-mam/09.png)

如需詳細資訊，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。


## <a name="exchange-online-and-sharepoint-online-policy"></a>Exchange Online 和 SharePoint Online 原則

以下情節為使用受核准的應用程式以採取行動裝置應用程式管理原則的條件式存取來存取 Exchange Online 和 SharePoint Online。

### <a name="scenario-playbook"></a>情節腳本

此情節假設使用者：

- 嘗試使用 SharePoint 應用程式連接還有檢視其公司網站

- 嘗試使用與 Outlook 應用程式認證相同的認證登入

- 不需要重新註冊，即可取得資源的存取權


### <a name="configuration"></a>組態

**步驟 1 - 設定 Exchange Online 和 SharePoint Online 的 Azure AD 條件式存取原則**

對於此步驟中的條件式存取原則，您需要設定下列元件：

![條件式存取](./media/active-directory-conditional-access-mam/71.png)

1. 條件式存取原則的**名稱**。

2. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。


3. **雲端應用程式：**您必須選取 [Office 365 Exchange Online] 和 [Office 365 SharePoint Online] 做為雲端應用程式。 

    ![條件式存取](./media/active-directory-conditional-access-mam/02.png)

4. **條件：**[條件] 需要設定 [裝置平台] 和 [用戶端應用程式]：

    a. [裝置平台] 請選取 [Android] 和 [iOS]。

    ![條件式存取](./media/active-directory-conditional-access-mam/03.png)

    b. [用戶端應用程式] 需選取 [行動裝置應用程式和桌面應用程式]。

    ![條件式存取](./media/active-directory-conditional-access-mam/04.png)

5. [存取控制項] 必須選取 [需要核准的用戶端應用程式 \(預覽\)\]。

    ![條件式存取](./media/active-directory-conditional-access-mam/05.png)




**步驟 2 - 使用 Active Sync (EAS) 設定 Exchange Online 的 Azure AD 條件式存取原則**

對於此步驟中的條件式存取原則，您需要設定下列元件：

![條件式存取](./media/active-directory-conditional-access-mam/06.png)

1. 條件式存取原則的**名稱**。

2. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。

3. **雲端應用程式：**您必須選取 [Office 365 Exchange Online] 做為雲端應用程式。 線上 

    ![條件式存取](./media/active-directory-conditional-access-mam/07.png)

4. **條件：**[條件] 需要設定 [用戶端應用程式]：

    a. [用戶端應用程式] 請選取 [Exchange Active Sync]。

    ![條件式存取](./media/active-directory-conditional-access-mam/08.png)

    b. [存取控制項] 必須選取 [需要核准的用戶端應用程式 \(預覽\)\]。

    ![條件式存取](./media/active-directory-conditional-access-mam/05.png)




**步驟 3 - 設定 iOS 和 Android 用戶端應用程式的 Intune 應用程式防護原則**


![條件式存取](./media/active-directory-conditional-access-mam/09.png)

如需詳細資訊，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。


## <a name="mobile-application-management-or-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online 或 SharePoint Online 的行動應用程式管理或相容裝置原則

以下情節為使用受核准的應用程式以採取行動裝置應用程式管理原則或相容裝置原則的條件式存取來存取 Exchange Online。


### <a name="scenario-playbook"></a>情節腳本

此情節假設：
 
- 某些使用者已申請 (無論是否使用公司裝置)

- 未使用應用程式保護的應用程式向 Azure AD 申請和註冊的使用者需要註冊裝置，才能存取資源

- 已使用應用程式保護的應用程式完成申請的使用者不需要重新註冊裝置


### <a name="configuration"></a>組態

**步驟 1 - 設定 Exchange Online 和 SharePoint Online 的 Azure AD 條件式存取原則**

對於此步驟中的條件式存取原則，您需要設定下列元件：

![條件式存取](./media/active-directory-conditional-access-mam/62.png)

1. 條件式存取原則的**名稱**。

2. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。

3. **雲端應用程式：**您必須選取 [Office 365 Exchange Online] 和 [Office 365 SharePoint Online] 做為雲端應用程式。 

     ![條件式存取](./media/active-directory-conditional-access-mam/02.png)

4. **條件：**[條件] 需要設定 [裝置平台] 和 [用戶端應用程式]。 
 
    a. [裝置平台] 請選取 [Android] 和 [iOS]。

    ![條件式存取](./media/active-directory-conditional-access-mam/03.png)

    b. [用戶端應用程式] 需選取 [行動裝置應用程式和桌面應用程式]。

    ![條件式存取](./media/active-directory-conditional-access-mam/04.png)

5. [存取控制項] 需要選取下列項目：

    - [裝置需要標記為合規]

    - [需要經過核准的用戶端應用程式 \(預覽\)\]

    - [需要其中一個選取的控制項]   
 
    ![條件式存取](./media/active-directory-conditional-access-mam/11.png)



**步驟 2 - 使用 Active Sync (EAS) 設定 Exchange Online 的 Azure AD 條件式存取原則**

對於此步驟中的條件式存取原則，您需要設定下列元件：

![條件式存取](./media/active-directory-conditional-access-mam/61.png)

1. 條件式存取原則的**名稱**。

2. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。

3. **雲端應用程式：**您必須選取 [Office 365 Exchange Online] 做為雲端應用程式。 

    ![條件式存取](./media/active-directory-conditional-access-mam/07.png)

4. **條件：**[條件] 需要設定 [用戶端應用程式]。 

    選取 [Exchange Active Sync] 做為 **用戶端應用程式*。

    ![條件式存取](./media/active-directory-conditional-access-mam/08.png)

5. [存取控制項] 必須選取 [需要核准的用戶端應用程式 \(預覽\)\]。
 
    ![條件式存取](./media/active-directory-conditional-access-mam/11.png)




**步驟 3 - 設定 iOS 和 Android 用戶端應用程式的 Intune 應用程式防護原則**


![條件式存取](./media/active-directory-conditional-access-mam/09.png)

如需詳細資訊，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。





## <a name="mobile-application-management-and-compliant-device-policy-for-exchange-online-and-sharepoint-online"></a>Exchange Online 和 SharePoint Online 的行動應用程式管理或合規裝置原則

以下情節為使用受核准的應用程式以採取行動裝置應用程式管理原則及相容裝置原則的條件式存取來存取 Exchange Online。

### <a name="scenario-playbook"></a>情節腳本

此情節假設使用者：
 
-   將使用 iOS 或 Android 原生郵件應用程式的電子郵件設定為連線到 Exchange
-   接收指出存取權要求註冊您裝置的電子郵件
-   下載公司入口網站並登入公司入口網站
-   檢查電子郵件，並且被要求使用 Outlook 應用程式
-   下載 Outlook 應用程式
-   開啟 Outlook 應用程式，並輸入申請時所用的認證
-   使用者即可存取電子郵件

任何 Intune 應用程式防護原則都會在存取公司資料時啟動，而且可能會提示使用者重新啟動應用程式、使用其他 PIN 碼等 (若有針對應用程式和平台設定)。


### <a name="configuration"></a>組態

**步驟 1 - 設定 Exchange Online 和 SharePoint Online 的 Azure AD 條件式存取原則**

對於此步驟中的條件式存取原則，您需要設定下列元件：

![條件式存取](./media/active-directory-conditional-access-mam/62.png)

1. 條件式存取原則的**名稱**。

2. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。

3. **雲端應用程式：**您必須選取 [Office 365 Exchange Online] 和 [Office 365 SharePoint Online] 做為雲端應用程式。 

     ![條件式存取](./media/active-directory-conditional-access-mam/02.png)

4. **條件：**[條件] 需要設定 [裝置平台] 和 [用戶端應用程式]。 
 
    a. [裝置平台] 請選取 [Android] 和 [iOS]。

    ![條件式存取](./media/active-directory-conditional-access-mam/03.png)

    b. [用戶端應用程式] 需選取 [行動裝置應用程式和桌面應用程式]。

    ![條件式存取](./media/active-directory-conditional-access-mam/04.png)

5. [存取控制項] 需要選取下列項目：

    - [裝置需要標記為合規]

    - [需要經過核准的用戶端應用程式 \(預覽\)\]

    - [需要其中一個選取的控制項]   
 
    ![條件式存取](./media/active-directory-conditional-access-mam/11.png)



**步驟 2 - 使用 Active Sync (EAS) 設定 Exchange Online 的 Azure AD 條件式存取原則**

對於此步驟中的條件式存取原則，您需要設定下列元件：

![條件式存取](./media/active-directory-conditional-access-mam/61.png)

1. 條件式存取原則的**名稱**。

2. **使用者和群組**：每個條件式存取原則必須至少選取一個使用者或群組。

3. **雲端應用程式：**您必須選取 [Office 365 Exchange Online] 做為雲端應用程式。 

    ![條件式存取](./media/active-directory-conditional-access-mam/07.png)

4. **條件：**[條件] 需要設定 [用戶端應用程式]。 

    [用戶端應用程式] 請選取 [Exchange Active Sync]。

    ![條件式存取](./media/active-directory-conditional-access-mam/08.png)

5. [存取控制項] 需要選取下列項目：

    - [裝置需要標記為合規]

    - [需要經過核准的用戶端應用程式 \(預覽\)\]

    - [需要所有選取的控制項]   
 
    ![條件式存取](./media/active-directory-conditional-access-mam/64.png)




**步驟 3 - 設定 iOS 和 Android 用戶端應用程式的 Intune 應用程式防護原則**


![條件式存取](./media/active-directory-conditional-access-mam/09.png)

如需詳細資訊，請參閱[使用 Microsoft Intune 保護應用程式和資料](https://docs.microsoft.com/intune-classic/deploy-use/protect-apps-and-data-with-microsoft-intune)。



## <a name="migration-considerations"></a>移轉考量

如果您已在 Azure 傳統入口網站中設定原則，請將這些原則移轉到 Azure 入口網站，因為：


- 在 Azure 傳統入口網站原則和 Azure 入口網站原則中的使用者必須符合這兩項原則的需求 

- 如果您不移轉您現有的原則，將無法實作授與存取權的原則


## <a name="migration-from-the-azure-classic-portal"></a>從 Azure 傳統入口網站移轉

在此情節中： 

- 在 [Azure 傳統入口網站](https://manage.windowsazure.com)中，已設定：

    - SharePoint Online

    ![條件式存取](./media/active-directory-conditional-access-mam/14.png)

    - 裝置型條件式存取原則

    ![條件式存取](./media/active-directory-conditional-access-mam/15.png)

- 您想要在 Azure 入口網站中設定行動應用程式管理條件式存取原則 
 

### <a name="configuration"></a>組態 

- 檢閱您的裝置型條件式存取原則

- 將這些原則移轉到 Azure 入口網站 

- 新增行動應用程式管理條件式存取原則


## <a name="migrating-from-intune"></a>從 Intune 移轉 

在此情節中：

- 在 [Intune](https://portal.azure.com/#blade/Microsoft_Intune/SummaryBlade ) 中，您已設定 Exchange Online 或 SharePoint Online 的行動應用程式管理條件式存取原則

    ![條件式存取](./media/active-directory-conditional-access-mam/15.png)

- 您想要在 Azure 入口網站中移轉為使用行動應用程式管理條件式存取


### <a name="configuration"></a>組態 
 
- 檢閱您的裝置型條件式存取原則

- 將這些原則移轉到 Azure 入口網站 

- 檢閱已在 Intune 中設定的 Exchange Online 或 SharePoint Online 的行動應用程式管理條件式存取原則

- 除了裝置型控制項之外，新增 [需要經過核准的應用程式] 控制項 
 

## <a name="migrating-from-the-azure-classic-portal-and-intune"></a>從 Azure 傳統入口網站和 Intune 移轉

在此情節中：

- 您已設定下列項目：

    - **Azure 傳統入口網站：**裝置型條件式 

    - **Intune：**行動應用程式管理條件式存取原則 
    
- 您想要在 Azure 入口網站中將兩個原則移轉為使用行動應用程式管理條件式存取原則


### <a name="configuration"></a>組態

- 檢閱您的裝置型條件式存取原則

- 將這些原則移轉到 Azure 入口網站 

- 檢閱已在 Intune 中設定的 Exchange Online 或 SharePoint Online 的行動應用程式管理條件式存取原則

- 除了裝置型之外，新增 [需要經過核准的應用程式] 控制項 




## <a name="next-steps"></a>後續步驟

如果您想要知道如何設定條件式存取原則，請參閱[開始使用 Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal-get-started.md)。

如果您已準備好設定您環境的條件式存取原則，請參閱 [Azure Active Directory 中條件式存取的最佳做法](active-directory-conditional-access-best-practices.md)。 
