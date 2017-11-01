---
title: "適用於行動電話的 Microsoft Authenticator 應用程式 | Microsoft Docs"
description: "了解如何升級至最新版的 Azure Authenticator。"
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: femila
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: H1Hack27Feb2017, end-user
ms.openlocfilehash: cae11f53df768daf5846ac719a45715709326d76
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2017
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>開始使用 Microsoft 驗證器應用程式
Microsoft Authenticator 應用程式可以為您的工作或學校帳戶 (例如 bsimon@contoso.com) 或 Microsoft 帳戶 (例如 bsimon@outlook.com) 提供額外一層安全性。

此應用程式可以下列兩種方式之一運作︰

* **通知**。 此應用程式可協助防止未經授權即存取帳戶，並藉由推播通知到您的手機或平板電腦來停止詐騙交易。 只需檢視通知，如果合法，則選取 [驗證] 。 否則，您可以選取 [拒絕] 。
* **驗證碼**。 此應用程式可以作為軟體權杖來產生 OATH 驗證碼。 在您輸入使用者名稱和密碼後，必須在登入畫面出現提示時輸入應用程式提供的驗證碼。 驗證碼提供第二種形式的驗證。

Microsoft Authenticator 應用程式會取代 Azure Authenticator 應用程式。 Azure 驗證器應用程式仍可運作，但如果您決定改用新的 Microsoft Authenticator 應用程式，本文可協助您。  

## <a name="opt-in-for-two-step-verification"></a>選擇雙步驟驗證

Microsoft Authenticator 無法自行運作。 將您的每個帳戶設定為在以您的使用者名稱和密碼登入之後，提示您選擇第二個驗證方法。

如果是公司或學校帳戶，您通常不需要自行選擇此功能。 相反地，安全性系統管理員會代替您選擇，然後通知您為您的帳戶註冊驗證方法。 如果此案例適用於您，請參閱 [Azure Multi-Factor Authentication 對我有何意義](multi-factor-authentication-end-user.md)以深入了解。

如果是個人帳戶，您必須自行設定雙步驟驗證。 如果您有 Microsoft 帳戶，[關於雙步驟驗證](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)中說明這些步驟。

您也可以搭配非 Microsoft 帳戶使用 Microsoft Authenticator。 它們可能不是將此功能叫做雙步驟驗證，但您應該能夠在安全性或登入設定下找到它。

## <a name="install-the-app"></a>安裝應用程式
Microsoft Authenticator 應用程式適用於 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072) 和 [iOS](http://go.microsoft.com/fwlink/?Linkid=825073)。

## <a name="add-accounts-to-the-app"></a>將帳戶新增至應用程式
針對每個想要新增至 Microsoft Authenticator 應用程式的帳戶，請使用下列其中一個程序：

### <a name="add-a-personal-microsoft-account-to-the-app"></a>將個人的 Microsoft 帳戶新增至應用程式

針對個人的 Microsoft 帳戶 (您用來登入 Outlook.com、Xbox、Skype 等的帳戶)，您只需要登入在 Microsoft Authenticator應用程式中的帳戶。

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>使用 QR 代碼掃描器將工作或學校帳戶新增至應用程式
1. 移至安全性驗證設定畫面。  如需如何進入此畫面的詳細資訊，請參閱 [變更安全性設定](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page)。
2. 核取 **Authenticator 應用程式**旁的方塊，然後選取 [設定]。

    ![安全性驗證設定畫面上的設定按鈕](./media/authenticator-app-how-to/azureauthe.png)

    這會顯示有 QR 代碼的畫面。

    ![提供 QR 代碼的畫面](./media/authenticator-app-how-to/barcode2.png)
3. 開啟 Microsoft 驗證器應用程式。 在 [帳戶] 畫面上，選取 [+]，然後指定您要新增工作帳戶或學校帳戶。
4. 使用相機來掃描 QR 代碼，然後選取 [完成]  關閉 QR 代碼畫面。

    如果相機運作不正常，您可以[手動輸入 QR 代碼和 URL](#add-an-account-to-the-app-manually)。

5. 當應用程式顯示您的帳戶名稱與下面的六位數代碼時，則您已完成。

    ![帳戶畫面](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>手動新增帳戶至應用程式
1. 移至安全性驗證設定畫面。  如需如何進入此畫面的詳細資訊，請參閱 [變更安全性設定](multi-factor-authentication-end-user-manage-settings.md)。
2. 選取 [設定] 。

    ![安全性驗證設定畫面上的設定按鈕](./media/authenticator-app-how-to/azureauthe.png)

    這會顯示有 QR 代碼的畫面。  記下代碼和 URL。

    ![提供 QR 代碼與 URL 的畫面](./media/authenticator-app-how-to/barcode2.png)
3. 開啟 Microsoft 驗證器應用程式。 在 [帳戶] 畫面上，選取 [+]，然後指定您要新增工作帳戶或學校帳戶。

4. 在掃描器上，選取 [手動輸入代碼] 。

    ![掃描 QR 代碼的畫面](./media/multi-factor-authentication-end-user-first-time/scan2.png)
5. 在應用程式的適當方塊中輸入代碼和 URL，然後選取 [完成]。

    ![輸入代碼和 URL 的畫面](./media/authenticator-app-how-to/manual.png)

6. 當應用程式顯示您的帳戶名稱與下面的六位數代碼時，則您已完成。

    ![帳戶畫面](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>將帳戶新增至使用您裝置之指紋或臉部辨識功能的應用程式
您的組織可能需要 PIN 才能完成驗證挑戰。 Microsoft Authenticator 應用程式可以使用您裝置的指紋或臉部辨識功能，而非 PIN。 若要在應用程式中針對第一個驗證設定此功能，您會看到使用 Touch ID (適用於 iOS) 或是改為指紋識別的選項。 

若要設定 Microsoft Authenticator 的 Touch ID，您需要使用 PIN 完成一般驗證挑戰。 Microsoft Authenticator 將會針對支援 Touch ID 的裝置自動設定它。 

![Touch ID 安裝程式的驗證](./media/authenticator-app-how-to/touchid1.png)

往後當您被要求驗證登入時，您只要選取收到的推播通知並且掃描您的指紋即可，不需要輸入您的 PIN。

![推播通知](./media/authenticator-app-how-to/touchid2.png)

## <a name="use-the-app-when-you-sign-in"></a>當您登入時使用應用程式

當您的帳戶新增至應用程式後，可能會提示您執行測試驗證，以確保一切都已正確設定。 之後，就大功告成了！ 直到下次再登入之前，您不需要採取任何動作。

如果您選擇在應用程式中使用驗證碼，您會開始在首頁上看到它們。 它們每 30 秒變更一次，所以當您需要時，一定可以獲得新的驗證碼。 但在您登入並接獲提示輸入驗證碼之前，不必理會它們。  
