---
title: "Microsoft 驗證器電話登入 - Azure 與 Microsoft 帳戶 | Microsoft Docs"
description: "使用您的電話來登入您的 Microsoft 帳戶，而不是輸入您的密碼。 本文回答有關這項功能的常見問題集。"
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: femila
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: end-user
ms.openlocfilehash: b62900b780884c241d96f0bed8e93a1a0927cfe3
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2017
---
# <a name="sign-in-with-your-phone-not-your-password"></a>使用您的電話而不是您的密碼登入

Microsoft 驗證器應用程式可協助您保持帳戶安全，方法為在您輸入密碼之後執行兩步驟驗證。 但是您知道它可以完全取代您個人 Microsoft 帳戶的密碼嗎？

這項功能可在 iOS 和 Android 的裝置上提供，並且可用於個人 Microsoft 帳戶。

## <a name="how-it-works"></a>運作方式

你們有許多人在登入您的 Microsoft 帳戶時，會使用 Microsoft 驗證器應用程式進行兩步驟驗證。 您輸入密碼，然後移至應用程式，以核准通知或取得驗證碼。 使用電話登入，您會略過密碼，並在電話上進行所有身分識別驗證作業。 因為電話登入是一種雙步驟驗證，您仍必須提供您知道的項目以及您擁有的項目來驗證您的身分。 電話仍是您擁有的項目，而您的電話 PIN 碼或生物識別索引鍵則為您知道的項目。

## <a name="how-to-get-started"></a>如何開始使用

若要使用您的電話登入您個人的 Microsoft 帳戶，請執行下列步驟︰

1. 啟用電話登入帳戶。

  - 如果您還沒有 Microsoft 驗證器應用程式，請根據 [Microsoft 驗證器頁面](microsoft-authenticator-app-how-to.md)上的步驟，安裝並新增您個人的 Microsoft 帳戶。 新增的帳戶會自動啟用，以便您可以大顯身手。

  - 如果您已經使用 Microsoft 驗證器進行兩步驟驗證，請從應用程式首頁選取您的帳戶，然後從下拉式功能表中選取 [啟用電話登入]。

  >[!NOTE]
  >若要保護您的帳戶，我們需要裝置上的 PIN 碼或生物識別鎖定。 如果您保持解除鎖定您的電話，應用程式會快顯要求，要求您設定鎖定後，才能啟用電話登入。

3. 您會正常輸入 Microsoft 帳戶密碼的大部分頁面都有一個連結，說明**改為使用應用程式**。 選取此連結以使用電話進行登入。

4. Microsoft 會傳送通知至您的電話。 核准通知以登入您的帳戶。   

## <a name="faq"></a>常見問題集

### <a name="how-is-signing-in-with-my-phone-more-secure-than-typing-a-password"></a>使用我的電話登入會如何比輸入密碼更安全？  

現今大部分的人會使用使用者名稱和密碼登入網站或應用程式。  不幸的是，密碼通常會遺失、遭竊或遭駭客猜到。 當您設定 Microsoft 驗證器應用程式以登入時，我們會在您的電話上產生金鑰，可以解除鎖定您的帳戶。 我們會使用您已在您的電話上使用之 PIN 碼或生物特徵辨識來保護這個金鑰。  當您使用您的電話登入時，此金鑰可用來以兩個因素安全地證明您的身份 – 電話本身，以及您是否可將它解除鎖定。 

所使用的金鑰很類似 Windows Hello 和 FIDO 結盟 UAF 規格中所用的金鑰。 生物資料只會用於在本機保護金鑰，且永遠不會傳送至或儲存在雲端。 
 
### <a name="where-can-i-use-my-phone-to-replace-my-password-and-where-would-i-still-need-the-password"></a>我可以在何處使用我的電話來取代我的密碼，以及我在何處仍需要密碼？  

現在，電話登入功能僅適用於使用個人 Microsoft 帳戶的個人 Microsoft 帳戶、iOS 或 Android 應用程式所提供的 Web Apps 和服務，以及使用個人 Microsoft 帳戶的 Windows 10 上之應用程式。 當您登入其中一個網站或應用程式時，在您通常輸入密碼的頁面上，有一個連結會顯示**改為使用應用程式**。 

電話登入目前無法用來解除鎖定 Windows PC、XBOX 或任何桌面版本的 Microsoft 應用程式，例如 Office 應用程式。
 
### <a name="does-this-replace-two-step-verification-should-i-turn-it-off"></a>這會取代雙步驟驗證嗎？ 應該將它關機嗎？   

有時是。 我們正努力擴充電話登入的範圍，但現在 Microsoft 生態系統中仍有些位置不支援。 在這些位置中，我們仍會使用兩步驟驗證進行安全登入。 基於這個理由，不可以，您不應該關閉您帳戶的雙步驟驗證。
 
### <a name="okay-if-i-keep-two-step-verification-turned-on-for-my-account-do-i-have-to-approve-two-notifications"></a>好了，如果開啟我帳戶的雙步驟驗證，是否必須核准兩個通知？

否，您不會。 使用您的電話登入您的 Microsoft 帳戶都算是兩步驟驗證。 而不是輸入您的密碼，然後藉由了解如何解除鎖定您的電話核准通知證明您的身分識別，然後核准通知。 我們不會傳送第二個通知進行核准。

### <a name="what-if-i-lose-my-phone-or-dont-have-it-with-me-how-can-i-access-my-account"></a>如果我遺失電話或沒有攜帶電話，要如何存取我的帳號？  

您隨時可以按一下登入頁面上的 [改為使用密碼] 切換回使用您的密碼。 請記住，如果您使用兩步驟驗證，您仍需要第二個方法來驗證您的登入。 這就是為什麼我們強烈建議您先確認您的帳戶具有額外、最新的安全性資訊。 您可以在 https://account.live.com/proofs/manage 管理您的安全性資訊。
 
### <a name="how-do-i-stop-using-this-feature-and-go-back-to-entering-my-password"></a>如何停止使用此功能並返回輸入我的密碼？

當您登入時，按一下 [改為使用密碼]。 我們記得您最近的選擇，並在您下次登入時依預設提供該選擇。 如果您想要回到使用您的電話登入，按一下 [改為使用應用程式]。 
 
### <a name="can-i-use-the-app-to-sign-in-to-all-my-accounts-with-microsoft"></a>可以使用應用程式登入我所有 Microsoft 的帳戶嗎？   
這項功能只適用於個人 Microsoft 帳戶。 
 
### <a name="can-i-sign-into-my-pc-with-my-phone"></a>可以使用我的電話登入我的電腦嗎？  
針對您的電腦，我們建議您使用臉部、指紋或 PIN 碼，以 Windows 10 上的 Windows Hello 登入。   
 
### <a name="can-i-sign-in-with-my-windows-phone"></a>我可以使用 Windows Phone 登入嗎？  
此時，我們並未在 Windows Phone 上開發 Microsoft 驗證器的這項功能。 

## <a name="next-steps"></a>後續步驟
如果您尚未下載 Microsoft 驗證器應用程式，請將它簽出。應用程式適用於 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)，Microsoft 驗證器應用程式上的電話登入功能適用於 [Android](http://go.microsoft.com/fwlink/?Linkid=825072) 和 [iOS](http://go.microsoft.com/fwlink/?Linkid=825073)。

如果您有關於應用程式的一般問題，請查看 [Microsoft 驗證器常見問題集](microsoft-authenticator-app-faq.md)
