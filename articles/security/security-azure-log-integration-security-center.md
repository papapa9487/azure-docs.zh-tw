---
title: "使用資訊安全中心進行 Azure 整合 | Microsoft Docs"
description: "了解如何使用記錄整合取得 Azure 安全性中心警示"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/29/2017
ms.author: barclayn
ms.custom: azlog
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 46758e9aa8d95bb10d91c315c1d712ab8f607188
ms.contentlocale: zh-tw
ms.lasthandoff: 08/30/2017

---
# <a name="how-to-get-your-security-center-alerts-in-azure-log-integration"></a>如何在 Azure 記錄整合中取得 Azure 安全性中心警示

本文章提供啟用 Azure 記錄整合服務，以提取 Azure 資訊安全中心 產生的安全性警示資訊所需的步驟。 您必須順利完成[開始](security-azure-log-integration-get-started.md)文章中的步驟，才能執行這份文件中的步驟。

## <a name="detailed-steps"></a>詳細步驟

下列步驟將會建立所需的 Azure Active Directory 服務主體，並指派訂用帳戶服務主體的讀取權限︰
1. 開啟命令提示字元，並前往 **c:\Program Files\Microsoft Azure Log Integration**
2. 執行命令``azlog createazureid``

    此命令會提示您登入 Azure。 此命令接著會建立託管 Azure 訂用帳戶 (其中登入的使用者是系統管理員、共同管理員或擁有者) 的 Azure AD 租用戶中的 [Azure Active Directory 服務主體](../active-directory/develop/active-directory-application-objects.md) 。 若登入的使用者只是 Azure AD 租用戶中的來賓使用者，則此命令將會失敗。 對 Azure 的驗證會透過 Azure Active Directory (AD) 來進行。 建立 Azure 記錄整合的服務主體會建立 Azure AD 身分識別，以獲得 Azure 訂用帳戶的讀取權限。

3. 接下來您將執行命令，該命令會將訂用帳戶的讀取者存取權指派給您剛建立的服務主體。 如果您未指定 SubscriptionID，則命令會嘗試將服務主體的讀取者角色，指派給您有任何存取權的所有訂用帳戶。 </br></br>
``azlog authorize <SubscriptionID>`` </br> 例如 </br>
``azlog authorize 0ee55555-0bc4-4a32-a4e8-c29980000000``

    >[!NOTE]
    如果您在執行 ```createazureid``` 命令之後立即執行 authorize 命令，可能會看見警告。 建立 Azure AD 帳戶到帳戶可供使用之間會有一些延遲。 如果您在執行 ```createazureid``` 命令後等待約 60 秒再執行 authorize 命令，應該就不會看到這些警告。

4. 檢查下列資料夾以確認其中有稽核記錄檔 JSON 檔案︰
 * **c:\Users\azlog\AzureResourceManagerJson**
 * **c:\Users\azlog\AzureResourceManagerJsonLD** </br></br>
5. 檢查下列資料夾以確認其中有資訊安全中心警示︰</br></br>
 * **c:\Users\azlog\AzureSecurityCenterJson**
 * **c:\Users\azlog\AzureSecurityCenterJsonLD** </br></br>

如果您在安裝和設定期間遇到任何問題，請開啟[支援要求](/azure-supportability/how-to-create-azure-support-request.md)，選取 [記錄整合]  作為您要求支援的服務。

## <a name="next-steps"></a>後續步驟
若要深入了解 Azure 記錄整合，請參閱下列文件：

* [Azure 記錄檔的 Microsoft Azure 記錄整合](https://www.microsoft.com/download/details.aspx?id=53324) – Azure 記錄整合詳細資料、系統需求和安裝指示的下載中心。
* [Azure 記錄整合簡介](security-azure-log-integration-overview.md) – 這份文件為您介紹 Azure 記錄整合、其主要功能以及運作方式。
* [合作夥伴設定步驟](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – 此部落格文章說明如何設定 Azure 記錄整合，以搭配使用合作夥伴解決方案 Splunk、HP ArcSight 和 IBM QRadar。
* [Azure 記錄整合常見問題集 (FAQ)](security-azure-log-integration-faq.md) - 此常見問題集會回答有關 Azure 記錄整合的問題。
* [Azure 診斷和 Azure 稽核記錄檔的新功能](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – 此部落格文章為您介紹 Azure 稽核記錄檔和其他功能，協助您深入了解您的 Azure 資源的作業。

