---
title: "Visual Studio 適用的 Azure 串流分析工具安裝指示 | Microsoft Docs"
description: "Visual Studio 適用的 Azure 串流分析工具安裝指示"
keywords: visual studio
documentationcenter: 
services: stream-analytics
author: su-jie
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/19/2017
ms.author: sujie
ms.openlocfilehash: 80ce672ae91231e432f7ac9da49df29bb03efeca
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2017
---
# <a name="installation-instructions-for-stream-analytics-tools-for-visual-studio"></a>Visual Studio 適用的串流分析工具安裝指示
Azure 串流分析工具現在支援 Visual Studio 2017、2015 和 2013。 本文件介紹如何安裝和解除安裝這些工具。

了解如何使用 [Visual Studio 適用的串流分析工具](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio)。

## <a name="install"></a>Install
### <a name="visual-studio-2017"></a>Visual Studio 2017
* 下載 [Visual Studio 2017 (15.3 或更新版本)](https://www.visualstudio.com/)。 支援 Enterprise (Ultimate/Premium)、Professional 和 Community 版本。 不支援 Express 版本。 
* 串流分析工具在 Visual Studio 2017 中，屬於 **Azure 開發**與**資料儲存和處理**工作負載的一部分。 安裝 Visual Studio 時，請啟用其中一個工作負載。

啟用**資料儲存和處理**工作負載，如下所示：

![資料儲存和處理工作負載](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-01.png)

啟用 **Azure 開發**工作負載，如下所示：

![Azure 開發工作負載](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013、2015
* 安裝 Visual Studio 2015 或 Visual Studio 2013 Update 4。 支援 Enterprise (Ultimate/Premium)、Professional 和 Community 版本。 不支援 Express 版本。 
* 使用 [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx) 安裝 Microsoft Azure SDK for .NET 2.7.1 版或更新版本。
* 安裝 [Visual Studio 適用的 Azure 串流分析工具](http://aka.ms/asatoolsvs)。



## <a name="update"></a>更新

### <a name="visual-studio-2017"></a>Visual Studio 2017
新版本提醒會顯示在 Visual Studio 通知中。 

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013、2015
已安裝之 Visual Studio 適用的串流分析工具會自動檢查新版本。 請依照快顯視窗中的指示來安裝最新的版本。 


## <a name="uninstall"></a>解除安裝

### <a name="visual-studio-2017"></a>Visual Studio 2017
按兩下 Visual Studio 安裝程式，然後選取 [修改]。 從 [資料儲存和處理] 或 [Azure 開發] 工作負載取消核取 [Azure Data Lake 和串流分析工作] 核取方塊。

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013、2015
移至控制台，並解除安裝 **Visual Studio 適用的 Microsoft Azure Data Lake 和串流分析工具**。





