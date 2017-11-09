---
title: "在 Azure 中的 Windows VM 上安裝 MongoDB | Microsoft Docs"
description: "了解如何在以執行 Windows Server 的傳統部署模型建立的 Azure VM 上安裝 MongoDB。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 4095df41-bb69-4bbe-9c1c-70923b0d84ba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: iainfou
ms.openlocfilehash: d70194f30b7866cab38cfbbe1ea06664b0ddc1f5
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2017
---
# <a name="install-mongodb-on-a-windows-vm-in-azure"></a>在 Azure 中的 Windows VM 上安裝 MongoDB
> [!IMPORTANT]
> Azure 建立和處理資源的部署模型有二種： [資源管理員和傳統](../../../resource-manager-deployment-model.md)。  本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議讓大部分的新部署使用資源管理員模式。 若要使用 Resource Manager 部署模型安裝及設定 MongoDB，請參閱[本文章](../install-mongodb.md)。
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

[MongoDB][MongoDB] 是一個受歡迎的高效能開放原始碼 NoSQL 資料庫。 本文會引導您使用 [Azure 入口網站][AzurePortal]來建立 Windows Server 虛擬機器 (VM)。 接著您會建立資料磁碟，並將其連接至 VM，然後安裝及設定 MongoDB。 如果您想要使用 Azure 中現有的 VM，您可以直接跳到[安裝並設定 MongoDB](#install-and-run-mongodb-on-the-virtual-machine)。

## <a name="create-a-virtual-machine-running-windows-server"></a>建立執行 Windows Server 的虛擬機器
請遵循這些指示來建立虛擬機器。

[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

> [!NOTE]
> 您可以在建立虛擬機器時為 MongoDB 加入端點，並遵循下列方式進行設定：將它命名為 **Mongo**，使用 **TCP** 作為通訊協定，然後將公用和私人通訊埠設定為 **27017**。
>
>

## <a name="attach-a-data-disk"></a>連接資料磁碟
若要為虛擬機器提供儲存體，請連接並初始化資料磁碟，這樣 Windows 才能使用該磁碟。 如果您已經有資料磁碟，您可以連接現有的磁碟，或可以連接空的磁碟。

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-windows-linux.md)]

如需初始化磁碟的指示，請參閱 [如何將資料磁碟連接至 Windows 虛擬機器](attach-disk.md)中的〈作法：在 Windows Server 中初始化新的資料磁碟〉。

## <a name="install-and-run-mongodb-on-the-virtual-machine"></a>在虛擬機器上安裝及執行 MongoDB
[!INCLUDE [install-and-run-mongo-on-win2k8-vm](../../../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## <a name="summary"></a>摘要
在本教學課程中，您已了解如何建立執行 Windows Server 的虛擬機器、從遠端連線至此虛擬機器，以及連接資料磁碟。  同時也已了解如何在 Windows 虛擬機器上安裝及設定 MongoDB。 您現在可以依照 [MongoDB 文件][MongoDocs] 中的進階主題操作，來存取 Windows 型虛擬機器上的 MongoDB。

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: https://portal.azure.com/

