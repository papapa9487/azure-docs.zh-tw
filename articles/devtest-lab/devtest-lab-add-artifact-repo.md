---
title: "將 Git存放庫加入 Azure DevTest Labs 中的實驗室 | Microsoft Docs"
description: "將適用於自訂構件來源的 GitHub 或 Visual Studio Team Services Git 儲存機制加入 Azure DevTest Labs"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: tarcher
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 053f92a65f9ae29154d471fd22ee842620b4f273
ms.contentlocale: zh-tw
ms.lasthandoff: 08/24/2017

---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-azure-resource-manager-templates"></a>新增可以存放自訂構件和 Azure Resource Manager 範本的 Git 存放庫

如果您想要在實驗室中為 VM [建立自訂構件](devtest-lab-artifact-author.md)，或[使用 Azure Resource Manager 範本來建立自訂的測試環境](devtest-lab-create-environment-from-arm.md)，您必須新增 Git 存放庫，來容納您的團隊建立的構件或 Azure Resource Manager 範本。 儲存機制可以裝載在 [GitHub](https://github.com) 或 [Visual Studio Team Services (VSTS)](https://visualstudio.com) 上。

我們提供[構件的 Github 存放庫](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)，您可以為您的實驗室依原樣部署為自行自訂。 自訂或建立構件時，您無法將構件儲存在公共存放庫中，您必須建立自己的私人存放庫。 

建立 VM 時，您可以儲存 Azure Resource Manager 範本，並依您的需求自訂，並在稍後使用範本來更輕易地建立更多 VM。 您必須建立您自己的私人存放庫，來儲存您自訂的 Azure Resource Manager 範本。  

* 若要了解如何建立 GitHub 儲存機制，請參閱 [GitHub Bootcamp](https://help.github.com/categories/bootcamp/)。
* 若要了解如何使用 Git 儲存機制建立 Team Services 專案，請參閱 [連接到 Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online)。

下列螢幕擷取畫面顯示包含構件的儲存機制在 GitHub 中的可能外觀範例：  
![GitHub 構件儲存機制範例](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>取得儲存機制資訊和認證
若要將存放庫加入您的實驗室，您必須先從存放庫取得特定資訊。 下列各節會引導您取得 GitHub 與 Visual Studio Team Services 上裝載的儲存庫資訊。

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>取得 GitHub 儲存機制複製 URL 和個人存取權杖
若要取得 GitHub 儲存機制複製 URL 和個人存取權杖，請遵循下列步驟：

1. 瀏覽至包含構件或 Azure Resource Manager 範本定義的 GitHub 儲存庫首頁。
2. 選取 [複製或下載] 。
3. 選取將 **HTTPS 複製 URL** 複製到剪貼簿的按鈕，並儲存該 URL 供稍後使用。
4. 選取 GitHub 右上角的設定檔影像，然後選取 [設定] 。
5. 在左側的 [個人設定] 功能表上，選取 [個人存取權杖]。
6. 選取 [產生新的權杖] 。
7. 在 [新增個人存取權杖] 頁面上，輸入 [權杖描述]、接受 [選取範圍] 中的預設項目，然後選擇 [產生權杖]。
8. 儲存產生的權杖，因為您稍後需要用到。
9. 您現在可以關閉 GitHub。   
10. 繼續 [將您的實驗室連接至存放庫](#connect-your-lab-to-the-repository) 一節。

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>取得 Visual Studio Team Services 儲存機制複製 URL 和個人存取權杖
若要取得 Visual Studio Team Services 儲存機制複製 URL 和個人存取權杖，請遵循下列步驟：

1. 開啟您的小組集合首頁 (例如 `https://contoso-web-team.visualstudio.com`)，然後選取專案。
2. 在專案首頁上，選取 [程式碼] 。
3. 若要檢視複製 URL，可在專案 [程式碼] 頁面上，選取 [複製]。
4. 儲存 URL，因為在本教學課程稍後將需要此資訊。
5. 若要建立個人存取權杖，可從使用者帳戶下拉式功能表中選取 [我的設定檔]  。
6. 在 [設定檔資訊] 頁面上，選取 [安全性] 。
7. 在 [安全性] 索引標籤上，選取 [新增]。
8. 在 [建立個人存取權杖]  頁面中：

   * 輸入權杖的 **描述** 。
   * 從 [到期日] 清單中選取 [180 天]。
   * 從 [帳戶] 清單中選擇 [所有可存取的帳戶]。
   * 選擇 [所有範圍]  選項。
   * 選擇 [建立權杖] 。
9. 完成時，新的權杖會出現在 [個人存取權杖]  清單中。 選取 [複製權杖] ，然後儲存權杖值供稍後使用。
10. 繼續 [將您的實驗室連接至存放庫](#connect-your-lab-to-the-repository) 一節。

## <a name="connect-your-lab-to-the-repository"></a>將實驗室連接至存放庫
1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。
3. 從實驗室清單中，選取所需的實驗室。   
4. 在左窗格中，選取 [組態和原則]。
5. 在實驗室的 [組態和原則] 區域上，選取 [存放庫]。
6. 在 [存放庫] 區域上，選取 [+ 新增]。

    ![[新增存放庫] 按鈕](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
7. 在第二個 [存放庫] 頁面上，指定下列資訊︰

   * **名稱** - 輸入儲存機制的名稱。
   * **Git 複製 URL** - 輸入您先前從 GitHub 或 Visual Studio Team Services 複製的 Git HTTPS 複製 URL。
   * 分支 - 輸入取得定義的分支。
   * **個人存取權杖** - 輸入您先前從 GitHub 或 Visual Studio Team Services 取得的個人存取權杖。
   * 資料夾路徑 - 輸入至少與複製 URL 相關的一個資料夾路徑，其中包含構件或 Azure Resource Manager 範本。 指定子目錄時，請確定資料夾路徑中包含斜線。

     ![存放庫區域](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
8. 選取 [ **儲存**]。

## <a name="next-steps"></a>後續步驟
建立私人 Git 存放庫之後，您可以執行下列一或兩個動作，視您的需求而定︰
* 儲存您的 [自訂構件](devtest-lab-artifact-author.md)，您可以在稍後用來建立新的 VM。
* [透過 Azure Resource Manager 範本建立多個 VM 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)，並將範本儲存在您的私人存放庫。

建立 VM 時，您可以確認構件或範本已新增至 Git存放庫。 它們會立即出現在構件或範本清單，您的私人存放庫名稱會顯示在欄位中，藉此顯示其來源。 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="related-blog-posts"></a>相關部落格文章
* [如何在 Azure DevTest Labs 疑難排解失敗的構件](devtest-lab-troubleshoot-artifact-failure.md)
* [Join a VM to existing AD Domain using a resource manager template in Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs) (在 Azure DevTest Labs 使用 Resource Manager 範本將 VM 加入至現有 AD 網域)

