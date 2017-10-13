---
title: "資訊安全中心平台移轉常見問題集 | Microsoft Docs"
description: "本常見問題集回答 Azure 資訊安全中心平台移轉的相關問題。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: 4b88b5015fcf44e8979b8b1a3aa1eb26f0fbb704
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="security-center-platform-migration-faq"></a>資訊安全中心平台移轉常見問題集
在 2017 年 6 月初，Azure 資訊安全中心開始使用 Microsoft Monitoring Agent 來收集與儲存資料。 如需詳細資訊，請參閱 [Azure 資訊安全中心平台移轉](security-center-platform-migration.md)。 本常見問題集回答平台移轉的相關問題。

## <a name="data-collection-agents-and-workspaces"></a>資料收集、代理程式及工作區

### <a name="how-is-data-collected"></a>資料如何收集？
資訊安全中心使用 Microsoft Monitoring Agent 從您的虛擬機器收集安全性資料。 這些安全性資料包括用來識別弱點之安全性設定，以及用來偵測威脅之安全性事件的相關資訊。 代理程式所收集的資料會儲存在連線到虛擬機器的既存 Log Analytics 工作區，或是在資訊安全中心建立的新工作區。 資訊安全中心建立新工作區時，會將虛擬機器的地理位置納入考量。

> [!NOTE]
> Microsoft Monitoring Agent 是 Operations Management Suite (OMS)、Log Analytics 服務及 System Center Operations Manager (SCOM) 所使用的代理程式。
>
>

初次啟用資料收集或訂用帳戶移轉後，資訊安全中心會確認 Microsoft Monitoring Agent 是否已經以 Azure 擴充功能的形式安裝在您的虛擬機器上。 如果未安裝 Microsoft Monitoring Agent，資訊安全中心將：

- 在虛擬機器上安裝 Microsoft Monitoring Agent
   - 若資訊安全中心已經建立了與虛擬機器存在於相同地理位置的工作區，代理程式即會連線到此工作區
   - 如果沒有這樣的工作區，資訊安全中心會在此地理位置建立新的資源群組和預設工作區，並將代理程式連線到該工作區。 工作區和資源群組的命名慣例如下：

       工作區：DefaultWorkspace-[subscription-ID]-[geo]

       資源群組：DefaultResouceGroup-[geo]
- 在工作區上安裝資訊安全中心解決方案

工作區的位置取決於虛擬機器的位置。 若要深入了解，請參閱[資料安全性](security-center-data-security.md)。

> [!NOTE]
> 平台移轉之前，資訊安全中心已使用 Azure Monitoring Agent 從您的虛擬機器收集安全性資料，並將資料儲存到您的儲存體帳戶。 平台移轉之後，資訊安全中心會使用 Microsoft Monitoring Agent 及工作區來與儲存該資料。 移轉之後就可以移除儲存體帳戶。
>
>

### <a name="am-i-billed-for-log-analytics-or-oms-on-the-workspaces-created-by-security-center"></a>資訊安全中心建立之工作區的 Log Analytics 或 OMS 是否需要付費？
否。 資訊安全中心所建立的工作區雖然設定以每節點之 OMS 計費，但實際上不會產生 OMS 費用。 資訊安全中心的計費一律根據您的資訊安全中心的安全性原則，以及工作區安裝的解決方案：

- **免費層** – 資訊安全中心在預設工作區安裝 SecurityCenterFree 解決方案。 免費層不須付費。
- **標準層** – 資訊安全中心在預設工作區安裝 SecurityCenterFree 及 Security 解決方案。

如需詳細資訊，請參閱[資訊安全中心價格](https://azure.microsoft.com/pricing/details/security-center/)。 價格頁面反映了自 2017 年起 6 月起安全性資料儲存體及依比例計費的改變。

> [!NOTE]
> 資訊安全中心所建立之工作區的 OMS 定價層不會影響資訊安全中心的收費。
>
>

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>我可以刪除資訊安全中心所建立的預設工作區嗎？
**建議您不要刪除預設工作區。** 資訊安全中心會使用預設工作區儲存您虛擬機器傳來的安全性資料。  如果您刪除了工作區，資訊安全中心無法收集此資料，特定安全性建議及警示就無法使用

若要復原，請將連線到刪除工作區之虛擬機器上的 Microsoft Monitoring Agent 移除。 資訊安全中心會重新安裝代理程式，並建立新的預設工作區。

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>如何使用我現有的 Log Analytics 工作區？

您可以選取現有的 Log Analytics 工作區來儲存資訊安全中心收集的資料。 若要使用現有的 Log Analytics 工作區：

- 工作區必須與您選取的 Azure 訂用帳戶相關聯。
- 至少必須擁有讀取權限以存取工作區。

若要選取現有的 Log Analytics 工作區：

1. 在 [安全性原則 - 資料收集] 下，選取 [使用其他工作區]。

   ![使用其他工作區][5]

2. 從下拉式功能表中，選取要儲存收集資料的工作區。

   > [!NOTE]
   > 在下拉式功能表中，只會顯示您可存取，而且在您 Azure 訂用帳戶中的工作區。
   >
   >

3. 選取 [ **儲存**]。
4. 選取 [儲存] 之後，會詢問您是否要重新設定受監控的虛擬機器。

   - 如果您希望新的工作區設定**僅套用在新的虛擬機器**，請選取 [否]。 新的工作區設定只會套用在新安裝的代理程式，以及新探索到的未安裝 Microsoft Monitoring Agent 之虛擬機器。
   - 如果您希望新的工作區設定**套用在所有虛擬機器**，請選取 [是]。 此外，每個連線到資訊安全中心建立之工作區的虛擬機器會重新連線到新的目標工作區。

   > [!NOTE]
   > 如果您選取 [是]，您必須刪除資訊安全中心建立的工作區，直到所有虛擬機器已重新連線至新的目標工作區。 如果過早刪除工作區，這項作業將會失敗。
   >
   >

   - 選取 [取消] 以取消作業。

      ![重新設定監控的虛擬機器][6]

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>如果虛擬機器上 Microsoft Monitoring Agent 已經以擴充功能的形式安裝了呢？
資訊安全中心不會覆寫既存的使用者工作區連線。 資訊安全中心會將虛擬機器送出的資料儲存到已經連線的工作區。

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>如果我已經在機器上安裝了 Microsoft Monitoring Agent，但不是以擴充功能的形式呢？
如果 Microsoft Monitoring Agent 已經直接安裝在虛擬機器上 (而非作為 Azure 擴充功能)，資訊安全中心不會再安裝 Microsoft Monitoring Agent，且資訊安全監控的功能會受到限制。

### <a name="what-is-the-impact-of-removing-these-extensions"></a>移除這些擴充功能會有什麼影響？
如果您移除了 Microsoft Monitoring 擴充功能，資訊安全中心會無法收集虛擬機器送出的安全性資料，且特定安全性建議及警示將無法使用。 24 小時內，資訊安全中心會判定虛擬機器缺少了擴充功能，並重新安裝。

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>我要如何避免自動安裝代理程式和建立工作區？
您可以在安全性原則中關閉訂用帳戶的自動佈建，但不建議您這麼做。 關閉自動佈建會限制資訊安全中心的建議和警示。 標準層上的訂用帳戶需要進行自動佈建。 若要停用自動佈建：

1. 如果您的訂用帳戶設定為標準層，請開啟該訂用帳戶的安全性原則，並選取**免費**層。

   ![定價層 ][1]

2. 接下來，請選取 [安全性原則 – 資料收集] 刀鋒視窗上的 [關閉]，來停用自動佈建。
   ![資料收集][2]

### <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>我要如何移除資訊安全中心安裝的 OMS 擴充功能？
您可以手動移除 Microsoft Monitoring Agent。 關閉資料收集會限制資訊安全中心的建議和警示，所以不建議您這麼做。

> [!NOTE]
> 如果已啟用資料收集，資訊安全中心將會在您移除代理程式後重新安裝程式。  您必須停用資料收集，再手動移除代理程式。 若要了解如何停用資料收集，請參閱[我要如何避免自動安裝代理程式和建立工作區？](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation?)
>
>

若要手動移除代理程式：

1.  在入口網站中，開啟**Log Analytics**。
2.  在 [Log Analytics] 刀鋒視窗中，選取工作區：
3.  選取您不想監控的虛擬機器，並選取 [中斷連線]。

   ![移除代理程式][3]

> [!NOTE]
> 如果 Linux VM 已經有非擴充功能的 OMS 代理程式，移除功能的同時也會移除代理程式，客戶必須要重新安裝。
>
>

## <a name="existing-oms-customers"></a>現有的 OMS 客戶

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>資訊安全中心是否會覆寫任何虛擬機器及工作區之間現有的連線？
如果虛擬機器已經以擴充功能的形式安裝 Microsoft Monitoring Agent，資訊安全中心不會覆寫既存的工作區連線。 相反地，資訊安全中心會使用現有的工作區。

資訊安全中心解決方案若尚未安裝，此時會安裝至工作區，且此解決方案只會套用至相關的虛擬機器。 當您新增解決方案時，依預設會該解決方案會自動部署到與您 Log Analytics 工作區連線的所有 Windows 與 Linux 代理程式。 [解決方案目標設定](../operations-management-suite/operations-management-suite-solution-targeting.md)是 OMS 的功能，讓您能將範圍套用至解決方案。

如果 Microsoft Monitoring Agent 已經直接安裝在虛擬機器上 (而非作為 Azure 擴充功能)，資訊安全中心不會再安裝 Microsoft Monitoring Agent，且資訊安全監控的功能會受到限制。

### <a name="what-should-i-do-if-i-suspect-that-the-data-platform-migration-broke-the-connection-between-one-of-my-vms-and-my-workspace"></a>如果我懷疑資料平台移轉中斷了我的工作區及虛擬機器之一的連線，我該怎麼做？
這種情況不應發生。 如果連線真的中斷了，請[建立一個 Azure 支援要求](../azure-supportability/how-to-create-azure-support-request.md)，並隨附下列詳細資料：

- 受影響虛擬機器的 Azure 資源識別碼
- 工作區連線中斷前，擴充功能所設定之工作區的 Azure 資源識別碼
- 代理程式和先前已安裝的版本

### <a name="does-security-center-install-solutions-on-my-existing-oms-workspaces-what-are-the-billing-implications"></a>資訊安全中心是否會在我現有的 OMS 工作區上安裝解決方案？ 這會對計費造成什麼影響？
當資訊安全中心發現已經有虛擬機器連線到您建立的工作區時，資訊安全中心會根據您的定價層啟用此工作區上的解決方案。 這些解決方案會透過[方案目標設定](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting)，只套用至相關的 Azure 虛擬機器，所以收費不會改變。

- **免費層** – 資訊安全中心在工作區安裝 SecurityCenterFree 解決方案。 免費層不須付費。
- **標準層** – 資訊安全中心在工作區安裝 SecurityCenterFree 及 Security 解決方案。

   ![預設工作區的解決方案][4]

> [!NOTE]
> Log Analytics 中的 Security 解決方案即為 OMS 中的 Security & Audit 解決方案。
>
>

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>我的環境中已經有工作區，我是否能使用這些工作區來收集安全性資料？
如果虛擬機器已經以擴充套件的形式安裝 Microsoft Monitoring Agent，資訊安全中心會使用現有已連線的工作區。 資訊安全中心解決方案若尚未安裝，此時會安裝至工作區，且透過[解決方案目標設定](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting)，此解決方案只會套用至相關的虛擬機器。

資訊安全中心在虛擬機器上安裝 Microsoft Monitoring Agent 時，會使用資訊安全中心建立的預設工作區。 不久之後客戶將能自行設定要使用那些工作區。

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>我的工作區上已經有安全性解決方案。 這會對計費造成什麼影響？
Security & Audit 解決方案會用來啟用 Azure 虛擬機器上資訊安全中心標準層的功能。 如果工作區已經安裝有 Security & Audit 解決方案，資訊安全中心會使用現有的解決方案。 收費不會改變。

## <a name="next-steps"></a>後續步驟
若要深入了解資訊安全中心平台移轉，請參閱

- [Azure 資訊安全中心平台移轉](security-center-platform-migration.md)
- [Azure 資訊安全中心疑難排解指南](security-center-troubleshooting-guide.md)

<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
[6]: ./media/security-center-platform-migration-faq/reconfigure-monitored-vm.png
