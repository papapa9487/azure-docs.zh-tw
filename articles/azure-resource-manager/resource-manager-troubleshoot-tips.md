---
title: "了解 Azure 部署錯誤 | Microsoft Docs"
description: "說明如何了解 Azure 部署錯誤。"
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "部署錯誤, azure 部署, 部署至 azure"
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: tomfitz
ms.openlocfilehash: b67bb30fa259fa08e37e11afec724c8b8c3eb633
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2017
---
# <a name="understand-azure-deployment-errors"></a>了解 Azure 部署錯誤
本主題說明部署錯誤，以及如何找出與錯誤相關的詳細資訊。 如需常見部署錯誤的解決方案，請參閱[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](resource-manager-common-deployment-errors.md)。

## <a name="two-types-of-errors"></a>兩種錯誤類型
您可能會收到兩種錯誤類型︰

* 驗證錯誤
* 部署錯誤

下圖顯示訂用帳戶的活動記錄檔。 它代表兩個部署。 在其中一個部署中，範本驗證失敗 (**驗證**) 且無法繼續進行。 在另一個部署中，範本已通過驗證，但在建立資源時失敗 (**寫入部署**)。 

![顯示錯誤碼](./media/resource-manager-troubleshoot-tips/show-activity-log.png)

驗證錯誤來自可在部署之前判斷的情況。 其中包含您範本中的語法錯誤，或者嘗試部署會超出您訂用帳戶配額的資源。 部署程序期間出現的情況下會發生驗證錯誤。 其中包括嘗試存取以平行方式部署的資源。

這兩種錯誤類型都會傳回錯誤碼，以供您針對部署進行疑難排解。 這兩種錯誤類型都會出現在[活動記錄](resource-group-audit.md)中。 不過，驗證錯誤不會出現在部署歷程記錄中，因為部署永遠不會啟動。

## <a name="determine-error-code"></a>判斷錯誤碼

您可以藉由查看錯誤訊息和錯誤碼來了解錯誤。 [使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](resource-manager-common-deployment-errors.md)一文會依錯誤碼列出解決方案。 本主題說明如何使用 Azure 入口網站來探索錯誤碼。

### <a name="validation-errors"></a>驗證錯誤

透過入口網站進行部署時，您在提交值之後看到驗證錯誤。

![顯示入口網站驗證錯誤](./media/resource-manager-troubleshoot-tips/validation-error.png)

選取訊息以取得詳細資訊。 在下圖中，您會看到 **InvalidTemplateDeployment** 錯誤，以及指出某個原則封鎖了部署的訊息。

![顯示驗證詳細資料](./media/resource-manager-troubleshoot-tips/validation-details.png)

### <a name="deployment-errors"></a>部署錯誤

當作業通過驗證，但在部署期間失敗時，您會在通知中看見錯誤。 選取通知。

![通知錯誤](./media/resource-manager-troubleshoot-tips/notification.png)

您會看到更多與部署相關的詳細資料。 選取選項來尋找與錯誤相關的詳細資訊。

![部署失敗](./media/resource-manager-troubleshoot-tips/deployment-failed.png)

您會看到錯誤訊息和錯誤碼。 請注意，有兩個錯誤碼。 第一個錯誤碼 (**DeploymentFailed**) 是一般錯誤，不會提供您解決錯誤所需的詳細資料。 第二個錯誤碼 (**StorageAccountNotFound**) 會提供您需要的詳細資料。 

![錯誤詳細資料](./media/resource-manager-troubleshoot-tips/error-details.png)

## <a name="enable-debug-logging"></a>啟用偵錯記錄
有時您需要與要求和回應相關的詳細資訊，以探索哪裡出了錯。 使用 PowerShell 或 Azure CLI，您可以要求在部署期間記錄其他資訊。

- PowerShell

   在 PowerShell 中，將 **DeploymentDebugLogLevel** 參數設定為 [All]、[ResponseContent] 或 [RequestContent]。

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   檢查具有下列 Cmdlet 的要求內容︰

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   或者，具有下列項目的回應內容︰

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   此資訊可協助您判斷範本中的值是否會正確設定。

- Azure CLI

   使用下列命令檢查部署作業︰

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- 巢狀範本

   若要記錄巢狀範本的偵錯資訊，請使用 **debugSetting** 項目。

  ```json
  {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri": "{template-uri}",
              "contentVersion": "1.0.0.0"
          },
          "debugSetting": {
             "detailLevel": "requestContent, responseContent"
          }
      }
  }
  ```


## <a name="create-a-troubleshooting-template"></a>建立疑難排解範本
在某些情況下，針對範本進行疑難排解的最簡單方式是測試其組件。 您可以建立簡化範本，以便專注於您認為是錯誤成因的組件。 例如，假設您在參考資源時收到錯誤。 不需要處理整個範本，而是建立一個會傳回可能造成問題之組件的範本。 它可協助您判斷是否傳入正確的參數、正確地使用範本函式，以及取得所預期的資源。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

或者，假設您遇到您認為與未正確設定之相依性有關的部署錯誤。 將其細分為簡化範本以測試您的範本。 首先，建立可部署單一資源 (例如 SQL Server) 的範本。 當您確定已正確定義該資源時，加入依存該資源的資源 (例如 SQL Database)。 當您正確定義這兩個資源時，加入其他相依的資源 (例如稽核原則)。 在每個測試部署之間，刪除資源群組以確保您充分測試相依性。 

## <a name="check-deployment-sequence"></a>檢查部署順序

當資源是以非預期的順序部署時，會發生許多部署錯誤。 相依性未正確設定時，就會發生這些錯誤。 當您遺漏必要的相依性時，一個資源會嘗試使用另一個資源的值，但另一個資源還不存在。 您會收到錯誤表示找不到資源。 您可能會不斷遇到這種錯誤類型，因為每個資源的部署時間可能有所不同。 例如，您第一次部署資源成功，因為必要的資源恰好即時完成。 不過，您第二次嘗試失敗，因為必要的資源沒有即時完成。 

但是，您要避免設定不需要的相依性。 當您有不必要的相依性時，您會阻止不互相相依的資源以平行方式部署，而延長部署的時間。 此外，您可以建立封鎖部署的循環相依性。 在同一個範本中部署參考的資源時，[reference](resource-group-template-functions-resource.md#reference) 函式會於該資源上建立隱含的相依性。 因此，您的相依性可能會比 **dependsOn** 屬性中指定的相依性還多。 [resourceId](resource-group-template-functions-resource.md#resourceid) 函式不會建立隱含的相依性或驗證資源存在。

當您遇到相依性問題時，您需要深入了解資源部署的順序。 若要檢視部署作業的順序︰

1. 選取資源群組的部署歷程記錄。

   ![選取部署歷程記錄](./media/resource-manager-troubleshoot-tips/select-deployment.png)

2. 從歷程記錄中選取部署，然後選取 [事件]。

   ![選取部署事件](./media/resource-manager-troubleshoot-tips/select-deployment-events.png)

3. 檢查每個資源的事件順序。 注意每個作業的狀態。 例如，下列映像顯示平行部署的三個儲存體帳戶。 請注意，三個儲存體帳戶會同時啟動。

   ![平行部署](./media/resource-manager-troubleshoot-tips/deployment-events-parallel.png)

   下一個映像顯示非平行部署的三個儲存體帳戶。 第二個儲存體帳戶相依於第一個儲存體帳戶，而第三個儲存體帳戶相依於第二個儲存體帳戶。 因此，第一個儲存體帳戶會在下一個儲存體帳戶啟動之前啟動、接受及完成。

   ![連續部署](./media/resource-manager-troubleshoot-tips/deployment-events-sequence.png)

即使針對更複雜的案例，您還是可以使用相同的技巧來探索每個資源的部署何時啟動及完成。 請查閱您的部署事件以查看順序是否與您預期的不同。 如果是，請重新評估此資源的相依性。

Resource Manager 範本會在驗證期間識別循環相依性。 它會傳回錯誤訊息，特別指出循環相依性存在。 解決循環相依性︰

1. 在範本中，找出循環相依性中所識別的資源。 
2. 針對該資源，檢查 **dependsOn** 屬性和任何使用的 **reference** 函式，查看相依於哪些資源。 
3. 檢查這些資源，查看所相依的資源。 跟隨相依性，直到您找出相依於原始資源的資源。
5. 針對參與循環相依性的資源，仔細檢查所有使用的 **dependsOn** 屬性，以識別不需要的任何相依性。 移除這些相依性。 如果您不確定是否需要某個相依性，請嘗試移除它。 
6. 重新部署範本。

移除 **dependsOn** 屬性的值可能會在您部署範本時導致錯誤。 如果您遇到錯誤，請將相依性新增回範本。 

如果該方法無法解決循環相依性，請考慮將一部分部署邏輯移到子資源 (例如擴充或設定值)。 設定這些子資源在參與循環相依性的資源之後才進行部署。 例如，假設您要部署兩部虛擬機器，但是您必須分別在上面設定互相參考的屬性。 您可以採取下列順序部署︰

1. vm1
2. vm2
3. vm1 的擴充相依於 vm1 和 vm2。 擴充在 vm1 上設定從 vm2 取得的值。
4. vm2 的擴充相依於 vm1 和 vm2。 擴充在 vm2 上設定從 vm1 取得的值。

相同的方法也適用於 App Service 應用程式。 請考慮將設定值移入應用程式資源的子資源。 您可以採取下列順序部署兩個 Web 應用程式︰

1. webapp1
2. webapp2
3. webapp1 的設定相依於 webapp1 和 webapp2。 它包含使用 webapp2 的值的應用程式設定。
4. webapp2 的設定相依於 webapp1 和 webapp2。 它包含使用 webapp1 的值的應用程式設定。


## <a name="next-steps"></a>後續步驟
* 如需常見部署錯誤的解決方案，請參閱[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](resource-manager-common-deployment-errors.md)。
* 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](resource-group-audit.md)。
* 若要了解部署期間可採取哪些動作來判斷錯誤，請參閱 [檢視部署作業](resource-manager-deployment-operations.md)。
