---
title: "Azure Stack 方案、供應項目、配額和訂用帳戶概觀 | Microsoft Docs"
description: "身為雲端操作員，我想要了解 Azure Stack 方案、供應項目、配額和訂用帳戶。"
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 8/22/2017
ms.author: erikje
ms.openlocfilehash: 083ca2f0a06625810d2f90a682ba0b3110032e60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="plan-offer-quota-and-subscription-overview"></a>方案、優惠、配頭和訂用帳戶概觀

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

[Azure Stack](azure-stack-marketplace-azure-items.md) 可讓您提供各式各樣的服務，例如虛擬機器、SQL Server 資料庫、SharePoint、Exchange，甚至 [Azure Marketplace 項目](azure-stack-poc.md)。 身為 Azure Stack 操作員，您可以使用方案、供應項目與配額，在 Azure Stack 中設定並提供這類服務。

供應項目包含一個或多個方案，而且每個方案包含一個或多個服務。 透過建立方案並將其組合成不同的供應項目，您可以控制
- 使用者可以存取的服務和資源
- 使用者可以取用這些資源的數量
- 可以存取資源的區域

當您提供服務時，將遵循下列大致步驟：

1. 新增您要提供給使用者的服務。
2. 建立包含一個或多個服務的方案。 建立方案時，您將會選取或建立配額，以定義方案中每個服務的資源限制。
3. 建立包含一個或多個方案 (包含基本方案及選擇性的附加方案) 的供應項目。

建立供應項目之後，您的使用者就可以訂閱供應項目，以存取它所提供的服務和資源。 使用者可以訂閱所需的任意數量供應項目。 下圖顯示的簡單範例是已訂閱兩個供應項目的使用者。 每個供應項目都有一個或兩個方案，而且每個方案都能提供對服務的存取權。

![](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>方案

方案結合一或多項服務。 身為 Azure Stack 操作員，您可以[建立方案](azure-stack-create-plan.md)以提供給使用者。 使用者接著即可訂閱您的供應項目，以使用其中的方案與服務。 建立方案時，請務必設定配額、定義基本方案，並考慮包含選擇性的附加方案。

### <a name="quotas"></a>配額

為了協助管理雲端容量，您可以在方案中，為每個服務選取或建立配額。 配額會定義使用者訂用帳戶可以佈建或取用的資源上限。 例如，配額可能會允許使用者建立最多五部虛擬機器。 配額可以限制各種不同的資源，例如虛擬機器、RAM 和 CPU 限制。

配額可依地區設定。 例如，包含來自區域 A 計算服務的方案，其配額可能是兩部虛擬機器、4-GB RAM 與 10 個 CPU 核心。 在 Azure Stack 開發套件中，只有一個區域 (名為 *local*) 可供使用。

### <a name="base-plan"></a>基本方案

建立供應項目時，服務系統管理員可以包含基本方案。 當使用者訂閱該供應項目時，預設會包含這些基本方案。 當使用者訂閱時，即可存取這些基本方案中所指定的所有資源提供者 (同時受到對應的配額限制)。

### <a name="add-on-plans"></a>附加方案

您也可以在供應項目中包含選擇性的附加方案。 訂用帳戶中預設不會包含加購方案。 供應項目中提供的附加方案屬於額外的方案 (具有配額)，訂閱者可將其加入其訂用帳戶中。 例如，您可以提供資源有限的基本方案以供試用，並為決定採用服務的客戶提供更多實質資源的附加方案。

## <a name="offers"></a>優惠

供應項目是您建立的一個或多個方案的群組，使用者可以訂閱這些供應項目。 例如，供應項目 Alpha 可以包含方案 A (其中包含一組計算服務) 與方案 B (其中包含一組儲存體與網路服務)。 

當您[建立供應項目](azure-stack-create-offer.md)時，必須至少包含一個基本方案，但是您也可以建立使用者可以加入至其訂用帳戶的附加方案。


## <a name="subscriptions"></a>訂用帳戶

訂用帳戶是使用者存取供應項目的方式。 如果您是服務提供者的 Azure Stack 操作員，使用者 (租用戶) 會透過訂閱供應項目的方式來購買您的服務。 如果您是組織的 Azure Stack 操作員，使用者 (員工) 可以訂閱您提供的服務，而不必付錢。 使用者與供應項目的每個組合都是一個唯一的訂用帳戶。 因此，使用者可以擁有多個供應項目的訂用帳戶，但是每個訂用帳戶都僅適用於一個供應項目。 方案、供應項目與配額僅適用於每個唯一的訂用帳戶，而無法在訂用帳戶之間共用。 使用者建立的每個資源都與一個訂用帳戶相關聯。


### <a name="default-provider-subscription"></a>預設的提供者訂用帳戶

當您部署 Azure Stack 開發套件時，會自動建立預設的提供者訂用帳戶。 此訂用帳戶可用來管理 Azure Stack、部署其他資源提供者，以及為使用者建立方案與供應項目。 基於安全性和授權的緣故，此訂用帳戶不應用於執行客戶工作負載和應用程式。 

## <a name="next-steps"></a>後續步驟

[建立方案](azure-stack-create-plan.md)
