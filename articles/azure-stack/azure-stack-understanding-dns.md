---
title: "了解 Azure Stack 中的 DNS | Microsoft Docs"
description: "了解 Azure Stack 中的 DNS 特性與功能"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 60f5ac85-be19-49ac-a7c1-f290d682b5de
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: scottnap
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 2a19b435777ba848835dcd90a1ebb8a0cbcb0e9b
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---
# <a name="introducing-idns-for-azure-stack"></a>適用於 Azure Stack 的 iDNS 簡介
================================

iDNS 是 Azure Stack 中的一個功能，可讓您解析外部 DNS 名稱 (例如 http://www.bing.com)。
它也可以讓您登錄內部虛擬網路名稱。 如此一來，您就可以依名稱 (而非 IP 位址) 解析相同虛擬網路上的 VM，而不需要提供自訂的 DNS 伺服器項目。

這是 Azure 中的內建功能，但是也可以在 Windows Server 2016 和 Azure Stack 中使用。

## <a name="what-does-idns-do"></a>iDNS 的用途為何？
您可以使用 Azure Stack 中的 iDNS 取得下列功能，而不需要指定自訂的 DNS 伺服器項目。

* 適用於租用戶工作負載的共用 DNS 名稱解析服務。
* 適用於租用戶虛擬網路內的名稱解析和 DNS 登錄的權威 DNS 服務。
* 從租用戶 VM 解析網際網路名稱的遞迴 DNS 服務。 租用戶不再需要指定自訂的 DNS 項目，就可以解析網際網路名稱 (例如 www.bing.com)。

您仍然可以沿用您自己的 DNS，也可以使用自訂的 DNS 伺服器 (如果您想要的話)。 但現在，如果您只想要能夠解析網際網路 DNS 名稱，而且能夠連線到相同虛擬網路中的其他虛擬機器，則您不需要指定任何項目也能夠運作。

## <a name="what-does-idns-not-do"></a>iDNS 不適用於哪些用途？
iDNS 不允許您針對可以從虛擬網路外部解析的名稱，建立 DNS 記錄。

在 Azure 中，您可以選擇指定能夠與公用 IP 位址相關聯的 DNS 名稱標籤。 您可以選擇標籤 (首碼)，但 Azure 會根據您建立公用 IP 位址所在的區域，選擇尾碼。

![DNS 名稱標籤的螢幕擷取畫面](media/azure-stack-understanding-dns-in-tp2/image3.png)

在上圖中，Azure 將會在 DNS 中，為區域 **westus.cloudapp.azure.com** 底下指定的 DNS 名稱標籤，建立 “A” 記錄。首碼和尾碼可構成完整網域名稱 (FQDN)，此名稱可以從公用網際網路上的任何位置解析。

Azure Stack 僅支援用於內部名稱登錄的 iDNS，因此無法執行下列作業。

* 在現有的託管 DNS 區域 (例如 local.azurestack.external) 底下建立 DNS 記錄。
* 建立 DNS 區域 (例如 Contoso.com)。
* 在您自己的自訂 DNS 區域底下建立記錄。
* 支援購買網域名稱。


