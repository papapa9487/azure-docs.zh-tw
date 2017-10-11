---
title: "保護 Azure 資訊安全中心內的  Azure SQL 服務和資料 | Microsoft Docs"
description: "本文件說明「Azure 資訊安全中心」中的建議，這些建議可協助您保護您的資料和 Azure SQL 服務，以及遵守安全性原則。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2017
ms.author: terrylan
ms.openlocfilehash: 0c3a11e9a86767641533b16de1b96b4c59bfdf51
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>保護 Azure 資訊安全中心內的 Azure SQL 服務和資料
「Azure 資訊安全中心」會分析 Azure 資源的安全性狀態。 當資訊安全中心發現潛在的安全性弱點時，它會建立可引導您完成所需控制之設定程序的建議。  這些建議適用於下列 Azure 資源類型︰虛擬機器 (VM)、網路、SQL 和資料，以及應用程式。

本文說明適用於 Azure SQL 服務和資料的建議。 這些建議是以下列主題為中心：為 Azure SQL 伺服器和資料庫啟用稽核、為 SQL 資料庫啟用加密，以及啟用 Azure 儲存體帳戶加密。  請使用下表作為參考來協助您了解可用的 SQL 服務和資料建議，以及每一項建議在套用後將產生的作用。

## <a name="available-sql-service-and-data-recommendations"></a>可用的 SQL 服務和資料建議
| 建議 | 說明 |
| --- | --- |
| [在 SQL Server 上啟用稽核與威脅偵測](security-center-enable-auditing-on-sql-servers.md) |建議您針對 Azure SQL Server 開啟稽核與威脅偵測 (僅適用於 Azure SQL 服務，不包括在您虛擬機器上執行的 SQL)。 |
| [在 SQL 資料庫上啟用稽核與威脅偵測](security-center-enable-auditing-on-sql-databases.md) |建議您針對 Azure SQL Database 開啟稽核與威脅偵測 (僅適用於 Azure SQL 服務，不包括在您虛擬機器上執行的 SQL)。 |
| [在 SQL 資料庫上啟用透明資料加密](security-center-enable-transparent-data-encryption.md) |建議您針對 SQL 資料庫啟用加密 (僅適用於 Azure SQL 服務)。 |

## <a name="see-also"></a>另請參閱
若要深入了解適用於其他 Azure 資源類型的建議，請參閱下列文章︰

* [保護 Azure 資訊安全中心內的虛擬機器](security-center-virtual-machine-recommendations.md)
* [保護 Azure 資訊安全中心內的應用程式](security-center-application-recommendations.md)
* [保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)

如要深入了解資訊安全中心，請參閱下列主題：

* [在 Azure 資訊安全中心設定安全性原則](security-center-policies.md) -- 了解如何為您的 Azure 訂用帳戶及資源群組設定安全性原則。
* [管理與回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md) -- 了解如何管理與回應安全性警示。
* [Azure 資訊安全中心常見問題集](security-center-faq.md) -- 尋找有關使用服務的常見問題。
