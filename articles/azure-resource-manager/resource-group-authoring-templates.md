---
title: "Azure Resource Manager 範本結構和語法 | Microsoft Docs"
description: "使用宣告式 JSON 語法描述 Azure Resource Manager 範本的結構和屬性。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: dc9b64062d7f68c83aa090eec96744819a5ca423
ms.contentlocale: zh-tw
ms.lasthandoff: 06/16/2017


---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>了解 Azure Resource Manager 範本的結構和語法
本主題說明 Azure Resource Manager 範本的結構。 它會呈現範本的不同區段，以及這些區段中可用的屬性。 範本由 JSON 與運算式所組成，可讓您用來為部署建構值。 如需建立範本的逐步教學課程，請參閱[建立第一個 Azure Resource Manager 範本](resource-manager-create-first-template.md)。

## <a name="template-format"></a>範本格式
在最簡單的結構中，範本包含下列元素：

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "resources": [  ],
    "outputs": {  }
}
```

| 元素名稱 | 必要 | 說明 |
|:--- |:--- |:--- |
| $schema |是 |JSON 結構描述檔案的位置，說明範本語言的版本。 使用上述範例所示的 URL。 |
| contentVersion |是 |範本版本 (例如 1.0.0.0)。 您可以為此元素提供任何值。 使用範本部署資源時，這個值可用來確定使用的是正確的範本。 |
| parameters |否 |執行部署以自訂資源部署時所提供的值。 |
| variables |否 |範本中做為 JSON 片段以簡化範本語言運算式的值。 |
| resources |是 |在資源群組中部署或更新的資源類型。 |
| outputs |否 |部署後傳回的值。 |

每個元素都包含可以設定的屬性。 下列範例包含範本的完整語法：

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  
        "<parameter-name>" : {
            "type" : "<type-of-parameter-value>",
            "defaultValue": "<default-value-of-parameter>",
            "allowedValues": [ "<array-of-allowed-values>" ],
            "minValue": <minimum-value-for-int>,
            "maxValue": <maximum-value-for-int>,
            "minLength": <minimum-length-for-string-or-array>,
            "maxLength": <maximum-length-for-string-or-array-parameters>,
            "metadata": {
                "description": "<description-of-the parameter>" 
            }
        }
    },
    "variables": {  
        "<variable-name>": "<variable-value>",
        "<variable-name>": { 
            <variable-complex-type-value> 
        }
    },
    "resources": [
      {
          "condition": "<boolean-value-whether-to-deploy>",
          "apiVersion": "<api-version-of-resource>",
          "type": "<resource-provider-namespace/resource-type-name>",
          "name": "<name-of-the-resource>",
          "location": "<location-of-resource>",
          "tags": {
              "<tag-name1>": "<tag-value1>",
              "<tag-name2>": "<tag-value2>"
          },
          "comments": "<your-reference-notes>",
          "copy": {
              "name": "<name-of-copy-loop>",
              "count": "<number-of-iterations>",
              "mode": "<serial-or-parallel>",
              "batchSize": "<number-to-deploy-serially>"
          },
          "dependsOn": [
              "<array-of-related-resource-names>"
          ],
          "properties": {
              "<settings-for-the-resource>",
              "copy": [
                  {
                      "name": ,
                      "count": ,
                      "input": {}
                  }
              ]
          },
          "resources": [
              "<array-of-child-resources>"
          ]
      }
    ],
    "outputs": {
        "<outputName>" : {
            "type" : "<type-of-output-value>",
            "value": "<output-value-expression>"
        }
    }
}
```

我們在本主題稍後更詳細探討範本的各個區段。

## <a name="expressions-and-functions"></a>運算式和函式
範本的基本語法是 JSON。 不過，運算式和函式可延伸範本中提供的 JSON 值。  運算式是在 JSON 字串常值內撰寫，其第一個字元和最後一個字元分別為下列兩種方括號：`[` 和 `]`。 部署範本時，會評估運算式的值。 雖然是以字串常值撰寫，評估運算式的結果，根據實際的運算式可能會是不同的 JSON 類型 (例如陣列或整數)。  針對開頭為方括號 `[` 的常值字串，若不要將它解譯為運算式，請加入額外的方括號，使字串開頭成為 `[[`。

一般而言，您可以將運算式搭配函數使用，以執行可設定部署的作業。 和在 JavaScript 中相同，函式呼叫的格式為 `functionName(arg1,arg2,arg3)`。 您可以使用點與 [index] 運算子來參考屬性。

下列範例將示範如何在結構化值時使用數個函數：

```json
"variables": {
    "location": "[resourceGroup().location]",
    "usernameAndPassword": "[concat(parameters('username'), ':', parameters('password'))]",
    "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
}
```

如需範本函數的完整清單，請參閱 [Azure 資源管理員範本函數](resource-group-template-functions.md)。 

## <a name="parameters"></a>參數
在範本的 parameters 區段中，您會指定可在部署資源時輸入的值。 提供針對特定環境 (例如開發、測試和生產環境) 量身訂做的參數值，可讓您自訂部署。 您不必在範本中提供參數，但若沒有參數，您的範本一律會部署具有相同名稱、位置和屬性的相同資源。

您會定義結構如下的參數：

```json
"parameters": {
    "<parameter-name>" : {
        "type" : "<type-of-parameter-value>",
        "defaultValue": "<default-value-of-parameter>",
        "allowedValues": [ "<array-of-allowed-values>" ],
        "minValue": <minimum-value-for-int>,
        "maxValue": <maximum-value-for-int>,
        "minLength": <minimum-length-for-string-or-array>,
        "maxLength": <maximum-length-for-string-or-array-parameters>,
        "metadata": {
            "description": "<description-of-the parameter>" 
        }
    }
}
```

| 元素名稱 | 必要 | 說明 |
|:--- |:--- |:--- |
| parameterName |是 |參數名稱。 必須是有效的 JavaScript 識別碼。 |
| type |是 |參數值類型。 請參閱此表格之後的允許類型清單。 |
| defaultValue |否 |如果未提供參數值，則會使用參數的預設值。 |
| allowedValues |否 |參數的允許值陣列，確保提供正確的值。 |
| minValue |否 |int 類型參數的最小值，含此值。 |
| maxValue |否 |int 類型參數的最大值，含此值。 |
| minLength |否 |字串、secureString 及陣列類型參數長度的最小值，含此值。 |
| maxLength |否 |字串、secureString 及陣列類型參數長度的最大值，含此值。 |
| description |否 |透過入口網站向使用者顯示的參數說明。 |

允許的類型和值為：

* **string**
* **secureString**
* **int**
* **bool**
* **object** 
* **secureObject**
* **array**

若要將參數指定為選用，請提供 defaultValue (可為空字串)。 

如果您在範本中指定的參數名稱和要部署範本的命令中的參數相符，提供的值就有可能模稜兩可。 Resource Manager 會在範本參數加上後置詞 **FromTemplate** 以避免混淆。 例如，如果您在範本中包含名為 **ResourceGroupName** 的參數，這會與 [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) Cmdlet 中的 **ResourceGroupName** 參數發生衝突。 部署期間，系統會提示您為 **ResourceGroupNameFromTemplate** 提供值。 一般而言，在為參數命名時，請勿使用與部署作業所用參數相同的名稱，以避免發生這種混淆的情形。

> [!NOTE]
> 所有密碼、金鑰和其他密碼都應該使用 **secureString** 類型。 如果您在 JSON 物件中傳遞敏感資料，請使用 **secureObject** 類型。 部署資源後，無法讀取類型為 secureString 或 secureObject 的範本參數。 
> 
> 例如，在部署歷程記錄中，下列項目會顯示字串和物件的值，但不會顯示 secureString 和 secureObject 的值。
>
> ![show deployment values](./media/resource-group-authoring-templates/show-parameters.png)  
>

下列範例示範如何定義參數：

```json
"parameters": {
    "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
    },
    "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
    },
    "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
    },
    "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
    }
}
```

如需如何在部署期間輸入參數值的資訊，請參閱 [使用 Azure Resource Manager 範本部署資源](resource-group-template-deploy.md)。 

## <a name="variables"></a>變數
在 variables 區段中，您會建構可用於整個範本中的值。 您不需要定義變數，但它們通常會經由減少複雜運算式來簡化您的範本。

您可使用以下結構定義變數：

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    }
}
```

下列範例說明如何定義由兩個參數值建構的變數：

```json
"variables": {
    "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
}
```

接下來的範例說明複雜 JSON 類型的變數，以及由其他變數建構的變數：

```json
"parameters": {
    "environmentName": {
        "type": "string",
        "allowedValues": [
          "test",
          "prod"
        ]
    }
},
"variables": {
    "environmentSettings": {
        "test": {
            "instancesSize": "Small",
            "instancesCount": 1
        },
        "prod": {
            "instancesSize": "Large",
            "instancesCount": 4
        }
    },
    "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
    "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
    "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
}
```

## <a name="resources"></a>資源
在資源區段中，您會定義要部署或更新的資源。 此區段會變得複雜，因為您必須了解您要部署的類型才能提供正確的值。 針對您必須設定的資源特定值 (apiVersion、類型及屬性)，請參閱[定義 Azure Resource Manager 範本中的資源](/azure/templates/)。 

您會定義結構如下的資源：

```json
"resources": [
  {
      "condition": "<boolean-value-whether-to-deploy>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": "<number-of-iterations>",
          "mode": "<serial-or-parallel>",
          "batchSize": "<number-to-deploy-serially>"
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| 元素名稱 | 必要 | 說明 |
|:--- |:--- |:--- |
| condition | 否 | 布林值，表示是否已部署資源。 |
| apiVersion |是 |要用來建立資源的 REST API 版本。 |
| type |是 |資源類型。 這個值是資源提供者的命名空間與資源類型的組合 (例如 **Microsoft.Storage/storageAccounts**)。 |
| name |是 |資源名稱。 此名稱必須遵循在 RFC3986 中定義的 URI 元件限制。 此外，將資源名稱公開到外部合作對象的 Azure 服務會驗證該名稱，確定不是有人嘗試詐騙其他身分識別。 |
| location |視情況而異 |所提供資源的支援地理位置。 您可以選取任何可用的位置，但通常選擇接近您的使用者的位置很合理。 通常，將彼此互動的資源放在相同區域也合乎常理。 大部分的資源類型都需要有位置，但某些類型 (例如角色指派) 不需要位置。 請參閱[設定 Azure Resource Manager 範本中的資源位置](resource-manager-template-location.md)。 |
| tags |否 |與資源相關聯的標記。 請參閱[標記 Azure Resource Manager 範本中的資源](resource-manager-template-tags.md)。 |
| comments |否 |您在範本中記錄資源的註解 |
| 複製 |否 |如果需要多個執行個體，要建立的資源數目。 預設模式為平行。 如果您不想要同時部署所有或某些資源，請指定序列模式。 如需詳細資訊，請參閱[在 Azure Resource Manager 中建立資源的多個執行個體](resource-group-create-multiple.md)。 |
| dependsOn |否 |在部署這項資源之前必須部署的資源。 Resource Manager 會評估資源之間的相依性，並依正確的順序進行部署。 資源若不互相依賴，則會平行部署資源。 值可以是以逗號分隔的資源名稱或資源唯一識別碼清單。 只會列出此範本中已部署的資源。 此範本中未定義的資源必須已經存在。 避免加入不必要的相依性，因為可能會降低部署速度並產生循環相依性。 如需設定相依性的指引，請參閱[定義 Azure Resource Manager 範本中的相依性](resource-group-define-dependencies.md)。 |
| properties |否 |資源特定的組態設定。 屬性的值和您在 REST API 作業 (PUT 方法) 要求主體中提供來建立資源的值是一樣的。 您也可以指定複本陣列，以建立屬性的多個執行個體。 如需詳細資訊，請參閱[在 Azure Resource Manager 中建立資源的多個執行個體](resource-group-create-multiple.md)。 |
| resources |否 |與正在定義的資源相依的下層資源。 只提供父資源的結構描述允許的資源類型。 子資源的完整類型包含父資源類型，例如 **Microsoft.Web/sites/extensions**。 沒有隱含父資源的相依性。 您必須明確定義該相依性。 |

resources 區段包含要部署的資源陣列。 在每個資源內，您也可以定義子資源陣列。 因此，您的 resources 區段可能會有類似以下的結構：

```json
"resources": [
  {
      "name": "resourceA",
  },
  {
      "name": "resourceB",
      "resources": [
        {
            "name": "firstChildResourceB",
        },
        {   
            "name": "secondChildResourceB",
        }
      ]
  },
  {
      "name": "resourceC",
  }
]
```      

如需定義子資源的詳細資訊，請參閱[在 Resource Manager 範本中設定子資源的名稱和類型](resource-manager-template-child-resource.md)。

**condition** 元素會指定是否要部署資源。 此元素的值會解析為 true 或 false。 例如，若要指定是否要部署新的儲存體帳戶，請使用：

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

如需使用新的或現有資源的範例，請參閱[新的或現有條件範本](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResources.NewOrExisting.json)。

若要指定是否要透過密碼或 SSH 金鑰部署虛擬機器，請在範本中定義兩個版本的虛擬機器，並使用 **condition** 區分使用情況。 傳遞可指定要部署哪個案例的參數。

```json
{
    "condition": "[equals(parameters('passwordOrSshKey'),'password')]",
    "apiVersion": "2016-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('vmName'),'password')]",
    "properties": {
        "osProfile": {
            "computerName": "[variables('vmName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
        },
        ...
    },
    ...
},
{
    "condition": "[equals(parameters('passwordOrSshKey'),'sshKey')]",
    "apiVersion": "2016-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(variables('vmName'),'ssh')]",
    "properties": {
        "osProfile": {
            "linuxConfiguration": {
                "disablePasswordAuthentication": "true",
                "ssh": {
                    "publicKeys": [
                        {
                            "path": "[variables('sshKeyPath')]",
                            "keyData": "[parameters('adminSshKey')]"
                        }
                    ]
                }
            }
        },
        ...
    },
    ...
}
``` 

如需使用密碼或 SSH 金鑰來部署虛擬機器的範例，請參閱[使用者名稱或 SSH 條件範本](https://github.com/rjmax/Build2017/blob/master/Act1.TemplateEnhancements/Chapter05.ConditionalResourcesUsernameOrSsh.json)。

## <a name="outputs"></a>輸出
在輸出區段中，您可以指定從部署傳回的值。 例如，您可以傳回 URI 以存取所部署的資源。

下列範例顯示輸出定義的結構：

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| 元素名稱 | 必要 | 說明 |
|:--- |:--- |:--- |
| outputName |是 |輸出值的名稱。 必須是有效的 JavaScript 識別碼。 |
| type |是 |輸出值的類型。 輸出值支援與範本輸入參數相同的類型。 |
| value |是 |評估並傳回做為輸出值的範本語言運算式。 |

下列範例顯示在輸出區段中傳回的值。

```json
"outputs": {
    "siteUri" : {
        "type" : "string",
        "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
    }
}
```

如需使用輸出的相關資訊，請參閱 [Azure Resource Manager 範本中的共用狀態](best-practices-resource-manager-state.md)。

## <a name="template-limits"></a>範本限制

將範本大小限制為 1 MB，並將每個參數檔案限制為 64 KB。 1 MB 的限制適用於已增加反覆資源定義和變數和參數值之範本的最終狀態。 

您也受限於：

* 256 個參數
* 256 個變數
* 800 個資源 (包括複本計數)
* 64 個輸出值
* 範本運算式中的 24,576 個字元

使用巢狀範本，即可超出一些範本限制。 如需詳細資訊，請參閱[在部署 Azure 資源時使用連結的範本](resource-group-linked-templates.md)。 若要減少參數、變數或輸出數目，您可以將數個值合併成一個物件。 如需詳細資訊，請參閱[物件作為參數](resource-manager-objects-as-parameters.md)。

## <a name="next-steps"></a>後續步驟
* 若要檢視許多不同類型解決方案的完整範本，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。
* 如需您可以在範本內使用哪些函式的詳細資料，請參閱 [Azure Resource Manager 範本函式](resource-group-template-functions.md)。
* 若要在部署期間合併多個範本，請參閱 [透過 Azure Resource Manager 使用連結的範本](resource-group-linked-templates.md)。
* 您可能需要使用不同資源群組內的資源。 這案例常見於使用多個資源群組之間所共用的儲存體帳戶或虛擬網路時。 如需詳細資訊，請參閱 [resourceId 函式](resource-group-template-functions-resource.md#resourceid)。

