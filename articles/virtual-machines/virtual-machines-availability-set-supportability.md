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
ms.topic: article
ms.date: 7/15/2017
ms.author: delhan
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: f07108f5d7a792dcc9aae8a4fe889878db168bc6
ms.contentlocale: zh-tw
ms.lasthandoff: 07/21/2017

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
