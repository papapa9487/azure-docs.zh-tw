---
title: "Azure 流量管理員中的流量檢視 | Microsoft Docs"
description: "流量管理員流量檢視簡介"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: 0a07ff578c6afeedc6f3806d52bfe5aef6945c04
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2017
---
# <a name="traffic-manager-traffic-view"></a>流量管理員流量檢視

>[!NOTE]
>「流量管理員」中的「流量檢視」功能目前為「公開預覽版」，可能沒有與正式發行版本功能相同層級的可用性和可靠性。 不支援該功能、可能已經限制功能，以及可能無法在所有 Azure 位置提供使用。 如需此功能可用性和狀態的最新通知，請查看 [Azure 流量管理員](https://azure.microsoft.com/updates/?product=traffic-manager)分頁。

流量管理員為您提供 DNS 層級路由，讓您的使用者根據您在建立設定檔時設定的路由方法，被導向至狀況良好的端點。 這個作業提供流量管理員一個使用者群體 (在 DNS 解析程式資料粒度層級) 及其流量模式的檢視。 當您啟用流量檢視時，系統會處理這項資訊以提供可採取動作的深入資訊。 

藉由使用流量檢視，您可以：
- 了解您的使用者群體所在的位置 (取決於本機 DNS 解析程式層級資料粒度)。
- 檢視源自於這些區域的流量 (當作 Azure Traffic Manager 所處理的 DNS 觀察)。
-  洞悉這些使用者體驗的代表延遲是什麼。
- 深入探討從使用者群體之每個個體到您具有端點之 Azure 區域的特定流量模式。 

例如，您可以使用流量檢視來了解哪些區域有大量流量，但是受更高的延遲所苦。 接下來您可以使用這項資訊來規劃到新的 Azure 區域的使用量擴充，讓這些使用者可以擁有較低的延遲體驗。

## <a name="how-traffic-view-works"></a>流量檢視的運作方式

流量檢視的運作方式是讓流量管理員查看在過去七天內針對已啟用此功能之設定檔所收到的連入查詢。 它會根據這項資訊擷取 DNS 解析程式的來源 IP，然後用來作為使用者位置的代表。 然後會在 DNS 解析程式層級資料粒度群組在一起，藉由使用由流量管理員維護之 IP 位址的地理資訊，來建立使用者群體區域。 流量管理員接著會查看查詢路由前往的 Azure 區域，並且為這些區域的使用者建構流量流程對應。
在下一個步驟中，流量管理員會將 Azure 區域對應的使用者群組區域與它為不同使用者網路維護的網路智慧延遲資料表相互關聯，以了解當連接到 Azure 區域時，這些區域的使用者所體驗的平均延遲。 所有這些計算接著會在呈現給您之前，在每個本機 DNS 解析程式 IP 層級製成資料表。 您可以在您選擇的分析工作流程中處理此資訊，也可以將此資訊下載為 CSV 檔案。
當您使用流量檢視時，您會根據用來建立呈現之深入資訊的資料點數目收到帳單。 目前，唯一使用的資料點類型是流量管理員設定檔收到的查詢。 如需價格的詳細資訊，請瀏覽[流量管理員定價分頁](https://azure.microsoft.com/pricing/details/traffic-manager/)。


## <a name="next-steps"></a>後續步驟

- 了解 [流量管理員的運作方式](traffic-manager-overview.md)
- 深入了解流量管理員支援的 [流量路由方法](traffic-manager-routing-methods.md)
- 了解如何 [建立流量管理員設定檔](traffic-manager-create-profile.md)

