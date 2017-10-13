---
title: "尋找 Azure 訂用帳戶或管理群組 - Azure | Microsoft Docs"
description: "如何在多個目錄之間瀏覽以查看您的管理群組和訂用帳戶"
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.openlocfilehash: df4d85d0556a62311c112f24431b54d042333c7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="find-an-azure-subscription-or-management-group"></a>尋找 Azure 訂用帳戶或管理群組

如果您在 Azure 中找不到訂用帳戶或管理群組，可能是所查看的目錄錯誤。 當您的帳戶存在於多個 Azure Active Directory.中時，就可能發生這種情況。 每個 [Active Directory 都是獨立的](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-directory-independence)，並不會跨目錄繼承存取權。      

![切換目錄功能表](media/billing-enterprise-mgmt-groups/mgempty.png)



## <a name="switch-directories"></a>切換目錄 
您可以在 Azure 入口網站中輕鬆切換目錄。
1.  登入 [Azure 入口網站](https://portal.azure.com)。
2.  在畫面右上角選取您的名稱。 
3.  功能表底部會列出您可以存取的所有目錄。
4.  選取要存取的目錄名稱。 

![切換目錄功能表](media/billing-enterprise-mgmt-groups/switch-directory.png)

## <a name="asset-is-unavailable"></a>資產無法使用？ 
如果您在嘗試存取訂用帳戶或管理群組時收到「此資產無法使用。」錯誤訊息，則表示您不具備可檢視此項目的正確角色。  

![找不到資產](media/billing-enterprise-mgmt-groups/asset-not-found.png)

請連絡訂用帳戶或管理群組的系統管理員，以取得存取權。  
* 針對訂用帳戶，請參考 [Azure 角色型存取控制 (RBAC)](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure) 文件，以取得有關所需角色的說明。
* 針對管理群組，目前無法使用 RBAC 存取，但即將推出。 請連絡您的企業版入口網站系統管理員，以請求指派存取權。   

## <a name="improve-your-experience-with-management-groups-and-subscriptions-in-the-same-directory"></a>將管理群組和訂用帳戶放在相同目錄中來改善體驗 
您可以將訂用帳戶或管理群組轉移到所選擇的目錄中，讓所有項目都存在於相同的位置。  將訂用帳戶和管理群組合併到相同目錄中不僅有助於降低切換目錄的需求，還可允許繼承原則。  


### <a name="transfer-your-subscriptions"></a>轉移訂用帳戶 
您可以將訂用帳戶移到與管理群組關聯的目錄中。 只要請註冊管理員將您的訂用帳戶轉移到目標目錄中的帳戶，即可達到此目的。 若要深入了解請登入[企業版入口網站](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription)。


 






