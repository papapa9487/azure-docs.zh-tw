---
title: "Azure Container Instances 概觀 | Azure Docs"
description: "了解 Azure Container Instances"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: a678700884b612cad6281eb8f3b74ce63a0ebb69
ms.openlocfilehash: b77ba307938b6b1f5221de40cc135d17c15c1d9b
ms.contentlocale: zh-tw
ms.lasthandoff: 07/26/2017

---

# <a name="azure-container-instances"></a>Azure Container Instances

容器正快速成為封裝、部署及管理雲端應用程式的慣用方法。 Azure Container Instances 提供更簡單快速的方法，讓您無需佈建任何虛擬機器或採用更高層級的服務，就能在 Azure 中執行容器。

對於可在隔離容器中運作的任何情節，包括簡單的應用程式、工作自動化及建置工作，Azure Container Instances 是很棒的解決方案。 對於需要完整容器協調流程的情節，包括跨多個容器的服務探索、自動縮放及協調性應用程式升級，我們建議使用 [Azure Container Service](https://docs.microsoft.com/azure/container-service/)。

## <a name="fast-startup-times"></a>快速啟動時間

容器提供比虛擬機器更多的啟動優點。 Azure Container Instances 可讓您在幾秒內啟動 Azure 中的容器，而不需要佈建和管理 VM。

## <a name="hypervisor-level-security"></a>Hypervisor 等級安全性

在過去，雖然容器提供應用程式相依性隔離和資源控管，但並沒有足夠的能力防範惡意的多租用戶使用方式。 Azure Container Instances 可以將您的應用程式隔離在容器中，就像在 VM 中一樣。

## <a name="custom-sizes"></a>自訂大小

容器通常已最適合只執行單一應用程式，但這些應用程式的確切需求可能明顯不同。 Azure Container Instances 可讓您要求剛好符合所需的 CPU 核心和記憶體數量。 您只需要根據所要求的數量來付費，依秒計費，因此能夠根據需求來精密規劃支出。

## <a name="public-ip-connectivity"></a>公用 IP 連線能力

Azure Container Instances 可讓您利用公用 IP 位址，直接向網際網路公開您的容器。 未來，我們將擴充網路功能，以便與虛擬網路、負載平衡器，以及 Azure 網路基礎結構的其他核心部分整合在一起。

## <a name="persistent-storage"></a>永續性儲存體

為了擷取和保存 Azure Container Instances 的狀態，我們支援直接裝載 Azure 檔案共用。

## <a name="linux-and-windows-containers"></a>Linux 和 Windows 容器

Azure Container Instances 可讓您使用相同的 API 來排程 Windows 和 Linux 容器。 只要指出基本 OS 類型即可，其餘都完全相同。

## <a name="co-scheduled-groups"></a>共同排程的群組

Azure Container Instances 支援排程多個共用主機、區域網路、儲存體和生命週期的容器群組。 這可讓您合併主要應用程式與其他扮演支援角色的應用程式，例如記錄。

## <a name="next-steps"></a>後續步驟

使用我們[快速入門指南](container-instances-quickstart.md)，嘗試使用單一命令將容器部署至 Azure。
