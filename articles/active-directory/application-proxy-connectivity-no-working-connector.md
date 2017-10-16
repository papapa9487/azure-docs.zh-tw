---
title: "針對應用程式 Proxy 應用程式找不到作用中的連接器群組 | Microsoft Docs"
description: "解決在具有 Azure AD 應用程式 Proxy 之應用程式的連接器群組中沒有作用中的連接器時可能會遇到問題"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 4945958deedc8a1d9989ff901192c03a5363b4dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>針對應用程式 Proxy 應用程式找不到作用中的連接器群組

本文協助您解決針對與 Azure Active Directory 整合的應用程式 Proxy 應用程式無法偵測到連接器時所需面對的常見問題。

## <a name="overview-of-steps"></a>步驟概觀
如果您應用程式的連接器群組中沒有作用中的連接器，有幾種方法可以解決此問題：

-   如果群組中沒有連接器，您可以：

    -   在正確的內部部署伺服器上下載新的連接器，然後將它指派到此群組

    -   將作用中的連接器移至群組中

-   如果群組中沒有作用中的連接器，您可以：

    -   找出連接器無法作用的原因並加以解決

    -   將作用中的連接器移至群組中

若要確認是哪一個問題，請在您的應用程式中開啟 [應用程式 Proxy] 功能表，然後查看連接器群組警告訊息。 它會指出群組至少需要一個連接器 (您在群組中完全沒有連接器)，或是沒有作用中的連接器 (不過您很可能有非作用中的連接器)。

   ![Azure 入口網站中的連接器群組選項](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

如需每個選項的詳細資料，請參閱下列對應的小節。 這些小節都假設您是從連接器管理頁面開始。 如果您看到上述的錯誤訊息，可以按一下警告訊息來移至此頁面。 或者，您可以移至 [Azure Active Directory]，依序按一下 [企業應用程式]、[應用程式 Proxy] 來找到它。

   ![Azure 入口網站中的連接器群組管理](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>下載新的連接器

若要下載新的連接器，請使用頁面頂端的 [下載連接器] 按鈕。

請注意，連接器必須安裝在可直接看到後端應用程式的電腦上，而且通常會置於與應用程式相同的伺服器上。 下載之後，連接器應該會出現在此功能表中。 按一下連接器，然後使用 [連接器群組] 下拉式清單來確定它屬於正確的群組。 儲存變更。

   ![從 Azure 入口網站下載連接器](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>移動作用中的連接器

如果您有應該屬於群組的作用中連接器，而且能直接看到目標後端應用程式，您可以將連接器移入指派的群組。 若要這麼做，請按一下連接器。 在 [連接器群組] 欄位中，使用下拉式清單選取正確的群組，然後按一下 [儲存]。

## <a name="resolve-an-inactive-connector"></a>解決非作用中的連接器

如果在群組中唯一的連接器為非作用中，它們所在的電腦很可能未解除所有必要連接埠的封鎖。

請參閱《連接埠疑難排解》文件，以取得調查此問題的詳細資料。

## <a name="next-steps"></a>後續步驟
[了解 Azure AD 應用程式 Proxy 連接器](application-proxy-understand-connectors.md)


