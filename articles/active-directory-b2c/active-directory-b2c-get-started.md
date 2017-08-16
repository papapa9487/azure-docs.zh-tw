---
title: "Azure Active Directory B2C：建立 Azure Active Directory B2C 租用戶 | Microsoft Docs"
description: "有關如何建立 Azure Active Directory B2C 租用戶的主題"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: patricka
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 06/07/2017
ms.author: swkrish
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 8a1d4935397f59e5813afc6f04559e471187a779
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---
# <a name="create-an-azure-active-directory-b2c-tenant-in-the-azure-portal"></a>在 Azure 入口網站中建立 Azure Active Directory B2C 租用戶

本快速入門協助您在數分鐘內建立 Microsoft Azure Active Directory (Azure AD) B2C 租用戶。 完成後，您便有 B2C 租用戶可用於註冊 B2C 應用程式。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

##  <a name="log-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-an-azure-ad-b2c-tenant"></a>建立 Azure AD B2C 租用戶

B2C 功能無法在您現有的租用戶中啟用。 您必須建立 Azure AD B2C 租用戶。

[!INCLUDE [active-directory-b2c-create-tenant](../../includes/active-directory-b2c-create-tenant.md)]

恭喜，您已建立 Azure Active Directory B2C 租用戶。 您是租用戶的「全域管理員」。 您可視需要新增其他「全域管理員」。 若要切換到新的租用戶，請按一下*管理新租用戶連結*。

![管理新租用戶連結](./media/active-directory-b2c-get-started/manage-new-b2c-tenant-link.png)

> [!IMPORTANT]
> 如果您打算將 B2C 租用戶用於生產應用程式，請閱讀 [生產級別租用戶與預覽 B2C 租用戶](active-directory-b2c-reference-tenant-type.md)一文。 當您刪除現有的 B2C 租用戶並使用相同的網域名稱加以重建時，會發生已知的問題。 您需要使用不同的網域名稱建立 B2C 租用戶。
>
>

## <a name="link-your-tenant-to-your-subscription"></a>將您的租用戶連結至您的訂用帳戶

您必須將您的 Azure AD B2C 租用戶連結至您的 Azure 訂用帳戶，才能啟用所有的 B2C 功能，並支付使用費用。 若要深入了解，請閱讀[這篇文章](active-directory-b2c-how-to-enable-billing.md)。 如果您未將 Azure AD B2C 租用戶連結到您的 Azure 訂用帳戶，有些功能會遭到封鎖，您將會在 B2C 設定中看見警告訊息 (「沒有訂用帳戶與此 B2C 租用戶連結，或訂用帳戶需要您的注意。」)。 請務必先進行此步驟，然後才將您的應用程式傳送到生產環境。

## <a name="easy-access-to-settings"></a>輕鬆存取設定

[!INCLUDE [active-directory-b2c-find-service-settings](../../includes/active-directory-b2c-find-service-settings.md)]

您也可以在入口網站頂端的 [搜尋資源] 中尋入 `Azure AD B2C` 來存取刀鋒視窗。 在結果清單中選取 [Azure AD B2C] 以存取 B2C 設定刀鋒視窗。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在 B2C 租用戶中註冊您的 B2C 應用程式](active-directory-b2c-app-registration.md)
