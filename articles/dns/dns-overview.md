---
title: "Azure DNS 概觀 | Microsoft Docs"
description: "在 Microsoft Azure 上裝載 Azure DNS 服務的概觀。 在 Microsoft Azure 上裝載您的網域。"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 68747a0d-b358-4b8e-b5e2-e2570745ec3f
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: gwallace
ms.openlocfilehash: 3705457e4c90f8869496f7f5177531bd128d1057
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-dns-overview"></a>Azure DNS 概觀

網域名稱系統 (DNS) 負責將網站或服務名稱轉譯 (或解析) 為其 IP 位址。 Azure DNS 是 DNS 網域的主機服務，採用 Microsoft Azure 基礎結構提供名稱解析。 只要將您的網域裝載於 Azure，就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。

![DNS 概觀](./media/dns-overview/scenario.png)

## <a name="features"></a>特性

* **可靠性和效能** - Azure DNS 中的 DNS 網域是裝載於 Azure 的 DNS 名稱伺服器全球網路。 我們使用「任一傳播」網路，所以每個 DNS 查詢是由最接近的可用 DNS 伺服器回答。 這為您的網域提供快速的效能與高可用性。

* **緊密整合** - Azure DNS 服務可用為您 Azure 服務管理 DNS 記錄，也可用來為您的外部資源提供 DNS。 Azure DNS 已在 Azure 入口網站中整合，並且使用與您其他 Azure 服務相同的認證、計費及支援合約。

* **安全性** - Azure DNS 服務是以 Azure Resource Manager 為基礎。 因此可得益於 Resource Manager 的功能，如角色型存取控制、稽核記錄檔、資源鎖定。 您可以透過 Azure 入口網站、Azure PowerShell Cmdlet 和跨平台 Azure CLI 來管理網域和記錄。 需要自動化 DNS 管理的應用程式可以透過 REST API 和 SDK 與服務進行整合。

Azure DNS 目前不支援購買網域名稱。 若想要購買網域，必須洽詢第三方網域名稱註冊機構。 註冊機構通常會收取些微年費。 然後便可以在 Azure DNS 裝載這些網域來管理 DNS 記錄。 如需詳細資訊，請參閱 [將網域委派給 Azure DNS](dns-domain-delegation.md) 。

## <a name="pricing"></a>價格

DNS 的計費方式是依據 Azure 中裝載的 DNS 區域數量，以及 DNS 查詢的數量。 若要深入瞭解定價，請瀏覽 [Azure DNS 定價](https://azure.microsoft.com/pricing/details/dns/)。

## <a name="faq"></a>常見問題集

如需有關 Azure DNS 的常見問題集，請參閱 [Azure DNS 常見問題集](dns-faq.md)。

## <a name="next-steps"></a>後續步驟

如需深入了解 DNS 區域和記錄，請瀏覽：[DNS 區域和記錄的概觀](dns-zones-records.md)。

了解如何在 Azure DNS 中[建立 DNS 區域](./dns-getstarted-create-dnszone-portal.md)。

深入了解 Azure 的一些其他重要[網路功能](../networking/networking-overview.md)。

