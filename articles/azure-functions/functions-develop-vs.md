---
title: "使用 Visual Studio 開發 Azure Functions |Microsoft 文件"
description: "了解如何使用 Azure Functions Tools for Visual Studio 2017 開發和測試 Azure Functions。"
services: functions
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: glenga
ms.openlocfilehash: a3d4e5367dd41ce2ade7033cefcc774dcec9c82c
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2017
---
# <a name="azure-functions-tools-for-visual-studio"></a>Azure Functions Tools for Visual Studio  

Azure Functions Tools for Visual Studio 2017 是 Visual Studio 的延伸模組，可讓您開發、測試 C# 函數及將它部署到 Azure。 如果這是您第一次體驗 Azure Functions，可至 [Azure Functions 簡介](functions-overview.md)深入了解。

Azure Functions Tools 提供下列優點： 

* 在本機開發電腦上編輯、建置及執行函數。 
* 直接將 Azure Functions 專案發佈至 Azure。 
* 使用 WebJobs 屬性直接在 C# 程式碼中宣告函數繫結，而不必針對繫結定義維護個別的 function.json。
* 開發及部署預先編譯的 C# 函數。 預先編譯的函數提供的冷啟動效能比 C# 指令碼型函數更好。 
* 在 C# 中編寫函數，同時享有 Visual Studio 開發的所有優點。 

本主題示範如何使用 Azure Functions Tools for Visual Studio 2017 在 C# 中開發函數。 您也會了解如何將專案發佈到 Azure 成為 .NET 組件。

## <a name="prerequisites"></a>必要條件

[Visual Studio 2017 15.3](https://www.visualstudio.com/vs/) 及其以上版本之 Azure 開發工作負載包含了 Azure Functions 工具。 在安裝 Visual Studio 2017 15.3 版時，務必包含 **Azure 開發**工作負載：

![安裝包含 Azure 開發工作負載的 Visual Studio 2017](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-install-note.md)] 

若要建立及部署函數，您也需要：

* 有效的 Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，可以使用[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* Azure 儲存體帳戶。 若要建立儲存體帳戶，請參閱[儲存體帳戶](../storage/common/storage-create-storage-account.md#create-a-storage-account)。  
## <a name="create-an-azure-functions-project"></a>建立 Azure Functions 專案 

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]


## <a name="configure-the-project-for-local-development"></a>設定專案以進行本機開發

當您使用 Azure Functions 範本建立新專案時，會取得空 C# 專案，其中包含下列檔案：

* **host.json**：讓您設定 Functions 主機。 這些設定同時適用於在本機執行及在 Azure 中執行。 如需詳細資訊，請參閱 [host.json 參考](functions-host-json.md)。
    
* **local.settings.json**：維持在本機執行函數時所使用的設定。 Azure 不會使用這些設定，[Azure Functions Core Tools](functions-run-local.md) 會使用這些設定。 使用此檔案來指定設定，例如連至其他 Azure 服務的連接字串。 針對專案中函數所需的每個連接，將新機碼新增至 [值] 陣列。 如需詳細資訊，請參閱＜Azure Functions Core Tools＞主題中的[本機設定檔](functions-run-local.md#local-settings-file)。

函數執行階段會在內部使用 Azure 儲存體帳戶。 針對 HTTP 和 Webhook 以外的所有觸發程序類型，您必須將 **Values.AzureWebJobsStorage** 機碼設定為有效的 Azure 儲存體帳戶連接字串。

[!INCLUDE [Note to not use local storage](../../includes/functions-local-settings-note.md)]

 設定儲存體帳戶連接字串：

1. 在 Visual Studio 中，開啟 [Cloud Explorer]，展開 [儲存體帳戶] > 「您的儲存體帳戶」，然後選取 [屬性] 並複製 [主要連接字串] 值。   

2. 在您的專案中，開啟 local.settings.json 專案檔案並將 **AzureWebJobsStorage** 機碼的值設定為您所複製的連接字串。

3. 重複上一步，針對函數所需的其他任何連接，將唯一機碼新增至 [值] 陣列。  

## <a name="create-a-function"></a>建立函式

在預先編譯的函數中，函數所使用的繫結是透過在程式碼中套用屬性來定義的。 當您使用 Azure Functions Tools 從提供的範本建立函數時，會為您套用這些屬性。 

1. 在 [方案總管] 中，於專案節點上按一下滑鼠右鍵，然後選取 [新增]  > [新項目]。 選取 [Azure 函數]，輸入類別的 [名稱]，然後按一下 [新增]。

2. 選擇您的觸發程序，設定繫結屬性，然後按一下 [建立]。 下列範例顯示建立佇列儲存體觸發之函數時的設定。 

    ![](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)
    
    會提供名為 **QueueStorage** 的連接字串機碼，它是在 local.settings.json 檔案中定義的。 
 
3. 檢查新加入的類別。 您會看到靜態 [執行] 方法，它是使用 **FunctionName** 屬性來屬性化。 這個屬性指出該方法是函數的進入點。 

    例如，下列 C# 類別代表基本佇列儲存體觸發的函數：

    ````csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    
    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]        
            public static void Run([QueueTrigger("myqueue-items", Connection = "QueueStorage")]string myQueueItem, TraceWriter log)
            {
                log.Info($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    } 
    ````
 
    會將繫結特定屬性套用至提供給進入點方法的每個繫結參數。 屬性會將繫結資訊作為參數使用。 在上述範例中，第一個參數套用了 **QueueTrigger** 屬性，指出佇列觸發的函數。 佇列名稱和連接字串設定名稱會作為參數傳遞。  

## <a name="testing-functions"></a>測試函式

Azure Functions Core Tools 可讓您在本機開發電腦上執行 Azure Functions 專案。 第一次從 Visual Studio 啟動函式時，系統會提示您安裝這些工具。  

若要測試您的函式，請按 F5。 如果出現提示，接受來自 Visual Studio 之下載及安裝 Azure Functions Core (CLI) 工具的要求。  您可能也需要啟用防火牆例外狀況，工具才能處理 HTTP 要求。

隨著專案的執行，您可以像測試部署函數一樣測試程式碼。 如需詳細資訊，請參閱[在 Azure Functions 中測試程式碼的策略](functions-test-a-function.md)。 在偵錯模式下執行時，會依預期在 Visual Studio 中遇到中斷點。 

如需如何測試佇列觸發函數的範例，請參閱[佇列觸發函數快速入門教學課程](functions-create-storage-queue-triggered-function.md#test-the-function)。  

若要深入了解如何使用 Azure Functions Core Tools，請參閱[在本機撰寫和測試 Azure Functions 程式碼](functions-run-local.md)。

## <a name="publish-to-azure"></a>發佈至 Azure

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

>[!NOTE]  
>您在 local.settings.json 中所新增的所有設定，也必須新增至 Azure 中的函式應用程式。 系統並不會自動新增這些設定。 您可以透過下列方法將必要的設定新增至函式應用程式：
>
>* [使用 Azure 入口網站](functions-how-to-use-azure-function-app-settings.md#settings)。
>* [使用 Azure Functions Core Tools 中的 `--publish-local-settings` 發行選項](functions-run-local.md#publish)。
>* [使用 Azure CLI](/cli/azure/functionapp/config/appsettings#set)。 

## <a name="next-steps"></a>後續步驟

如需 Azure Functions Tools 的詳細資訊，請參閱 [Visual Studio 2017 Tools for Azure Functions](https://blogs.msdn.microsoft.com/webdev/2017/05/10/azure-function-tools-for-visual-studio-2017/) \(英文\) 部落格文章的＜常見問題＞一節。

若要深入了解 Azure Functions Core Tools，請參閱[在本機撰寫和測試 Azure Functions 程式碼](functions-run-local.md)。  
若要深入了解如何將函式開發為 .NET 類別庫，請參閱[搭配使用 .NET 類別庫與 Azure Functions](functions-dotnet-class-library.md)。 本主題也提供範例以示範如何使用屬性宣告 Azure Functions 所支援的各種繫結類型。    
