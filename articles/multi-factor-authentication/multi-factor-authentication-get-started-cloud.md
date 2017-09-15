---
title: "在雲端中開始使用 Azure MFA | Microsoft Docs"
description: "這是說明如何開始在雲端中使用 Azure MFA 的 Microsoft Azure Multi-Factor Authentication 頁面。"
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: yossib
ms.assetid: 6b2e6549-1a26-4666-9c4a-cbe5d64c4e66
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/24/2017
ms.author: joflore
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: 1cce449a87571fdabd0dbf76f764f442b2990ffe
ms.contentlocale: zh-tw
ms.lasthandoff: 08/12/2017

---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>開始在雲端中使用 Azure Multi-Factor Authentication
本文將帶領您逐步了解如何在雲端中開始使用 Azure Multi-Factor Authentication。

> [!NOTE]
> 以下文件提供有關如何使用 **Azure 傳統入口網站**啟用使用者的詳細資訊。 如果您要尋找有關如何設定適用於 O365 使用者的 Azure Multi-Factor Authentication 的詳細資訊，請參閱[設定適用於 Office 365 的 Multi-Factor Authentication](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)。

![雲端中的 MFA](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## <a name="prerequisite"></a>必要條件
[註冊 Azure 訂用帳戶](https://azure.microsoft.com/pricing/free-trial/) - 如果您還沒有 Azure 訂用帳戶，可以免費註冊。 如果您剛開始使用 Azure MFA，您可以使用試用版訂用帳戶

## <a name="enable-azure-multi-factor-authentication"></a>啟用 Azure Multi-Factor Authentication
只要您的使用者具有包含 Azure Multi-Factor Authentication 的授權，您就不需要手動開啟 Azure MFA。 您可以開始要求對個別使用者進行雙步驟驗證。 可啟用 Azure MFA 的授權如下︰
- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

如果您沒有上述其中一種授權，或沒有足夠的授權來涵蓋所有的使用者，也沒關係。 您只必須執行額外一個步驟以及在您的目錄中[建立多因素驗證提供者](multi-factor-authentication-get-started-auth-provider.md)。

## <a name="turn-on-two-step-verification-for-users"></a>對使用者開啟雙步驟驗證

使用[如何要求使用者或群組進行雙步驟驗證](multi-factor-authentication-get-started-user-states.md)中所列的其中一個程序，開始使用 Azure MFA。 您可以選擇對所有登入強制執行雙步驟驗證，也可以建立條件式存取原則，只有在與您有關時要求雙步驟驗證。

## <a name="next-steps"></a>後續步驟
現在您已在雲端中設定 Azure Multi-Factor Authentication，接下來您可以設定及安裝您的部署。 如需詳細資訊，請參閱[設定 Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md)。


