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
ms.date: 08/21/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: a4ce9b219c05f8469379c2aac5241fcfffd16033
ms.contentlocale: zh-tw
ms.lasthandoff: 08/24/2017

---

# <a name="no-subscriptions-found-error-in-azure-portal-or-azure-account-center"></a>在 Azure 入口網站或 Azure 帳戶中心內，未於訂用帳戶中找到任何錯誤
當您嘗試登入 [Azure 入口網站](https://portal.azure.com/)或 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions)時，可能會收到「找不到訂用帳戶」的錯誤訊息。 本文會提供此問題的解決方案。

## <a name="symptom"></a>徵狀

當您嘗試登入 [Azure 入口網站](https://portal.azure.com/)或 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions)時，會收到下列錯誤訊息：「找不到訂用帳戶」。

## <a name="cause"></a>原因

如果您的帳戶沒有足夠的權限，就會發生這個問題。 

## <a name="solution"></a>方案

請確定您是以正確的系統管理員身分登入。 帳戶管理員只能存取帳戶中心。 服務管理員 (SA) 和共同管理員 (CA) 只有存取 Azure 入口網站或 Azure 傳統入口網站的權限。

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>情節 1：在 [Azure 入口網站](https://portal.azure.com)收到錯誤訊息

若要修正此問題：

* 確定您已選取正確的 Azure 目錄，方法是按一下右上角的帳戶。

  ![選取 Azure 入口網站右上角的目錄](./media/billing-no-subscriptions-found/directory-switch.png)

* 若已選取正確的 Azure 目錄，但您仍收到錯誤訊息，請[將您的帳戶新增為擁有者](billing-add-change-azure-subscription-administrator.md)。

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>情節 2：在 [Azure 帳戶中心](https://account.windowsazure.com/Subscriptions)收到錯誤訊息

請檢查您所使用的帳戶是否為帳戶管理員。 若要確認誰是帳戶管理員，請依照下列步驟操作︰

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [中樞] 功能表中，選取 [訂用帳戶] 。
3. 選取您想要檢查的訂用帳戶，然後選取 [設定] 。
4. 選取 [屬性] 。 該訂用帳戶的帳戶管理員會顯示在 [帳戶管理員]  方塊中。

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。
如果仍需要協助，請[連絡支援人員](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)以快速解決您的問題。 

