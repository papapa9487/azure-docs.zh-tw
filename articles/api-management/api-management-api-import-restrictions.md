---
title: "在 Azure API 管理 API 匯入的限制和已知問題 | Microsoft Docs"
description: "有關使用 Open API、WSDL 或 WADL 格式匯入 Azure API 管理的已知問題和限制的詳細資料。"
services: api-management
documentationcenter: 
author: vladvino
manager: vlvinogr
editor: 
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: 758babce3ed387ed4864f1934650cf701bda788f
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2017
---
# <a name="api-import-restrictions-and-known-issues"></a>API 匯入的限制和已知問題
## <a name="about-this-list"></a>關於本清單
匯入 API 時，您可能會遇到一些限制或識別問題，必須先解決這些問題，您才能成功匯入。 這篇文章說明這些項目，依 API 的匯入格式整理說明。

## <a name="open-api"> </a>Open API/Swagger
如果您匯入 Open API 文件時收到錯誤，請務必確認您的文件 - 可使用 Azure 入口網站中的設計工具 ([設計] - [前端] - [Open API 規格編輯器])，或使用第 3 方工具 (例如 <a href="http://www.swagger.io">Swagger 編輯器</a>)。

* **主機名稱** APIM 需要主機名稱屬性。
* **基本路徑** APIM 需要基本路徑屬性。
* **配置** APIM 需要配置陣列。 

## <a name="wsdl"> </a>WSDL
WSDL 檔案是用來產生 SOAP 傳遞的 API，或作為「SOAP 至 REST」API 的後端。

* **WSDL:Import** - APIM 目前不支援使用這個屬性的 API。 客戶應該將匯入的項目合併成一份文件。
* **具有多個部分的訊息**目前 APIM 不支援這些類型的訊息。
* **WCF wsHttpBinding** 使用 Windows Communication Foundation 建立的 SOAP 服務應該使用 basicHttpBinding - 不支援 wsHttpBinding。
* **MTOM** - 使用 MTOM 的服務「可能」<em></em>可以運作。 目前不提供官方支援。
* APIM 不支援以遞迴方式定義的**遞迴**類型 (例如，參考自己的陣列)。

## <a name="wadl"> </a>WADL
目前沒有任何已知的 WADL 匯入問題。
