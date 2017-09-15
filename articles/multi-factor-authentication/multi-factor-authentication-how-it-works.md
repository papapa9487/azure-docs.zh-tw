---
title: "Azure Multi-Factor Authentication -它的作用"
description: "Azure Multi-Factor Authentication 有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。 它藉由要求第二種形式的驗證提供額外的安全性，並透過一系列簡單的驗證選項提供增強式驗證。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: d14db902-9afe-4fca-b3a5-4bd54b3d8ec5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: joflore
ms.reviewer: yossib
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 6fee02885cc76b3a4fdad11e8702f623d6fe6597
ms.contentlocale: zh-tw
ms.lasthandoff: 06/30/2017

---
# <a name="how-azure-multi-factor-authentication-works"></a>Azure Multi-Factor Authentication 的作用
雙步驟驗證的安全性仰賴其分層方法。 使用多重驗證因素會為攻擊者帶來相當程度的挑戰。 即使攻擊者試圖打探使用者的密碼，在不持有信任裝置的情況下便沒有任何意義。 

![Proofup](./media/multi-factor-authentication-how-it-works/howitworks.png)

Azure Multi-Factor Authentication 有助於保護對資料與應用程式的存取，同時可以滿足使用者對簡單登入程序的需求。  它藉由要求第二種形式的驗證提供額外的安全性，並透過一系列簡單的驗證選項提供增強式驗證。


## <a name="methods-available-for-two-step-verification"></a>雙步驟驗證適用的方法
當使用者登入時，系統會將額外的驗證傳送給使用者。  以下是適用於這個第二次驗證的方法清單。

| 驗證方法 | 說明 |
| --- | --- |
| 撥打電話 |撥打一通電話到使用者所註冊的電話號碼。 使用者視需要輸入 PIN，然後按下 # 鍵。 |
| 簡訊 |傳送含六位數代碼的簡訊到使用者的手機。 使用者在登入頁面輸入此代碼。 |
| 行動應用程式通知 |傳送驗證要求到使用者的智慧型手機。 使用者視需要輸入 PIN，然後在行動裝置應用程式上選取 [驗證]。 |
| 行動應用程式驗證碼 |在使用者智慧型手機上執行的行動裝置應用程式，會顯示每 30 秒就變更一次的驗證碼。 使用者尋找最新的驗證碼，並在登入頁面上輸入。 |
| 協力廠商 OATH 權杖 | Azure Multi-Factor Authentication Server 可以設定為接受協力廠商驗證方法。 |

Azure Multi-Factor Authentication 為雲端與伺服器提供可選取的驗證方法。 您可以選擇可供使用者使用的方法：撥打電話、簡訊、應用程式通知或應用程式驗證碼。 如需詳細資訊，請參閱 [可選取的驗證方法](multi-factor-authentication-whats-next.md#selectable-verification-methods)。

## <a name="next-steps"></a>後續步驟

- 請參閱不同的 [Azure Multi-Factor Authentication 版本和耗用方法](multi-factor-authentication-versions-plans.md)

- 選擇要[在雲端還是內部部署](multi-factor-authentication-get-started.md)部署 Azure MFA

- 閱讀[常見問題](multi-factor-authentication-faq.md)的解答
