---
title: "使用 Azure Cloud Shell (預覽) 視窗 | Microsoft Docs"
description: "逐步解說 Azure Cloud Shell 視窗。"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: a47961dfdaf178a6b793bd68105d9792a9275bb3
ms.contentlocale: zh-tw
ms.lasthandoff: 08/17/2017

---

# <a name="using-the-azure-cloud-shell-window"></a>使用 Azure Cloud Shell 視窗

本文件說明如何使用 Cloud Shell 視窗。

## <a name="concurrent-sessions"></a>並行工作階段
Cloud Shell 可透過允許每個工作階段以個別 Bash 處理序的形式存在，來允許跨瀏覽器索引標籤進行多個並行工作階段。
如果要結束工作階段，請務必從每個工作階段視窗結束，因為每個處理序雖然是在相同的電腦上執行，但卻是獨立執行的。

## <a name="restart-cloud-shell"></a>重新啟動 Cloud Shell
![](media/recycle.png)
> [!WARNING]
> 重新啟動 Cloud Shell 將會重設電腦狀態，而所有未由檔案共用保存的檔案都將遺失。

* 按一下工具列上的 [重新啟動] 圖示，即可接收新的 Cloud Shell 環境。

## <a name="minimize--maximize-cloud-shell-window"></a>將 Cloud Shell 視窗最大化和最小化
![](media/minmax.png)
* 按一下視窗右上方的 [最小化] 圖示，即可將視窗隱藏。 再按一下 [Cloud Shell] 圖示，即可取消隱藏。
* 按一下 [最大化] 圖示，即可將視窗設成最大高度。 若要將視窗還原到先前的大小，請按一下 [還原]。

## <a name="copy-and-paste"></a>複製和貼上
* Windows：`Ctrl-insert` 進行複製，`Shift-insert` 即可貼上。 在下拉式清單上按一下滑鼠右鍵，也能進行複製/貼上。
  * FireFox/IE 可能無法正確支援剪貼簿權限。
* Mac 作業系統：`Cmd-c` 進行複製，`Cmd-v` 即可貼上。 在下拉式清單上按一下滑鼠右鍵，也能進行複製/貼上。

## <a name="resize-cloud-shell-window"></a>調整 Cloud Shell 視窗大小
* 按一下工具列上邊緣並向上或向下拖曳，即可調整 Cloud Shell 視窗大小。

## <a name="scrolling-text-display"></a>捲動文字顯示
* 使用您的滑鼠或觸控板來捲動終端機文字。

## <a name="exit-command"></a>結束命令
執行 `exit` 可終止作用中的工作階段。 此行為預設會在 20 分鐘後發生，不須進行互動。

## <a name="next-steps"></a>後續步驟
[Cloud Shell 快速入門](quickstart.md)

