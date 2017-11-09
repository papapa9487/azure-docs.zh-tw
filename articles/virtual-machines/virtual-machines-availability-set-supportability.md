---
title: "在現有的可用性設定組中新增 Azure VM 的可支援性 | Microsoft Docs"
description: "在現有的可用性設定組中新增 Azure VM 的可支援性。"
services: virtual-machines-linux
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
ms.assetid: 
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: delhan
ms.openlocfilehash: 8bf2a55563772e26239445732b2b08df677436ef
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2017
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>在現有的可用性設定組中新增 Azure VM 的可支援性

當您將新的虛擬機器 (VM) 新增至現有的可用性設定組時，偶爾可能會遇到限制。 下表詳細列出您可以在同一個可用性設定組中混合的 VM 系列。

以下是混合不同 VM 類型的可支援性對照表：

系列與可用性設定組|第二部 VM|具有使用 |Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|第一部 VM|||||||
|具有使用 ||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

其他所有系列由於需要特定硬體，因此無法存在於相同的可用性設定組。

因為專用 RDMA 後端網路的需求，所以 A8/A9 VM 大小不能混合。
