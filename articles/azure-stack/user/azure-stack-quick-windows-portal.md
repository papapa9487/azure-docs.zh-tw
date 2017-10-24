---
title: "Azure Stack 快速入門 - 建立 Windows 虛擬機器"
description: "Azure Stack 快速入門 - 使用入口網站來建立 Windows VM"
services: azure-stack
author: ErikjeMS
manager: byronr
ms.service: azure-stack
ms.topic: azure-stack
ms.date: 09/15/2017
ms.author: erikje
ms.custom: mvc
ms.openlocfilehash: abca538f28bbc0a8f3f00311ca1a69d196f10272
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-with-the-azure-stack-portal"></a>使用 Azure Stack 入口網站建立 Windows 虛擬機器

您可以使用 Azure Stack 入口網站建立 Windows 虛擬機器。 入口網站是以瀏覽器作為基礎的使用者介面，您可以在其中建立、設定和管理資源。

## <a name="sign-in-to-the-azure-stack-portal"></a>登入 Azure Stack 入口網站

登入 Azure Stack 入口網站。 Azure Stack 入口網站的位址取決於您所連線的 Azure Stack 產品：

* 如果是「Azure Stack 開發套件」(ASDK)，請移至：https://portal.local.azurestack.external。
* 針對 Azure Stack 整合系統，請移至您 Azure Stack 操作員所提供的 URL。

## <a name="create-a-virtual-machine"></a>建立虛擬機器

1. 按一下 [+ 新增] > [計算] > [Windows Server 2016 Datacenter Eval] > [建立]。 如果您沒有看到 [Windows Server 2016 Datacenter Eval] 項目，請連絡您的 Azure Stack 操作員。 如[將 Windows Server 2016 VM 映像新增到 Azure Stack 市集](../azure-stack-add-default-image.md)文章中所述，請要求他們將它新增至市集。 
    ![](media/azure-stack-quick-windows-portal/image01.png)
2. 在 [基本] 中，輸入 [名稱]、[使用者名稱] 與 [密碼]。 選擇 [訂用帳戶] 。 建立 資源群組 或選取現有的資源群組，選取 位置，然後按一下確定。

    ![](media/azure-stack-quick-windows-portal/image02.png)
3. 在 [選擇大小] 下，按一下 [D1 標準] > [選取]。
    ![](media/azure-stack-quick-windows-portal/image03.png)
4. 在 [設定] 下，接受預設值並按一下 [確定]。
    ![](media/azure-stack-quick-windows-portal/image04.png)
5. 在 [摘要] 下，按一下 [確定]，以建立虛擬機器。 
    ![](media/azure-stack-quick-windows-portal/image05.png)
6. 若要查看您的新虛擬機器，請按一下 [所有資源]，然後搜尋虛擬機器並按一下其名稱。
    ![](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>清除資源

不再需要虛擬機器時，可刪除資源群組、虛擬機器和所有相關資源。 若要這樣做，請從虛擬機器頁面選取資源群組，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟
在這個快速入門中，您已部署簡單的 Windows 虛擬機器。 若要深入了解 Azure Stack 虛擬機器，請繼續移至 [Azure Stack 中虛擬機器的考量](azure-stack-vm-considerations.md)。
