---
title: "管理雙步驟驗證設定 | Microsoft Docs"
description: "管理您使用 Azure Multi-Factor Authentication 的方式，包括變更您的連絡資訊或是設定您的裝置。"
services: multi-factor-authentication
keywords: "多重要素驗證用戶端, 驗證的問題, 相互關聯識別碼"
documentationcenter: 
author: barlanmsft
manager: femila
editor: yossib
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 2328fe6e6b35ce523f8bf90cbe6159c7ba23d697
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-your-settings-for-two-step-verification"></a>管理您的雙步驟驗證設定
本文章回答有關如何更新雙步驟驗證或 Multi-Factor Authentication 的設定。 如果您登入帳戶時遇到問題，請參閱[雙步驟驗證遇到困難](multi-factor-authentication-end-user-troubleshoot.md)以取得疑難排解說明。

## <a name="where-to-find-the-settings-page"></a>哪裡可以找到設定頁面
視您的公司使用 Azure Multi-Factor Authentication 的方式而定，有一些地方可讓您變更如電話號碼等設定。

如果您的 IT 管理員送出特定的 URL 或步驟來管理雙步驟驗證，請遵循這些指示。 否則，下面的指示應可適用於其他所有人。 如果您遵循下列步驟，但沒有看到相同的選項，表示您的工作或學校自訂了自己的網站。 請要求系統管理員提供您的 Azure Multi-factor Authentication 入口網站連結。

1. 登入 [https://myapps.microsoft.com](https://myapps.microsoft.com)  
2. 在右上方選取您的帳戶名稱，然後選取 [設定檔]。  
3. 選取 [其他安全性驗證]。  

    ![Myapps](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. [其他安全性驗證] 頁面會載入您的設定。

    ![Proofup](./media/multi-factor-authentication-end-user-manage/proofup.png)

## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>我想要變更我的電話號碼，或新增次要號碼
請務必設定次要驗證電話號碼。  由於您的主要電話號碼與行動應用程式可能在同一個手機上，因此如果您的手機遺失或遭竊，您只能依靠次要電話號碼恢復使用您的帳戶。

> [!NOTE]
> 如果您不能存取您的主要電話號碼，並需要協助進入您的帳戶，請參閱我們 [雙步驟驗證遇到困難](multi-factor-authentication-end-user-troubleshoot.md)中的說明主題。  

**若要變更您的主要電話號碼︰**  

1. 在 [其他安全性驗證] 頁面上，選取包含目前電話號碼的文字方塊，並使用新的電話號碼進行編輯。  
2. 選取 [ **儲存**]。  
3. 如果這是您針對慣用驗證選項使用的數字，您必須先確定新的數字後，才能進行儲存。  

**若要新增次要電話號碼︰**  

1. 在 [其他安全性驗證] 頁面上，勾選 [替代驗證電話] 旁的核取方塊。  
2. 在文字方塊中輸入您的次要電話號碼。  
3. 選取 [儲存] 您的變更即完成。  

## <a name="require-two-step-verification-again-on-a-device-youve-marked-as-trusted"></a>需要在已標示為受信任的裝置上再次進行雙步驟驗證

根據您組織的設定，當您在您的瀏覽器上執行雙步驟驗證時，您可能會看到核取方塊指出「**X** 天內不要再問我」。 如果核取此方塊然後遺失您的裝置，或認為您的帳戶遭到入侵，您應該將雙步驟驗證還原到所有的裝置。

1. 在 [其他安全性驗證] 頁面上，選取 [還原先前受信任裝置上的 Multi-Factor Authentication]。
2. 下次您在任何裝置上登入時，程式將會提示您執行雙步驟驗證。

## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>如何清除舊裝置的 Microsoft驗證器並移到新裝置？
無論您是從裝置解除安裝應用程式或是重設裝置，都不會移除後端的啟動功能。 如需詳細資訊，請參閱 [Microsoft Authenticator](microsoft-authenticator-app-how-to.md)。

## <a name="next-steps"></a>後續步驟
* 取得疑難排解提示以及[使用雙步驟驗證遇到困難](multi-factor-authentication-end-user-troubleshoot.md)的說明。
* 針對不支援雙步驟驗證的任何應用程式，設定[應用程式密碼](multi-factor-authentication-end-user-app-passwords.md)。
