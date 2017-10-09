---
title: "支援可供 Azure Machine Learning 資料準備使用的偵測器 | Microsoft Docs"
description: "本文件提供 Azure ML 資料準備可使用的完整偵測器清單"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: bc14c051fb0f518b1cff2236a61d24cb052700f0
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="supported-inspectors-for-this-preview"></a>此預覽支援的偵測器
本文件概述此預覽中可用的偵測器集合。

## <a name="the-halo-effect"></a>光暈效果 
某些偵測器支援光暈效果，這種效果會使用兩個不同的色彩來立即顯示轉換的視覺化方式變更。 灰色表示先前的值早於最新的轉換，藍色顯示目前的值。 這個效果可以選擇啟用/停用。

## <a name="graphical-filtering"></a>圖形化篩選 
某些偵測器支援使用偵測器作為編輯器來篩選資料。 這樣做牽涉到選取圖形化元素，然後使用偵測器視窗右上方的工具列，來篩選選取的值。 

## <a name="column-statistics"></a>資料行統計資料
針對數值資料行，這個偵測器會提供關於資料行的各種不同統計資料。 統計資料包括：
- 最小值
- 較低的四分位數
- 中間值
- 較高的四分位數
- 最大值
- 平均值
- 標準差


### <a name="options"></a>選項 
- None

## <a name="histogram"></a>長條圖 
計算並顯示單一數值資料行的長條圖。 使用 Scott 規則計算預設的貯體數目，此規則會透過選項覆寫。
這個偵測器支援光暈效果。


### <a name="options"></a>選項
- 貯體數目最小值 (即使在檢查預設貯體時也會套用)
- 預設貯體數目 (Scott 的規則) 
- 顯示光暈
- 核心密度繪圖重疊 (高斯核心) 


### <a name="actions"></a>動作
這個偵測器支援透過貯體 (單一或多個選取貯體) 篩選，並且套用篩選，如先前所述。

## <a name="value-counts"></a>值計數
這個偵測器會顯示目前選取資料行的值頻率表。 預設顯示是針對前 6 名，限制可以變更為任何數字，或從最下層而非最上層計數。 這個偵測器支援光暈效果。

### <a name="options"></a>選項 
- 頂端數值數目
- 遞減
- 包含 Null/錯誤值
- 顯示光暈


### <a name="actions"></a>動作 
這個偵測器支援透過橫條 (單一或多個選取橫條) 篩選，並且套用篩選，如先前所述。

## <a name="box-plot"></a>盒狀圖 
數值資料行的盒鬚圖

### <a name="options"></a>選項 
- 依資料行分組

## <a name="scatter-plot"></a>散佈圖
兩個數值資料行的散佈圖，資料會基於效能考量向下取樣，取樣大小可以在選項中覆寫。

### <a name="options"></a>選項  
- X 軸資料行
- Y 軸資料行
- 取樣大小
- 依資料行分組


## <a name="time-series"></a>時間序列
X 軸上具有時間感知的折線圖

### <a name="options"></a>選項
- 日期資料行
- 數值資料行
- 取樣大小


### <a name="actions"></a>動作
這個偵測器支援透過按一下和拖曳選取的方法來選取圖形上的範圍，進行篩選。 完成選取之後，套用篩選，如先前所述。


## <a name="map"></a>對應 
具有繪製點的地圖，假設已指定經度和緯度。 必須先選取緯度。

### <a name="options"></a>選項
- 緯度資料行
- 經度資料行
- 叢集
- 依資料行分組


### <a name="actions"></a>動作
這個偵測器支援透過地圖上的點選取範圍進行篩選。 按下 Ctrl 鍵，然後按一下並拖曳滑鼠，在點周圍形成方形。 然後套用篩選，如先前所述。
就能快速調整地圖的大小，以顯示所有可能的點，並且不再需要按下地圖左側的 **E**。

