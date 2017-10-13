---
title: "使用 JSON 定義工作流程 - Azure Logic Apps | Microsoft Docs"
description: "如何以 JSON 撰寫 Logic Apps 的工作流程定義"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 03/29/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 7f9e5a10066df8a464c285273e77a85c0d562ebb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-workflow-definitions-for-logic-apps-using-json"></a>使用 JSON 建立邏輯應用程式的工作流程定義

您可以使用簡單、宣告式的 JSON 語言建立 [Azure Logic Apps](logic-apps-what-are-logic-apps.md) 的工作流程定義。 如果您還沒有這麼做，請先檢閱[如何使用邏輯應用程式設計工具建立第一個邏輯應用程式](logic-apps-create-a-logic-app.md)。 此外，請參閱[工作流程定義語言的完整參考](http://aka.ms/logicappsdocs)。

## <a name="repeat-steps-over-a-list"></a>對清單重複執行步驟

若要逐一查看有多達 10,000 個項目的陣列，並針對每個項目執行動作，請使用 [foreach 類型](logic-apps-loops-and-scopes.md)。

## <a name="handle-failures-if-something-goes-wrong"></a>若發生錯誤時會處理失敗

通常，您想要包括*補救步驟* — 如果*且唯有*當一或多個呼叫失敗時執行的一些邏輯。 此範例會從各種地方取得資料，但如果呼叫失敗，我們想要在某處 POST 訊息，方便稍後追蹤該失敗。：  

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "postToErrorMessageQueue": {
      "type": "ApiConnection",
      "inputs": "...",
      "runAfter": {
        "readData": [
          "Failed"
        ]
      }
    }
  },
  "outputs": {}
}
```

若要指定 `postToErrorMessageQueue` 僅在 `readData` 已 `Failed` 之後才會執行，使用 `runAfter` 屬性，例如，指定可能值的清單，以便 `runAfter` 可能是 `["Succeeded", "Failed"]`。

最後，因為此範例現在會處理錯誤，我們不再將執行結果標示為 `Failed`。 因為我們在此範例中新增處理此失敗的步驟，執行結果為 `Succeeded`，雖然其中一個步驟 `Failed`。

## <a name="execute-two-or-more-steps-in-parallel"></a>平行執行兩個以上步驟

若要平行執行多個動作，`runAfter` 屬性在執行階段必須相同。 

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "triggers": {
    "Request": {
      "kind": "http",
      "type": "Request"
    }
  },
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      }
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

在此範例中，`branch1` 和 `branch2` 設定為在 `readData` 之後執行。 如此一來，這兩個分支會以平行方式執行。 兩個分支的時間戳記完全相同。

![平行](media/logic-apps-author-definitions/parallel.png)

## <a name="join-two-parallel-branches"></a>聯結兩個平行分支

透過先前範例中的方式在 `runAfter` 屬性新增項目，即可聯結設定為平行執行的兩個動作。

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-04-01-preview/workflowdefinition.json#",
  "actions": {
    "readData": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {}
    },
    "branch1": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "branch2": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "readData": [
          "Succeeded"
        ]
      }
    },
    "join": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://myurl"
      },
      "runAfter": {
        "branch1": [
          "Succeeded"
        ],
        "branch2": [
          "Succeeded"
        ]
      }
    }
  },
  "parameters": {},
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "Http",
      "inputs": {
        "schema": {}
      }
    }
  },
  "contentVersion": "1.0.0.0",
  "outputs": {}
}
```

![平行](media/logic-apps-author-definitions/join.png)

## <a name="map-list-items-to-a-different-configuration"></a>將清單項目對應至不同的組態

接下來，假設我們想要根據屬性的值取得不同的內容。 我們可以建立值與目的地的對應做為參數：  

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "http://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

在此情況下，我們先取得文章清單。 根據已定義為參數的類別，第二個步驟會使用對應查詢 URL 來取得內容。

這裡要注意的某些時間︰ 

*   [ `intersection()` ](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection) 函式會檢查類別是否符合其中一個已知的定義類別。

*   在取得類別後，我們可以使用方括號從對應提取項目：`parameters[...]`

## <a name="process-strings"></a>處理序字串

您可以使用不同的函式來操作字串。 例如，假設我們有想要傳遞到系統上的字串，但我們並不十分熟悉字元編碼的正確處理。 一種作法是以 base64 將此字串編碼。 不過，為了避免在 URL 中逸出，我們要取代幾個字元。 

我們也想要訂單名稱的子字串，因為不會用到前五個字元。

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "orderer": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

使用從內到外︰

1. 取得訂購者名稱的 [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length)，以便我們傳回字元總數。

2. 減 5，因為我們要較短的字串。

3. 實際取得 [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring)。 我們從索引 `5` 開始，並取得字串的其餘部分。

4. 將這個子字串轉換成 [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64) 字串。

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) 所有 `+` 字元換成 `-` 字元。

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) 所有 `/` 字元換成 `_` 字元。

## <a name="work-with-date-times"></a>使用日期時間

日期時間很有用，特別是在嘗試從不支援的觸發程序的資料來源提取資料時。 您也可以使用日期時間算出各步驟花費的時間。

```
{
  "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "http://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

在此範例中，我們從前一個步驟擷取 `startTime`。 然後我們會取得目前的時間，並減去一秒︰

[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) 

您可以使用其他的時間單位，例如 `minutes` 或 `hours`。 最後，我們可以比較這兩個值。 如果第一個值小於第二個值，則自從訂單最初提交以來已超過一秒。

若要格式化日期，我們可以使用字串格式子。 例如，若要取得 RFC1123，我們使用 [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow)。 若要深入了解日期格式設定，請參閱[工作流程定義語言](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow)。

## <a name="deployment-parameters-for-different-environments"></a>不同環境的部署參數

通常，部署生命週期中會有開發環境、預備環境及生產環境。 例如，您可以在所有這些環境中使用相同的定義，但使用不同的資料庫。 同樣地，您可能想要跨不同的區域使用相同的定義，以發揮高可用性，但希望每個邏輯應用程式執行個體與該區域資料庫互動。
這種情況與在執行階段採用參數不同，相反地，您應該如先前範例所示使用 `trigger()` 函式。

您可以從基本定義開始，如下範例所示：

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "uri": {
            "type": "string"
        }
    },
    "triggers": {
        "request": {
          "type": "request",
          "kind": "http"
        }
    },
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('uri')"
            }
        }
    },
    "outputs": {}
}
```

在邏輯應用程式的實際 `PUT` 要求中，您可以提供參數 `uri`。 因為預設值不存在，邏輯應用程式承載需要此參數︰

```
{
    "properties": {},
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

在每個環境中，您可以提供不同的值給 `connection` 參數。 

如需有關建立及管理邏輯應用程式的所有可用選項，請參閱 [REST API 文件](https://msdn.microsoft.com/library/azure/mt643787.aspx)。 
