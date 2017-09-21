---
title: "Azure Batch 在雲端執行大規模的平行運算解決方案 | Microsoft Docs"
description: "了解如何將 Azure Batch 服務用於大規模的平行工作負載和 HPC 工作負載 "
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 93e37d44-7585-495e-8491-312ed584ab79
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/05/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: a99f96db0c1e8bcd0cf29c564e5badf0eb728e56
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---
# <a name="run-intrinsically-parallel-workloads-with-batch"></a>使用 Batch 執行本質平行的工作負載

Azure Batch 是一項平台服務，可用於在雲端有效地執行大規模的平行和高效能運算 (HPC) 應用程式。 Azure Batch 可排程要在一組受管理的虛擬機器上執行的計算密集型工作，而且可以調整計算資源以符合工作的需求。

使用 Batch 時，您可以輕鬆定義用來大規模平行執行應用程式的 Azure 計算資源。 不需要手動建立、設定和管理 HPC 叢集、個別的虛擬機器、虛擬網路或複雜的作業和工作排程基礎結構。 Azure Batch 會為您自動執行或簡化這些工作。

## <a name="use-cases-for-batch"></a>Batch 的使用案例
Batch 是一項受管理的 Azure 服務，可用於批次處理或批次運算 -- 執行大量的類似工作以得到期望的結果。 定期處理、轉換和分析大量資料的組織最常使用批次運算。

Batch 很適合處理本質平行 (也稱為「超簡單平行」) 的應用程式和工作負載。 本質上平行的工作負載可輕易分割成多個工作，以在多部電腦上同時執行。

![平行工作][1]<br/>

常見使用這項技術處理的一些工作負載範例如下：

* 財務風險模型
* 氣候和水文資料分析
* 影像轉譯、分析和處理
* 媒體編碼及轉碼
* 基因序列分析
* 工程壓力分析
* 軟體測試

Batch 也可以執行平行計算 (最後加上歸納步驟)，以及執行其他更複雜的 HPC 工作負載，例如 [訊息傳遞介面 (MPI)](batch-mpi.md) 應用程式。

如需 Batch 與 Azure 中其他 HPC 解決方案選項的比較，請參閱 [HPC、Batch 和 Big Compute 解決方案](../virtual-machines/linux/high-performance-computing.md)。

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="scenario-scale-out-a-parallel-workload"></a>案例：相應放大平行工作負載
使用 Batch API 來與 Batch 服務互動的一個常見案例涉及在一組計算節點上相應放大本質平行工作，例如轉譯 3D 場景的影像。 例如，這組計算節點可能是您的「轉譯伺服器陣列」，提供數十、數百或甚至數千個核心來呈現作業。

下圖顯示一個常見的 Batch 工作流程，其中有一個用戶端應用程式或託管服務使用 Batch 執行平行工作負載。

![Batch 解決方案工作流程][2]

在這個常見的案例中，應用程式或服務執行下列步驟，在 Azure Batch 中處理計算工作負載：

1. 將**輸入檔**和處理這些檔案的**應用程式**上傳到您的 Azure 儲存體帳戶。 輸入檔可以是您的應用程式將會處理的任何資料，例如財務模型化資料或要轉碼的視訊檔案。 應用程式檔案可以是任何用於處理資料的應用程式，例如 3D 轉譯應用程式或媒體轉碼器。
2. 在 Batch 帳戶中建立計算節點的 Batch **集區** -- 這些節點是將執行工作的虛擬機器。 您需要指定屬性，例如[節點大小](../cloud-services/cloud-services-sizes-specs.md)、其作業系統，以及節點加入集區時要安裝的應用程式在 Azure 儲存體中的位置 (您在步驟 #1 中上傳的應用程式)。 您也可以設定集區來隨著工作所產生的工作負載而[自動調整](batch-automatic-scaling.md)。 自動調整功能可動態調整集區中的計算節點數目。
3. 建立 Batch **作業** 以在計算節點集區上執行工作負載。 當您建立作業時，您需要將它與 Batch 集區建立關聯。
4. 將 **工作** 加入至作業。 當您將工作加入至作業時，Batch 服務會自動排程工作在集區中的計算節點上執行。 每一項工作會使用您上傳的應用程式來處理輸入檔。
   
   * 4a. 工作執行之前，它可以將它要處理的資料 (輸入檔) 下載至它被指派的計算節點。 如果應用程式未安裝在節點上 (請參閱步驟 2#)，您可以從這裡下載它。 下載完成時，工作就會在它被指派的節點上執行。
5. 工作執行時，您可以查詢 Batch 來監視作業及其工作的進度。 用戶端應用程式或服務可透過 HTTPS 來與 Batch 服務進行通訊。 因為您可能會監視在數千個計算節點上執行的數千個工作，請務必[有效率地查詢 Batch 服務](batch-efficient-list-queries.md)。
6. 當工作完成時，它們可以將其輸出資料上傳至 Azure 儲存體。 您也可以直接從計算節點上的檔案系統擷取檔案。
7. 當您的監視偵測到作業中的工作已完成時，用戶端應用程式或服務可以下載輸出資料來進一步處理或評估。

請記住，這只是使用 Batch 的一種方式，此案例只描述它可用的幾項功能。 例如，您可以在每個計算節點上[以平行方式執行多項工作](batch-parallel-node-tasks.md)，也可以使用[作業準備和完成工作](batch-job-prep-release.md)來準備作業的節點，然後再清除。

## <a name="next-steps"></a>後續步驟
既然您已大致了解 Batch 服務，現在可以更深入探索服務，了解如何使用它來處理計算密集平行工作負載。

* 請參閱 [適用於開發人員的 Batch 功能概觀](batch-api-basics.md)，這是任何準備使用 Batch 的人員不可或缺的資訊。 本文包含 Batch 服務資源 (例如集區、節點、作業和工作) 的詳細資訊，以及在建置 Batch 應用程式時可使用的許多 API 功能。
* 了解可用來建置 Batch 解決方案的 [Batch API 和工具](batch-apis-tools.md)。
* [開始使用適用於 .NET 的 Azure Batch 程式庫](batch-dotnet-get-started.md) ，了解如何使用 C# 和 Batch .NET 程式庫，透過一般的批次工作流程來執行簡單的工作負載。 本文應該是您學習如何使用 Batch 服務的第一站。 另外還有 [Python 版本](batch-python-tutorial.md) 的教學課程。
* 下載 [GitHub 上的程式碼範例][github_samples]，看看 C# 和 Python 如何與 Batch 相互作用，以排程和處理範例工作負載。
* 查看 [Batch 學習路徑][learning_path]，了解您在學習使用 Batch 時可用的資源。


[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png

