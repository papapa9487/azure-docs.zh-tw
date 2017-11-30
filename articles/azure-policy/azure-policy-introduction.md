---
title: "Azure 原則概觀 | Microsoft Docs"
description: "Azure 原則是 Azure 中的一個服務，您可以在 Azure 環境中用來建立、指派和管理原則定義。"
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders; nini
ms.date: 11/06/2017
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 8ffa4c22c39bcd241b36b55bbcba24ac3f75fff2
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2017
---
# <a name="what-is-azure-policy"></a>什麼是 Azure 原則？

IT 管理可以讓商務目標與 IT 專案之間變得清晰。 良好的 IT 管理包括規劃您的方案，以及在策略層級上設定優先順序。 您公司是否遇到似乎永遠無法解決的大量 IT 問題？ 實作原則將可幫助您更有效地管理和預防這些問題。 實作原則正是「Azure 原則」存在的理由。

Azure 原則是 Azure 中的一個服務，您可以用來建立、指派和管理原則定義。 原則定義會對您的資源強制執行不同的規則和動作，讓這些資源能符合公司標準和服務等級協定的規範。 「Azure 原則」會執行資源評估，掃描出與您所擁有的原則定義不符的資源。 例如，您可以有一個只允許特定類型虛擬機器的原則。 再另有一個要求所有資源都具有特定標籤的原則。 接著，在建立和更新資源時，就會評估這些原則。

## <a name="how-is-it-different-from-rbac"></a>它和 RBAC 有什麼不同？

原則和角色型存取控制 (RBAC) 之間有幾個主要差異。 RBAC 著重於不同範圍的使用者動作。 例如，您可能被加入所需範圍之資源群組的參與者角色中。 該角色可讓您對該資源群組進行變更。 原則著重於部署期間及既有資源的資源屬性。 例如，您能夠透過原則控制可以佈建的資源類型。 或者，您可以限制資源可以佈建的位置。 不同於 RBAC，原則是個預設允許和明確拒絕的系統。

若要使用原則，您必須透過 RBAC 驗證。 具體來說，您的帳戶需要：

- 可定義原則的 `Microsoft.Authorization/policydefinitions/write` 權限。
- 可指派原則的 `Microsoft.Authorization/policyassignments/write` 權限。

這些權限不包括在**參與者**角色中。


## <a name="policy-definition"></a>原則定義

每個原則定義都有其強制執行條件。 而且，還有符合條件時會進行的伴隨動作。

在 Azure 原則中，我們預設提供一些可供您使用的內建原則。 例如：

- **需要 SQL Server 12.0**：此原則定義有條件/規則，以確保所有 SQL Server 都使用 12.0 版。 它的動作是拒絕不符合這些準則的所有伺服器。
- **允許的儲存體帳戶 SKU**：此原則定義有一組條件/規則，判斷正在部署的儲存體帳戶是否在一組 SKU 大小內。 它的動作是拒絕不符合已定義 SKU 大小集合的所有伺服器。
- **允許的資源類型**：此原則定義有一組條件/規則，指定您的組織可以部署的資源類型。 它的動作是拒絕不屬於此定義清單的所有資源。
- **允許的位置**：此原則可讓您限制您組織在部署資源時可指定的位置。 其動作可用來強制執行您的地理合規性需求。
- **允許的虛擬機器 SKU**：此原則可讓您指定您組織可部署的一組虛擬機器 SKU。
- **套用標籤及其預設值**：此原則會套用必要的標籤及其預設值 (如果使用者未指定的話)。
- **強制執行標籤及其值**：此原則會對資源強制執行必要的標籤及其值。
- **不允許的資源類型**：此原則可讓您指定您組織不可以部署的資源類型。

您可以透過 Azure 入口網站、PowerShell 或 Azure CLI 來指派任何這些原則。

若要深入了解原則定義的結構，請閱讀這篇文章 - [原則定義結構](policy-definition.md)。

## <a name="policy-assignment"></a>原則指派

原則指派是已指派在特定範圍內發生的原則定義。 此範圍可從管理群組到資源群組。 「範圍」一詞係指作為原則定義指派對象的所有資源群組、訂用帳戶或管理群組。 原則指派會由所有子資源繼承。 因此，如果將原則套用至某個資源群組，它就會套用至該資源群組中的所有資源。 不過，您可以從原則指派中排除某個子範圍。 例如，在訂用帳戶範圍，您可以指派一個防止建立網路資源的原則。 不過，您將訂用帳戶內一個要用於網路基礎結構的資源群組排除。 您需將此網路資源群組的存取權授與您所信任來建立網路資源的使用者。

如需有關設定原則定義和指派的詳細資訊，請參閱[建立原則指派，以識別 Azure 環境中不符合規範的資源](assign-policy-definition.md)。

## <a name="policy-parameters"></a>原則參數

原則參數減少您必須建立的原則定義數量，有助於簡化原則管理。 在建立更通用的原則定義時，您可以定義參數。 接著，您便可以針對不同的案例，重複使用該原則定義。 若要這麼做，只要在指派原則定義時傳入不同的值即可。 例如，為訂用帳戶指定一組位置。

參數是在建立原則定義時定義/建立。 定義參數時，需要指定名稱並選擇性地指定值。 例如，您可以為原則定義一個標題為 *location* 的參數。 接著，您可以在指派原則時給予它不同的值，例如 *EastUS* 或 *WestUS*。

<!--
Next link should point to new Concept page for Parameters
-->
如需原則參數的詳細資訊，請參閱[資源原則概觀 - 參數](policy-definition.md#parameters)。

## <a name="initiative-definition"></a>計畫定義
計畫定義是針對達到單一主要目標而設計的原則定義集合。 計畫定義可讓管理及指派原則定義更為簡單。 其簡化方式是將一組原則組成單一項目。 例如，您可以建立一個標題為**在 Azure 資訊安全中心啟用監視**的計畫，目標是要在您的 Azure 資訊安全中心監視所有可用的安全性建議。

在這項計畫之下，您可能會有如下的原則定義：

1. **在資訊安全中心監視未加密的 SQL Database** – 適用於監視未加密的 SQL 資料庫和伺服器。
2. **在資訊安全中心監視作業系統弱點**– 適用於監視不符合設定基準的伺服器。
3. **在資訊安全中心監視遺失的 Endpoint Protection** – 適用於監視沒有安裝 Endpoint Protection 代理程式的伺服器。

<!--
For more information about initiative definitions, see Initiative Definitions.+ (instead of linking to this, link out to Concept page on Initiative Definitions)
-->

## <a name="initiative-assignment"></a>計畫指派
類似原則指派，計畫指派是指派至特定範圍的計畫定義。 計畫指派會減少為每個範圍進行多個計畫定義的需求。 此範圍也可從管理群組到資源群組。

從上述範例中，**在 Azure 資訊安全中心啟用監視**計畫可以指派至不同的範圍。 例如，一個指派項目可以指派給**subscriptionA**。 另一個可以指派給 **subscriptionB**。

## <a name="initiative-parameters"></a>計畫參數
類似原則參數，計畫參數減少重複性，有助於簡化計畫管理。 計畫參數基本上是計畫內原則定義所要使用的參數清單。

例如，以您有計畫定義 **initiativeC** (有兩個原則定義) 的案例為例。 每個原則定義都有一個定義的參數：

| 原則 | 參數名稱 |參數類型  |注意 |
|---|---|---|---|
| policyA | allowedLocations | array  |因為參數類型已定義為陣列，所以此參數應該要有一個字串清單作為值 |
| policyB | allowedSingleLocation |字串 |因為參數類型已定義為字串，所以此參數應該要有一個字組作為值 |

在此案例中，定義 **initiativeC** 的計畫參數時，有三個選項：

1. 使用此計畫內原則定義的參數：在此範例中，*allowedLocations* 和 *allowedSingleLocation* 會變成 **initiativeC** 的計畫參數。
2. 提供值給此計畫定義內原則定義的參數。 在此範例中，您可以提供位置清單給 **policyA 的參數 – allowedLocations** 和 **policyB 的參數 – allowedSingleLocation**。
您也可以在指派此計畫時提供值。
3. 提供指派此計畫時可以使用的*值*選項清單。 當您指派此計畫時，從計畫內原則定義繼承的參數，只能有來自此提供清單中的值。

例如，您可能會在計畫定義中建立一個包含 *EastUS*、*WestUS*、*CentralUS* 及 *WestEurope* 的值選項清單。 如果是這樣，在進行計畫指派時，您就無法輸入其他的值，例如 *Southeast Asia*，因為此值並不包含在該清單中。

## <a name="recommendations-for-managing-policies"></a>管理原則的建議

建立和管理原則定義和指派時，我們建議您遵循以下幾個指標：

- 如果您要在環境中建立原則定義，建議您從稽核效果而不是從拒絕效果開始著手，以記錄原則定義對環境中資源的影響。 如果您已經有可自動調整應用程式的指令碼，設定拒絕效果可能會妨礙您既有的這些自動化工作。
- 在建立定義和指派時，請務必記住組織階層。 我們建議在較高的層級 (例如在管理群組或訂用帳戶層級) 建立定義，然後在下一個子層級指派。 例如，如果您在管理群組層級建立原則定義，該定義的原則指派範圍便可向下延伸到該管理群組內的訂用帳戶層級。
- 我們鼓勵使用標準定價層，以深入了解您環境的合規性狀態。 如需有關定價模型及其個別提供內容的詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/azure-policy)。
- 我們建議一律使用計畫定義而不是原則定義，即使您只要一個原則亦同。 例如，如果您有原則定義 - *policyDefA* ，而且將它建立在計畫定義 - *initiativeDefC* 底下，當您稍後決定為 *policyDefB* 建立另一個目標與 *policyDefA* 目標類似的原則定義時，您就可以在 *initiativeDefC* 底下新增它，然後以此方式更有效地追蹤這些定義。

   請記住，在您從計畫定義建立了計畫指派之後，任何新增到計畫定義中的新原則定義，都會自動彙總在該計畫定義下的計畫指派底下。 不過，如果在新原則定義中引進了新的參數，您就必須編輯計畫定義或指派來更新定義和指派。

## <a name="next-steps"></a>後續步驟

您已經大概了解 Azure 原則，以及一些我們介紹的重要概念，以下是建議的後續步驟：

- [指派原則定義](./assign-policy-definition.md)
- [使用 Azure CLI 指派原則定義](./assign-policy-definition-cli.md)
- [使用 PowerShell 指派原則定義](./assign-policy-definition-ps.md)
