---
title: "Linux VM 配置失敗的疑難排解 | Microsoft Docs"
description: "在 Azure 中建立、重新啟動或調整 Linux VM 大小時，對配置失敗進行疑難排解"
services: virtual-machines-linux, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue,azure-resourece-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 02/02/2016
ms.author: cjiang
ms.openlocfilehash: 71ef6d4d132c2ab08137f549d538176d25528d4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-linux-vms-in-azure"></a>在 Azure 中建立、重新啟動或調整 Linux VM 大小時，對配置失敗進行疑難排解
建立 VM、重新啟動已停止 (已解除配置) 的 VM 或調整 VM 大小時，Microsoft Azure 會配置計算資源給您的訂用帳戶。 當您執行這些作業時，即使您尚未達到 Azure 訂用帳戶限制，也可能偶爾收到錯誤訊息。 本文說明一些常見配置失敗的原因，並建議可能的補救方法。 規劃服務的部署時，本資訊可能也很有用。 [當您在 Azure 中建立、重新啟動或調整 Windows VM 大小時，也可以對配置失敗進行疑難排解](../windows/allocation-failure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

[!INCLUDE [virtual-machines-common-allocation-failure](../../../includes/virtual-machines-common-allocation-failure.md)]

