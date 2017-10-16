---
title: "為企業成本檢視疑難排解 - Azure | Microsoft Docs"
description: "了解如何解決 Azure 入口網站中與組織成本檢視有關的任何問題。"
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
ms.openlocfilehash: eca1ac9ed51e6c2243be451a074792fbec2840d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-enterprise-cost-views"></a>為企業成本檢視疑難排解 

在企業註冊內，有多個設定可能導致註冊內的使用者無法檢視成本。  如果並未直接向 Microsoft 購買註冊，註冊系統管理員或夥伴會管理這些設定。  本文可協助您了解設定，以及這些設定如何影響註冊。 這些設定與 [Azure RBAC 角色](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure)無關。 

> [!Note]
> 此功能目前為私人預覽版。 請[在這裡註冊](https://forms.office.com/Pages/DesignPage.aspx#FormId=v4j5cvGGr0GRqy180BHbR0YtfU6ham9OsGsPPYdu2xdUNk1BQUwzTkUyOVc5NUpCTFcwR0pIOVFETS4u)，以讓您的註冊加入預覽版。     

## <a name="enabling-access-to-costs"></a>啟用成本存取權

您查看成本資訊時，是否看見「未經授權」訊息或「成本檢視在您的註冊中停用。」 ？![未授權](media/billing-enterprise-mgmt-groups/unauthorized.png)

這可能是下列其中一個原因所造成：

1. 您透過企業夥伴購買 Azure，且該夥伴未發佈價格。 若要發佈價格，請連絡夥伴更新[企業版入口網站](https://ea.azure.com)內的設定。
2. 或者，如果您是 EA Direct 客戶，有幾種可能：
    * 您是帳戶擁有者且您的申請管理員已停用「AO 檢視費用」設定。  
    * 您是部門管理員且您的申請管理員已停用「DA 檢視費用」設定。
    * 請連絡您的註冊管理員以取得存取權。 註冊管理員可以造訪[企業版入口網站](https://ea.azure.com/manage/enrollment)並更新如下所示的設定：

![企業版入口網站設定](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)


## <a name="asset-is-unavailable"></a>資產無法使用？ 
如果您在嘗試存取訂用帳戶或管理群組時收到「此資產無法使用。」錯誤訊息，則表示您不具備可檢視此項目的正確角色。  

![找不到資產](media/billing-enterprise-mgmt-groups/asset-not-found.png)

請連絡訂用帳戶或管理群組的系統管理員，以取得存取權。  
* 針對訂用帳戶，請參考 [Azure 角色型存取控制 (RBAC)](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure) 文件，以取得有關所需角色的說明。
* 針對管理群組，目前無法使用 RBAC 存取，但即將推出。 請連絡您的企業版入口網站系統管理員，以請求指派存取權。   
