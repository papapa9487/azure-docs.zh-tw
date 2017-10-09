---
title: "使用 Azure 管理群組來組織資源 - Azure | Microsoft Docs"
description: "了解管理群組及如何使用它們。"
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 18541c68b02ae1b59ae4a6a85122dff614c9978c
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---


# <a name="organize-your-resources-with-azure-management-groups"></a>使用 Azure 管理群組來組織資源 

如果您有多個訂用帳戶，您可以將它們組織到稱為「管理群組」的容器中，以協助您管理所有訂用帳戶的存取、原則、成本及合規性。 舉例來說，您可以將原則套用到管理群組，以限制可建立的資源類型。

> [!Note]
> 此功能目前為私人預覽版。 請[在這裡註冊](https://aka.ms/MGPreviewSignup)，以讓您的註冊加入預覽版。   
 


管理群組可以組織成階層。 顯示的結構是可能存在之管理群組階層的範例表示法：


![階層樹狀結構](media/billing-enterprise-mgmt-groups/tree.png)



## <a name="how-management-groups-are-related-to-your-azure-enterprise-enrollment"></a>管理群組與您的 Azure 企業版註冊如何相關

管理群組的導入是將[企業版入口網站](https://ea.azure.com)功能轉換至 [Azure 入口網站](https://portal.azure.com)之較大過程中的一個步驟。

管理群組結構是依據它在企業版入口網站中的定義建立的。 由註冊、部門及帳戶所組成的整個階層會與對應的管理群組對應。 

以下是目前 EA 結構與管理群組階層的對應方式。 

![階層樹狀結構](media/billing-enterprise-mgmt-groups/tree2.png)

下表顯示企業版入口網站使用者與管理群組階層的對應方式。

|    EA 角色                                       |    所對應管理群組節點上的角色    |    管理群組節點上的權限                                                          |
|--------------------------------------------------|--------------------------------------------------|----------------------------------------------------------------------------------------------------|
|    EA 系統管理員                              |    資源原則參與者                   |    可以在註冊節點及其底下檢視成本、管理資源原則和檢視階層    |
|    唯讀模式的 EA 系統管理員            |    帳單讀取器                                |    可以在註冊節點及其底下讀取成本和檢視階層                              |
|    部門系統管理員                      |    帳單讀取器                                |    可以在部門節點及其底下讀取成本和檢視階層                                 |
|    唯讀模式的部門系統管理員    |    帳單讀取器                                |    可以在部門節點及其底下讀取成本和檢視階層                                 |
|    帳戶擁有者                                 |    資源原則參與者                   |    可以在帳戶節點及其底下檢視成本、管理資源原則和檢視階層       |




## <a name="view-management-groups-in-the-azure-portal"></a>檢視 Azure 入口網站中的管理群組

若要檢視預覽版內的註冊、部門或帳戶，請透過歡迎電子郵件中的連結登入 Azure 入口網站。   

![階層](media/billing-enterprise-mgmt-groups/hierarchy.png)

### <a name="viewing-costs"></a>檢視成本 
在管理群組的詳細資料畫面上，您會看到目前的月初迄今成本。 這些成本根據的是使用量，並不包括預付金額、超額部分、包含的數量、調整內容和稅金。 針對與註冊對應的管理群組，成本區段會顯示剩餘的承諾用量。  

![註冊詳細資料](media/billing-enterprise-mgmt-groups/enrollment.png)

 「分別計費的成本」是個別費用 (例如市集、超額部分，以及其他不包含在註冊承諾用量中的成本) 的每月累計。  如需有關成本明細的詳細資訊，請參閱 [企業版入口網站](https://ea.azure.com)。 

### <a name="enabling-access-to-costs"></a>啟用成本存取權
如果您看不到成本，請參閱我們的[針對企業版成本檢視進行疑難排解](https://aka.ms/enableazurecosts) (英文) 文件來取得說明。  

### <a name="delays-between-the-enterprise-portal-and-azure-portal"></a>企業版入口網站與 Azure 入口網站之間的延遲 
* 在預覽版期間，與企業版入口網站中的值相比，Azure 入口網站中顯示的金額可能會有所延遲。 此問題是暫時的，目前正在處理中。
* 企業版入口網站中的設定更新後，在 Azure 入口網站中會延遲幾分鐘才反映該更新。 

## <a name="management-groups-have-a-relationship-with-your-directory"></a>管理群組與您的目錄有關聯性   
與訂用帳戶類似，管理群組與 Azure AD 也有信任關係。 管理群組階層會信任單一目錄來驗證使用者。 與某個管理群組階層關聯的所有管理員都必須屬於相同的目錄。 

由於您的註冊階層會對應到管理群組，因此會與單一目錄建立信任關係。 可能的話，會選取一個與註冊的使用者帳戶關聯的現有目錄。 在某些情況下，會建立一個新目錄，並將所有現有的註冊使用者都邀請到該目錄中。 與註冊的訂用帳戶關聯的目錄不會受影響。 因此，可能會在與訂用帳戶不同的目錄中建立階層。 [深入了解](billing-enterprise-mgmt-grp-find.md)此程序如何影響階層與其訂用帳戶之間的瀏覽體驗。

## <a name="administering-your-management-groups"></a>管理您的管理群組
Azure 入口網站中的管理群組目前為預覽版，並且在這個初始版本中為唯讀狀態。 若要進行任何更新，請移至企業版入口網站。 Azure 入口網站中會自動反映您的更新。 如需有關進行編輯和新增的說明，請參閱企業入口網站中的文件。   

## <a name="policy-management"></a>原則管理
Resource Manager 可讓您建立自訂的原則，以便管理您的資源。 有了管理群組，便可以在階層中的任何層級指派原則，而資源會繼承這些原則。  [深入了解](https://go.microsoft.com/fwlink/?linkid=858942)

> [!Note]
> 原則不會跨目錄強制執行。 



