---
title: "管理 Machine Learning 工作區 | Microsoft Docs"
description: "管理 Azure 機器學習工作區的存取權，並部署和管理 ML API Web 服務"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: daf3d413-7a77-4beb-9a7a-6b4bdf717719
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: garye
ms.openlocfilehash: 2f90234bdd5c917a502d24cd16256bc11c7fbed0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-an-azure-machine-learning-workspace"></a>管理 Azure Machine Learning 工作區

> [!NOTE]
> 如需在 Machine Learning Web 服務入口網站中管理 Web 服務的相關資訊，請參閱[使用 Azure Machine Learning Web 服務入口網站管理 Web 服務](manage-new-webservice.md)。
> 
> 

您可以在 Azure 入口網站或 Azure 傳統入口網站中管理 Machine Learning 工作區。

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

若要在 Azure 入口網站中管理工作區︰

1. 使用 Azure 訂用帳戶系統管理員帳戶登入 [Azure 入口網站](https://portal.azure.com/)。
2. 在頁面頂端的搜尋方塊中輸入「Machine Learning 工作區」，然後選取 [Machine Learning 工作區]。
3. 按一下您想要管理的工作區。

除了標準的資源管理資訊和可用的選項，您還可以︰

- 檢視**屬性** - 此頁面會顯示工作區和資源的資訊，而且您可以變更這個工作區所連線的訂用帳戶和資源群組。
- **重新同步儲存體金鑰** - 工作區會保有儲存體帳戶的金鑰。 如果儲存體帳戶變更了金鑰，則您可以按一下 [重新同步金鑰] 來與工作區同步處理金鑰。

若要管理與此工作區相關聯的 Web 服務，請使用 Machine Learning Web 服務入口網站。 如需詳細資訊，請參閱[使用 Azure Machine Learning Web 服務入口網站管理 Web 服務](manage-new-webservice.md)。

> [!NOTE]
> 若要部署或管理新的 Web 服務，您必須獲得下列角色的指派：要部署 Web 服務之訂用帳戶上的參與者或系統管理員角色。 如果您邀請另一位使用者到 Machine Learning 工作區，就必須為他們指派訂用帳戶上的參與者或系統管理員角色，然後他們才能部署或管理 Web 服務。 
> 
>如需有關設定存取權限的詳細資訊，請參閱[在 Azure 入口網站 (公開預覽) 中檢視使用者和群組的存取權指派](../../active-directory/role-based-access-control-manage-assignments.md)。

## <a name="use-the-azure-classic-portal"></a>使用 Azure 傳統入口網站

您可以使用 Azure 傳統入口網站來管理您的機器學習服務工作區，以便：

* 監視工作區的使用方式
* 設定工作區以允許或拒絕存取
* 管理工作區中建立的 Web 服務
* 刪除工作區

此外，儀表板索引標籤會提供工作區使用方式的概觀和工作區資訊的快速概覽。  

> [!TIP]
> 在 Azure Machine Learning Studio 中，您可以在 [Web 服務] 索引標籤上新增、更新或刪除 Machine Learning Web 服務。
> 
> 

若要在 Azure 傳統入口網站中管理工作區︰

1. 使用您的 Microsoft Azure 帳戶登入 [Azure 傳統入口網站](https://manage.windowsazure.com/) - 使用與 Azure 訂用帳戶相關聯的帳戶。
2. 在 Microsoft Azure 服務面板中，按一下 [機器學習] 。
3. 按一下您想要管理的工作區。

工作區頁面有三個索引標籤：

* **儀表板** - 可讓您檢視工作區使用方式和資訊
* **設定** - 可讓您管理對工作區的存取
* **Web 服務** - 可讓您管理從此工作區發佈的 Web 服務

### <a name="to-monitor-how-the-workspace-is-being-used"></a>監視工作區的使用方式
按一下 [ **儀表板** ] 索引標籤。

從儀表板，您可以檢視工作區的整體使用量，並取得工作區資訊的快速概覽。

* **計算** 圖表會顯示工作區正在使用的計算資源。 您可以變更檢視來顯示相對或絕對值，並且可以變更圖表中顯示的時間範圍。
* **使用量概觀** 顯示工作區正在使用的 Azure 儲存體。
* **快速概覽** 提供工作區資訊和實用連結的摘要。

> [!NOTE]
> [ **登入 ML Studio** ] 連結會使用目前登入的 Microsoft 帳戶來開啟 Machine Learning Studio。 您用來登入 Azure 傳統入口網站以建立工作區的 Microsoft 帳戶，不會自動具備開啟該工作區的權限。 若要開啟工作區，您必須使用定義為工作區擁有者的 Microsoft 帳戶登入，或者您需要收到來自擁有者的邀請，才能加入工作區。
> 
> 

### <a name="to-grant-or-suspend-access-for-users"></a>授與或暫停使用者的存取權
按一下 [設定]  索引標籤。

您可以從設定索引標籤：

* 按一下 [拒絕] 來擱置對機器學習服務工作區的存取。 使用者將不再能在 Machine Learning Studio 中開啟工作區。 若要還原存取，請按一下 [允許]。

若要管理可以存取 Machine Learning Studio 中工作區的其他帳戶，請按一下 [儀表板] 索引標籤中的 [登入 ML Studio]\(請參閱上述有關**登入 ML Studio** 的附註)。 這會在 Machine Learning Studio 中開啟工作區。 從這裡按一下 [設定] 索引標籤，然後按一下 [使用者]。 您可以按一下 [邀請使用者]，讓使用者存取工作區，或選取使用者，並按一下 [移除]。

### <a name="to-manage-web-services-in-this-workspace"></a>管理此工作區中的 Web 服務
按一下 [ **Web 服務** ] 索引標籤。

這會顯示從此工作區發佈的 Web 服務的清單。
若要管理 Web 服務，請按一下清單中的名稱以開啟 Web 服務頁面。

Web 服務可能會有一個或多個定義的端點。

* 除了「預設」端點以外，您可以定義更多端點。 若要新增端點，請按一下儀表板底部的 [管理端點]，開啟 Azure Machine Learning Web 服務入口網站。
* 若要刪除端點 (您無法刪除「預設」端點)，請按一下端點列開頭的核取方塊，然後按一下 [刪除]。 這會從 Web 服務移除端點。
  
  > [!NOTE]
  > 如果刪除端點時應用程式使用 Web 服務端點，應用程式會在下一次嘗試存取服務時收到錯誤。
  > 
  > 

按一下 Web 服務端點的名稱以開啟它。 

您可以從儀表板中檢視您的 Web 服務經過一段時間的整體使用量。 您可以從使用量圖表右上角的 [期間] 下拉式功能表中選取要檢視的期間。 儀表板會顯示下列資訊：

* **一段時間的要求數** 顯示選取的一段時間內，要求數目的步階圖形。 它可以協助識別您所遇到的使用量尖峰。
* **要求-回應要求數** 顯示服務在選取的一段時間內收到的要求-回應呼叫總數，以及失敗的數目。
* **平均要求-回應計算時間** 顯示執行收到的要求所需的平均時間。
* **批次要求數** 顯示服務在選取的一段時間內收到的批次要求總數，以及失敗的數目。
* **平均作業延遲** 顯示執行收到的要求所需的平均時間。
* **錯誤數**顯示呼叫 Web 服務時所發生的錯誤彙總數目。
* **服務成本** 顯示與服務相關聯的計費方案費用。

您可以從 [設定] 頁面更新下列屬性：

* [描述] 可讓您輸入 Web 服務的描述。 [描述] 必要欄位。
* [記錄] 可讓您啟用或停用端點上的錯誤記錄。 如需有關記錄的詳細資訊，請參閱[為 Machine Learning Web 服務啟用記錄](web-services-logging.md)。
* [啟用範例資料] 可讓您提供範例資料，用來測試要求-回應服務。 如果您是在 Machine Learning Studio 中建立 Web 服務，範例資料會取自您用來訓練模型的資料。 如果您是以程式設計方式建立服務，資料會取自您提供做為 JSON 套件一部分的範例資料。

