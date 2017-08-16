---
title: "了解 Azure 消費限制 | Microsoft Docs"
description: "說明 Azure 消費限制的運作方式及如何移除此限制"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: a2743ef34bde0faabb3afd2ace27acddd59d3d70
ms.contentlocale: zh-tw
ms.lasthandoff: 08/09/2017

---
# <a name="understand-azure-spending-limit-and-how-to-remove-it"></a>了解 Azure 消費限制及如何移除此限制

Azure 消費限制是對您 Azure 訂用帳戶消費額度的限制。 所有新客戶只要註冊試用方案或包含多月點數的方案，預設都會開啟消費限制。 消費限制是 $0。 此限制無法變更。 隨用隨付訂用帳戶和承諾方案之類的訂用帳戶類型無法使用消費限制。 請參閱[完整的 Azure 優惠及消費限制可用性清單](https://azure.microsoft.com/support/legal/offer-details/)。

## <a name="what-happens-when-i-reach-the-spending-limit"></a>達到消費限制時會發生什麼情況？

當您因使用而產生的費用用罄優惠中所包含的每月金額時，您部署的服務將在該計費月份的剩餘天數中停用。 舉例來說，您部署的「雲端服務」會從生產環境中移除，而 Azure 虛擬機器則會停止並解除配置。 若要避免服務遭到停用，您可以選擇移除消費限制。 當您的服務遭到停用時，您儲存體帳戶及資料庫中的資料仍然可供系統管理員以唯讀方式使用。 在下一個計費月份開始時，若您的優惠包含多月點數，您的訂用帳戶將會重新啟用。 接著，您便可以重新部署「雲端服務」，並完整存取您的儲存體帳戶和資料庫。

在免費試用訂用帳戶達到消費限制後，您可以在 90 天內重新啟用訂用帳戶，並讓它自動[升級到標準隨用隨付優惠](billing-upgrade-azure-subscription.md)。

當您達到所用優惠的消費限制時，將會收到通知。 請登入 [Azure 帳戶中心](https://account.windowsazure.com)，選取 [帳戶]，然後選取 [訂用帳戶]。 您會看到已達到消費限制之訂用帳戶的相關通知。

## <a name="things-you-are-charged-for-even-if-you-have-a-spending-limit-enabled"></a>即使已啟用消費限制也需要付費的項目

即使已設定消費限制，有些 Azure 服務及 [Marketplace 購買](https://azure.microsoft.com/marketplace/)仍可能因付款方式 (CC) 而產生費用。 例如 Visual Studio 授權、Azure Active Directory Premium、支援方案以及透過 Marketplace 銷售的大多數協力廠商品牌服務。


## <a name="when-not-to-use-the-spending-limit"></a>何時不要使用消費限制

消費限制會防止您部署或使用特定的市集及 Microsoft 服務。 以下是您應該移除訂用帳戶消費限制的情況。

- 您打算部署第一方映像 (例如 Oracle) 或第一方服務 (例如 Visual Studio Team Services)。 此情況會讓您幾乎立即超過消費限制，而導致訂用帳戶遭停用。

- 您有不可中斷的服務。

- 您的服務及資源有您不想遺失的設定 (例如虛擬 IP 位址)。 將服務及資源解除配置時，將會遺失這些設定。


## <a name="remove-the-spending-limit"></a>移除消費限制

只要您的訂用帳戶所關聯的付款方式有效，您就可以隨時移除消費限制。 針對具有多月點數的優惠，您也可以在下一個計費週期開始時重新啟用消費限制。

若要移除您的消費限制，請依照下列步驟操作：

1. 登入 [Azure 帳戶中心](https://account.windowsazure.com)。

2. 選取一個訂用帳戶。

3. 如果訂用帳戶因為達到「消費限制」而被停用，請按一下此通知：[訂用帳戶已達消費限制，並已停用，以避免收費。] 否則，請按一下 [訂用帳戶狀態] 區域中的 [移除消費限制]。

4. 選取適合您的選項。

|選項|效果|
|-------|-----|
|無限期移除消費限制|移除消費限制，不在下一個計費週期開始時自動開啟。|
|移除目前計費週期的消費限制|移除消費限制，讓它在下一個計費週期開始時自動開啟。|

## <a name="need-help-contact-support"></a>需要協助嗎？ 請連絡支援人員。
如果仍需要協助，請[連絡支援人員](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解決您的問題。

