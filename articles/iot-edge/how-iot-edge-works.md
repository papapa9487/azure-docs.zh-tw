---
title: "什麼是 Azure IoT Edge | Microsoft Docs"
description: "Azure IoT Edge 服務的概觀"
services: iot-Edge
documentationcenter: 
author: kgremban
manager: timlt
editor: chipalost
ms.assetid: 
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: kgremban
ms.custom: 
ms.openlocfilehash: 5f69041572729d1458a22a855128639056d61586
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2017
---
# <a name="what-is-azure-iot-edge---preview"></a>什麼是 Azure IoT Edge - 預覽

Azure IoT Edge 會將雲端分析和自訂商務邏輯移至裝置，讓您的組織可以專注於商業深入解析，而不是資料管理。 設定 IoT 軟體、透過標準容器將它部署到裝置，並從雲端進行全面監視，讓您實現真正的解決方案。

分析可促進 IoT 解決方案的商務價值，但並非所有分析都必須在雲端進行。 如果您希望裝置能盡快回應緊急事件，您可以在裝置本身執行異常偵測。 同樣地，如果您想要降低頻寬成本，並避免傳送數 TB 的原始資料，您可以在本機執行資料清除和彙總。 然後將深入解析傳送至雲端。 

Azure IoT Edge 是由三個元件組成：
* IoT Edge 模組是執行 Azure 服務、第三方服務或自有程式碼的容器。 這類模組會部署到 IoT Edge 裝置，並在這些裝置本機上執行。 
* IoT Edge 執行階段會在每個 IoT Edge 裝置上執行，並管理部署到每個裝置的模組。 
* 以雲端為基礎的介面可讓您在遠端監視及管理 IoT Edge 裝置。

## <a name="iot-edge-modules"></a>IoT Edge 模組

IoT Edge 模組是執行的單位，目前實作為 Docker 相容容器，可在終端執行您的商務邏輯。 您可以將多個模組設定為彼此通訊，並建立資料處理的管道。 您可以開發自訂模組，或將特定 Azure 服務封裝為可離線和在終端提供深入解析的模組。 

### <a name="artificial-intelligence-on-the-edge"></a>終端人工智慧

Azure IoT Edge 可讓您部署複雜的事件處理、機器學習、影像辨識和其他高價值的 AI，而不需要在公司內部撰寫它。 Azure 服務 (例如 Azure Functions、Azure Stream Analytics 和 Azure Machine Learning) 全都可以透過 Azure IoT Edge 在內部部署環境執行；不過您不限於使用 Azure 服務。 任何人都能夠建立 AI 模組，並讓它們可供社群使用。 

### <a name="bring-your-own-code"></a>自備程式碼

當您要將自己的程式碼部署到裝置時，Azure IoT Edge 也支援該功能。 Azure IoT Edge 採用與其他 Azure IoT 服務相同的程式設計模型。 相同的程式碼可以在裝置上或在雲端中執行。 Azure IoT Edge 支援 Linux 和 Windows，因此您可以在您所選的平台撰寫程式碼。 它可支援 Java、.NET Core 2.0、Node.js、C 及 Python，所以您的開發人員可以用他們已知的語言撰寫程式碼，並使用現有的商務邏輯，而不需從頭撰寫。

## <a name="iot-edge-runtime"></a>IoT Edge 執行階段

Azure IoT Edge 執行階段可在 IoT Edge 裝置上啟用自訂和雲端邏輯。 它位於 IoT Edge 裝置上並可執行管理和通訊作業。 此執行階段會執行數個功能：

* 在裝置上安裝和更新工作負載。
* 在裝置上維護 Azure IoT Edge 安全性標準。
* 確保 IoT Edge 模組一律執行中。
* 將模組健康情況報告至雲端，以便進行遠端監控。
* 促進下游分葉裝置與 IoT Edge 裝置之間的通訊。
* 促進 IoT Edge 裝置上模組之間的通訊。
* 促進 IoT Edge 裝置與雲端之間的通訊。

![IoT Edge 執行階段會將深入解析和報告傳送至 IoT 中樞][1]

您可以全然決定如何使用 Azure IoT Edge 裝置。 此執行階段通常用於將 AI 部署至閘道，以便彙總及處理多個其他內部部署裝置上的資料，不過這只是選項之一。 不論分葉裝置是連線到閘道或直接連到雲端，分葉裝置也可能是 Azure IoT Edge 裝置。

Azure IoT Edge 執行階段可在大量 IoT 裝置上執行，以便用各種不同的方式使用執行階段。 它可支援 Linux 和 Windows 作業系統，以及擷取硬體詳細資料。 如果您並未處理很多資料或相應增加為工業化伺服器，請使用小於 Raspberry Pi 3 的裝置，以執行資源密集的工作負載。

## <a name="iot-edge-cloud-interface"></a>IoT Edge 雲端介面

管理企業裝置的軟體生命週期很複雜。 管理數百萬部異質性 IoT 裝置的軟體生命週期更加困難。 您必須針對特定類型的裝置建立和設定工作負載、將工作負載大規模部署到您解決方案中的數百萬個裝置，並且進行監控以攔截任何異常裝置。 這些活動無法在每個裝置上進行，而必須大規模進行。

Azure IoT Edge 能夠與 Azure IoT 套件緊密整合，以針對您的解決方案需求提供一個控制平面。 雲端服務可讓使用者：

* 建立並設定要在特定類型的裝置上執行的工作負載。
* 將工作負載傳送到一組裝置。
* 監視在現場裝置上執行的工作負載。

![裝置的遙測、深入解析及動作會透過雲端協調][2]

## <a name="next-steps"></a>後續步驟

[在模擬裝置上部署 IoT Edge][lnk-quickstart]，試試看了解這些概念。

<!-- Images -->
[1]: ./media/how-iot-edge-works/runtime.png
[2]: ./media/how-iot-edge-works/cloud-interface.png

<!-- Links -->
[lnk-quickstart]: quickstart.md