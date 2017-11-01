---
title: "Azure 上的 OpenShift 概觀 | Microsoft Docs"
description: "Azure 上的 OpenShift 概觀。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: f9641b52db91a4356f6d5789a8cd78a6bb3da02b
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="openshift-overview"></a>OpenShift 概觀

OpenShift 為企業帶來 docker 和 Kubernetes 開放且可延伸的容器應用程式平台。  

OpenShift 包含容器協調流程與管理的 Kubernetes。 它會加入開發人員和作業中心的工具，可讓：

- 快速開發應用程式
- 容易部署及調整
- 長期小組和應用程式的生命週期維護

有多個 OpenShift 供應項目，其中兩個可在 Azure 中執行。

- OpenShift Origin
- Red Hat OpenShift 容器平台
- OpenShift 線上
- OpenShift 專用

涵蓋四個供應項目，兩個可供客戶自行部署至 Azure - OpenShift Origin 和 OpenShift 容器平台。

## <a name="openshift-origin"></a>OpenShift Origin

社群支援之 OpenShift 的[開放原始碼](https://www.openshift.org/)上游專案。 CentOS 或 RHEL 上可安裝的原點。

## <a name="openshift-container-platform"></a>Red Hat OpenShift 容器平台

Red Hat 支援的 Red Hat 企業備妥 ([商業供應項目](https://www.openshift.com))。 客戶購買 OpenShift 容器平台的必要權利，並負責安裝與管理整個基礎結構。

由於客戶「擁有」整個平台，因此它們可以安裝在其內部部署資料中心、公用雲端 (Azure、AWS、Google 等等) 和其他內容。

## <a name="openshift-online"></a>OpenShift 線上

受管理的 Red Hat **多租用戶** OpenShift (使用容器平台)。 Red Hat 會管理所有的基礎結構 (VM、OpenShift 叢集、網路、儲存體等)。 

客戶部署容器，但是無法控制執行容器的主機。 因為這是多租用戶，容器可能會共置於相同的 VM 主機，做為其他客戶的容器。 成本按每個容器計算。

## <a name="openshift-dedicated"></a>OpenShift 專用

受管理的 Red Hat **單租用戶** OpenShift (使用容器平台)。 Red Hat 會管理所有的基礎結構 (VM、OpenShift 叢集、網路、儲存體等)。 叢集是一位客戶所特有，並會在公用雲端 (AWS、Google、Azure - 2018 年早期推出) 中執行。 啟動叢集包含四個每年 48000 美元的應用程式節點 (整年前方付款)。

## <a name="next-steps"></a>後續步驟

- [在 Azure 中設定 OpenShift 的一般必要條件](./openshift-prerequisites.md)
- [部署 OpenShift Origin](./openshift-origin.md)
- [部署 Red Hat OpenShift 容器平台](./openshift-container-platform.md)
- [後續部署工作](./openshift-post-deployment.md)
- [OpenShift 部署疑難排解](./openshift-troubleshooting.md)
