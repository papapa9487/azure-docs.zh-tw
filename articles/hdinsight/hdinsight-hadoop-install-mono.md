---
title: "在 HDInsight 上安裝或更新 Mono - Azure | Microsoft Docs"
description: "了解如何搭配 HDInsight 叢集使用特定版本的 Mono。 Mono 是用於在以 Linux 為基礎的 HDInsight 叢集上執行 .NET 應用程式。"
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/31/2017
ms.author: larryfr
ms.custom: hdinsightactive
ms.openlocfilehash: fd284542e1de65f323f1e3a092689f847e025be6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="install-or-update-mono-on-hdinsight"></a>在 HDInsight 上安裝或更新 Mono

了解如何在 HDInsight 3.4 或更新版本上安裝特定版本的 [Mono](https://www.mono-project.com)。

Mono 安裝在 HDInsight 3.4 和更新版本，並用來執行 .NET 應用程式。 如需每個 HDInsight 版本包含之 Mono 版本的相關資訊，請參閱 [HDInsight 元件版本設定](hdinsight-component-versioning.md)。 若要在叢集上安裝不同版本，請使用此文件中的指令碼動作。 

## <a name="how-it-works"></a>運作方式

此指令碼接受下列參數︰

* __Mono 版本號碼__：要安裝的 Mono 版本。 版本必須可從 [https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/](https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/) 取得。

指令碼會安裝下列 Mono 套件︰

* __mono-complete__

* __ca-certificates-mono__

## <a name="the-script"></a>指令碼

__指令碼位置__：[https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash](https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash)

__需求__：

* 指令碼必須套用於__前端節點__和__背景工作節點__。

## <a name="to-use-the-script"></a>使用指令碼

如需如何搭配 HDInsight 使用此指令碼的資訊，請參閱[使用指令碼動作來自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)文件。 您可以透過 Azure 入口網站、Azure PowerShell 或 Azure CLI 來使用指令碼。

遵循指令碼動作文件時，請使用下列 URI：

    https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash

> [!NOTE]
> 使用此指令碼設定 HDInsight 時，請將指令碼標示為 __Persisted__。 此設定可讓 HDInsight 將指令碼套用到透過調整作業所新增的背景工作節點。


## <a name="next-steps"></a>後續步驟

您已了解如何在 HDInsight 上進行升級或安裝特定版本的 Mono。 如需在 HDInsight 上搭配 Mono 使用 .NET 應用程式的詳細資訊，請參閱下列文件：

* [在 HDInsight 上使用 .NET 進行串流 MapReduce](hdinsight-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [在 HDInsight 上搭配 Hive 與 Pig 使用 .NET](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)
* [在 HDInsight 上使用 Storm 開發 C# 方案](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [將 .NET 方案移轉至以 Linux 為基礎的 HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md)

如需使用指令碼動作的詳細資訊，請參閱[使用指令碼動作自訂以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-customize-cluster-linux.md)