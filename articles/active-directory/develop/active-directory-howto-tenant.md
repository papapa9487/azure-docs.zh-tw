---
title: "如何取得 Azure AD 租用戶 | Microsoft Docs"
description: "如何取得 Azure Active Directory 租用戶，以供註冊及建置應用程式使用。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/19/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: fe33d490b754e2f793f5c7a13dc55ca038b1b71c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>如何取得 Azure Active Directory 租用戶
在 Azure Active Directory (Azure AD) 中， [租用戶](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) 代表組織。  它是組織在註冊 Microsoft 雲端服務 (例如 Azure、Microsoft Intune 或 Office 365) 時所收到和擁有的專屬 Azure AD 服務執行個體。  每個 Azure AD 租用戶都不同，並與其他 Azure AD 租用戶分開。  

租用戶可裝載公司中的使用者及其相關資訊 (密碼、使用者設定檔資料、權限等)。  它還包含群組、應用程式和關於組織及其安全性的其他資訊。

若要允許 Azure AD 使用者登入您的應用程式，您必須在您自己的租用戶中註冊您的應用程式。  在 Azure AD 租用戶中發佈應用程式 **完全免費**。  事實上，大部分的開發人員會建立數個租用戶和應用程式，以供實驗、開發、預備和測試等用途使用。  如果註冊和使用應用程式的組織想要充分利用進階的目錄功能，他們可以選擇購買授權。

因此，要如何取得 Azure AD 租用戶？  此程序可能會有小小的不同，如果您：

* [具有現有的 Office 365 訂用帳戶](#use-an-existing-office-365-subscription)
* [具有與 Microsoft 帳戶相關聯的現有 Azure 訂用帳戶](#use-an-msa-azure-subscription)
* [具有與組織帳戶相關聯的現有 Azure 訂用帳戶](#use-an-organizational-azure-subscription)
* [以上皆無並想要從頭開始](#start-from-scratch)

## <a name="use-an-existing-office-365-subscription"></a>使用現有的 Office 365 訂用帳戶
如果您擁有現有的 Office 365 訂用帳戶，就已擁有 Azure AD 租用戶！ 您可以使用您的 O365 帳戶登入 [Azure 入口網站](https://portal.azure.com)並開始使用 Azure AD。

## <a name="use-an-msa-azure-subscription"></a>使用 MSA Azure 訂用帳戶
如果您先前已使用個別的 Microsoft 帳戶註冊 Azure 訂用帳戶，則您已經有一個租用戶！  當您登入 [Azure 入口網站](https://portal.azure.com)時，會自動登入您的預設租用戶。 您可以視需要任意使用此租用戶，但您可能會想要建立組織系統管理員帳戶。

若要這樣做，請遵循下列步驟。  或者，您可能會想要建立新的租用戶，並按照類似的程序在該租用戶中建立系統管理員。

1. 使用您的個別帳戶登入 [Azure 入口網站](https://portal.azure.com)
2. 瀏覽至入口網站的 [Azure Active Directory] 區段 (您可在左側瀏覽列的**更多服務**下找到)
3. 您應該會自動登入「預設目錄」，如果未自動登入，您可以按一下右上角的帳戶名稱切換目錄。
4. 從 [快速工作] 區段中，選擇 [新增使用者]。
5. 在 [新增使用者表單] 中，請提供下列詳細資料：

   * 名稱：(選擇適當的值)
   * 使用者名稱：(為此系統管理員選擇一個使用者名稱)
   * 設定檔：(在名字、姓氏、職稱和部門中填入適當的值)
   * 角色：全域系統管理員
6. 當您完成新增使用者表單，並收到新系統管理使用者的暫時密碼時，請務必記錄此密碼，因為您必須以此新使用者身分登入，才能變更密碼。 您也可以使用替代電子郵件，直接將密碼傳送給使用者。
7. 按一下 [建立] 來建立新的使用者。
8. 若要變更暫時密碼，請使用這個新使用者帳戶登入 [https://login.microsoftonline.com](https://login.microsoftonline.com)變更密碼。

## <a name="use-an-organizational-azure-subscription"></a>使用組織的 Azure 訂用帳戶
如果您先前已使用組織帳戶註冊 Azure 訂用帳戶，則您已經有一個租用戶！  在 [Azure 入口網站](https://portal.azure.com)中，當您瀏覽至 [更多服務] 和 [Azure Active Directory] 時應該會找到租用戶。  您可以視需要任意使用此租用戶。

## <a name="start-from-scratch"></a>從頭開始
如果上述對您沒有太大的意義，別擔心。  您只需造訪 [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) ，並以新的組織身分註冊 Azure。  完成此程序時，您將會有自己專屬的 Azure AD 租用戶，並且它會有您在註冊時選擇的網域名稱。  在 [Azure 入口網站](https://portal.azure.com)中，您可以透過瀏覽至左側導覽中的 [Azure Active Directory] 找到租用戶。

註冊 Azure 的過程中，您將需要提供信用卡的詳細資料。  您可以放心繼續執行，您將不會被收取在 Azure AD 中發佈應用程式或建立新租用戶的費用。
