---
title: "自訂連接器常見問題集 - Azure Logic Apps | Microsoft Docs"
description: "關於建立自訂連接器的需求、觸發程序等相關常見問題集"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 225e56de3985acae871ddec447b763e7de61cb80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="faq-custom-connectors"></a>常見問題集：自訂連接器

## <a name="requirements"></a>需求

**問：**我是否可以不使用 REST API 來建置連接器？ </br>
**答：**否，若要建置連接器，您必須支援服務的穩定 HTTP REST API。 

**問：**我可以使用哪些工具來建立連接器？ </br>
**答：** Azure 提供許多功能和服務，可讓您用來公開任何服務作為 API，例如用於裝載的 Azure App Service、API 管理等等。

**問：**支援下列哪些驗證類型？ </br>
**答：**您可以使用這些支援的驗證標準：

* [OAuth 2.0](https://oauth.net/2/)，包括 [Azure Active Directory](https://azure.microsoft.com/develop/identity/) 或特定服務，例如 Dropbox、GitHub 及 SalesForce
* Generic OAuth 2.0
* [基本驗證](https://swagger.io/docs/specification/authentication/basic-authentication/)
* [API 金鑰](https://swagger.io/docs/specification/authentication/api-keys/)

## <a name="triggers"></a>觸發程序

**問：**我是否可以不使用 webhook 來建置觸發程序？ </br>
**答：**否，Azure Logic Apps 與 Microsoft Flow 的自訂連接器，僅支援以 webhook 作為基礎的觸發程序。 如果您需要要求實作的其他模式，請連絡[condevhelp@microsoft.com](mailto:condevhelp@microsoft.com)並提供您 API 的更多相關詳細資料。

## <a name="certification"></a>認證

**問：**：我不是 Microsoft 合作夥伴或獨立軟體廠商 (ISV)。 仍然可以建立連接器嗎？ </br>
**答**：是，您可以註冊這些連接器以供貴組織內部使用，但如果您需要認證和公開發行連接器，就必須擁有基礎服務或出示明確的權限，才能使用 API。

## <a name="other"></a>其他

**問：**我的 API 是使用動態主機。 如何使用 OpenAPI 來實作它們？ </br>
**答：**自訂連接器不支援動態主機。 請改用靜態主機進行開發和測試之用。 如果您需要確認連接器，請洽詢 Microsoft 連絡人有關動態實作的資訊。

**問：**是否支援 Postman Collection V2？ </br>
**答：**否，目前僅支援 Postman Collection V1。

**問：**是否支援 OpenAPI 3.0？ </br>
**答：**否，目前支援僅 OpenAPI 2.0。