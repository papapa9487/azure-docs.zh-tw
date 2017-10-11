---
title: "分析平台：Apache Storm 與串流分析之間的比較 | Microsoft Docs"
description: "本文將使用 Apache Storm 與串流分析之間的比較，提供您選擇雲端分析平台的指引。 了解功能和差異。"
keywords: "分析平台, 分析平台, 雲端分析平台, storm 比較"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: b9aac017-9866-4d0a-b98f-6f03881e9339
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/27/2017
ms.author: samacha
ms.openlocfilehash: 97044cb5d7b0b3fcb3b85328df618a265bc59b61
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="choosing-a-streaming-analytics-platform-comparing-apache-storm-and-azure-stream-analytics"></a>選擇串流分析平台：比較 Apache Storm 與 Azure 串流分析
Azure 提供多種解決方案來分析串流資料：[Azure 串流分析](https://docs.microsoft.com/azure/stream-analytics/)和 [Azure HDInsight 上的 Apache Storm](https://azure.microsoft.com/services/hdinsight/apache-storm/)。 這兩個分析平台提供 PaaS 解決方案的優點。 但這兩個平台的功能及設定和管理方式有一些顯著的差異。 

本文將功能對照比較，協助您選擇 Apache Storm 或 Azure 串流分析作為雲端分析平台。 

## <a name="general-features"></a>一般功能

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 串流分析</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm on HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>開放原始碼？</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
不可以。 Azure 串流分析 Microsoft 專屬服務。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
是。 Apache Storm 是 Apache 授權技術。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Microsoft 支援服務？</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
是 </p>
            </td>
            <td width="246" valign="top">
                <p>
是 </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>硬體需求</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
無。 Azure 串流分析是一種 Azure 服務。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
無。 Apache Storm 是一種 Azure 服務。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>最上層單位</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
使用者可部署和監視串流作業。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
使用者可部署和監視整個叢集，該叢集可裝載多個 Storm 作業，以及其他工作負載 (包括批次)。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>價格</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
依處理的資料量和作業執行每小時所需的串流單位數量來計價。 
                </p>
                    <p>如需詳細資訊，請參閱<a href="http://azure.microsoft.com/pricing/details/stream-analytics/">串流分析定價</a>。</p>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
購買的單位是根據叢集，收費是根據叢集執行的時間，與部署的作業無關。
                </p>
                <p>
如需詳細資訊，請參閱 <a href="http://azure.microsoft.com/pricing/details/hdinsight/">HDInsight 定價</a>。
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="authoring"></a>編寫

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 串流分析</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm on HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>功能：SQL DSL？</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
是。 串流分析提供類似 SQL 的語言來建立轉換。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
否。 使用者可用 Java 或 C# 來撰寫程式碼，或使用 Trident API。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>功能：時態性運算子？</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
預設支援時間範圍的彙總和時態性聯結。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
時態性運算子必須由使用者實作。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>開發經驗</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
使用者可以透過 Azure 入口網站，使用衍生自即時資料流的範例資料來建立、偵錯和監視作業。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
使用 .NET 使用者可以透過 Visual Studio 來開發、偵錯和監視。 使用 Java 或其他語言的使用者可以使用自己選擇的 IDE。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>偵錯支援</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
有基本的作業狀態和作業記錄可協助偵錯。 串流分析目前不允許使用者指定將什麼內容或多少內容包括在記錄中 (亦即，詳細資訊模式)。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
有詳細的記錄可用。 使用可以在 Visual Studio 中存取記錄，或登入叢集並直接存取記錄。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>使用自訂程式碼來擴充？</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
部分支援 JavaScript UDF。 如需詳細資訊，請參閱 <a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-javascript-user-defined-functions">JavaScript UDF 整合</a>。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
是。 使用者可以使用 C#、Java 或 Strom 支援的其他任何語言來撰寫自訂程式碼。
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="data-sources-inputs-and-outputs"></a>資料來源 (輸入) 和輸出 ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 串流分析</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm on HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>輸入資料來源</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Azure 事件中樞和 Azure Blob 儲存體。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
有適用於 Azure 事件中樞、Microsoft Azure 服務匯流排、Kafka 等的連接器可用。 使用者可以使用自訂程式碼來建立其他連接器。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>輸入資料格式</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Avro、JSON、CSV </p>
            </td>
            <td width="246" valign="top">
                <p>
使用者可以使用自訂程式碼來實作任何格式。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>輸出</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
一個串流作業可以有多個輸出。 支援的輸出包括 Azure 事件中樞、Azure Blob 儲存體、Azure 資料表儲存體、Azure SQL DB 和 Power BI。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Storm 在一個拓撲中支援多個輸出，每個輸出可以有自訂邏輯來進行下游處理。 Storm 包含適用於 Power BI、Azure 事件中樞、Azure Blob 儲存體、Azure Cosmos DB、SQL 和 HBase 的連接器。 使用者可以使用自訂程式碼來建立其他連接器。    
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>資料編碼格式</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
資料必須以 UTF-8 製作格式。
                </p>
            </td>   
            <td width="246" valign="top">
                <p>
使用者可以使用自訂程式碼來實作任何資料編碼。
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="management-and-operations"></a>管理和作業 ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 串流分析</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm on HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>作業部署模型</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Azure 入口網站、PowerShell 和 REST API。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Azure 入口網站、PowerShell、Visual Studio 和 REST API。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>監視 (統計資料、計數器等)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
使用者可使用 Azure 入口網站和 REST API 實作監視。 使用者也可以設定 Azure 警示。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
使用者可使用 Storm UI 和 REST API 實作監視。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>延展性</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
延展性取決於每個作業的串流單位 (SU) 數目。 每個串流單位每秒最多可處理 1 MB，最多可使用 50 個單位。 如需詳細資訊，請參閱<a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-jobs">調整規模以增加輸送量</a>。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
延展性取決於 HDInsight Storm 叢集中的節點數目。 節點數目上限取決於使用者的 Azure 配額。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>資料處理限制</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
使用者可以增減串流單位數目來加強資料處理或將成本最佳化，上限為 1 GB/秒。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
使用者可以將叢集相應增加或相應減少。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>停止/繼續</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
停止並從上次的停止處繼續。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
停止並根據浮水印從上次的停止處繼續。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>服務和架構更新</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
可不停機自動修補。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
可不停機自動修補。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>透過具有 SLA 保證所保障的高可用性服務提供商務持續性</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <ul>
                <li>SLA 保證運作時間達 99.9%</li>
                <li>可在失敗時自動復原</li>
                <li>內建復原可設定狀態的時態性運算子</li>
                </ul>
            </td>
            <td width="246" valign="top">
                <p>
SLA 保證 Storm 叢集的運作時間達 99.9%。 
                </p>
                <p>
Apache Storm 是容錯的串流平台。 不過，使用者有責任確保串流作業執行不會中斷。
                </p>
            </td>
        </tr>
    </tbody>
</table>

## <a name="advanced-features"></a>進階功能 ##

<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Azure 串流分析</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Apache Storm on HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>延遲傳入和失序事件處理</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
內建可設定的原則可以重新排序事件、捨棄事件或調整事件時間。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
使用者必須實作邏輯來處理這種情節。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>參考資料</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
可從 Azure Blob 儲存體取得參考資料，記憶體內部快取最大為 100 MB。 參考資料由服務重新整理。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
資料大小沒有限制。 有適用於 HBase、Azure Cosmos DB、SQL Server 和 Azure 的連接器可用。 使用者可以使用自訂程式碼來建立其他連接器。 必須使用自訂程式碼來重新整理參考資料。
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>與 Machine Learning 整合</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
在建立作業期間，已發佈的 Azure Machine Learning 模型可設定為函式。 如需詳細資訊，請參閱<a href="https://docs.microsoft.com/azure/stream-analytics/stream-analytics-scale-with-machine-learning-functions">為 Machine Learning 函式進行規模調整</a>。
                </p>
            </td>
            <td width="246" valign="top">
                <p>
透過 Storm Bolt 提供。
                </p>
            </td>
        </tr>
    </tbody>
</table>
