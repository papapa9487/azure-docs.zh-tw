---
title: "使用工作流程中的 XML 訊息 - Azure Logic Apps | Microsoft Docs"
description: "使用企業整合套件處理、驗證、轉換和擴充邏輯應用程式和商務中的 XML 訊息案例"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 02/27/2017
ms.author: LADocs; mandia
ms.openlocfilehash: 3fec4935f5317be4bf8c9e05f1c24a7c05381b1e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="validate-and-transform-xml-encode-and-decode-flat-files-and-enrich-messages-features-in-logic-apps"></a>驗證和轉換的 XML、編碼和解碼一般檔案，並擴充邏輯應用程式中的訊息功能

使用邏輯應用程式，您可以處理您傳送和接收的 XML 訊息。 這項功能包含於企業整合套件。 針對具有 BizTalk Server 背景的這些使用者，企業整合套件可為您提供與轉換和驗證訊息、使用一般檔案，甚至使用 XPath 加強或從訊息擷取特定屬性的類似功能。 

此空間不熟悉的使用者，這些功能可展開您工作流程內的處理訊息方式。 例如，如果您是在企業對企業案例中，並且使用特定的 XML 結構描述，則您可以使用企業整合套件以增強貴公司處理這些訊息的方式。 

企業整合套件包含： 

* [XML 驗證](logic-apps-enterprise-integration-xml-validation.md "了解 XML 訊息驗證") - 根據特定的結構描述，驗證傳入或傳出 XML 訊息。
* [XML 轉換](../logic-apps/logic-apps-enterprise-integration-transform.md "了解 XML 訊息轉換和對應") - 根據您的需求或夥伴的需求轉換或自訂 XML 訊息。
* [一般檔案編碼與一般檔案解碼](logic-apps-enterprise-integration-flatfile.md "了解一般檔案編碼/解碼") - 編碼或解碼一般檔案。 例如，SAP 接受並以一般檔案格式傳送 IDOC 檔案。 許多整合平台會建立 XML 訊息，包括邏輯應用程式。 因此，您可以建立使用一般檔案編碼器將 XML 檔案「轉換」為一般檔案的邏輯應用程式。 
* [XPath](https://msdn.microsoft.com/library/mt643789.aspx) - 可擴充訊息，並從訊息擷取特定的屬性。 然後您可以使用擷取的屬性將訊息傳送至目的地或中繼端點。

## <a name="try-it-out"></a>試做
使用 Azure Logic Apps 中的 XML 功能，[部署全功能的邏輯應用程式 (GitHub sample)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline)。

## <a name="learn-more"></a>詳細資訊
[深入了解企業整合套件](../logic-apps/logic-apps-enterprise-integration-overview.md "了解企業整合套件")
