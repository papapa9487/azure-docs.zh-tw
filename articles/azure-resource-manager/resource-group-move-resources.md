---
title: "將 Azure 資源移至新的訂用帳戶或資源群組 | Microsoft Docs"
description: "使用 Azure Resource Manager 將資源移到新的資源群組或訂用帳戶。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: fe6a28afbee573351186e361126ddca7db764311
ms.contentlocale: zh-tw
ms.lasthandoff: 07/17/2017

---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>將資源移動到新的資源群組或訂用帳戶
本主題說明如何將資源移至新的訂用帳戶或相同訂用帳戶中新的資源群組。 您可以使用入口網站、PowerShell、Azure CLI 或 REST API 來移動資源。 本主題中的移動作業可供您使用而不需要任何 Azure 支援的協助。

移動資源時，在此作業期間會同時鎖定來源群組和目標群組。 資源群組上的寫入和刪除作業將會封鎖，直到移動完成。 此鎖定表示您無法新增、更新或刪除資源群組中的資源，但不表示資源已遭到凍結。 例如，如果您將 SQL Server 和其資料庫移至新的資源群組，使用該資料庫的應用程式不會發生停機時間。 它仍可對資料庫讀取和寫入。

您無法變更資源的位置。 移動資源只會將它移動到新的資源群組。 新的資源群組可能會有不同的位置，但那樣不會變更資源的位置。

> [!NOTE]
> 本文說明如何在現有的 Azure 帳戶提供項目內移動資源。 如果您真的想要變更 Azure 帳戶提供項目 (例如，從隨用隨付升級為預付)，同時繼續使用現有的資源，請參閱 [切換至不同的 Azure 訂用帳戶優惠](../billing/billing-how-to-switch-azure-offer.md)。
>
>

## <a name="checklist-before-moving-resources"></a>移動資源前的檢查清單
在移動資源之前，要執行的重要步驟如下︰ 藉由驗證這些條件，您可以避免錯誤。

1. 來源和目的地的訂用帳戶必須存在於相同的 [Azure Active Directory 租用戶](../active-directory/active-directory-howto-tenant.md)內。 若要檢查這兩個訂用帳戶都有相同的租用戶識別碼，請使用 Azure PowerShell 或 Azure CLI。

  如果是 Azure PowerShell，請使用：

  ```powershell
  (Get-AzureRmSubscription -SubscriptionName "Example Subscription").TenantId
  ```

  如果是 Azure CLI 2.0，請使用：

  ```azurecli
  az account show --subscription "Example Subscription" --query tenantId
  ```

  如果來源和目的地訂用帳戶的租用戶識別碼不相同，您可以嘗試變更訂用帳戶的目錄。 不過，此選項僅提供給使用 Microsoft 帳戶 (非組織帳戶) 登入的服務系統管理員。 若要嘗試變更目錄，登入到[傳統入口網站](https://manage.windowsazure.com/)，然後選取 [設定]，再選取訂用帳戶。 如果 [編輯目錄] 圖示可用，請選取它來變更相關聯的 Azure Active Directory。

  ![編輯目錄](./media/resource-group-move-resources/edit-directory.png)

  如果該圖示不可用，您必須連絡支援人員將資源移動到新的租用戶。

2. 服務必須啟用移動資源的功能。 本主題列出哪些服務可實現移動資源，哪些服務無法實現移動資源。
3. 必須針對要移動之資源的資源提供者註冊其目的地訂用帳戶。 否則，您會收到錯誤，指出 **未針對資源類型註冊訂用帳戶**。 將資源移至新的訂用帳戶時，可能會因為該訂用帳戶不曾以指定的資源類型使用過而遇到問題。 若要了解如何檢查註冊狀態及註冊資源提供者，請參閱 [資源提供者和類型](resource-manager-supported-services.md)。

## <a name="when-to-call-support"></a>呼叫支援的時機
您可以透過本主題顯示的自助式作業，移動大部分資源。 使用自助式作業︰

* 移動 Resource Manager 資源。
* 根據[傳統部署限制](#classic-deployment-limitations)移動傳統資源。

當您需要進行下列作業時，請連絡支援人員︰

* 將資源移至新的 Azure 帳戶 (與 Azure Active Directory 租用戶)。
* 移動傳統資源，但有限制的問題。

## <a name="services-that-enable-move"></a>啟用移動的服務
目前啟用移動到新資源群組與訂用帳戶的服務有：

* API 管理
* App Service 應用程式 (Web 應用程式) - 請參閱 [App Service 限制](#app-service-limitations)
* Application Insights
* 自動化
* Batch
* Bing 地圖
* CDN
* 雲端服務 - 請參閱 [傳統部署限制](#classic-deployment-limitations)
* 辨識服務
* 內容仲裁
* 資料目錄
* Data Factory
* Data Lake Analytics
* Data Lake Store
* DNS
* Azure Cosmos DB
* 事件中樞
* HDInsight 叢集 - 請參閱 [HDInsight 限制](#hdinsight-limitations)
* IoT 中樞
* Key Vault
* 負載平衡器
* Logic Apps
* 機器學習服務
* 媒體服務
* Mobile Engagement
* 通知中樞
* Operational Insights
* Operations Management
* Power BI
* Redis 快取
* 排程器
* 搜尋
* 伺服器管理
* 服務匯流排
* Service Fabric
* 儲存體
* 儲存體 (傳統) - 請參閱 [傳統部署限制](#classic-deployment-limitations)
* 串流分析
* SQL Database 伺服器 - 資料庫和伺服器必須位於相同的資源群組。 當您移動 SQL 伺服器時，其所有資料庫也會跟著移動。
* 流量管理員
* 虛擬機器
* 憑證儲存在 Key Vault 中的虛擬機器 - 已啟用移動至相同訂用帳戶中新資源群組的功能，但未啟用跨訂用帳戶之間的移動。
* 虛擬機器 (傳統) - 請參閱 [傳統部署限制](#classic-deployment-limitations)
* 虛擬機器擴展集
* 虛擬網路 - 目前，在停用 VNet 對等互連之前，將無法移動對等的虛擬網路。 一旦停用之後，就能成功移動虛擬網路，並接著啟用 VNet 對等互連。 此外，如果虛擬網路包含任何有資源導覽連結的子網路，則無法將虛擬網路移動到其他訂用帳戶。 例如，當 Microsoft.Cache Redis 資源部署到虛擬網路子網路時，該子網路便具有資源導覽連結。
* VPN 閘道


## <a name="services-that-do-not-enable-move"></a>不啟用移動的服務
目前不啟用移動資源的服務有：

* AD Domain Services
* AD 混合式健康狀態服務
* 應用程式閘道
* 具有使用受控磁碟之虛擬機器的可用性設定組
* BizTalk 服務
* 容器服務
* ExpressRoute
* DevTest Labs - 已啟用移動至相同訂用帳戶中新資源群組的功能，但未啟用跨訂用帳戶之間的移動。
* Dynamics LCS
* 從受控磁碟建立的映像
* 受控磁碟
* 受管理的應用程式
* 復原服務保存庫 - 也不會移動與「復原服務」保存庫關聯的「計算」、「網路」及「儲存體」資源，請參閱 [復原服務限制](#recovery-services-limitations)。
* 安全性
* 從受控磁碟建立的快照集
* StorSimple 裝置管理員
* 使用受控磁碟的虛擬機器
* 虛擬網路 (傳統) - 請參閱 [傳統部署限制](#classic-deployment-limitations)
* 從 Marketplace 資源建立的虛擬機器 - 無法在訂用帳戶之間移動。 資源必須先在目前的訂用帳戶中取消佈建，並於新訂用帳戶中再次部署

## <a name="app-service-limitations"></a>App Service 限制
使用 App Service 應用程式時，您無法只移動 App Service 方案。 若要移動 App Service 應用程式，您的選項如下：

* 將該資源群組中的 App Service 方案和所有其他 App Service 資源，都移到還沒有 App Service 資源的新資源群組。 這項需求意謂著您甚至必須移動與 App Service 方案沒有關聯的 App Service 資源。
* 將應用程式移到不同的資源群組，但在原始資源群組中保留所有 App Service 方案。

App Service 方案不需要與應用程式位於相同的資源群組，應用程式就能正確運作。

例如，如果您的資源群組包含︰

* 與 **plan-a** 關聯的 **web-a**
* 與 **plan-b** 關聯的 **web-b**

您的選項如下：

* 移動 **web-a**、**plan-a**、**web-b** 及 **plan-b**
* 移動 **web-a** 和 **web-b**
* 移動 **web-a**
* 移動 **web-b**

所有其他組合都會留下移動 App Service 方案時無法留下的資源類型 (任何類型的 App Service 資源)。

如果 Web 應用程式與其 App Service 方案位於不同的資源群組，但您想要將兩者移到新的資源群組，則必須使用兩個步驟來執行移動。 例如：

* **web-a** 位於 **web-group** 中
* **plan-a** 位於 **plan-group** 中
* 您想要讓 **web-a** 和 **plan-a** 位於 **combined-group** 中

若要完成這項移動，請依下列序列執行兩個不同的移動作業︰

1. 將 **web-a** 移到 **plan-group**
2. 將 **web-a** 和 **plan-a** 移到 **combined-group**。

您可以將 App Service 憑證移至新資源群組或訂用帳戶，而不會發生任何問題。 不過，如果您的 Web 應用程式包含在外部購買，並上傳至應用程式的 SSL 憑證，您必須在移動 Web 應用程式之前刪除憑證。 例如，您可以執行下列步驟︰

1. 刪除從 Web 應用程式上傳的憑證
2. 移動 Web 應用程式
3. 將憑證上傳至 Web 應用程式

## <a name="recovery-services-limitations"></a>復原服務限制
無法移動用來設定 Azure Site Recovery 相關災害復原的「儲存體」、「網路」或「計算」資源。

舉例來說，假設您已設定將內部部署機器複寫到某個儲存體帳戶 (Storage1)，而想要讓受保護的機器在容錯移轉到 Azure 之後，以連接到虛擬網路 (Network1) 的虛擬機器 (VM1) 身分上線。 您無法跨相同訂用帳戶內的資源群組或跨訂用帳戶來移動任何這些 Azure 資源 - Storage1、VM1 及 Network1。

## <a name="hdinsight-limitations"></a>HDInsight 限制

您可以將 HDInsight 叢集移至新的訂用帳戶或資源群組。 不過，您無法跨訂用帳戶來移動連結至 HDInsight 叢集的網路資源 (例如虛擬網路、NIC 或負載平衡器)。 此外，您無法將已連接至叢集虛擬機器的 NIC 移至新的資源群組。

將 HDInsight 叢集移至新的訂用帳戶時，請先移動其他資源 (例如儲存體帳戶)。 然後，移動 HDInsight 叢集本身。

## <a name="classic-deployment-limitations"></a>傳統部署限制
移動透過傳統模型所部署之資源的選項，會根據移動訂用帳戶內的資源還是將資源移到新的訂用帳戶而有所不同。

### <a name="same-subscription"></a>相同訂用帳戶
將資源從一個資源群組移到相同訂用帳戶內的另一個資源群組時，適用下列限制︰

* 無法移動虛擬網路 (傳統)。
* 虛擬機器 (傳統) 必須與雲端服務一起移動。
* 只有當移動作業包含雲端服務的所有虛擬機器時，才能移動雲端服務。
* 一次只能移動一個雲端服務。
* 一次只能移動一個儲存體帳戶 (傳統)。
* 透過相同的作業，儲存體帳戶 (傳統) 不能與虛擬機器或雲端服務一起移動。

若要將傳統資源移到相同訂用帳戶內的新資源群組，請透過[入口網站](#use-portal)、[Azure PowerShell](#use-powershell)、[Azure CLI](#use-azure-cli) 或 [REST API](#use-rest-api)，使用標準移動作業。 當您移動 Resource Manager 資源時，您會使用相同的作業。

### <a name="new-subscription"></a>新的訂用帳戶
將資源移到新訂用帳戶時，適用下列限制︰

* 必須透過相同的作業移動訂用帳戶中的所有傳統資源。
* 目標訂用帳戶不得包含任何其他傳統資源。
* 只能透過適用於傳統移動的個別 REST API 來要求移動。 將傳統資源移到新的訂用帳戶時，標準 Resource Manager 移動命令無法運作。

若要將傳統資源移到新的訂用帳戶，請使用傳統資源特定的 REST 作業。 若要使用 REST，請執行下列步驟：

1. 請檢查來源訂用帳戶是否可以參與跨訂用帳戶移動。 請使用下列作業：

  ```HTTP   
  POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     在要求本文中包含：

  ```json
  {
    "role": "source"
  }
  ```

     驗證作業的回應格式如下︰

  ```json
  {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
  }
  ```

2. 請檢查目的地訂用帳戶是否可以參與跨訂用帳戶移動。 請使用下列作業：

  ```HTTP
  POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     在要求本文中包含：

  ```json
  {
    "role": "target"
  }
  ```

     回應的格式與來源訂用帳戶驗證的格式相同。
3. 如果兩個訂用帳戶都通過驗證，使用下列作業將所有傳統資源從某個訂用帳戶移到另一個訂用帳戶︰

  ```HTTP
  POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
  ```

    在要求本文中包含：

  ```json
  {
    "target": "/subscriptions/{target-subscription-id}"
  }
  ```

這項作業可能需要幾分鐘的時間執行。

## <a name="use-portal"></a>使用入口網站
若要移動資源，請選取包含這些資源的資源群組，然後選取 [移動] 按鈕。

![移動資源](./media/resource-group-move-resources/select-move.png)

選擇將資源移動到新的資源群組或新的訂用帳戶。

選取要移動的資源和目的地資源群組。 認可您需要更新這些資源的指令碼，然後選取 [確定] 。 如果您在上一個步驟中選取了 [編輯訂用帳戶] 圖示，則也必須選取目的地訂用帳戶。

![選取目的地](./media/resource-group-move-resources/select-destination.png)

在 [通知] 中，您會看到移動作業正在執行。

![顯示移動狀態](./media/resource-group-move-resources/show-status.png)

完成後，您會收到結果的通知。

![顯示移動結果](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>使用 PowerShell
若要將現有的資源移動到另一個資源群組或訂用帳戶，請使用 `Move-AzureRmResource` 命令。

第一個範例顯示如何將某個資源移動到新的資源群組。

```powershell
$resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId
```

第二個範例顯示如何將多個資源移動到新的資源群組。

```powershell
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

若要移動到新的訂用帳戶，請包含 `DestinationSubscriptionId`參數的值。

系統會要求您確認您想要移動指定的資源。

```powershell
Confirm
Are you sure you want to move these resources to the resource group
'/subscriptions/{guid}/resourceGroups/newRG' the resources:

/subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
/subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
```

## <a name="use-azure-cli-20"></a>使用 Azure CLI 2.0
若要將現有的資源移動到另一個資源群組或訂用帳戶，請使用 `az resource move` 命令。 提供要移動之資源的資源識別碼。 您可以使用下列命令取得資源識別碼︰

```azurecli
az resource show -g sourceGroup -n storagedemo --resource-type "Microsoft.Storage/storageAccounts" --query id
```

下列範例說明如何將儲存體帳戶移動到新的資源群組。 請在 `--ids` 參數中，為要移動的資源識別碼提供以空格分隔的清單。

```azurecli
az resource move --destination-group newgroup --ids "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo"
```

若要移動到新的訂用帳戶，請提供 `--destination-subscription-id` 參數。

## <a name="use-azure-cli-10"></a>使用 Azure CLI 1.0
若要將現有的資源移動到另一個資源群組或訂用帳戶，請使用 `azure resource move` 命令。 提供要移動之資源的資源識別碼。 您可以使用下列命令取得資源識別碼︰

```azurecli
azure resource list -g sourceGroup --json
```

它會傳回下列格式︰

```azurecli
[
  {
    "id": "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo",
    "name": "storagedemo",
    "type": "Microsoft.Storage/storageAccounts",
    "location": "southcentralus",
    "tags": {},
    "kind": "Storage",
    "sku": {
      "name": "Standard_RAGRS",
      "tier": "Standard"
    }
  }
]
```

下列範例說明如何將儲存體帳戶移動到新的資源群組。 請在 `-i` 參數中，為要移動的資源識別碼提供以逗號分隔的清單。

```azurecli
azure resource move -i "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo" -d "destinationGroup"
```

系統會要求您確認您想要移動指定的資源。

## <a name="use-rest-api"></a>使用 REST API
若要將現有的資源移動到另一個資源群組或訂用帳戶，請執行：

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

在要求主體中，您可以指定目標資源群組以及要移動的資源。 如需有關 REST 移動作業的詳細資訊，請參閱 [移動資源](https://msdn.microsoft.com/library/azure/mt218710.aspx)。

## <a name="next-steps"></a>後續步驟
* 若要了解用於管理訂用帳戶的 PowerShell Cmdlet，請參閱 [搭配使用 Azure PowerShell 與 Azure Resource Manager](powershell-azure-resource-manager.md)。
* 若要了解用於管理訂用帳戶的 Azure CLI 命令，請參閱 [搭配使用 Azure CLI 與 Azure Resource Manager](xplat-cli-azure-resource-manager.md)。
* 若要了解用於管理訂用帳戶的入口網站功能，請參閱 [使用 Azure 入口網站來管理資源](resource-group-portal.md)。
* 若要了解如何將邏輯組織套用到您的資源，請參閱 [使用標記來組織您的資源](resource-group-using-tags.md)。

