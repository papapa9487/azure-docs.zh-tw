---
title: "Azure Active Directory 中條件式存取的最佳做法 | Microsoft Docs"
description: "了解您在設定條件式存取原則時應該知道的事件及應避免的動作。"
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
ms.date: 09/07/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: HT
ms.sourcegitcommit: f2ac16c2f514aaa7e3f90fdf0d0b6d2912ef8485
ms.openlocfilehash: fedc72f8fe1ada9a991d417cc77b8ca659589f55
ms.contentlocale: zh-tw
ms.lasthandoff: 09/08/2017

---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Azure Active Directory 中條件式存取的最佳做法

此主題提供與您在設定條件式存取原則時應該知道的事件及應避免的動作相關的資訊。 在閱讀本主題前，您應熟悉 [Azure Active Directory 中條件式存取 (英文)](active-directory-conditional-access-azure-portal.md) 中所列的概念與術語

## <a name="what-you-should-know"></a>您應該知道的事情

### <a name="whats-required-to-make-a-policy-work"></a>讓原則運作的必要條件？

當您建立新的原則時，未選取任何使用者、群組、應用程式或存取控制項。

![雲端應用程式](./media/active-directory-conditional-access-best-practices/02.png)


若要讓您的原則運作，您必須設定下列各項：


|何事           | 方式                                  | 理由|
|:--            | :--                                  | :-- |
|**雲端應用程式** |您需要選取一或多個應用程式。  | 條件式存取原則的目標是要讓您微調授權的使用者如何存取您的應用程式。|
| **使用者和群組** | 您需要選取至少一個使用者或群組，已獲授權存取您選取的雲端應用程式。 | 永遠不會觸發未指派使用者和群組的條件式存取原則。 |
| **存取控制** | 您必須選取至少一個存取控制。 | 原則處理器需要知道滿足您的條件時該怎麼辦。|


除了這些基本需求，在許多情況下，您也應該設定條件。 雖然沒有設定條件時原則也可以運作，但是條件是微調應用程式存取權的驅動因素。


![雲端應用程式](./media/active-directory-conditional-access-best-practices/04.png)



### <a name="how-are-assignments-evaluated"></a>如何評估指派？

所有指派邏輯上都會使用 **AND** 運算子。 如果您已設定多個指派，若要觸發原則，則必須滿足所有的指派。  

如果您需要設定一個位置條件，以套用至從您的組織網路外部進行的所有連線，您可以藉由下列方式達成︰

- 包含**所有位置**
- 排除**所有信任的 IP**

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>如果您已在 Azure 傳統入口網站和 Azure 入口網站中設定一些原則，則會發生什麼情況？  
Azure Active Directory 會強制執行這兩個原則，而且只有在符合所有需求時，使用者才可取得存取權。

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>如果您已在 Intune Silverlight 入口網站和 Azure 入口網站中設定一些原則，則會發生什麼情況？
Azure Active Directory 會強制執行這兩個原則，而且只有在符合所有需求時，使用者才可取得存取權。

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>如果我已針對同一個使用者設定多個原則，則會發生什麼情況？  
針對每次登入，Azure Active Directory 會評估所有的原則，並確保在授與使用者存取權之前已符合所有的需求。


### <a name="does-conditional-access-work-with-exchange-activesync"></a>條件式存取是否適用於 Exchange ActiveSync？

是，您可以在條件式存取原則中使用 Exchange ActiveSync。


## <a name="what-you-should-avoid-doing"></a>您應該避免做的事

條件式存取架構可為您提供絕佳的組態彈性。 不過，絕佳的彈性也意謂著您應該先仔細地檢閱每個組態原則，然後才發行它，以避免產生不想要的結果。 在此情況下，您應該特別注意影響整個集合的指派，例如 **all users / groups / cloud apps**。

在您的環境中，應該避免使用下列組態：


**針對所有使用者、所有雲端應用程式：**

- **封鎖存取** - 此組態會封鎖您整個組織，這絕對不是一個好方法。

- **需要符合規範的裝置** - 針對尚未註冊其裝置的使用者，此原則會封鎖所有存取，包括對 Intune 入口網站的存取。 如果您是沒有已註冊裝置的系統管理員，此原則會阻擋您回到 Azure 入口網站來變更此原則。

- **需要加入網域** - 如果您還沒有已加入網域的裝置，此原則也可能封鎖您組織中所有使用者的存取。


**針對所有使用者、所有雲端應用程式、所有裝置平台：**

- **封鎖存取** - 此組態會封鎖您整個組織，這絕對不是一個好方法。



## <a name="policy-migration"></a>原則移轉

如果您已在 Azure 傳統入口網站中設定原則，請將這些原則移轉到 Azure 入口網站，因為：


- 在 Azure 傳統入口網站原則和 Azure 入口網站原則中的使用者必須符合這兩項原則的需求 

- 如果您不移轉您現有的原則，將無法實作授與存取權的原則


### <a name="migration-from-the-azure-classic-portal"></a>從 Azure 傳統入口網站移轉

在此情節中： 

- 在 [Azure 傳統入口網站](https://manage.windowsazure.com)中，已設定：

    - SharePoint Online

    ![條件式存取](./media/active-directory-conditional-access-best-practices/14.png)

    - 裝置型條件式存取原則

    ![條件式存取](./media/active-directory-conditional-access-best-practices/15.png)

- 您想要在 Azure 入口網站中設定行動應用程式管理條件式存取原則 
 

#### <a name="configuration"></a>組態 

- 檢閱您的裝置型條件式存取原則

- 將這些原則移轉到 Azure 入口網站 

- 新增行動應用程式管理條件式存取原則


### <a name="migrating-from-intune"></a>從 Intune 移轉 

在此情節中：

- 在 [Intune](https://portal.azure.com/#blade/Microsoft_Intune/SummaryBlade ) 中，您已設定 Exchange Online 或 SharePoint Online 的行動應用程式管理條件式存取原則

    ![條件式存取](./media/active-directory-conditional-access-best-practices/15.png)

- 您想要在 Azure 入口網站中移轉為使用行動應用程式管理條件式存取


#### <a name="configuration"></a>組態 
 
- 檢閱您的裝置型條件式存取原則

- 將這些原則移轉到 Azure 入口網站 

- 檢閱已在 Intune 中設定的 Exchange Online 或 SharePoint Online 的行動應用程式管理條件式存取原則

- 除了裝置型控制項之外，新增 [需要經過核准的應用程式] 控制項 
 

### <a name="migrating-from-the-azure-classic-portal-and-intune"></a>從 Azure 傳統入口網站和 Intune 移轉

在此情節中：

- 您已設定下列項目：

    - **Azure 傳統入口網站：**裝置型條件式 

    - **Intune：**行動應用程式管理條件式存取原則 
    
- 您想要在 Azure 入口網站中將兩個原則移轉為使用行動應用程式管理條件式存取原則


#### <a name="configuration"></a>組態

- 檢閱您的裝置型條件式存取原則

- 將這些原則移轉到 Azure 入口網站 

- 檢閱已在 Intune 中設定的 Exchange Online 或 SharePoint Online 的行動應用程式管理條件式存取原則

- 除了裝置型之外，新增 [需要經過核准的應用程式] 控制項 












## <a name="common-scenarios"></a>常見案例

### <a name="requiring-multi-factor-authentication-for-apps"></a>應用程式需要 Multi-Factor Authentication

許多環境中的應用程式需要比其他應用程式更高層級的保護。
例如，有權存取敏感性資料的應用程式。
如果您想要對這些應用程式新增另一層的保護，您可以設定當使用者存取這些應用程式時需要 Multi-Factor Authentication 的條件式存取原則。


### <a name="requiring-multi-factor-authentication-for-access-from-networks-that-are-not-trusted"></a>從不受信任的網路存取時需要 Multi-Factor Authentication

此案例類似於前一個案例，因為這會新增 Multi-Factor Authentication 的需求。
不過，主要差異在於這項需求的條件。  
雖然前一個案例的焦點在於有權存取敏感性資料的應用程式，而此案例的焦點則在於信任的位置。  
換句話說，如果使用者從您不信任的網路存取應用程式，您可能需要 Multi-Factor Authentication。


### <a name="only-trusted-devices-can-access-office-365-services"></a>只有受信任的裝置可以存取 Office 365 服務

如果您在自己的環境中使用 Intune，您可以在 Azure 主控台中立即開始使用條件式存取原則介面。

許多 Intune 客戶都使用條件式存取來確保只有受信任的裝置可以存取 Office 365 服務。 這表示行動裝置已向 Intune 進行註冊並符合合規性原則需求，而且 Windows 電腦已加入內部部署網域。 您不需要為每項 Office 365 服務設定相同的原則，是一項重大改進。  當您建立新原則時，請設定雲端應用程式包含您想要使用條件式存取保護的每個 O365 應用程式。

## <a name="next-steps"></a>後續步驟

如果您想要知道如何設定條件式存取原則，請參閱[開始使用 Azure Active Directory 中的條件式存取](active-directory-conditional-access-azure-portal-get-started.md)。

