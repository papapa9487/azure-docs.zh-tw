---
title: "在 Azure 中使用範本建立邏輯應用程式 | Microsoft Docs"
description: "使用 Azure Resource Manager 範本部署邏輯應用程式以定義工作流程。"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2016
ms.author: LADocs; mandia
ms.openlocfilehash: 161adeacd6da2b15225c8a4ddae171e19e539967
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-logic-app-using-a-template"></a>使用範本建立邏輯應用程式
範本提供快速的方式，在邏輯應用程式中使用不同連接器。 邏輯應用程式包含 Azure Resource Manager 範本，供您用來建立可用於定義商務工作流程的邏輯應用程式。 您可以定義要部署的資源，以及部署邏輯應用程式時定義參數的方式。 您可以直接在自己的商務案例中使用此範本，或自訂此範本以符合您的需求。

如需邏輯應用程式屬性的詳細資料，請參閱 [邏輯應用程式工作流程管理 API](https://msdn.microsoft.com/library/azure/mt643788.aspx)。 

如需定義本身的範例，請參閱 [作者邏輯應用程式定義](logic-apps-author-definitions.md)。 

如需關於建立範本的詳細資訊，請參閱 [編寫 Azure 資源管理員範本](../azure-resource-manager/resource-group-authoring-templates.md)。

如需完整範本，請參閱 [邏輯應用程式範本](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json)。

## <a name="what-you-deploy"></a>您會部署什麼
使用此範本，您將部署邏輯應用程式。

若要自動執行部署，請選取下列按鈕：  

[![部署至 Azure](media/logic-apps-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## <a name="parameters"></a>參數
[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### <a name="testuri"></a>testUri
     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
      }

## <a name="resources-to-deploy"></a>要部署的資源
### <a name="logic-app"></a>邏輯應用程式
建立邏輯應用程式。

範本會使用邏輯應用程式名稱的參數值。 它會將邏輯應用程式的位置設為與資源群組相同的位置。 

這個特定的定義每小時執行一次，並 Ping **testUri** 參數中所指定的位置。 

    {
      "type": "Microsoft.Logic/workflows",
      "apiVersion": "2016-06-01",
      "name": "[parameters('logicAppName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "displayName": "LogicApp"
      },
      "properties": {
        "definition": {
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      }
    }


## <a name="commands-to-run-deployment"></a>執行部署的命令
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup



