---
title: "在 Microsoft Azure 中保護個人資料 | Microsoft Docs"
description: "此系列文章的第一篇文章是協助您使用 Azure 來保護個人資料"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 4dbdb2dc11bdc515fb3856dd45203868122c7726
ms.contentlocale: zh-tw
ms.lasthandoff: 08/30/2017

---
# <a name="protect-personal-data-in-microsoft-azure"></a>在 Microsoft Azure 中保護個人資料

本文介紹一系列文章，以協助您使用 Azure 安全性技術和服務來保護個人資料。 這是許多公司和業界合規性和治理計劃的重要需求。 本文將包含案例、問題陳述和公司目標。

## <a name="scenario-and-problem-statement"></a>案例與問題陳述

總部設於美國的大型郵輪公司將擴展其營運，以提供地中海、亞得里亞海、波羅的海和不列顛群島的行程。 為了支援這些工作，它收購了以義大利、德國、丹麥和英國為據點的數個較小型郵輪公司。

此公司使用 Microsoft Azure 在雲端儲存公司資料。 這可能包含類似如下的客戶及/或員工資訊：

- 地址
- 電話號碼
- 稅務識別編號
- 信用卡資訊

此公司必須保護客戶和員工資料的隱私權，同時讓需要的部門 (例如薪資部門和訂位部門) 可以存取資料。

## <a name="company-goals"></a>公司目標 

- 當包含個人資料的資料來源位於雲端儲存體時，會加密此資料來源。

- 當個人資料從一個位置傳輸到另一個位置時，會加密傳輸中的資料。 這適用於在虛擬網路之間，或在公司資料中心與 Azure 雲端的網際網路之間傳輸的資料。

- 透過增強式身分識別管理與存取控制技術，來防止個人資料的機密性和完整性遭到未經授權的存取。

- 透過監控弱點和威脅，來防止攻擊者利用資料缺口公開個人資料。

- 評定儲存或傳輸個人資料之 Azure 服務的安全性狀態，以發現進一步保護個人資料的機會。

## <a name="data-protection-guidance"></a>資料保護指引

下列文章包含技術性操作指南，可協助您達成以上所列的個人資料保護目標：

- [Azure 加密技術](protect-personal-data-at-rest.md)

- [Azure 加密技術](protect-personal-data-in-transit-encryption.md)

- [Azure 身分識別與存取技術](protect-personal-data-identity-access-controls.md)

- [Azure 網路安全性技術](protect-personal-data-network-security.md)

- [Azure 資訊安全中心](protect-personal-data-azure-security-center.md)



## <a name="next-steps"></a>後續步驟

- [Azure 安全性資訊網站](https://aka.ms/AzureSecInfo)

- [Microsoft 信任中心](https://www.microsoft.com/TrustCenter/default.aspx)

- [Azure 資訊安全中心](https://azure.microsoft.com/services/security-center/)

- [Azure 安全性小組部落格](https://www.azuresecurityorg)

- [Azure.com 部落格 - 安全性](https://azure.microsoft.com/blog/topics/security/)

