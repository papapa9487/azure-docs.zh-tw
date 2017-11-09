---
title: "Windows VM 配置失敗的疑難排解 | Microsoft Docs"
description: "在 Azure 中建立、重新啟動或調整 Windows VM 大小時，對配置失敗進行疑難排解"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2016
ms.author: cjiang
ms.openlocfilehash: a27b728dd902f6230a8e0a029ab69dabdd4cc4aa
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-windows-vms-in-azure"></a>在 Azure 中建立、重新啟動或調整 Windows VM 大小時，對配置失敗進行疑難排解
建立 VM、重新啟動已停止 (已解除配置) 的 VM 或調整 VM 大小時，Microsoft Azure 會配置計算資源給您的訂用帳戶。 當您執行這些作業時，即使您尚未達到 Azure 訂用帳戶限制，也可能偶爾收到錯誤訊息。 本文說明一些常見配置失敗的原因，並建議可能的補救方法。 規劃服務的部署時，本資訊可能也很有用。 [當您在 Azure 中建立、重新啟動或調整 Linux VM 大小時，也可以對配置失敗進行疑難排解](../linux/allocation-failure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

[!INCLUDE [virtual-machines-common-allocation-failure](../../../includes/virtual-machines-common-allocation-failure.md)]

