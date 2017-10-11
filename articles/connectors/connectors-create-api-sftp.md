---
title: "了解如何在您的邏輯應用程式中使用 SFTP 連接器 | Microsoft Docs"
description: "使用 Azure App Service 建立邏輯應用程式。 連接到 SFTP API 來傳送及接收檔案。 您可以執行各種作業，例如建立、更新、取得或刪除檔案。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 31253d8daee1581167a96a20ba8ad529a04b3e92
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-the-sftp-connector"></a>開始使用 SFTP 連接器
使用 SFTP 連接器存取 SFTP 帳戶來傳送及接收檔案。 您可以執行各種作業，例如建立、更新、取得或刪除檔案。  

若要使用[任何連接器](apis-list.md)，您必須先建立邏輯應用程式。 您可以從[立即建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)來開始。

## <a name="connect-to-sftp"></a>連接至 SFTP
您必須先建立與服務的連線，才能透過邏輯應用程式存取任何服務。 [連線](connectors-overview.md)可讓邏輯應用程式與另一個服務連線。  

### <a name="create-a-connection-to-sftp"></a>建立至 SFTP 的連線
> [!INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## <a name="use-an-sftp-trigger"></a>使用 SFTP 觸發程序
觸發程序是可用來啟動邏輯應用程式中所定義之工作流程的事件。 [深入了解觸發程序](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。  

在此範例中，當在 SFTP 伺服器上新增或修改檔案時，就會使用 **SFTP - 新增或修改檔案時**觸發程序起始邏輯應用程式工作流程。 您也可以新增會檢查新的或修改之檔案內容的條件，並在使用內容之前如果檔案指出它應該要解壓縮時，決定將檔案解壓縮。 最後，新增動作以解壓縮檔案的內容，並將解壓縮的內容放在 SFTP 伺服器上的資料夾。 

在企業範例中，您可以使用此觸發程序來監視代表客戶訂單的新檔案 SFTP 資料夾。  然後，您可以使用 SFTP 連接器動作 (例如**取得檔案內容**) 取得訂單的內容以進一步處理並儲存在訂單資料庫中。

> [!INCLUDE [Steps to create an SFTP trigger](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>新增條件
> [!INCLUDE [Steps to add a condition](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## <a name="use-an-sftp-action"></a>使用 SFTP 動作
動作是由邏輯應用程式中定義的工作流程所執行的作業。 [深入了解動作](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)。  

> [!INCLUDE [Steps to create an SFTP action](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## <a name="connector-specific-details"></a>連接器特定的詳細資料

檢視 Swagger 中定義的任何觸發程序和動作，另請參閱[連接器詳細資料](/connectors/sftpconnector/)的所有限制。

## <a name="more-connectors"></a>其他連接器
返回 [API 清單](apis-list.md)。