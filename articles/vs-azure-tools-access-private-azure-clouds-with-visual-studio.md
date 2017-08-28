---
title: "使用 Visual Studio 存取 Azure 私人雲端 | Microsoft Docs"
description: "了解如何使用 Visual Studio 存取私人雲端資源。"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 9d733c8d-703b-44e7-a210-bb75874c45c8
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/19/2017
ms.author: kraigb
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b29a3299433e57709ea31de3c7849230ea09c09a
ms.contentlocale: zh-tw
ms.lasthandoff: 03/21/2017

---
# <a name="accessing-private-azure-clouds-with-visual-studio"></a>使用 Visual Studio 存取 Azure 私人雲端
根據預設，Visual Studio 支援 Azure 公用雲端 REST 端點。 在本主題中，您將了解如何使用私人雲端的憑證，從 Visual Studio 存取私人雲端 (並進行互動)。

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>存取 Visual Studio 中的 Azure 私人雲端
1. 在私人雲端的 [Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)中下載發佈設定檔，或連絡您的系統管理員以取得發佈設定檔。 在 Azure 的公用版本上，下載此檔案的連結是 [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/)。 (下載檔案的副檔名應該是 `.publishsettings`)

1. 開啟 Visual Studio

1. 在 [伺服器總管] 中，以滑鼠右鍵按一下 [Azure] 節點，然後從操作功能表中選取 [管理及篩選訂閱]。
   
    ![管理訂用帳戶命令](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. 在 [管理 Microsoft Azure 訂用帳戶] 對話方塊中，選取 [憑證] 索引標籤，然後選取 [匯入]。
   
    ![正在匯入 Azure 憑證](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. 在 [匯入 Microsoft Azure 訂用帳戶] 對話方塊中，選取 [瀏覽]。

    ![[匯入 Microsoft Azure 訂用帳戶] 對話方塊上的 [瀏覽] 按鈕](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/browse-button.png)

1. 在 [開啟] 對話方塊中，瀏覽至您儲存發佈設定檔的目錄、選取檔案，然後選取 [開啟]。

    ![選取發佈設定檔](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/select-publish-settings-file.png)

1. 當您返回 [匯入 Microsoft Azure 訂用帳戶] 對話方塊時，選取 [匯入]。

    ![匯入發佈設定檔](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

    憑證會從發佈設定檔匯入 Visual Studio，而您現在可以與您的私人雲端資源互動。
   
## <a name="next-steps"></a>後續步驟
- [從 Visual Studio 發佈至 Azure 雲端服務](https://msdn.microsoft.com/library/azure/ee460772.aspx)
- [如何：下載和匯入發佈設定和訂用帳戶資訊](https://msdn.microsoft.com/library/dn385850\(v=nav.70\).aspx)

