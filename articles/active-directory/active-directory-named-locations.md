---
title: "Azure Active Directory 中的具名位置 | Microsoft Docs"
description: "透過設定具名位置，您可以避免組織所擁有的 IP 位址針對不可能到達非典型位置的移動風險事件類型，產生誤判。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: da437908509e40386ed23863648bd6956b308186
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="named-locations-in-azure-active-directory"></a>Azure Active Directory 中的具名位置

透過 Azure Active Directory 的具名位置功能，您可以標記組織中信任的 IP 位址範圍。 在您的環境中，您可以在[風險事件](active-directory-reporting-risk-events.md)偵測內容中使用具名位置。 此功能可協助減少「不可能進入非慣用位置」風險事件類型的回報誤判次數。 

## <a name="configuration"></a>組態

若要設定具名位置：

1. 以全域管理員身分登入 [Azure 入口網站](https://portal.azure.com)。

2. 在左窗格中，按一下 [Azure Active Directory]。

    ![左窗格中的 Azure Active Directory 連結](./media/active-directory-named-locations/01.png)

3. 在 [Azure Active Directory] 刀鋒視窗的 [安全性] 區段中，按一下 [條件式存取]。

    ![條件式存取命令](./media/active-directory-named-locations/05.png)


4. 在 [條件式存取] 刀鋒視窗的 [管理] 區段中，按一下 [具名位置]。

    ![具名位置命令](./media/active-directory-named-locations/06.png)


5. 在 [具名位置] 刀鋒視窗上，按一下 [新增位置]。

    ![新增位置命令](./media/active-directory-named-locations/07.png)


6. 在 [新增] 刀鋒視窗上，執行下列動作：

    ![[新增] 刀鋒視窗](./media/active-directory-named-locations/56.png)

    a. 在 [名稱] 方塊中，輸入具名位置的名稱。

    b.這是另一個 C# 主控台應用程式。 在 [IP 範圍] 方塊中，輸入 IP 範圍。 IP 範圍的格式必須為「無類別網域間路由選擇」(CIDR)。  

    c. 按一下 [建立] 。



## <a name="what-you-should-know"></a>您應該知道的事情

**大量更新**：當您建立或更新具名位置時，您可以上傳或下載包含 IP 範圍的 CSV 檔案來進行大量更新。 透過上傳，會將檔案中的 IP 位址新增到清單中，而不是覆寫清單。

![上傳和下載連結](./media/active-directory-named-locations/09.png)


**限制**：您最多可以定義 60 個具名位置，每個位置皆指派一個 IP 範圍。 如果您只設定一個具名位置，則最多可以為該位置定義 500 個 IP 範圍。


## <a name="next-steps"></a>後續步驟

若要深入了解風險事件，請參閱 [Azure Active Directory 風險事件](active-directory-reporting-risk-events.md)。

