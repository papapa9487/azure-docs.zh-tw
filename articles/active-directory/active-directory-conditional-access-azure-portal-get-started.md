---
title: "開始使用 Azure Active Directory 中的條件式存取 | Microsoft Docs"
description: "使用位置條件測試條件式存取。"
services: active-directory
keywords: "應用程式的條件式存取, Azure AD 條件式存取, 安全存取公司資源, 條件式存取原則"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: b9a95cea57f4d0a4a927679cd4802bb56420887a
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2017
---
# <a name="get-started-with-conditional-access-in-azure-active-directory"></a>開始使用 Azure Active Directory 中的條件式存取

條件式存取是 Azure Active Directory 的功能，可讓您定義獲得授權的使用者可以存取您的應用程式的條件。 

本主題根據您環境中的位置條件，為您提供測試條件式存取的指示。  


## <a name="scenario-description"></a>案例描述

許多組織有一個共同需求：只需要 Multi-Factor Authentication，即可存取不是從公司內部網路執行的應用程式。 使用 Azure Active Directory 設定以位置為基礎的條件式存取原則，即可輕鬆地完成此目標。 本主題提供設定相關原則的詳細指示。 原則會利用[信任的 IP](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips) 來區別從公司內部網路與所有其他位置進行的存取嘗試。


## <a name="prerequisites"></a>必要條件

本主題中概述的案例假設您熟悉 [Azure Active Directory 條件式存取](active-directory-conditional-access-azure-portal.md)中所述的概念。

若要測試此案例，您必須：

- 建立測試使用者 

- 將 Azure AD Premium 授權指派給測試使用者

- 設定受管理的應用程式，並將您的測試使用者指派給它

- 設定信任的 IP

如果您需要更多有關信任 IP 的詳細資訊，請參閱[信任的 IP](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips)。


## <a name="policy-configuration-steps"></a>原則設定步驟

**若要設定條件式存取原則，請執行：**

1. 在 Azure 入口網站的左側導覽列上，按一下 [Azure Active Directory]。 

    ![條件式存取](./media/active-directory-conditional-access-azure-portal-get-started/01.png)

2. 在 [Azure Active Directory] 刀鋒視窗的 [管理] 區段中，按一下 [條件式存取]。

    ![條件式存取](./media/active-directory-conditional-access-azure-portal-get-started/02.png)
 
3. 在 [條件式存取] 刀鋒視窗上，若要開啟 [新增] 刀鋒視窗，請在頂端的工具列中按一下 [新增]。

    ![條件式存取](./media/active-directory-conditional-access-azure-portal-get-started/03.png)

4. 在 [新增] 刀鋒視窗的 [名稱] 文字方塊中，輸入您的原則名稱。

    ![條件式存取](./media/active-directory-conditional-access-azure-portal-get-started/04.png)

5. 在 [指派] 區段中，按一下 [使用者和群組]。

    ![條件式存取](./media/active-directory-conditional-access-azure-portal-get-started/05.png)

6. 在 [使用者和群組] 刀鋒視窗上，執行下列步驟︰

    ![條件式存取](./media/active-directory-conditional-access-azure-portal-get-started/06.png)

    a. 按一下 [選取使用者和群組]。

    b.這是另一個 C# 主控台應用程式。 按一下 [選取] 。

    c. 在 [選取] 刀鋒視窗上，選取您的測試使用者，然後按一下 [選取]。

    d. 在 [使用者和群組] 刀鋒視窗上，按一下 [完成]。

7. 在 [新增] 刀鋒視窗上，若要開啟 [雲端應用程式] 刀鋒視窗，請在 [指派] 區段中，按一下 [雲端應用程式]。

    ![條件式存取](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. 在 [雲端應用程式] 刀鋒視窗上，執行下列步驟︰

    ![條件式存取](./media/active-directory-conditional-access-azure-portal-get-started/08.png)

    a. 按一下 [選取應用程式]。

    b.這是另一個 C# 主控台應用程式。 按一下 [選取] 。

    c. 在 [選取] 刀鋒視窗上，選取您的雲端應用程式，然後按一下 [選取]。

    d. 在 [雲端應用程式] 刀鋒視窗上，按一下 [完成]。

9. 在 [新增] 刀鋒視窗上，若要開啟 [條件] 刀鋒視窗，請在 [指派] 區段中，按一下 [條件]。

    ![條件式存取](./media/active-directory-conditional-access-azure-portal-get-started/09.png)

10. 在 [條件] 刀鋒視窗上，若要開啟 [位置] 刀鋒視窗，請按一下 [位置]。

    ![條件式存取](./media/active-directory-conditional-access-azure-portal-get-started/10.png)

11. 在 [位置] 刀鋒視窗上，執行下列步驟︰

    ![條件式存取](./media/active-directory-conditional-access-azure-portal-get-started/11.png)

    a. 在 [設定] 之下，按一下 [是]。

    b.這是另一個 C# 主控台應用程式。 在 [包含] 之下，按一下 [所有位置]。

    c. 按一下 [排除]，然後按一下 [所有信任的 IP]。

    ![條件式存取](./media/active-directory-conditional-access-azure-portal-get-started/12.png)

    d. 按一下 [完成] 。

12. 在 [條件] 刀鋒視窗上，按一下 [完成]。

13. 在 [新增] 刀鋒視窗上，若要開啟 [授與] 刀鋒視窗，請在 [控制] 區段中，按一下 [授與]。

    ![條件式存取](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. 在 [授與] 刀鋒視窗上，執行下列步驟︰

    ![條件式存取](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. 選取 [需要 Multi-Factor Authentication]。

    b.這是另一個 C# 主控台應用程式。 按一下 [選取] 。

15. 在 [新增] 刀鋒視窗的 [啟用原則] 之下，按一下 [開啟]。

    ![條件式存取](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. 在 [新增] 刀鋒視窗上，按一下 [建立]。


## <a name="testing-the-policy"></a>測試原則

若要測試您的原則，您應該從以下裝置存取您的應用程式︰ 

1. 具有在您設定的信任 IP 內的 IP 位址 

1. 具有不在您設定的信任 IP 內的 IP 位址

只有從不在您設定的信任 IP 內的裝置嘗試連線時，才需要 Multi-Factor Authentication。 


## <a name="next-steps"></a>後續步驟

如果您想要深入了解條件式存取，請參閱 [Azure Active Directory 條件式存取](active-directory-conditional-access-azure-portal.md)。

