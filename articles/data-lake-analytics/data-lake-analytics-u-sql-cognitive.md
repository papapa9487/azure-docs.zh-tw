---
title: "使用 Azure Data Lake Analytics 中的 U-SQL 辨識功能| Microsoft Docs"
description: "了解如何使用 U-SQL 中辨識功能的智慧"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: ec48a07af0aba78f2e508bad232f34102f0c2073
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2017
---
# <a name="tutorial-get-started-with-the-cognitive-capabilities-of-u-sql"></a>教學課程︰開始使用 U-SQL 的辨識功能

## <a name="overview"></a>概觀
U-SQL 的辨識功能讓開發人員可以在其公司的巨量資料程式中使用 put 智慧。 

下列認知功能可供使用：
* 映像：偵測字體
* 映像：偵測情緒
* 映像：偵測物件 (標記)
* 映像：OCR (光學字元辨識)
* 文字：關鍵片語擷取
* 文字：情感分析

## <a name="how-to-use-cognitive-in-your-u-sql-script"></a>如何在 U-SQL 指令碼中使用認知

整個程序簡述如下︰

* 使用 REFERENCE ASSEMBLY 陳述式啟用 U-SQL 指令碼的辨識功能
* 使用 Cognitive UDO 在輸入資料列集中使用 PROCESS，來產生輸出資料列集

### <a name="detecting-objects-in-images"></a>在映像中偵測物件

下列範例說明如何使用認知功能來偵測影像中的物件。

```
REFERENCE ASSEMBLY ImageCommon;
REFERENCE ASSEMBLY FaceSdk;
REFERENCE ASSEMBLY ImageEmotion;
REFERENCE ASSEMBLY ImageTagging;
REFERENCE ASSEMBLY ImageOcr;

// Get the image data

@imgs =
    EXTRACT 
        FileName string, 
        ImgData byte[]
    FROM @"/usqlext/samples/cognition/{FileName}.jpg"
    USING new Cognition.Vision.ImageExtractor();

//  Extract the number of objects on each image and tag them 

@tags =
    PROCESS @imgs 
    PRODUCE FileName,
            NumObjects int,
            Tags SQL.MAP<string, float?>
    READONLY FileName
    USING new Cognition.Vision.ImageTagger();

@tags_serialized =
    SELECT FileName,
           NumObjects,
           String.Join(";", Tags.Select(x => String.Format("{0}:{1}", x.Key, x.Value))) AS TagsString
    FROM @tags;

OUTPUT @tags_serialized
    TO "/tags.csv"
    USING Outputters.Csv();
```
如需更多範例，請參閱**後續步驟**一節中的 **U-SQL/認知範例**。

## <a name="next-steps"></a>後續步驟
* [U-SQL/認知範例](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [使用適用於 Visual Studio 的資料湖工具開發 U-SQL 指令碼](data-lake-analytics-data-lake-tools-get-started.md)
* [針對 Azure 資料湖分析工作使用 U-SQL 視窗函式](data-lake-analytics-use-window-functions.md)
