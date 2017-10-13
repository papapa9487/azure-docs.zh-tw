---
title: "建立具有靜態公用 IP 位址的 VM - Azure 入口網站 | Microsoft Docs"
description: "了解如何使用 Azure 入口網站建立具有靜態公用 IP 位址的 VM。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 233e4eea8439320c1c7446e2c2b2e9d379351a3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>使用 Azure 入口網站建立具有靜態公用 IP 位址的 VM

> [!div class="op_single_selector"]
> * [Azure 入口網站](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [範本](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (傳統)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Azure 建立和處理資源的部署模型有二種：[Resource Manager 和傳統](../resource-manager-deployment-model.md)。 本文涵蓋之內容包括使用 Resource Manager 部署模型，Microsoft 建議大部分的新部署使用此模型，而不是傳統部署模型。

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>使用靜態公用 IP 建立 VM

若要在 Azure 入口網站中建立具有靜態公用 IP 位址的 VM，請完成下列步驟：

1. 透過瀏覽器瀏覽至 [Azure 入口網站](https://portal.azure.com) ，並視需要使用您的 Azure 帳戶登入。
2. 在入口網站左上角，按一下 [新增]>>[計算]>[Windows Server 2012 R2 Datacenter]。
3. 在 [選取部署模型] 清單中，選取 [Resource Manager]，然後按一下 [建立]。
4. 在 基本 刀鋒視窗中，輸入如下所示的 VM 資訊，然後按一下確定。
   
    ![Azure 入口網站 - 基本](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. 在 選擇大小 刀鋒視窗中，按一下如下所示的 A1 標準，然後按一下選取。
   
    ![Azure 入口網站 - 選擇大小](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. 在 [設定] 刀鋒視窗中，按一下 [公用 IP 位址]，接著在 [建立公用 IP 位址] 刀鋒視窗的 [指派] 底下，按一下 [靜態]，如下所示。 然後按一下 [確定] 。
   
    ![Azure 入口網站 - 建立公用 IP 位址](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. 在 [設定] 刀鋒視窗中，按一下 [確定]。
8. 檢視如下所示的 摘要 刀鋒視窗，然後按一下確定。
   
    ![Azure 入口網站 - 建立公用 IP 位址](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. 請注意您儀表板中新的磚。
   
    ![Azure 入口網站 - 建立公用 IP 位址](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. VM 建立後，[設定]  刀鋒視窗隨即出現，如下所示
    
    ![Azure 入口網站 - 建立公用 IP 位址](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

