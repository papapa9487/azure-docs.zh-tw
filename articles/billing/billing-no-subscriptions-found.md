---
title: "嘗試登入 Azure 入口網站或 Azure 帳戶中心時，未在訂用帳戶中找到任何錯誤 | Microsoft Docs"
description: "針對登入 Azure 入口網站或 Azure 帳戶中心時，未在訂用帳戶中找到任何錯誤這個問題，提供解決方案。"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: 7d6f16f9aad41cc2867ce86361fcea2f93aca7c5
ms.contentlocale: zh-tw
ms.lasthandoff: 08/31/2017

---

# <a name="no-subscriptions-found-error-in-azure-portal-or-azure-account-center"></a>在 Azure 入口網站或 Azure 帳戶中心內，未於訂用帳戶中找到任何錯誤

嘗試登入 [Azure 入口網站](https://portal.azure.com/)或 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions)時，您可能會收到「找不到訂用帳戶」錯誤訊息。 本文會提供此問題的解決方案。

## <a name="symptom"></a>徵狀

當您嘗試登入 [Azure 入口網站](https://portal.azure.com/)或 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions)時，會收到下列錯誤訊息：「找不到訂用帳戶」。

## <a name="cause"></a>原因

如果您選取錯誤的目錄或帳戶沒有足夠的權限，就會發生此問題。 

## <a name="solution"></a>方案

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>情節 1：在 [Azure 入口網站](https://portal.azure.com)收到錯誤訊息

若要修正此問題：

* 確定您已選取正確的 Azure 目錄，方法是按一下右上角的帳戶。

  ![選取 Azure 入口網站右上角的目錄](./media/billing-no-subscriptions-found/directory-switch.png)
* 若已選取正確的 Azure 目錄，但您仍收到錯誤訊息，請[將您的帳戶新增為擁有者](billing-add-change-azure-subscription-administrator.md)。

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>案例 2：在 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions)中收到錯誤訊息

請檢查您所使用的帳戶是否為帳戶管理員。 若要確認誰是帳戶管理員，請依照下列步驟操作︰

1. 登入 [Azure 入口網站中的訂用帳戶檢視](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 選取您想要檢查的訂用帳戶，然後查看 [設定]。
1. 選取 [屬性] 。 該訂用帳戶的帳戶管理員會顯示在 [帳戶管理員]  方塊中。  

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。

如果仍需要協助，請[連絡支援人員](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)以快速解決您的問題。 

