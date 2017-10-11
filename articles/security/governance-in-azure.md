---
title: "Azure 中的治理 | Microsoft Docs"
description: "了解雲端式計算服務，其中包含各式各樣的計算執行個體和服務，可自動相應增加或縮小以符合您應用程式或企業的需求。"
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/01/2017
ms.author: TomSh
ms.openlocfilehash: f3fbca281dbbfca7ff9392d003429719c589afbd
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="governance-in-azure"></a>Azure 中的治理

我們知道安全性是雲端中的首要工作和其重要性，因為您可在其中找到精確且及時的 Azure 安全性資訊。 針對您的應用程式和服務使用 Azure 的最佳原因之一是可以利用它的各種安全性工具和功能。 這些工具和功能可協助您在安全的 Azure 平台上建立安全的解決方案。

為了協助您從客戶和 Microsoft 作業的觀點，深入了解實作於 Microsoft Azure 內的一系列治理控制項，因而編寫了本文＜Azure 中的治理＞，以提供 Microsoft Azure 所提供之治理功能的完整介紹。

## <a name="azure-platform"></a>Azure 平台

Azure 是一個公用雲端服務平台，支援廣泛的作業系統、程式設計語言、架構、工具、資料庫及裝置。 它可以透過 Docker 整合執行 Linux 容器；使用 JavaScript、Python、.NET、PHP、Java 及 Node.js 建置應用程式；為 iOS、Android 及 Windows 裝置建置後端。 Azure 公用雲端服務支援數百萬名開發人員和 IT 專家早已仰賴和信任的相同技術。

當您在公用雲端服務提供者上進行建置，或是將 IT 資產移轉至公用雲端服務提供者時，必須依賴該組織針對管理雲端式資產安全性所提供的服務與控制，以保護您應用程式和資料的能力。

Azure 的基礎結構設計涵蓋設備與應用程式，可同時裝載數以百萬計的客戶，並提供可靠的基礎，使企業得以符合其安全性需求。 此外，Azure 也提供許多安全性選項及控制它們的能力，讓您能夠自訂安全性以符合組織部署的特殊需求。

本文件有助於了解 Azure 治理功能如何協助您滿足這些需求。

## <a name="abstract"></a>摘要

Microsoft Azure 雲端治理會以整合的稽核與諮詢方式，為組織提供針對其 Azure 平台使用方式的檢閱及建議。 Microsoft Azure 雲端治理指的是雲端運算的規劃、架構、擷取、部署、作業及管理上所涉及的決策程序、條件及原則。

若要針對 Microsoft Azure 雲端治理建立計畫，您必須深入檢視目前的人員、程序及技術，然後建置可讓 IT 持續支援商務需求，並讓使用者彈性使用 Microsoft Azure 強大功能的架構。

本白皮書說明在 Microsoft Azure 中更深入治理 IT 資源的方式。 本白皮書可以協助您了解內建於 Microsoft Azure 中的安全性和治理功能。

下列為本白皮書所討論的主要治理問題：

- 針對組織目標實作原則、流程及程序。

- 符合組織標準的安全性及持續符合合規性

- 警示和監視

## <a name="implementation-of-policies-processes-and-procedures"></a>實作原則、流程及程序 

管理已建立跨 Azure 監督資訊安全性原則及作業持續性之實作的角色和責任。 Microsoft Azure 管理會負責監督安全性和持續性小組 (包括協力廠商) 在相對應作業上所採取的作法，並促進符合安全性原則、程序及標準的合規性。

以下是相關的因素：

- 帳戶佈建

- 訂用帳戶控制

- 角色型存取控制

- 資源管理

- 資源追蹤

- 重要資源控制

- 針對帳單資訊的 API 存取

- 網路控制

## <a name="account-provisioning"></a>帳戶佈建

定義帳戶階層是在公司內使用並構造 Azure 服務的重要步驟，也是核心的治理結構。 針對具有企業合約的客戶，客戶能夠進一步將環境細分成部門、帳戶和最終的訂用帳戶。

![帳戶佈建](./media/governance-in-azure/security-governance-in-azure-fig1.png)

如果您沒有企業合約，請考慮使用訂用帳戶層級的 [Azure 標記](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)來定義階層。 Azure 訂用帳戶是內含所有資源的基本單位。 它也可在 Azure 中定義數個限制，例如核心、資源等的數目。訂用帳戶可以包含[資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)，其中可以包含資源。 那三個層級需套用 [RBAC](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control) 準則。

每個企業都不同，而針對非企業客戶使用 Azure 標記的階層，對於在公司內組織 Azure 的方式能容許極大的彈性。 在將資源部署至 Microsoft Azure 之前，您應該製作階層的模型並了解這會對計費、資源存取及複雜性所帶來的影響。

## <a name="subscription-controls"></a>訂用帳戶控制

訂用帳戶能控制資源使用量的報告與計費方式。 訂用帳戶可以針對個別的計費與付款進行設定。 如先前所述，單一 Azure 帳戶底下可以有多個訂用帳戶。 訂用帳戶可以用來判斷公司內多個部門的 Azure 資源使用量。

例如，如果某個公司有 IT、HR 及行銷部門，而這些部門都在執行不同的專案。 根據 Azure 資源的使用量 (例如每個部門所使用的虛擬機器數目)，便可以向他們進行計費。 透過這麼做，我們便能控制每個部門的財務狀況。

Azure 訂用帳戶會建立三個參數：

- 唯一的訂閱者識別碼

- 計費位置

- 可用資源集合

針對個人，這將會包含一個 Microsoft 帳戶識別碼、信用卡號碼，以及完整的 Azure 服務套件 (雖然 Microsoft 會根據訂用帳戶類型強制套用耗用限制)。

Azure 註冊階層會定義服務在企業合約內的結構方式。 企業入口網站允許客戶根據可針對組織特殊需求進行自訂的彈性階層，將存取權分給與企業合約相關聯的 Azure 資源。 階層模式應符合組織的管理和地理結構，以準確記錄相關聯的計費和資源存取。

三個高層級模式為功能、商務單位及地理，並使用部門作為帳戶群組的系統管理建構。 在每個部門中，可以將訂用帳戶指派給帳戶，這能在 Azure 中建立計費和數個重要限制 (例如 VM 數目、儲存體帳戶等) 的隔閡。

![訂用帳戶控制](./media/governance-in-azure/security-governance-in-azure-fig2.png)


針對具有企業合約的組織，Azure 訂用帳戶會遵循四個層級的階層：

- 企業註冊系統管理員

- 部門系統管理員

- 帳戶擁有者

- 服務管理員

此階層可控制下列項目：

- 計費關聯性

- 帳戶管理

- 針對構件的角色型存取控制 (RBAC)

- 界線/限制

- 界線

  - 使用量和計費 (費率卡片會以優惠數目為基礎)

  - 限制

  - 虛擬網路

- 附加至 1 AAD (1 AAD 會與數個訂用帳戶相關聯)

- 與企業註冊帳戶相關聯

## <a name="role-based-access-controls"></a>角色型存取控制

Azure 最初發行時，訂用帳戶的存取控制是基本的︰系統管理員或共同管理員。 存取傳統模型中的訂用帳戶，意味著存取入口網站中的所有資源。 缺乏細微控制導致訂用帳戶激增，進而為 Azure Enrollment 提供合理的存取控制層級。

![角色型存取控制](./media/governance-in-azure/security-governance-in-azure-fig3.png)

不再需要此種訂用帳戶激增情況。 使用角色型存取控制，您可以將使用者指派給標準角色 (例如常見的「讀取者」和「寫入者」角色類型)。 您也可以定義自訂角色。

[Azure 角色型存取控制 (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) 可以對 Azure 進行更細緻的存取權管理。 使用 RBAC，您可以僅授與使用者執行其作業所需的存取權。 安全性導向公司應該將焦點放在提供員工所需的確切權限。 權限太多會讓帳戶暴露在攻擊者的威脅下。 權限太少則會讓員工無法有效率地完成工作。 Azure「角色型存取控制」(RBAC) 可以為 Azure 提供更細緻的存取管理來協助解決這個問題。 RBAC 可協助您區隔小組內的職責，而僅授與使用者執行作業所需的存取權。 您可以不授與每個人 Azure 訂用帳戶或資源中無限制的權限，而是只允許執行特定的動作。

例如，使用 RBAC 讓一位員工管理某個訂用帳戶中的虛擬機器，而讓另一位員工管理相同訂用帳戶中的 SQL 資料庫。

Azure RBAC 有適用於所有資源類型的三個基本角色：

- **擁有者**：具有所有資源的完整存取權，包括將存取權委派給其他人的權限。

- **參與者**：可以建立和管理所有類型的 Azure 資源，但是不能將存取權授與其他人。

- **讀者**：可以檢視現有的 Azure 資源。

Azure 中其餘的 RBAC 角色可以管理特定 Azure 資源。 例如，「虛擬機器參與者」角色可讓使用者建立和管理虛擬機器。 但不會授予他們存取虛擬機器所連接的虛擬網路或子網路的存取權。

[RBAC 內建角色](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) 會列出 Azure 中可用的角色。 它會指定每個內建角色授與使用者的作業和範圍。

您可以藉由在特定範圍將適當的 RBAC 角色指派給使用者、群組及應用程式，來授與存取權。 角色指派的範圍可以是訂用帳戶、資源群組或單一資源。 在父範圍指派的角色也會授與其內含子系的存取權。

例如，具有資源群組存取權的使用者可以管理其內含的所有資源，例如網站、虛擬機器和子網路。

Azure RBAC 僅支援在 Azure 入口網站和 Azure Resource Manager API 中的 Azure 資源管理作業。 它無法授權 Azure 資源的所有資料層級作業。 例如，您可以授權某個人管理「儲存體帳戶」，但無法授權他管理「儲存體帳戶」內的 Blob 或資料表。 同樣地，可以管理 SQL Database，但無法管理其中的資料表。

如果您需要有關 RBAC 如何協助您管理存取權的詳細資訊，請參閱 [什麼是角色型存取控制](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)。

如果內建角色都不符合您的特定存取需求，您也可以在 Azure 角色型存取控制 (RBAC) 中[建立自訂角色](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)。 自訂角色可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell)、[Azure 命令列介面 (CLI)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli) 和 [REST API](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-rest) 建立。 就像內建角色一樣，可以將自訂角色指派給訂用帳戶、資源群組和資源範圍的使用者、群組和應用程式。

在每個訂用帳戶內，您可以授與最多 2000 個角色指派。

## <a name="resource-management"></a>資源管理

Azure 原本指提供傳統部署模型。 在此模型中，每個資源會獨立存在；沒辦法將相關的資源群組在一起。 因此，您必須手動追蹤哪些資源組成您的解決方案或應用程式，並記得要以協調的方法進行管理。

若要部署解決方案，您必須透過傳統入口網站個別建立每個資源，或建立會以正確的順序部署所有資源的指令碼。 若要刪除解決方案，您必須個別刪除每個資源。 您無法輕易套用和更新相關資源的存取控制原則。 最後，您無法將標記套用至資源，以可協助您監視資源和管理計費的詞彙標示資源。

在 2014 年，Azure 引進了新增資源群組概念的 Resource Manager。 資源群組是共用共同生命週期的資源容器。 「資源管理員」部署模型提供數個優點：

- 您可以以群組形式部署、管理及監視方案的所有服務，而不是個別處理這些服務。

- 您可以在整個生命週期中重複部署方案，並確信您的資源會以一致的狀態部署。

- 您可以將存取控制套用至資源群組中的所有資源，而新資源加入至資源群組時，會自動套用這些原則。

- 您可以將標籤套用至資源，以便以邏輯方式組織訂用帳戶中的所有資源。

- 您可以使用 JavaScript 物件標記法 (JSON) 來定義您的解決方案的基礎結構。 JSON 檔案也稱為 Resource Manager 範本。

- 您可以定義之間的相依性，使得以正確的順序部署資源。

![資源管理](./media/governance-in-azure/security-governance-in-azure-fig4.png)

Resource Manager 可讓您將資源放入有意義的群組，以便管理、計費或達到自然親和性。 如先前所述，Azure 有兩個部署模型。 在舊版的[傳統模型](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)中，管理的基本單位是訂用帳戶。 訂用帳戶內的資源難以細分，以致建立大量的訂用帳戶。 使用 Resource Manager 模型，我們看到資源群組的引入。

資源群組是存放 Azure 方案相關資源的容器。 [資源群組](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)可以包含方案的所有資源，或只包含您要以群組方式管理的資源。 您可決定如何根據對組織最有利的方式，將資源配置到資源群組。

如需範本的建議，請參閱[建立 Azure Resource Manager 範本的最佳做法](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)。

Azure Resource Manager 會分析相依性，確保以正確的順序建立資源。 如果某個資源依賴另一個資源的值 (例如需要儲存體帳戶以供磁碟使用的虛擬機器)，您必須設定相依性。

>[!Note]
>如需詳細資訊，請參閱 [定義 Azure Resource Manager 範本中的相依性](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies)。

您也可以使用範本進行基礎結構的更新。 例如，您可以將資源新增至您的方案，並將組態規則新增至已部署的資源。 如果此範本指定建立資源，但該資源已經存在，Azure Resource Manager 會執行更新，而不必建立新資產。 Azure Resource Manager 會將現有資產更新為和新資產相同的狀態。

當您需要進行其他作業 (例如安裝不包含在安裝程式中的軟體) 時，Resource Manager 能針對這些案例提供擴充功能。

## <a name="resource-tracking"></a>資源追蹤

隨著組織中的使用者將資源新增至訂用帳戶，讓資源與適當的部門、客戶和環境建立關聯變得越來越重要。 您可以透過標記將中繼資料附加到資源。 您可以使用[標記](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)來提供資源或擁有者的相關資訊。 標記不僅可讓您以數種方式對資源進行彙總及群組，並可將該資料用於退款目的。

當您有複雜的資源群組和資源集合，而且必須以對您最有意義的方式視覺化資產時，請使用標籤。 例如，您可以標記在組織中具有類似角色，或屬於相同部門的資源。

如果不使用標籤，貴組織中的使用者可建立多個資源，如此對於日後的身分識別及管理來說可能很困難。 例如，您可能想要刪除某個專案的所有資源。 如果未對此專案標記那些資源，您必須手動尋找它們。 標記是降低訂用帳戶不必要成本的重要方法。

資源不需要位於相同的資源群組即可共用標記。 您可以建立自己的標記分類，以確保組織中的所有使用者都使用常見的標記，而不是使用者意外套用稍有不同的標記 (例如 "dept" 而不是 "department")。

資源原則可讓您建立組織適用的標準規則。 您可以建立原則，以確保會為資源標記適當的值。

> [!Note]
> 如需詳細資訊，請參閱[套用標籤的資源原則](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags)。

您也可以透過 Azure 入口網站檢視已加上標籤的資源。

訂用帳戶的[使用報告](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)包含標籤名稱和值，可讓您依標籤細分成本。

> [!Note]
> 如需標記的詳細資訊，請參閱 [使用標記來組織您的 Azure 資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)。

標籤具有下列限制：

- 每個資源或資源群組最多可以有 15 個標記索引鍵/值組。 此限制只適用於直接套用至資源群組或資源的標記。 資源群組可以包含許多資源，其各自有 15 個標記索引鍵/值組。

- 標籤名稱的上限為 512 個字元。

- 標籤值的上限為 256 個字元。

- 資源群組中的資源不會繼承套用至該資源群組的標籤。

如果您有超過 15 個要與資源產生關聯的值，請使用 JSON 字串作為標記值。 JSON 字串可以包含許多套用至單一標記索引鍵的值。

### <a name="tags-and-billing"></a>標記與計費

標籤可讓您將計費資料分組。 例如，如果您執行不同組織的多個 VM，您可以使用標籤根據成本中心將使用情況分組。 您也可以使用標記來根據執行階段環境分類成本。例如，在生產環境中執行之 VM 的計費使用情況。

您可以透過 [Azure 資源使用狀況和 RateCard API](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) 或使用狀況逗號分隔值 (CSV) 檔案，擷取關於標記的資訊。 您可以從 [Azure 帳戶入口網站](https://account.windowsazure.com/)或 [EA 入口網站](https://ea.azure.com/)下載使用狀況檔案。

>[!Note]
> 如需以程式設計方式存取帳單資訊的詳細資訊，請參閱 [深入了解 Microsoft Azure 資源耗用量](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview)。 若為 REST API 作業，請參閱 [Azure 計費 REST API 參考](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)。

當您針對支援具計費之標記的服務下載使用情況 CSV 時，標記會出現在 [標記] 資料行。

## <a name="critical-resource-controls"></a>重要資源控制

隨著您的組織將核心服務新增至訂用帳戶，確保這些服務的可用性以避免業務中斷變得越來越重要。 [資源鎖定](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)可讓您限制對珍貴資源執行的作業，而修改或刪除這類資源會嚴重影響您的應用程式或雲端基礎結構。 您可以將鎖定套用至訂用帳戶、資源群組或資源。 一般而言，您會將鎖定套用至基礎資源，例如虛擬網路、閘道和儲存體帳戶。

資源鎖定目前支援兩個值︰CanNotDelete 和 ReadOnly。 CanNotDelete 表示使用者 (具有適當權限) 仍可讀取或修改資源，但無法加以刪除。 ReadOnly 表示經過授權的使用者無法刪除或修改資源。

Resource Manager 鎖定只會套用於管理平面發生的作業，亦即要傳送至 <https://management.azure.com> 的作業。鎖定並不會限制資源執行自己函式的方式。 限制資源的變更，但沒有限制資源的作業。 例如，SQL 資料庫的 ReadOnly 鎖定會防止您刪除或修改資料庫，但它不會阻止您建立、更新或刪除資料庫中的資料。

套用 **ReadOnly** 會導致無法預期的結果，因為有些看似讀取作業的作業會需要進行其他動作。 例如，將 **ReadOnly** 鎖定放置在儲存體帳戶上，會防止所有使用者列出金鑰。 清單金鑰作業是透過 POST 要求進行處理，因為傳回的金鑰可用於寫入作業。

![重要資源控制](./media/governance-in-azure/security-governance-in-azure-fig5.png)

舉另一個例子來說，將 ReadOnly 鎖定放置在 App Service 資源上，會防止 Visual Studio 伺服器總管顯示該資源的檔案，因為該互動需要寫入存取權。

不同於角色型存取控制，您可以使用管理鎖定來對所有使用者和角色套用限制。 如要了解使用者和角色的設定權限，請參閱 [Azure 角色型存取控制](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)。

當您在父範圍套用鎖定時，該範圍內的所有資源都會都繼承相同的鎖定。 甚至您稍後新增的資源都會繼承父項的鎖定。 繼承中限制最嚴格的鎖定優先順序最高。

若要建立或刪除管理鎖定，您必須擁有 Microsoft.Authorization/ _或 Microsoft.Authorization/locks/_ 動作的存取權。 在內建角色中，只有 **擁有者** 和 **使用者存取管理員** 被授與這些動作的存取權。

## <a name="api-access-to-billing-information"></a>針對帳單資訊的 API 存取

使用 Azure 計費 API 將使用情況和資源資料提取到您慣用的資料分析工具。 Azure 資源使用情況和 RateCard API 可協助您準確地預測並管理成本。 這些 API 會實作為資源提供者，並成為 Azure Resource Manager 所公開之 API 系列的一部分。

### <a name="azure-resource-usage-api-preview"></a>Azure 資源使用情況 API (預覽)

使用 Azure [資源使用情況 API](https://msdn.microsoft.com/library/azure/mt219003) 來取得預估的 Azure 耗用量資料。 此 API 包含︰

- **Azure 角色型存取控制** - 在 [Azure 入口網站](https://portal.azure.com/)上或透過 [Azure PowerShell Cmdlet](https://docs.microsoft.com/powershell/azure/overview) \(英文\) 設定存取原則，以指定哪些使用者或應用程式可以存取訂用帳戶的使用情況資料。 呼叫端必須使用標準的 Azure Active Directory 權杖進行驗證。 將呼叫端新增到「發票讀者」、「讀者」、「擁有者」或「參與者」角色，以存取特定 Azure 訂用帳戶的使用情況資料。

- **每小時或每日彙總** - 呼叫端可以指定要 Azure 使用情況資料的每小時值區或每日值區。 預設值為每日值區。

- **執行個體中繼資料 (包括資源標記)** – 取得執行個體層級詳細資料，例如完整的資源 uri (/subscriptions/{subscription-id} /..)、資源群組資訊與資源標記。 此中繼資料可協助您以決定性及程式設計方式，根據標記為跨領域收費之類的使用案例配置使用量。

- **資源中繼資料** - 資源詳細資料 (例如計量名稱、計量類別、計量子類別、單位和區域) 可讓呼叫端深入了解耗用的內容。 我們也致力於跨 Azure 入口網站、Azure 使用情況 CSV、EA 計費 CSV 和其他向外公開的體驗統一資源中繼資料術語，以讓您跨體驗將資料相互關聯。

- **所有優惠類型的使用情況** – 所有優惠類型 (例如隨收隨付、MSDN、貨幣承諾、貨幣信用額度和 EA) 皆有提供使用情況資料。

**Azure 資源 RateCard API (預覽)**

使用 Azure 資源 RateCard API 來取得可用 Azure 資源的清單，以及每個資源的預估價格資訊。 此 API 包含︰

- **Azure 角色型存取控制** - 在 Azure 入口網站上或透過 Azure PowerShell Cmdlet 設定存取原則，以指定哪些使用者或應用程式可以存取 RateCard 資料。 呼叫端必須使用標準的 Azure Active Directory 權杖進行驗證。 將呼叫端新增至讀取者、擁有者或參與者角色，以存取特定 Azure 訂用帳戶的使用情況資料。

- **支援隨收隨付、MSDN、貨幣承諾、貨幣信用額度優惠 (不支援 EA)** - 此 API 提供 Azure 優惠層級費率資訊。 此 API 的呼叫端必須輸入優惠資訊以取得資源詳細資料和費率。 由於 EA 優惠已自訂每個註冊的費率，所以我們目前無法提供 EA 費率。 以下是一些案例，可產生使用情況和 RateCard API 的組合：

- **Azure 月份花費** - 使用使用情況和 RateCard API 的組合，來取得當月份雲端花費的更佳見解。 您可以分析每小時和每天的使用情況及收費評估值區。

- **設定警示** – 使用使用情況和 RateCard API 來取得預估的雲端耗用量和收費，並設定以資源為基礎或以貨幣為基礎的警示。

- **預測計費** – 取得預估的耗用量及雲端花費，並應用機器學習演算法來預測在計費週期結束時的計費情形。

- **耗用前成本分析** – 使用 RateCard API 來預測當您將工作負載移至 Azure 時，所預期的使用量需要多少費用。 如果您有其他雲端或私用雲端中的現有工作負載，您也可以對應您的使用情況和 Azure 費率，以取得 Azure 花費的較佳評估。 此預估可讓您以優惠為依據來進行選擇，並比較隨收隨付以外的不同優惠類型，包含貨幣承諾和貨幣信用額度。 此 API 也可讓您依區域查看成本差異，並可讓您執行假設成本分析，以幫助您做出部署決定。

- **模擬分析** - 您可以判斷在另一個區域執行工作負載，或是在 Azure 資源的另一個設定上執行工作負載是否會比較符合成本效益。 Azure 資源成本可能會因為您所使用的 Azure 區域而有所不同。

- 您也可以判斷另一個 Azure 優惠類型是否會在 Azure 資源上提供更好的費率。

## <a name="networking-controls"></a>網路控制

資源的存取可以是內部的 (在公司網路內) 或外部的 (透過網際網路)。 您組織中的使用者很容易不小心將資源放在錯誤的位置，並可能加以開啟而遭到惡意存取。 針對內部部署/裝置，企業必須加上適度的控制，以確保 Azure 使用者能做出適當的決策。

![網路控制](./media/governance-in-azure/security-governance-in-azure-fig6.png)

針對訂用帳戶治理，我們會找出可提供基本存取控制的核心資源。 核心資源是由下列各項所組成︰

### <a name="network-connectivity"></a>網路連線

[虛擬網路](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)是子網路的容器物件。 雖然不是絕對必要，但通常使用於將應用程式連接到內部公司資源時。 Azure 虛擬網路服務可讓 Azure 資源與虛擬網路 (VNet) 安全地彼此連接。

VNet 是您的網路在雲端中的身分。 VNet 是專屬於您訂用帳戶的 Azure 雲端邏輯隔離。 您也可以將 VNet 連線到內部部署網路。

以下為 Azure 虛擬網路的功能：

- **隔離**︰VNet 會彼此隔離。 您可以為使用相同 CIDR 位址區塊的開發、測試和生產環境建立個別的 VNet。 相反地，您可以建立多個使用不同 CIDR 位址區塊的 VNet 並將這些網路連接在一起。 您可以將 VNet 分成多個子網路。 Azure 會針對連線至 VNet 的 VM 和雲端服務角色執行個體提供內部名稱解析。 您可以選擇地將 VNet 設定成使用自己的 DNS 伺服器，而不是使用 Azure 內部名稱解析。

- **網際網路連線能力**︰根據預設，連線至 VNet 的所有 Azure 虛擬機器 (VM) 和雲端服務角色執行個體都可以存取網際網路。 您也可以視需要啟用特定資源的輸入存取。

- **Azure 資源連線能力**︰Azure 資源 (例如雲端服務和 VM) 可以連線至相同的 VNet。 如果資源位於不同的子網路，則可使用私人 IP 位址彼此連接。 Azure 會提供子網路、VNet 和內部部署網路之間的預設路由，因此您不必設定及管理路由。

- **VNet 連線能力**︰VNet 可以彼此連線，讓連線至任何 VNet 的資源能夠與任何其他 VNet 上的任何資源進行通訊。

- **內部部署連線能力**︰VNet 可以透過您的網路與 Azure 之間的私人網路連線，或透過經由網際網路的站對站 VPN 連線，連線到內部部署網路。

- **流量篩選**︰可以依照來源 IP 位址和連接埠、目的地 IP 位址和連接埠以及通訊協定，對 VM 和雲端服務角色執行個體網路流量進行輸入及輸出的篩選。

- **路由**︰您可以透過設定自己的路由，或使用透過網路閘道的 BGP 路由，選擇性地覆寫 Azure 的預設路由。

## <a name="network-access-controls"></a>網路存取控制

[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)類似於防火牆，可提供資源透過網路進行「交談」的規則。 這類群組可細微控制子網路 (或虛擬機器) 如何/是否可以連接到網際網路或相同虛擬網路中的其他子網路。

網路安全性群組 (NSG) 包含安全性規則的清單，可允許或拒絕已連線至 Azure 虛擬網路 (VNet) 之資源的網路流量。 NSG 可以與子網路、個別 VM (傳統) 或已連結至 VM (Resource Manager) 的個別網路介面 (NIC) 建立關聯。

當 NSG 與子網路相關聯時，系統會將規則套用至已連線至子網路的所有資源。 建立 NSG 與 VM 或 NIC 的關聯也可以進一步限制流量。

## <a name="security-and-continuous-compliance-with-organizational-standards"></a>符合組織標準的安全性及持續符合合規性

每個企業都有不同的需求，而每個企業都能透過雲端解決方案取得不同的好處。 不過，無論客戶的背景為何，他們都會對移至雲端抱持著一些基本的疑慮。 除了維持資料的透明度及合規性之外，他們也會想要掌握對資料的控制，並保持資料的安全性及私密性。

客戶想要從雲端提供者取得的是：

- **資料安全性**：雖然 IT 領導者同意雲端可以提供額外的資料安全性及管理上的控制，他們仍然擔心移轉至雲端可能會比其目前的內部解決方案更容易受到駭客攻擊。

- **資料私密性**：對企業來說，私人雲端服務也帶來了獨特的隱私問題。 在眾多公司考慮採納雲端以節省基礎結構成本並提升其彈性的同時，他們也擔心失去對資料的儲存位置、存取人員及使用方式的控制。

- **資料可控性**：雖然有許多公司成功利用雲端來部署更加創新的解決方案，他們也非常擔心會失去對資料的控制。 於近日浮上檯面，有關政府機關透過合法及不受法律支配的方式存取客戶資料的消息，使得許多 CIO 對於將資料儲存在雲端的方式抱持疑慮。

- **提升透明度**：雖然安全性、私密性及可控性對於業務決策者而言非常重要，他們也會想要能獨立確認資料的儲存、存取及保護方式。

- **維持合規性**：隨著公司持續擴展對雲端技術的運用，各種標準和法規的複雜度及範圍也會持續進化。 公司需要知道所有作業及程序皆會符合其合規性標準，且合規性也會隨著法規的變化而持續進化。

## <a name="security-configuration-monitoring-and-alerting"></a>安全性設定，監視和警示

Azure 訂閱者可從多種裝置管理其雲端環境，這些裝置包括管理工作站、開發人員的電腦，甚至是具有工作專用權限的特殊權限使用者裝置。 在某些情況下，管理功能是透過 Web 式主控台 (例如 Azure 入口網站) 來執行。 至於其他時候，則可能會從內部部署系統，透過虛擬私人網路 (VPN)、終端機服務、用戶端應用程式通訊協定或 Azure 服務管理 API (SMAPI) (以程式設計方式) 直接連線至 Azure。 此外，用戶端端點也可以加入網域或是遭到隔離且不受管理，例如平板電腦或智慧型手機。

雖然多項存取和管理功能可提供一組豐富的選項，但選項太多也可能會讓雲端部署承受巨大風險。 因而難以管理、追蹤和稽核管理動作。 選項太多也可能會因為用來管理雲端服務之用戶端端點所進行的存取不受管制而招致安全性威脅。 使用一般工作站或私人工作站來開發和管理基礎結構將會打開無法預期的威脅媒介，例如網頁瀏覽 (例如水坑攻擊) 或電子郵件 (例如社交工程和網路釣魚)。

監視、記錄和稽核可為追蹤和了解系統管理活動提供基礎，但受限於所產生的資料量，它不一定都能鉅細靡遺地稽核所有動作。 不過，稽核管理原則的效果是最佳作法。

使用來自 AD DS GPO 的 Azure 安全性治理來控制所有系統管理員的 Windows 介面，例如檔案共用。 將管理工作站納入稽核、監視和記錄程序內。 追蹤所有系統管理員和開發人員的存取和使用活動。

### <a name="azure-security-center"></a>Azure 資訊安全中心

[Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)可供集中檢視訂用帳戶中資源的安全性狀態，並提供有助於預防資源遭到入侵的相關建議。 它可以啟用更細微的原則 (例如，將原則套用至特定的資源群組，讓企業能針對他們所面臨的風險來調整其立場態度)。

![Azure 資訊安全中心](./media/governance-in-azure/security-governance-in-azure-fig7.png)

資訊安全中心能提供您 Azure 訂用帳戶之間的整合式安全性監視和原則管理，協助偵測原先可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。 在您為訂用帳戶的資源啟用[安全性原則](https://docs.microsoft.com/azure/security-center/security-center-policies)之後，資訊安全中心會分析資源的安全性狀態，以找出潛在的弱點。 您可以立即取得網路組態的相關資訊。

Azure 資訊安全中心是針對 Azure 訂用帳戶內所有資源的最佳做法分析及安全性原則管理組合。 這項既強大又易於使用的工具，可以自動收集並分析來自您 Azure 資源、網路及合作夥伴解決方案 (例如反惡意程式碼程式及防火牆) 的安全性資料，使安全性小組及風險人員能夠防止、偵測及回應安全性威脅。

除此之外，Azure 資訊安全中心能套用進階分析 (包括機器學習和行為分析)，同時利用來自 Microsoft 產品和服務、Microsoft 數位犯罪防治中心 (DCU)、Microsoft Security Response Center (MSRC) 以及外部摘要的全域威脅情報。 [安全性治理](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/) \(英文\) 可以廣泛套用至訂用帳戶層級，或是以特定細微需求的形式，透過原則定義套用至個別的資源。

最後，Azure 資訊安全中心會根據那些原則分析資源安全性健康情況，並使用此資訊來提供含深入資料的儀表板，以及針對惡意程式碼偵測或惡意 IP 連線嘗試等事件的警示功能。

>[!Note]
> 如需如何套用建議的詳細資訊，請參閱[實作 Azure 資訊安全中心的安全性建議](https://docs.microsoft.com/azure/security-center/security-center-recommendations)。

資訊安全中心會收集虛擬機器的資料，以便評估其安全性狀態、提供安全性建議，並對您發出威脅警示。 當您第一次存取資訊安全中心時，訂用帳戶中的所有虛擬機器都會啟用資料收集。 建議啟用資料收集，但您可以在資訊安全中心原則中 [停用資料收集](https://docs.microsoft.com/azure/security-center/security-center-faq) 來選擇退出。

最後，Azure 資訊安全中心是一個開放平台，可讓 Microsoft 合作夥伴和獨立軟體廠商建立可外掛到 Azure 資訊安全中心的軟體，以增強其功能。

Azure 資訊安全中心會監視下列 Azure 資源：

- 虛擬機器 (VM) (包括雲端服務)

- Azure 虛擬網路

- Azure SQL 服務

- 與您的 Azure 訂用帳戶整合的合作夥伴解決方案，例如位於 VM 和 [App Service 環境](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme)上的 Web 應用程式防火牆。

### <a name="operations-management-suite"></a>Operations Management Suite

OMS 軟體開發和服務小組的資訊安全性及[治理程式](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md) \(英文\) 可支援其商務需求，並且會遵守 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/)和 [Microsoft 信任中心合規性](https://www.microsoft.com/TrustCenter/Compliance/default.aspx) \(英文\) 所述的法律與法規。 上述位置也會描述 OMS 是如何建立安全性需求、識別安全性控制，以及管理和監視風險。 每年我們都會檢閱原則、標準、程序和指導方針。

每個 OMS 開發小組成員都會獲得正式的應用程式安全性訓練。 在內部，我們使用版本控制系統來開發軟體。 每個軟體專案都受到版本控制系統的保護。

Microsoft 備有會監看及評估 Microsoft 中所有服務的安全性和法規遵循小組。 資訊安全人員會組成小組，而且他們與開發 OMS 的工程部門並無關聯。 安全人員有他們自己的管理鏈，並且會獨立評估產品和服務，以確保安全性與法規遵循。

Operations Management Suite (也稱為 OMS) 是在雲端中從頭設計的管理服務集合。 與其部署及管理內部部署資源，OMS 元件會完全裝載於 Azure 中。 需要進行的設定很少，您可以在幾分鐘內完成啟動並執行。

![Operations Manager Suite](./media/governance-in-azure/security-governance-in-azure-fig8.png)

雖然 OMS 服務是在雲端中執行，這並不表示其無法有效地管理您的內部部署環境。

將代理程式置於資料中心的任何 Windows 或 Linux 電腦上，它會將資料傳送到 Log Analytics，以便與從雲端或內部部署服務收集而來的其他所有資料一起進行分析。 使用 Azure 備份和 Azure Site Recovery，以運用雲端針對內部部署資源取得備份及高可用性。

雲端中的 Runbook 通常無法存取內部部署資源，但您可以在一或多部電腦上安裝代理程式，以在您的資料中心裝載 Runbook。 當您啟動 Runbook 時，只要指定您希望它在雲端或在本機背景工作上執行。

OMS 的核心功能是由在 Azure 中執行的一組服務所提供。 每個服務會提供特定的管理功能，您可以合併服務以達成不同的管理案例。

![Operations Manager Suite](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

Azure Operation Manager 會透過提供管理解決方案來延伸其功能性。 [管理解決方案](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions)是預先封裝的邏輯集合，會實作能運用一或多項 OMS 服務的管理案例。

![Azure 作業管理](./media/governance-in-azure/security-governance-in-azure-fig10.png)

Microsoft 和合作夥伴會提供不同的解決方案，您可以輕鬆地將其新增至您的 Azure 訂用帳戶，以提升您的 OMS 投資價值。

身為合作夥伴的您可以建立自己的解決方案，來支援您的應用程式和服務，並透過 Azure Marketplace 或快速入門範本將其提供給使用者。

## <a name="performance-alerting-and-monitoring"></a>效能警示和監視

### <a name="alerting"></a>警示

警示是監視 Azure 資源度量、事件或記錄，並在您所指定條件符合時的通知方法。

**不同 Azure 服務中的警示**

警示可跨不同的服務使用，包括：

- Application Insights：允許 Web 測試及計量警示。

>[!Note]
> 請參閱 [ Application Insights 中設定警示](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)和[監視任何網站的可用性和回應性](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)。

- Log Analytics (Operations Management Suite)：允許將活動和診斷記錄路由至 Log Analytics。 Operations Management Suite 可允許度量、記錄和其他警示類型。

>[!Note]
> 如需詳細資訊，請參閱 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts) 中的＜警示＞。

- Azure 監視器：允許根據計量值和活動記錄檔事件的警示。 您可以使用 [Azure 監視器 REST API](https://msdn.microsoft.com/library/dn931943.aspx) 來管理警示。

>[!Note]
> 如需詳細資訊，請參閱 [使用 Azure 入口網站、PowerShell 或命令列介面來建立警示](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal)。

### <a name="monitoring"></a>監視

您雲端應用程式中的效能問題可能會對企業產生影響。 透過多個互連的元件和頻繁的發行，隨時都可能導致效能降低。 此外，如果您正在開發應用程式，您的使用者通常會找出測試時未發現的問題。 您應該立即知道這些問題，並具備可用以診斷和修復問題的工具。 Microsoft Azure 提供多種工具來識別這些問題。

**如何監視我的 Azure 雲端應用程式？**

有多種工具可用來監視 Azure 應用程式和服務。 其中有些功能會重疊。 這其中有部分是基於歷程記錄因素，而有部分是因為應用程式開發與作業之間的界限模糊不清而導致的。

以下是主要工具：

- **Azure 監視器**是用以監視 Azure 上執行之服務的基本工具。 它會提供關於服務輸送量及周遭環境之基礎結構等級的資料。 如果您正在 Azure 中管理所有的應用程式，決定是否要相應增加或減少資源，Azure 監視器接著就能提供可讓您用來做為起點的項目。

- **Application Insights** 可用來開發，並作為監視生產環境的解決方案。 它的運作方式是將套件安裝到您的應用程式，因此可讓您更深入檢視內部情形。 它的資料包括相依性的回應時間、例外狀況追蹤、偵錯快照集、執行設定檔。 它提供功能強大的智慧型工具來完整分析此遙測，以協助您偵錯應用程式，並協助您了解使用者正使用它來做什麼。 您可以判斷回應時間突然增加是否因為應用程式中的某些項目所引起，還是由一些外部資源問題所引起的。 如果您使用 Visual Studio 且應用程式發生問題，則可直接將您帶至發生問題的程式碼行，讓您能夠修正問題。

- **Log Analytics** 適用於需要微調效能並計劃維護在生產環境中執行之應用程式的使用者。 它是 Azure 中的基本工具。 它會從許多來源收集並彙總資料，但會有 10 到 15 分鐘的延遲。 它會針對 Azure、內部部署及協力廠商的雲端式基礎結構 (例如 Amazon Web 服務) 提供整體 IT 管理解決方案。 它提供更豐富的工具，可跨多個來源分析資料、允許跨所有記錄進行複雜的查詢，而且可以指定的條件主動提供警示。 您甚至可以將自訂資料收集到其中央存放庫，如此便能查詢並將其視覺化。

- **System Center Operations Manager (SCOM)** 可用來管理及監視大型雲端安裝。 您可能已經熟悉它做為適用於內部部署 Windows Sever 和 Hyper-V 型雲端的管理工具，但它也可以與 Azure 應用程式整合並加以管理。 在其他方面，它可以在現有的即時應用程式上安裝 Application Insights。 如果應用程式當機，它會在短時間內通知您。


## <a name="next-steps"></a>後續步驟

- [建立 Azure Resource Manager 範本的最佳做法](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)。

- [實作 Azure 訂用帳戶治理的範例](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-subscription-examples)。

- [Microsoft Azure Government](https://docs.microsoft.com/azure/azure-government/) \(英文\)。
