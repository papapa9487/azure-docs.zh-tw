---
title: "使用 REST API 和範本部署資源 | Microsoft Docs"
description: "使用 Azure Resource Manager 和 Resource Manager REST API 將資源部署到 Azure。 資源會定義在 Resource Manager 範本中。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 1d8fbd4c-78b0-425b-ba76-f2b7fd260b45
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/10/2017
ms.author: tomfitz
ms.openlocfilehash: 46856a25fb57bb2c5a3c1aeae13c11655e1a58a5
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>使用 Resource Manager 範本和 Resource Manager REST API 部署資源
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Azure CLI](resource-group-template-deploy-cli.md)
> * [入口網站](resource-group-template-deploy-portal.md)
> * [REST API](resource-group-template-deploy-rest.md)
> 
> 

這篇文章說明如何使用 Resource Manager REST API 與 Resource Manager 範本來將您的資源部署至 Azure。  

> [!TIP]
> 如需部署期間偵錯錯誤的說明，請參閱︰
> 
> * [檢視部署作業](resource-manager-deployment-operations.md)，以了解有關取得可協助您針對錯誤進行疑難排解的資訊
> * [針對使用 Azure Resource Manager 將資源部署至 Azure 時常見的錯誤進行疑難排解](resource-manager-common-deployment-errors.md) ，以了解如何解決常見的部署錯誤
> 
> 

您的範本可以是本機檔案，或者是透過 URI 提供使用的外部檔案。 當您的範本位於儲存體帳戶中時，您可以限制範本的存取權，並在部署期間提供共用存取簽章 (SAS) Token

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy-with-the-rest-api"></a>使用 REST API 部署
1. 設定[一般參數和標頭](https://docs.microsoft.com/rest/api/index) (包括驗證權杖)。
2. 如果您沒有現有資源群組，請建立新的資源群組。 提供您的訂用帳戶識別碼、新資源群組的名稱，以及需要解決方案的位置。 如需詳細資訊，請參閱[建立資源群組](https://docs.microsoft.com/rest/api/resources/resourcegroups#ResourceGroups_CreateOrUpdate)。
   
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
3. 請先執行[驗證範本部署作業](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Validate)來驗證部署，然後再執行部署。 測試部署時，請提供與執行部署時完全一致的參數 (如下個步驟所示)。
4. 建立部署。 提供您的訂用帳戶識別碼、資源群組的名稱、部署的名稱，以及範本的連結。 如需範本檔案的相關資訊，請參閱[參數檔案](#parameter-file)。 如需以 REST API 建立資源群組的相關詳細資訊，請參閱[建立範本部署](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_CreateOrUpdate)。 請注意，**mode** 是設為 **Incremental**。 若要執行完整部署，請將 **mode** 設為 **Complete**。 使用完整模式時請務必謹慎，因為您可能會不小心刪除不在範本中的資源。
   
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0"
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0"
              }
            }
          }
   
      如果您想要記錄回應內容及/或要求內容，可在要求中包括 **debugSetting** 。
   
        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }
   
      您可以設定儲存體帳戶以使用共用存取簽章 (SAS) Token。 如需詳細資訊，請參閱[使用共用存取簽章委派存取](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature)。
5. 取得範本部署的狀態。 如需詳細資訊，請參閱[取得範本部署的相關資訊](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get)。
   
          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

## <a name="parameter-file"></a>參數檔案

[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>後續步驟
* 若要了解如何處理非同步 REST 作業，請參閱[追蹤非同步 Azure 作業 (英文)](resource-manager-async-operations.md)。
* 如需透過 .NET 用戶端程式庫部署資源的範例，請參閱 [使用 .NET 程式庫與範本部署資源](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
* 若要在範本中定義參數，請參閱 [編寫範本](resource-group-authoring-templates.md#parameters)。
* 如需將您的方案部署到不同環境的指引，請參閱 [Microsoft Azure 中的開發和測試環境](solution-dev-test-environments.md)。
* 如需關於企業如何使用 Resource Manager 有效地管理訂用帳戶的指引，請參閱 [Azure 企業 Scaffold - 規定的訂用帳戶治理](resource-manager-subscription-governance.md)。

