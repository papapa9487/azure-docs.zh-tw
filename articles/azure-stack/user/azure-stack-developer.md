---
title: "開發適用於 Azure Stack 的應用程式 | Microsoft Docs"
description: "了解在 Azure Stack 上建立原型應用程式的開發考量"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 062c17173f87eec8e0eaa3f74323cbf8a8f48571
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="develop-for-azure-stack"></a>開發 Azure Stack
即使您沒有 Azure Stack 環境的存取權，您也可以現在就開始開發應用程式。 因為 Azure Stack 會傳遞在您資料中心執行的 Microsoft Azure 服務，所以您可以使用類似工具和流程針對 Azure Stack 進行開發，就如同您為 Azure 進行開發一樣。  只需要跟隨下列主題的準備和指導，您就可以使用 Azure 來模擬 Azure Stack 環境：

* 在 Azure 中，您可以建立 Azure Resource Manager 範本，其也將會部署至 Azure Stack。  請參閱《[範本考量](azure-stack-develop-templates.md)》取得開發您範本的指導並確保可攜性。
* 在 Azure 和 Azure Stack 之間的服務可用性和服務版本存在差異。 您可以使用 [Azure Stack 原則模組](azure-stack-policy-module.md)來限制 Azure Stack 可用的 Azure 服務可用性和資源型別。 限制可用服務將可協助您的應用程式依賴 Azure Stack 可用的服務。
* [Azure Stack 快速入門範本](https://github.com/Azure/AzureStack-QuickStart-Templates)是如何開發您範本，讓其能夠部署至 Azure 和 Azure Stack 的常見情節範例。



