---
title: "Azure Active Directory 裝置管理常見問題集 | Microsoft Docs"
description: "Azure Active Directory 裝置管理常見問題集。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 368589509b163cacf495fd0be893a8953fe2066e
ms.openlocfilehash: 2934b64c693f6505ddb389766374e31a5e6f249b
ms.contentlocale: zh-tw
ms.lasthandoff: 08/17/2017

---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory 裝置管理常見問題集

**問：我最近註冊了裝置。為什麼在 Azure 入口網站中我的使用者資訊底下看不到該裝置？**

**答：**透過自動裝置註冊加入網域的 Windows 10 裝置不會顯示在 [使用者資訊] 底下。
您必須使用 PowerShell，才能看到所有裝置。 

只有下列裝置會在 [使用者資訊] 底下列出：

- 所有未加入企業的個人裝置 
- 所有非 Windows 10/Windows Server 2016 裝置 
- 所有非 Windows 裝置 

---

**問：為什麼我在 Azure 入口網站中看不到在 Azure Active Directory 中註冊的所有裝置？** 

**答：**目前無法在 Azure 入口網站中看到所有已註冊的裝置。 您可以使用 Azure PowerShell 來尋找所有裝置。 如需更多詳細資料，請參閱 [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) Cmdlet。

--- 

**問：我要如何知道用戶端的裝置註冊狀態為何？**

**答：**裝置註冊狀態取決於：

- 裝置的類型
- 裝置的註冊方式 
- 裝置的任何相關詳細資料。 
 

---

**問：為什麼我已在 Azure 入口網站中或使用 Windows PowerShell 刪除的裝置仍列為已註冊？**

**答：**原先的設計就是如此。 該裝置將無法存取雲端中的資源。 如果您想要移除裝置並重新註冊它，就必須對該裝置採取手動動作。 

針對已加入內部部署 AD 網域的 Windows 10 與 Windows Server 2016：

1.  以系統管理員身分開啟命令提示字元。

2.  輸入 `dsregcmd.exe /debug /leave`

3.  登入並登出以觸發可重新註冊裝置的排定工作。 

針對其他已加入內部部署 AD 網域的 Windows 平台：

1.  以系統管理員身分開啟命令提示字元。
2.  輸入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`。
3.  輸入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`。

---

**問：為什麼我在 Azure 入口網站中看到重複的裝置項目？**

**答：**

-   就 Windows 10 和 Windows Server 2016 而言，如果重複地嘗試將同一個裝置取消加入再重新加入，就可能出現重複的項目。 

-   如果您使用了 [新增工作或學校帳戶]，則每個使用 [新增工作或學校帳戶] 的 Windows 使用者都會以相同的裝置名稱建立一個新的裝置記錄。

-   其他使用自動註冊來加入內部部署 AD 網域的 Windows 平台，將會針對登入裝置的每個網域使用者，以相同的裝置名稱建立一個新的裝置記錄。 

-   已清除、重新安裝再以相同名稱重新加入的 AADJ 機器將會顯示成具有相同裝置名稱的另一筆記錄。

---

**問：為什麼使用者仍然能夠從我已在 Azure 入口網站中停用的裝置存取資源？**

**答：**套用撤銷最多可能需要一小時的時間才能完成。

>[!Note] 
>針對已遺失的裝置，建議您將裝置清除，以確保使用者無法存取該裝置。 如需更多詳細資料，請參閱[註冊裝置以在 Intune 中管理](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune)。 


---

**問：為什麼我的使用者會看到「您無法從這裡前往該處」？**

**答：**如果您已設定某些條件式存取規則來要求特定的裝置狀態，而該裝置未符合這些準則，使用者就會遭封鎖並看到此訊息。 請重新評估規則並確保該裝置能夠符合準則，以避免出現此訊息。

---


**問：我在 Azure 入口網站中的 [使用者資訊] 底下看到裝置記錄，並且可以看到狀態為已在用戶端上註冊。我是否已針對使用條件式存取進行正確設定？**

**答：**Azure 入口網站上的裝置記錄 (deviceID) 和狀態必須符合用戶端，以及滿足所有條件式存取評估準則。 如需更多詳細資料，請參閱[開始使用 Azure Active Directory 裝置註冊](active-directory-device-registration.md)。

---

**問：為什麼針對剛加入 Azure AD 的裝置，我收到「使用者名稱或密碼不正確」訊息？**

**答：**此情況的常見原因如下：

- 您的使用者認證已經無效。

- 您的電腦無法與 Azure Active Directory 通訊。 請檢查是否有任何網路連線問題。

- 不符合 Azure AD Join 必要條件。 請確定您已依照[透過 Azure Active Directory Join 將雲端功能延伸到 Windows 10 裝置](active-directory-azureadjoin-overview.md)的步驟操作。  

- 同盟登入會要求您的同盟伺服器必須支援 WS-Trust 作用中端點。 

---

**問：當我嘗試將電腦加入時，為什麼會看到「糟糕，發生錯誤!」？**

**答：**這是使用 Intune 來設定 Azure Active Directory 註冊的結果。 如需更多詳細資料，請參閱[設定 Windows 裝置管理](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment)。  

---

**問：為什麼當我嘗試將電腦加入時，雖然沒有收到任何錯誤資訊，但卻發生失敗？**

**答：**可能是因為使用者使用內建的系統管理員帳戶來登入裝置。 在使用 Azure Active Directory Join 來完成設定之前，請先建立一個不同的本機帳戶。 

---

**問︰哪裡可以找到設定自動裝置註冊的指示？**

**答：**如需詳細指示，請參閱[如何設定讓已加入網域的 Windows 裝置自動向 Azure Active Directory 註冊](active-directory-conditional-access-automatic-device-registration-setup.md)

---

**問：哪裡可以找到有關自動裝置註冊的疑難排解資訊？**

**答：**如需疑難排解資訊，請參閱：

- [針對已加入 Azure AD 網域之電腦的自動註冊進行疑難排解 – Windows 10 和 Windows Server 2016](active-directory-device-registration-troubleshoot-windows.md)

- [針對已加入 Azure AD 網域之 Windows 下層用戶端電腦的自動註冊進行疑難排解](active-directory-device-registration-troubleshoot-windows-legacy.md)
 
---


