---
title: "從範本建立工作流程 - Azure Logic Apps | Microsoft Docs"
description: "入門 - 使用 Azure 邏輯應用程式範本快速建立工作流程，來連接應用程式並整合資料。"
author: kevinlam1
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 3656acfb-eefd-4e75-b5d2-73da56c424c9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: LADocs; klam
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 89272869f7dfaa34cbd2ad32d67dca0955e6158b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="configure-a-workflow-using-a-pre-built-template-or-pattern-to-get-started-quickly"></a>使用預先建置的範本或模式設定工作流程，以便快速開始

## <a name="what-are-logic-app-templates"></a>何謂邏輯應用程式範本
邏輯應用程式範本是預先建置的邏輯應用程式，可供您快速開始建立自己的工作流程。 

這些範本非常適合用來探索各種可使用邏輯應用程式建置的模式。 您可以直接使用這些範本，或者修改它們以符合您的案例。

## <a name="overview-of-available-templates"></a>可用範本概觀
邏輯應用程式平台目前已發佈了許多可用的範本。 以下列出一些範例類別以及其中所使用的連接器類型。

### <a name="enterprise-cloud-templates"></a>企業雲端範本
此範本會整合 Dynamics CRM、Salesforce、Box、Azure Blob 及其他連接器，以滿足您的企業雲端需求。 部分可使用這些範本來進行的範例包含組織您的潛在客戶和備份企業檔案資料。

### <a name="enterprise-integration-pack-templates"></a>企業整合套件範本
VETER (驗證、擷取、轉換、擴充、路由) 管線的組態、透過 AS2 接收 X12 EDI 文件並將其轉換為 XML，以及 X12 和 AS2 訊息處理。

### <a name="protocol-pattern-templates"></a>通訊協定模式範本
這些範本由包含通訊協定模式的邏輯應用程式所組成，例如透過 HTTP 的要求回應以及跨 FTP 和 SFTP 的整合。 使用這些現存模式，或將它們做為基礎來建立更複雜的通訊協定模式。  

### <a name="personal-productivity-templates"></a>個人生產力範本
用來協助提升個人生產力的模式所包含的範本會設定每日提醒、將重要的工作項目轉換成待辦事項清單，並將冗長的工作自動縮減為一個使用者核准步驟。

### <a name="consumer-cloud-templates"></a>取用者雲端範本
與社交媒體服務 (如 Twitter、Slack 和電子郵件) 整合的簡單範本，最終能夠強化社交媒體行銷計劃。 其中也包含多雲複製之類的範本，其可透過節省花在傳統重複性工作的時間來協助提高生產力。 

## <a name="how-to-create-a-logic-app-using-a-template"></a>使用範本建立邏輯應用程式的方式
若要開始使用邏輯應用程式範本，請進入邏輯應用程式設計工具。 如果您透過開啟現有邏輯應用程式來進入設計工具，邏輯應用程式會自動載入設計工具檢視中。 不過，如果您要建立新的邏輯應用程式，您會看到下列畫面。  
 ![](../../includes/media/app-service-logic-templates/template7.png)  

在此畫面中，您可以選擇以空白邏輯應用程式或預先建置的範本來開始著手。 如果您選取其中一個範本，系統會提供其他資訊給您。 在此範例中，我們使用「在 Dropbox 中建立新檔案時，將它複製到 OneDrive」  範本。  
 ![](../../includes/media/app-service-logic-templates/template2.png)  

如果您選擇使用此範本，則只要選取 [使用此範本]  按鈕。 系統會要求您根據範本所使用的連接器登入您的帳戶。 或者，如果您先前已建立使用這些連接器的連線，您可以選取 [繼續]，如下所示。  
 ![](../../includes/media/app-service-logic-templates/template3.png)  

建立連線並選取 [繼續] 之後，邏輯應用程式便會在設計工具檢視中開啟。  
 ![](../../includes/media/app-service-logic-templates/template4.png)  

在上述範例中，和許多範本所遇到的狀況相同，連接器內的某些必要屬性欄位可能已填入資料；不過，有些仍可能需要填入值，然後才能正確部署邏輯應用程式。 如果您嘗試不輸入遺漏的欄位就進行部署，將會出現錯誤訊息來通知您。

如果您想要返回範本檢視器，請選取頂端導覽列中的 [範本]  按鈕。 切換回範本檢視器將會讓您遺失任何未儲存的進度。 在切換回範本檢視器之前，您會看到警告訊息來告知您這一點。  
 ![](../../includes/media/app-service-logic-templates/template5.png)  

## <a name="how-to-deploy-a-logic-app-created-from-a-template"></a>如何部署從範本建立的邏輯應用程式
載入範本並進行所需的任何變更之後，請選取左上角的 [儲存] 按鈕。 這可儲存並發佈邏輯應用程式。  
 ![](../../includes/media/app-service-logic-templates/template6.png)  

如果您需要如何對現有邏輯應用程式範本新增更多步驟或進行一般編輯的詳細資訊，請參閱 [建立邏輯應用程式](../logic-apps/logic-apps-create-a-logic-app.md)。

