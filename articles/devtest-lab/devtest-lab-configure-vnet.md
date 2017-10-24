---
title: "設定 Azure DevTest Labs 中的虛擬網路 | Microsoft Docs"
description: "了解如何設定現有的虛擬網路和子網路，並在具備 Azure DevTest Labs 的 VM 中使用它們"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: tarcher
ms.openlocfilehash: 19dbb1625f46f8864413dc538a96b2413bc6eea0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>設定 Azure DevTest Labs 中的虛擬網路
如 [將具有構件的 VM 加入實驗室](devtest-lab-add-vm-with-artifacts.md)文章中所述，當您在實驗室中建立 VM 時，可以指定已設定的虛擬網路。 例如，如果您需要使用以 ExpressRoute 或站台對站台 VPN 設定的虛擬網路從您的 VM 存取公司資源時，就可以這麼做。 下列各節將說明如何將現有的虛擬網路加入至實驗室的虛擬網路設定，就能在建立 VM 時選擇它。

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>使用 Azure 入口網站設定適用於實驗室的虛擬網路
下列步驟會逐步引導您將現有虛擬網路 (和子網路) 加入至實驗室，以便在同一個實驗室中建立 VM 時加以使用。 

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。
3. 從實驗室清單中，選取所需的實驗室。 
4. 在實驗室的刀鋒視窗上，選取 [組態和原則]。
5. 在實驗室的 [組態和原則] 刀鋒視窗上，選取 [虛擬網路]。
6. 在 [虛擬網路]  刀鋒視窗中，您會看到您已針對目前的實驗室設定的虛擬網路清單，以及為實驗室所建立的預設虛擬網路。 
7. 選取 [+ 新增] 。
   
    ![將現有的虛擬網路加入至您的實驗室](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
8. 在 [虛擬網路] 刀鋒視窗中，選取 [選取虛擬網路]。
   
    ![選取現有的虛擬網路](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
9. 在 [選擇虛擬網路]  刀鋒視窗中，選取所需的虛擬網路。 刀鋒視窗會顯示訂用帳戶中與實驗室位於相同區域下方的所有虛擬網路。  
10. 選取虛擬網路之後，您會回到 [虛擬網路]。按一下刀鋒視窗底部清單中的子網路。

    ![子網路清單](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    [實驗室子網路] 刀鋒視窗隨即出現。

    ![實驗室子網路刀鋒視窗](./media/devtest-lab-configure-vnet/lab-subnet.png)

11. 指定 [實驗室子網路名稱]。
12. 若要允許在實驗室 VM 建立期間使用子網路，請選取 [在虛擬機器建立時使用]。
13. 若要啟用[共用公用 IP 位址](devtest-lab-shared-ip.md)，請選取 [啟用共用公用 IP]。
14. 若要允許子網路中使用公用 IP 位址，請選取 [允許建立公用 IP] 。
15. 在 [每位使用者的最大虛擬機器數] 欄位中，針對每個子網路指定每位使用者的最大 VM 數。 如果您想要不限數目的 VM 數，請將此欄位保留空白。
16. 選取 [確定]，關閉 [實驗室子網路] 刀鋒視窗。
17. 選取 [儲存]，關閉 [虛擬網路] 刀鋒視窗。
18. 現在已設定虛擬網路，在建立 VM 時就能選取它。 
    若要查看如何建立 VM 並指定虛擬網路，請參閱 [將具有構件的 VM 加入實驗室](devtest-lab-add-vm-with-artifacts.md)一文。 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>後續步驟
一旦您在實驗室中加入所需的虛擬網路之後，下一個步驟就是 [將 VM 加入至實驗室](devtest-lab-add-vm-with-artifacts.md)。

