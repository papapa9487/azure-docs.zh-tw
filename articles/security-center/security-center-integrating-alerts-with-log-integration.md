---
title: "整合 Azure 資訊安全中心警示與 Azure 記錄整合 | Microsoft Docs"
description: "本文可協助您開始整合資訊安全中心警示與 Azure 記錄整合。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: d2d088d3-d38d-47ff-a062-c78e0fd59226
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: terrylan
ms.openlocfilehash: d13e5b87c446e587091551b22d80fe568d5d8093
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="integrating-azure-security-center-alerts-with-azure-log-integration"></a>整合 Azure 資訊安全中心警示與 Azure 記錄整合
許多安全性作業和事件回應小組依賴安全性資訊及事件管理 (SIEM) 方案對安全性警示進行分級和調查做為起點。 透過 Azure 記錄整合，您可以將 Azure 資訊安全中心的警示與您的 SIEM 方案整合。

Azure 記錄整合目前支援 HP ArcSight、Splunk 及 IBM QRadar。

## <a name="what-logs-can-i-integrate"></a>可以整合哪些記錄檔？
Azure 會為每項服務產生大量記錄。 這些記錄檔會分類為︰

* **控制/管理記錄檔**：可讓您看到 Azure Resource Manager 的 CREATE、UPDATE 和 DELETE 作業。 這些控制平面事件會顯示在 Azure 活動記錄中
* **資料平面記錄檔**：可讓您看到使用 Azure 資源時所引發的事件。 Windows 事件記錄檔是其中一個範例，您可以從事件檢視器的安全性通道取得安全性事件資訊。 資料平面事件 (由虛擬機器或 Azure 服務所產生) 會由 Azure 診斷記錄顯示。

Azure 記錄檔整合目前支援下列項目的整合︰

* Azure VM 記錄檔
* Azure 稽核記錄檔
* Azure 資訊安全中心警示

## <a name="install-azure-log-integration"></a>安裝 Azure 記錄檔整合
下載 [Azure 記錄檔整合](https://www.microsoft.com/download/details.aspx?id=53324)。

Azure 記錄檔整合服務會從其安裝所在的電腦收集遙測資料。  所收集的遙測資料是︰

* Azure 記錄檔整合的執行期間所發生的例外狀況
* 所處理之查詢和事件數目的相關度量
* 正在使用哪些 Azlog.exe 命令列選項的相關統計資料

> [!NOTE]
> 您可以取消核取此選項，以關閉遙測資料的收集。
>
>

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>整合 Azure 稽核記錄檔和資訊安全中心警示
1. 開啟命令提示字元，並使用 **cd** 命令前往 **c:\Program Files\Microsoft Azure Log Integration**。
2. 執行 **azlog createazureid** 命令，在裝載 Azure 訂用帳戶的 Azure Active Directory (AD) 租用戶中建立 [Azure Active Directory 服務主體](../active-directory/active-directory-application-objects.md) 。

    系統會提示您登入 Azure。

   > [!NOTE]
   > 您必須是訂用帳戶擁有者或訂用帳戶的共同管理員。
   >
   >

    對 Azure 的驗證會透過 Azure AD 來進行。  建立 Azure 記錄整合的服務主體會建立 Azure AD 身分識別，以獲得 Azure 訂用帳戶的讀取權限。
3. 執行 **azlog authorize <SubscriptionID>** 命令，以將訂用帳戶的讀取者權限指派給在步驟 2 中建立的服務主體。 如果您未指定 **SubscriptionID**，則會對服務主體指派您可以存取之所有訂用帳戶的讀取者角色。

   > [!NOTE]
   > 如果您在執行 **createazureid** 命令之後立即執行 **authorize** 命令，可能會看見警告。 建立 Azure AD 帳戶到帳戶可供使用之間會有一些延遲。 如果您在執行 **createazureid** 命令後等待約 10 秒再執行 **authorize** 命令，應該就不會看到這些警告。
   >
   >
4. 檢查下列資料夾以確認其中有稽核記錄檔 JSON 檔案︰

   * **c:\Users\azlog\AzureResourceManagerJson**
   * **c:\Users\azlog\AzureResourceManagerJsonLD**
5. 檢查下列資料夾以確認其中有資訊安全中心警示︰

   * **c:\Users\azlog\ AzureSecurityCenterJson**
   * **c:\Users\azlog\AzureSecurityCenterJsonLD**
6. 將 SIEM 檔案轉寄站連接器設定至適當的資料夾。 程序會視您所使用的 SIEM 而有所不同。

## <a name="next-steps"></a>後續步驟
若要深入了解 Azure 活動記錄和屬性定義，請參閱︰

* [使用 Resource Manager 來稽核作業](../azure-resource-manager/resource-group-audit.md)

如要深入了解資訊安全中心，請參閱下列主題：

* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) — 了解如何管理與回應安全性警示。
* [Azure 資訊安全中心常見問題集](security-center-faq.md) — 尋找有關使用服務的常見問題。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) — 取得最新的 Azure 安全性新聞和資訊。
