---
title: "Azure 資訊安全中心的資料收集 | Microsoft Docs"
description: " 了解如何在 Azure 資訊安全中心啟用資料收集。 "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 226fc82abf7aa24a0aa1bd3c21279158e1ce8e95
ms.contentlocale: zh-tw
ms.lasthandoff: 09/13/2017

---
# <a name="data-collection-in-azure-security-center"></a>Azure 資訊安全中心的資料收集
資訊安全中心會從您的 Azure 虛擬機器 (VM) 和非 Azure 電腦收集資料，以監視安全性漏洞與威脅。 資料是使用 Microsoft Monitoring Agent 收集而得，收集的方式是讀取機器的各種安全性相關組態和事件記錄檔，並將資料複製到工作區進行分析。 這類資料的範例包括︰作業系統類型和版本、作業系統記錄檔 (Windows 事件記錄檔)、執行中程序、電腦名稱、IP 位址、已登入的使用者和租用戶識別碼。 Microsoft Monitoring Agent 也會將損毀傾印檔案複製到工作區。

## <a name="enable-automatic-provisioning-of-microsoft-monitoring-agent"></a>啟用 Microsoft Monitoring Agent 的自動佈建     
啟用自動佈建時，資訊安全中心會在所有支援的 Azure VM 和任何新建立的 VM 上佈建 Microsoft Monitoring Agent。 強烈建議使用自動佈建，而且資訊安全中心標準層的訂閱需要啟用自動佈建。

> [!NOTE]
> 停用自動佈建會限制對資源的安全性監視。 若要進一步了解，請參閱本文的[停用自動佈建](security-center-enable-data-collection.md#disable-automatic-provisioning)。 即使自動佈建已停用，仍然會啟用 VM 磁碟快照集和成品集合。
>
>

若要啟用 Microsoft Monitoring Agent 的自動佈建：
1. 在 [資訊安全中心] 主功能表下，選取 [安全性原則]。
2. 選取訂用帳戶。
3. 在 [安全性原則] 下，選取 [資料收集]。
4. 在 [上架] 下，選取 [開啟] 啟用自動佈建。
5. 選取 [ **儲存**]。

![啟用自動佈建][1]

## <a name="default-workspace-configuration"></a>預設工作區組態
資訊安全中心收集的資料會儲存在 Log Analytics 工作區。  您可以選擇將從 Azure VM 收集而得的資料儲存於資訊安全中心建立的工作區中，或儲存於您建立的現有工作區中。

若要使用現有的 Log Analytics 工作區：
- 工作區必須與您選取的 Azure 訂用帳戶相關聯。
- 至少必須擁有讀取權限以存取工作區。

若要選取現有的 Log Analytics 工作區：

1. 在 [安全性原則 - 資料收集] 下，選取 [使用其他工作區]。

   ![選取現有工作區][2]

2. 從下拉式功能表中，選取要儲存收集資料的工作區。

> [!NOTE]
> 在下拉式功能表中，只會顯示您可存取，而且在您 Azure 訂用帳戶中的工作區。
>
>

3. 選取 [ **儲存**]。
4. 選取 [儲存] 之後，會詢問您是否要重新設定受監控的虛擬機器。

   - 如果您希望新的工作區設定僅套用在新的虛擬機器上，請選取 [否]。 新的工作區設定只會套用在新安裝的代理程式，以及新探索到的未安裝 Microsoft Monitoring Agent 之虛擬機器。
   - 如果您希望新的工作區設定套用在所有虛擬機器，請選取 [是]。 此外，每個連線到資訊安全中心建立之工作區的虛擬機器會重新連線到新的目標工作區。

   > [!NOTE]
   > 如果您選取 [是]，您必須刪除資訊安全中心建立的工作區，直到所有虛擬機器已重新連線至新的目標工作區。 如果過早刪除工作區，這項作業將會失敗。
   >
   >

   - 選取 [取消] 以取消作業。

   ![選取現有工作區][3]

## <a name="data-collection-tier"></a>資料收集層
資訊安全中心可以減少事件的數量，同時維持足夠的事件以供調查、稽核和威脅偵測之用。 對於訂用帳戶和工作區，您可以從代理程式將收集的四組事件中選擇合適的篩選原則。

- **所有事件** – 適合想要確定收集到所有事件的客戶。 這是預設值。
- **一般** – 這組事件能滿足大部分客戶的需求，而能讓這些客戶使用完整的稽核記錄。
- **最小** – 適合想要將事件數量最小化的客戶，是較小的一組事件。
- **無** – 停用安全性和 App Locker 記錄檔的安全性事件收集。 對於選擇此選項的客戶，其安全性儀表板上僅有 Windows 防火牆記錄檔和主動式評估，例如反惡意程式碼軟體、基準和更新。

> [!NOTE]
> 這些集合都是設計用來處理一般情況。 實作之前，請務必評估哪一個適合您的需求。
>
>

為了判定屬於**一般**和**最小**事件集合的事件，我們致力於與客戶合作並配合業界標準，以了解每個事件及其使用方式的未篩選頻率。 此流程中採取下列指導方針：

- **最小** - 確保這個集合所涵蓋的僅有可能代表發生成功入侵和重大事件的事件，數量很少。 例如，這個集合包含使用者成功和失敗的登入活動 (事件識別碼 4624、4625)，不過不包含對於稽核而言相當重要但對於偵測而言沒有意義而且數量相對多的登出活動。 這個集合的大部分資料量是登入事件和流程建立事件 (事件識別碼 4688)。
- **一般** - 提供此集合中的完整使用者稽核記錄。 例如，這個集合包含使用者登入和使用者登出活動 (事件識別碼 4634)。 納入了各種稽核動作，例如安全性群組變更、主要網域控制站 Kerberos 作業，以及業界組織建議的其他事件。

「一般」集合包含的事件相當少，因為選擇一般集合而非其他事件的主要動機在於降低數量，且不篩選出特定事件。

以下是對於每個集合的安全性和 App Locker 事件識別碼的完整分析：

   ![事件識別碼][4]

若要選擇篩選原則：
1. 在 [安全性原則和設定] 刀鋒視窗的 [安全性事件] 下，選取您的篩選原則。
2. 選取 [ **儲存**]。

   ![選擇篩選原則][5]

## <a name="disable-automatic-provisioning"></a>停用自動佈建
您可以在安全性原則中關閉這項設定，隨時停用資源的自動佈建。 強烈建議進行自動佈建，以便取得安全性警示，以及系統更新、作業系統弱點和端點保護的建議。

> [!NOTE]
> 停用自動佈建不會從已佈建代理程式的 Azure VM 移除 Microsoft Monitoring Agent。
>
>

1. 返回 [資訊安全中心] 主功能表，並選取 [安全性原則]。

   ![停用自動佈建][6]

2. 選取您想要停用自動佈建的訂用帳戶。
3. 在 [安全性原則 - 資料收集] 刀鋒視窗的 [上架] 下，選取 [關閉]，停用自動佈建。
4. 選取 [ **儲存**]。  

## <a name="next-steps"></a>後續步驟
本文說明資料收集和自動佈建如何在資訊安全中心運作。 如要深入了解資訊安全中心，請參閱下列主題：

* [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md) --了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
* [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md) -- 了解建議如何協助您保護您的 Azure 資源。
* [Azure 資訊安全中心的安全性健康狀態監視](security-center-monitoring.md)--了解如何監視 Azure 資源的健康狀態。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)-- 了解如何管理與回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md) -- 了解如何監視合作夥伴解決方案的健康狀態。
- [Azure 資訊安全中心資料安全性](security-center-data-security.md) - 了解資訊安全中心如何管理及保護其中的資料。
* [Azure 資訊安全中心常見問題集](security-center-faq.md)-- 尋找有關使用服務的常見問題。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)-- 取得最新的 Azure 安全性新聞和資訊。

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[4]: ./media/security-center-enable-data-collection/event-id.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-automatic-provisioning.png

