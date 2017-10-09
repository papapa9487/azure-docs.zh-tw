---
title: "將虛擬機器提供給您的 Azure Stack 使用者| Microsoft Docs"
description: "此教學課程說明如何將虛擬機器提供給 Azure Stack"
services: azure-stack
documentationcenter: 
author: vhorne
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 9/25/2017
ms.author: victorh
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: afeec92c40262903e6cfd3c6d75a595fead616e3
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="make-virtual-machines-available-to-your-azure-stack-users"></a>將虛擬機器提供給您的 Azure Stack 使用者

*適用於：Azure Stack 整合系統和 Azure Stack 開發封裝*

身為 Azure Stack 雲端系統管理員，您可以建立供應項目，以供您的使用者 (有時稱為租用戶) 訂閱。 利用其訂用帳戶，使用者可以接著取用 Azure Stack 服務。

此文章說明如何建立供應項目並進行測試。 針對測試，您將必須以使用者身分登入入口網站，訂閱該供應項目，然後使用訂用帳戶建立虛擬機器。

您將了解：

> [!div class="checklist"]
> * 建立優惠
> * 新增映像
> * 測試供應項目


在 Azure Stack 中，能透過訂用帳戶、供應項目與方案，為使用者提供服務。 使用者可以訂閱多個供應項目。 優惠可以有一個或多個方案，而方案則可有一或多項服務。

![訂用帳戶、供應項目與方案](media/azure-stack-key-features/image4.png)

若要深入了解，請參閱 [Azure Stack 中的主要功能與概念](azure-stack-key-features.md)。

## <a name="create-an-offer"></a>建立優惠

現在您可以為使用者準備項目。 當您開始進行此程序時，系統會先提示您建立供應項目，然後建立方案，最後建立配額。

3. **建立優惠**

   供應項目是一組提供者供使用者購買或訂閱的一或多個方案。

   a. 以系統管理員身分[登入](azure-stack-connect-azure-stack.md)入口網站，然後按一下 [新增] > [租用戶供應項目 + 方案] > [供應項目]。
   ![新增供應項目](media/azure-stack-tutorial-tenant-vm/image01.png)

   b. 在 [新增供應項目] 區段中，填寫 [顯示名稱] 與 [資源名稱]，然後選取新的或現有的 [資源群組]。 [顯示名稱] 是供應項目的易記名稱。 只有雲端操作員可以看到 [資源名稱]。 它是系統管理員用來處理其他供應項目 (以 Azure Resource Manager 資源方式) 的名稱。

   ![顯示名稱](media/azure-stack-tutorial-tenant-vm/image02.png)

   c. 按一下 [基本方案]，然後在 [方案] 區段中，按一下 [新增] 以將新方案新增到供應項目中。

   ![新增方案](media/azure-stack-tutorial-tenant-vm/image03.png)

   d. 在 [新增方案] 區段中，填寫 [顯示名稱] 與 [資源名稱]。 [顯示名稱] 是使用者看到的方案易記名稱。 只有雲端操作員可以看到 [資源名稱]。 它是雲端操作員用來處理其他方案 (以 Azure Resource Manager 資源方式) 的名稱。

   ![方案顯示名稱](media/azure-stack-tutorial-tenant-vm/image04.png)

   e. 按一下 服務，選取 Microsoft.Compute、Microsoft.Network 及 Microsoft.Storage，然後按一下選取。

   ![方案服務](media/azure-stack-tutorial-tenant-vm/image05.png)

   f. 按一下 [配額]，然後選取您要建立配額的第一個服務。 針對 IaaS 配額，請針對計算、網路與儲存體服務依照這些步驟執行。

   在此範例中，我們先為計算服務建立配額。 在命名空間清單中，選取 [Microsoft.Compute] 命名空間，然後按一下 [建立新的配額]。
   
   ![建立新的配額](media/azure-stack-tutorial-tenant-vm/image06.png)

   g. 在 [建立配額] 區段中，輸入配額名稱並為配額設定想要的參數，然後按一下 [確定]。

   ![配額名稱](media/azure-stack-tutorial-tenant-vm/image07.png)

   h. 現在，針對 **Microsoft.Compute**，選取您所建立的配額。

   ![選取配額](media/azure-stack-tutorial-tenant-vm/image08.png)

   針對網路與儲存體服務重複這些步驟，然後按一下配額 區段中的 確定。

   i. 按一下 [新的方案] 區段中的 [確定]。

   j. 在 [方案] 區段中，選取新方案並按一下 [選取]。

   k. 在 [新增供應項目] 區段中，按一下 [建立]。 建立您的供應項目之後，您會看到通知。

   l. 在儀表板功能表上，按一下 [供應項目]，然後按一下您建立的供應項目。

   m. 按一下 變更狀態，然後按一下公開。

   ![公開映像](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>新增映像

佈建虛擬機器之前，您必須新增一張影像到 Azure Stack 市集。 您可以從 Azure Marketplace 新增您選擇的影像，包括 Linux 影像。

若您是在已連結情節中作業，且您已向 Azure 註冊 Azure Stack 執行個體，您可以使用[從 Azure 下載 項目到 Azure Stack](azure-stack-download-azure-marketplace-item.md) 主題中所述的步驟，從 Azure Marketplace 下載 Windows Server 2016 VM 映像。

如需有關將不同項目新增到 Marketplace 的資訊，請參閱 [Azure Stack Marketplace](azure-stack-marketplace.md)。

## <a name="test-the-offer"></a>測試供應項目

既然您已經建立供應項目，您可以進行測試。 以使用者身分登入並訂閱該供應項目，然後新增虛擬機器。

1. **訂閱提供項目**

   現在您能以使用者身分登入到入口網站，以訂閱供應項目。

   a. 以使用者身分登入使用者入口網站，並按一下 [Get a Subscription]\(取得訂用帳戶\)。
   - 整合系統的 URL 會由於操作員的地區和外部網域名稱而有所不同，格式為 https://portal.&lt;地區&gt;.&lt;FQDN&gt;。
   - 如果您使用 Azure Stack 開發封裝，入口網站位址是 https://portal.local.azurestack.external。

   ![取得訂用帳戶](media/azure-stack-subscribe-plan-provision-vm/image01.png)

   b. 在 顯示名稱 欄位中，輸入您的訂閱名稱，按一下 供應項目，按一下 選擇供應項目 區段中的其中一個供應項目，然後按一下建立。

   ![建立優惠](media/azure-stack-subscribe-plan-provision-vm/image02.png)

   c. 若要檢視您建立的訂用帳戶，請按一下 [更多服務]、按一下 [訂用帳戶]，然後按一下您的新訂用帳戶。  

   訂閱供應項目之後，請重新整理入口網站以查看哪些服務是新訂用帳戶的一部分。

2. **佈建虛擬機器**

   現在您能以使用者身分登入入口網站，以使用訂用帳戶佈建虛擬機器。 

   a. 以使用者身分登入使用者入口網站，然後按一下新增 > **計算** > **Windows Server 2016 Datacenter 評估版**。
      - 整合系統的 URL 會由於操作員的地區和外部網域名稱而有所不同，格式為 https://portal.&lt;地區&gt;.&lt;FQDN&gt;。
   - 如果您使用 Azure Stack 開發封裝，入口網站位址是 https://portal.local.azurestack.external。

   b. 在 [基本] 區段中，輸入 [名稱][使用者名稱]與 [密碼]。 針對 [VM 磁碟類型]，請選擇 [HDD]。 選擇 [訂用帳戶] 。 建立 資源群組，或選取現有的資源群組，然後按一下確定。  

   c. 在 選擇大小 區段中，按一下 A1 基本，然後按一下選取。  

   d. 在 [設定] 區段中，按一下 [虛擬網路]。 在 [選擇虛擬網路] 區段中，按一下 [建立新項目]。 在 [建立虛擬網路] 區段中，接受所有預設值，然後按一下 [確定]。 在 [設定] 區段中，按一下 [確定]。

   ![建立虛擬網路](media/azure-stack-provision-vm/image04.png)

   e. 在 [摘要] 區段中，按一下 [確定] 以建立虛擬機器。  

   f. 若要查看您的新虛擬機器，請按一下 [所有資源]，然後搜尋虛擬機器並按一下其名稱。

    ![所有資源](media/azure-stack-provision-vm/image06.png)

在本教學課程中，您已了解：

> [!div class="checklist"]
> * 建立優惠
> * 新增映像
> * 測試供應項目

> [!div class="nextstepaction"]
> [將 Web、行動裝置與 API 應用程式提供給您的 Azure Stack 使用者](azure-stack-tutorial-app-service.md)
