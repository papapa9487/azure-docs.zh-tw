---
title: "從 VHD 檔案建立 Azure DevTest Labs 自訂映像 | Microsoft Docs"
description: "了解如何使用 Azure 入口網站在 Azure DevTest Labs 中從 VHD 檔案建立自訂映像"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: b795bc61-7c28-40e6-82fc-96d629ee0568
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: tarcher
ms.openlocfilehash: 9983ea9b847f44ed18a6169a4bdb224b63626a64
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>從 VHD 檔案建立自訂映像

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>逐步指示

下列步驟將逐步引導您使用 Azure 入口網站從 VHD 檔案建立自訂映像：

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。

1. 從實驗室清單中，選取所需的實驗室。  

1. 在實驗室的刀鋒視窗上，選取 [組態] 。 

1. 在實驗室的 [組態] 刀鋒視窗上，選取 [自訂映像 (VHD)]。

1. 在 [自訂映像] 刀鋒視窗上，選取 [+加入]。

    ![加入自訂映像](./media/devtest-lab-create-template/add-custom-image.png)

1. 輸入自訂映像的名稱。 這個名稱會在建立 VM 時顯示於基本映像清單中。

1. 輸入自訂映像的描述。 這個描述會在建立 VM 時顯示於基本映像清單中。

1. 選取 [VHD]。

1. 從 [VHD] 刀鋒視窗，選取想要的 VHD 檔案。

1. 選取 [確定] 以關閉 [VHD] 刀鋒視窗。

1. 選取 [作業系統組態]。

1. 在 [作業系統組態] 索引標籤上，選取 [Windows] 或 [Linux]。

1. 如果選取 [Windows]  ，請透過核取方塊來指定 *Sysprep* 是否已在電腦上執行。 

1. 選取 [確定] 以關閉 [作業系統組態] 刀鋒視窗。

1. 選取 [確定]  ，以建立自訂映像。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>相關部落格文章

- [自訂映像或公式？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [在 Azure DevTest Labs 之間複製自訂映像](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

##<a name="next-steps"></a>後續步驟

- [將 VM 新增到實驗室](./devtest-lab-add-vm-with-artifacts.md)
