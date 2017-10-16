---
title: "從 VM 建立 Azure DevTest Labs 自訂映像 | Microsoft Docs"
description: "了解如何使用 Azure 入口網站在 Azure DevTest Labs 中從已佈建的 VM 建立自訂映像"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: tarcher
ms.openlocfilehash: 9d2dcf7164985508d691e8a0c123efaf3b8aa19a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-custom-image-from-a-vm"></a>從 VM 建立自訂映像

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

## <a name="step-by-step-instructions"></a>逐步指示

您可以從已佈建的 VM 建立自訂映像，之後再使用該自訂映像來建立完全相同的 VM。 下列步驟說明如何從 VM 建立自訂映像︰

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。

1. 從實驗室清單中，選取所需的實驗室。  

1. 在實驗室的刀鋒視窗中，選取 [我的虛擬機器] 。
 
1. 在 [我的虛擬機器]  刀鋒視窗中，選取要從中建立自訂映像的 VM。

1. 在 VM 的刀鋒視窗中，選取 [建立自訂映像 (VHD)] 。

    ![建立自訂映像的功能表項目](./media/devtest-lab-create-template/create-custom-image.png)

1. 在 [建立映像]  刀鋒視窗上，輸入自訂映像的名稱和描述。 此資訊會在建立 VM 時顯示於基底清單中。

    ![建立自訂映像刀鋒視窗](./media/devtest-lab-create-template/create-custom-image-blade.png)

1. 選取 sysprep 是否在 VM 上執行。 如果 sysprep 未在 VM 上執行，請指定您是否要在從這個自訂映像建立 VM 時讓 sysprep 執行。

1. 完成時選取 [確定]  ，以建立自訂映像。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相關部落格文章

- [自訂映像或公式？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [在 Azure DevTest Labs 之間複製自訂映像](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##<a name="next-steps"></a>後續步驟

- [將 VM 新增到實驗室](./devtest-lab-add-vm-with-artifacts.md)
