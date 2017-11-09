---
title: "登入傳統 Azure VM | Microsoft Docs"
description: "使用 Azure 入口網站來登入以傳統部署模型建立的 Windows 虛擬機器。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 510b394256bbe86a5eb5bfbc3af4681670b89de3
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2017
---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-portal"></a>使用 Azure 入口網站登入 Windows 虛擬機器
在 Azure 入口網站中，使用 [連接]  按鈕來啟動遠端桌面工作階段，並登入 Windows VM。

您想要連線至 Linux VM 嗎？ 請參閱[如何登入執行 Linux 的虛擬機器](../../linux/mac-create-ssh-keys.md)。

<!--
Deleting, but not 100% sure
Learn how to [perform these steps using new Azure portal](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

> [!IMPORTANT]
> Azure 建立和處理資源的部署模型有二種： [Resource Manager 和傳統](../../../resource-manager-deployment-model.md)。 本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議讓大部分的新部署使用資源管理員模式。 如需如何使用 Resource Manager 模型登入 VM 的詳細資訊，請參閱[這裡](../connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="connect-to-the-virtual-machine"></a>連接至虛擬機器
1. 登入 Azure 入口網站。
2. 按一下您想要存取的虛擬機器。 名稱會在 [所有資源] 窗格中列出。

    ![Virtual-machine-locations](./media/connect-logon/azureportaldashboard.png)

3. 按一下虛擬機器儀表板頂端命令列上的 [連接]。

    ![虛擬機器的 [連接] 圖示](./media/connect-logon/virtualmachine_dashboard_connect.png)

<!-- Don't know if this still applies
     I think we can zap this.
> [!TIP]
> If the **Connect** button isn't available, see the troubleshooting tips at the end of this article.
>
>
-->

## <a name="log-on-to-the-virtual-machine"></a>登入虛擬機器
[!INCLUDE [virtual-machines-log-on-win-server](../../../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>後續步驟
* 如果 [連接] 按鈕無法使用，或是有其他「遠端桌面」連線問題，請嘗試重設組態。 從虛擬機器儀表板按一下 [重設遠端存取]。

    ![Reset-remote-access](./media/connect-logon/virtualmachine_dashboard_reset_remote_access.png)

* 如果是您的密碼有問題，請嘗試重設密碼。 按一下沿著虛擬機器儀表板左側邊緣 [支援與疑難排解] 底下的 [重設密碼]。

    ![Reset-password](./media/connect-logon/virtualmachine_dashboard_reset_password.png)

如果這些秘訣無效，或者不是您所需要的，請參閱[針對以 Windows 為基礎之 Azure 虛擬機器的遠端桌面連線進行疑難排解](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 本文會逐步帶領您診斷及解決常見的問題。
