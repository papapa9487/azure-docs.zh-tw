---
title: "Azure 原則概觀 | Microsoft Docs"
description: "Azure 原則是 Azure 中的一個服務，您可以在 Azure 環境中用來建立、指派和管理原則定義。"
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark; nini
ms.date: 10/06/2017
ms.topic: overview
ms.service: azure-policy
manager: jochan
ms.custom: mvc
ms.openlocfilehash: 01b0915cdf37ddc00a4e6a38c99ce7f6f8c4f9c9
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-policy"></a>什麼是 Azure 原則？

IT 管理可以讓商務目標與 IT 專案之間變得清晰。 良好的 IT 管理包括規劃您的方案，以及在策略層級上設定優先順序。 如果您的公司遇到看起來永遠無法解決的大量 IT 問題，實作原則可協助您以更好的方式管理問題和預防問題。 這是 Azure 原則存在的理由。

Azure 原則是 Azure 中的一個服務，您可以用來建立、指派和管理原則定義。 原則定義會對您的資源強制執行不同的規則和動作，讓這些資源能符合公司標準和服務等級協定的規範。 它會執行並評估您的資源，以掃描出哪個資源不符合您既有原則定義的規範來達到目標。

## <a name="policy-definition"></a>原則定義

每個原則定義都有強制執行的條件，以及當條件符合時所採取的伴隨動作。

在 Azure 原則中，我們預設提供一些可供您使用的內建原則。 例如：

- **需要 SQL Server 12.0**：此原則定義有條件/規則，以確保所有 SQL Server 都使用 12.0 版。 它的動作是拒絕不符合這些準則的所有伺服器。
- **允許的儲存體帳戶 SKU**：此原則定義有一組條件/規則，判斷正在部署的儲存體帳戶是否在一組 SKU 大小內。 它的動作是拒絕不符合已定義 SKU 大小集合的所有伺服器。
- **允許的資源類型**：此原則定義有一組條件/規則，指定您的組織可以部署的資源類型。 它的動作是拒絕不屬於此定義清單的所有資源。

若要深入了解原則定義的結構，請閱讀這篇文章 - [原則定義結構](../azure-resource-manager/resource-manager-policy.md#policy-definition-structure)。

## <a name="policy-assignment"></a>原則指派
原則指派是已指派在特定範圍內發生的原則定義。 此範圍可從管理群組到資源群組。

如需有關設定原則定義和指派的詳細資訊，請參閱[資源原則概觀](../azure-resource-manager/resource-manager-policy.md)。

## <a name="policy-parameters"></a>原則參數
原則參數減少您必須建立的原則定義數量，有助於簡化原則管理。 在建立更通用的原則定義時，您可以定義參數。 然後，您可以在指派原則時藉由傳入不同的值 (例如，指定訂用帳戶的一組位置)，針對不同的案例重複使用該原則定義。

參數是在建立原則定義時定義/建立。 定義參數時，需要指定名稱並選擇性地指定值。 例如，您可以將原則的參數定義為位置，然後在指派原則時賦予它不同的值，例如 *EastUS* 或 *WestUS*。

如需原則參數的詳細資訊，請參閱[資源原則概觀 - 參數](../azure-resource-manager/resource-manager-policy.md#parameters)。

## <a name="initiative-definition"></a>計畫定義
計畫定義是針對達到單一主要目標而設計的原則定義集合。 例如，您可以建立一個標題為**在 Azure 資訊安全中心啟用監視**的計畫，目標是要在您的 Azure 資訊安全中心監視所有可用的安全性建議。

在這項計畫之下，您可能會有如下的原則定義：

1. **在資訊安全中心監視未加密的 SQL Database** – 適用於監視未加密的 SQL 資料庫和伺服器。
2. **在資訊安全中心監視作業系統弱點**– 適用於監視不符合設定基準的伺服器。
3. **在資訊安全中心監視遺失的 Endpoint Protection** – 適用於監視沒有安裝 Endpoint Protection 代理程式的伺服器。

## <a name="initiative-assignment"></a>計畫指派
類似原則指派，計畫指派是指派至特定範圍的計畫定義。 計畫指派會減少為每個範圍進行多個計畫定義的需求。 此範圍也可從管理群組到資源群組。

從上述範例中，**在 Azure 資訊安全中心啟用監視**計畫可以指派至不同的範圍。 例如，一個可以指派至 **subscriptionA**，而另一個可以指派至 **subscriptionB**。

## <a name="initiative-parameters"></a>計畫參數
類似原則參數，計畫參數減少重複性，有助於簡化計畫管理。 計畫參數基本上是計畫內原則定義所要使用的參數清單。

例如，以您有計畫定義 **initiativeC** (有兩個原則定義) 的案例為例。 每個原則定義都有一個定義的參數：

|原則  |參數名稱     |參數類型  |注意                                                                                                |
|--------|----------------------|-------|----------------------------------------------------------------------------------------------------------------|
|policyA |allowedLocations      |array  |因為參數類型已定義為陣列，所以此參數應該要有一個字串清單作為值 |
|policyB |allowedSingleLocation |字串 |因為參數類型已定義為字串，所以此參數應該要有一個字組作為值          |

在此案例中，定義 **initiativeC** 的計畫參數時，有三個選項：

1. 運用此計畫內原則定義的參數：在此範例中，*allowedLocations* 和 *allowedSingleLocation* 變成 **initiativeC** 的計畫參數。
2. 提供值給此計畫定義內原則定義的參數。 在此範例中，您可以提供位置清單給 **policyA 的參數 – allowedLocations** 和 **policyB 的參數 – allowedSingleLocation**。
您也可以在指派此計畫時提供值。
3. 提供指派此計畫時可以使用的*值*選項清單。 這表示當您指派此計畫時，從計畫內原則定義繼承的參數，只能有此提供清單中的值。

例如，如果您在建立計畫定義時提供的值選項清單，有 *EastUS*、*WestUS*、*CentralUS* 和 *WestEurope*，您在指派計畫時就無法輸入不同的值，例如 *Southeast Asia*，因為它不在清單中。

## <a name="recommendations-for-managing-policies"></a>管理原則的建議

建立和管理原則定義和指派時，我們建議您遵循以下幾個指標：

- 如果您要在環境中建立原則定義，我們建議從稽核效果而不是拒絕效果開始，以追蹤原則定義在環境中的影響。 如果您已經有可自動調整應用程式的指令碼，設定拒絕效果可能會妨礙您既有的這些自動化工作。
- 在建立定義和指派時，請務必記住組織階層。 我們建議在較高的層級 (例如在管理群組或訂用帳戶層級) 建立定義，然後在下一個子層級指派。 例如，如果您在管理群組層級建立原則定義，該定義的原則指派可以向下延伸到訂用帳戶層級。
- 我們鼓勵使用標準定價層，以深入了解您環境的合規性狀態。
- 我們建議一律使用計畫定義而不是原則定義，即使您只要一個原則亦同。 例如，如果您有原則定義 *policyDefA*，且在計畫定義 *initiativeDefC* 下建立，如果您決定建立另一個目標類似 *policyDefA* 的原則定義，您就可以在 *initiativeDefC* 下新增，並以此更好的方式追蹤這些定義。

   請記住，一旦您從計畫定義建立了計畫指派，加入計畫定義的任何新原則定義都會自動彙總在計畫定義下的計畫指派。 不過，如果在新原則定義中引入新的參數，您就需要編輯計畫定義或指派來更新定義和指派，才能反映此變更。

## <a name="next-steps"></a>後續步驟：
您已經大概了解 Azure 原則，以及一些我們介紹的重要概念，以下是建議的後續步驟：

- [指派原則定義](./assign-policy-definition.md)
- [使用 Azure CLI 指派原則定義](./assign-policy-definition-cli.md)
- [使用 PowerShell 指派原則定義](./assign-policy-definition-ps.md)
