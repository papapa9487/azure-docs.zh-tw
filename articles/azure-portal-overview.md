---
title: "Azure 入口網站概觀 | Microsoft Docs"
description: "了解如何使用 Microsoft Azure 入口網站。"
services: 
documentationcenter: 
author: davidwrede
manager: erikre
editor: jimbe
ms.assetid: 53cb9df1-c96a-4f4e-b022-18336cd3d697
ms.service: azure-portal
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 12/16/2015
ms.author: dwrede
ms.openlocfilehash: 45decfdec01b7086d1f9d18b31cf01cec1adb34d
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2017
---
# <a name="microsoft-azure-portal-overview"></a>Microsoft Azure 入口網站概觀
Microsoft Azure 入口網站是一個可以佈建和管理 Azure 資源的中央位置。  本教學課程將讓您熟悉此入口網站，並為您示範如何使用其中的某些重要功能︰

* **完整的 Marketplace** ，可讓您瀏覽數千個可購買及/或佈建的 Microsoft 和其他廠商項目。
* **統一且可調整的瀏覽經驗** ，可讓您輕鬆地尋找您所重視的資源，並執行各種管理作業。
* **一致的管理頁面** (或刀鋒視窗)，可讓您透過一致的方式 (公開設定、動作、帳單資訊、健全狀況監視與使用量資料等等) 來管理 Azure 的各種服務。
* **個人經驗** ，可讓您建立自訂的開始畫面，在您每次登入時顯示您想看到的資訊。  您也可以自訂任何包含磚的管理刀鋒視窗。
  
  ![Azure 入口網站 UI 定位][UIOrientation]

## <a name="before-you-get-started"></a>開始之前
您需要一個有效的 Azure 訂用帳戶才能完成本教學課程。  如果您沒有訂用帳戶，您可以立即 [註冊免費試用](https://azure.microsoft.com/pricing/free-trial/) 。  擁有訂用帳戶之後，您便可以存取入口網站，網址是 <https://portal.azure.com>。

## <a name="how-to-create-a-resource"></a>如何建立資源
Azure 擁有一個包含數千個項目的 Marketplace，您可以在一個位置即可建立這些項目。  例如，假設您想要建立新的 Windows Server 2012 VM。  [+ 新增] 中心是您進入 Marketplace 精選分類策劃組合的進入點。  每個類別都會有一小組的精選項目，以及一個可顯示所有類別和搜尋之完整 Marketplace 的連結。 若要建立新的 Windows Server 2012 VM，請執行下列動作：  

1. 採用 Windows Server 2012，因此您可以在 [計算] 類別中加以選取。  
2. 填寫表單上的某些基本輸入。
3. 按一下 [建立]，您的 VM 便會立即開始佈建。

通知中心會在完成建立資源時提醒您，且管理刀鋒視窗會隨即開啟 (您之後仍可隨時瀏覽至資源)。

![入口網站的類別目錄][PortalCategories]

## <a name="how-to-find-your-resources"></a>如何尋找您的資源
您可以隨時將經常存取的資源固定到您的儀表板，但有時您可能需要瀏覽至不常存取的項目。  如下所示的瀏覽中樞是您取得所有資源的方式。  您可以依訂用帳戶進行篩選、選擇/調整資料行大小，以及按一下個別項目以瀏覽至管理刀鋒視窗。

![Browse Hub][BrowseHub]

## <a name="how-to-manage-and-delegate-access-to-a-resource"></a>如何管理和委派資源存取
從這個刀鋒視窗中，您可以使用遠端桌面連接虛擬機器、監視關鍵效能度量、使用角色型存取 (RBAC) 來控制此 VM 的存取、設定 VM，以及執行其他重要管理工作。  根據角色委派存取對於大規模管理相當重要。  若要深入了解，請按一下[這裡](active-directory/role-based-access-control-configure.md)。 若要委派資源存取，請執行下列動作：

1. 瀏覽至您的資源。
2. 按一下 [Essentials] 區段中的 [所有設定]。
3. 按一下設定清單中的 [使用者]。
4. 按一下命令列中的 [新增]。
5. 選擇使用者和角色。

![管理資源][ManageResource]

## <a name="how-to-get-help"></a>如何取得協助
如果您有任何疑問，我們會隨時提供協助。  入口網站具備可協助您作業的說明及支援頁面。  視您的 [支援計畫](https://azure.microsoft.com/support/plans/)而定，您也可以直接在入口網站中建立支援票證。  建立支援票證之後，您便可以從入口網站中管理票證的生命週期。 您可以瀏覽至 [瀏覽] -> [說明 + 支援] 以取得說明及支援頁面。  

![說明及支援][HelpSupport]

## <a name="summary"></a>摘要
讓我們回顧一下您在本教學課程中學習到的內容︰

* 您已了解如何註冊、取得訂用帳戶，以及瀏覽至入口網站
* 您已熟習入口網站的 UI，並了解如何建立和瀏覽資源
* 您已了解如何建立資源和瀏覽資源
* 您已了解結構或管理刀鋒視窗，以及如何以一致的方式管理不同類型的資源
* 您已了解如何自訂入口網站，以將您關心的資訊放到顯而易見的位置
* 您已了解如何控制使用角色型存取 (RBAC) 之資源的存取權
* 您已了解如何取得說明和支援

Microsoft Azure 入口網站會徹底簡化建置及管理您在雲端中的應用程式。  由於我們不斷地[聆聽意見](https://feedback.azure.com/forums/223579-azure-preview-portal/)並加以改善，請查看[管理部落格](https://azure.microsoft.com/blog/topics/management/)以了解最新資訊。  [ScottGu 的部落格](http://weblogs.asp.net/scottgu) 是尋找所有 Azure 更新的另一個絕佳網站。

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png
