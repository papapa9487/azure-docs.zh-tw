---
title: "Azure Machine Learning 預覽功能的概念性概觀 | Microsoft Docs"
description: "Azure Machine Learning 預覽功能 (例如訂用帳戶、帳戶、工作區、專案等) 的概念性概觀。"
services: machine-learning
author: serinakaye
ms.author: serinak
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: 3d4ba2ca6f7adc8b51030c02d9e9eeb2b9995bb4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning---concepts"></a>Azure Machine Learning - 概念

本文會定義並說明您必須知道才能使用 Azure Machine Learning 的概念。 

![概念的階層架構](media/overview-general-concepts/hierarchy.png)

- **訂用帳戶：**Azure 訂用帳戶可授與您 Azure 資源的存取權。 Azure Machine Learning 會與計算、儲存體以及許多其他 Azure 資源和服務緊密整合，因此 Workbench 會要求每位使用者都能夠存取有效的 Azure 訂用帳戶。 使用者也必須擁有該訂用帳戶的足夠權限以便建立資源。


- **測試帳戶：**測試帳戶是 Azure ML 所需的 Azure 資源，也是用來向您收費的媒介。 此帳戶會包含您的工作區，您的工作區又會包含專案。 您可以在測試帳戶中新增多名使用者 (稱為「基座」)。 您必須能夠存取測試帳戶才能使用 Azure ML Workbench 來執行實驗。 


- **模型管理帳戶：**模型管理帳戶也是 Azure ML 在管理模型時所需的 Azure 資源。 您可以使用此帳戶來註冊模型和資訊清單、建置容器化的 Web 服務，並將服務部署在本機或雲端中。 此帳戶也是 Azure ML 的另一個收費媒介。


- **工作區：**工作區是用來在 Azure ML 中進行共用和共同作業的主要元件。 專案會分組到工作區內。 工作區接著可與多個已新增至測試帳戶的使用者共用。


- **專案：**在 Azure Machine Learning 中，專案是用來容納為解決問題所進行之所有工作的邏輯容器。 專案會對應到本機磁碟上的單一檔案資料夾，您可以在其中新增任何檔案或子資料夾。 專案可選擇性地與 Git 存放庫相關聯，以便進行原始檔控制和共同作業。  

- **實驗：**在 Azure ML 中，實驗是一或多個可以從單一進入點執行的原始程式碼檔案。 實驗可以包含資料擷取、特徵設計、模型訓練或模型評估等工作。 目前，Azure ML 只支援 Python 或 PySpark 實驗。


- **模型：**在 Azure Machine Learning 中，模型是指機器學習實驗的產物。 它們是在正確套用至資料時會產生預測值的配方。 模型可以部署至測試或生產環境，並用於對新資料評分。 在部署至生產環境後，您便可以監視模型的效能與資料漂移，並視需要加以重新訓練。 

- **計算目標：**計算目標是您設定用來執行實驗的計算資源。 目標可以是本機電腦 (Windows 或 macOS)、在本機電腦上或在 Azure 的 Linux VM 中執行的 Docker 容器，也可以是 HDInsight Spark 叢集。


- **執行：**測試服務會將執行定義為在計算目標中執行實驗的期限。 Azure ML 會自動擷取每個執行的資訊，並以執行歷程記錄的形式顯示特定實驗的整個歷程記錄。

- **環境：**在 Azure Machine Learning 中，環境代表的是用來部署及管理模型的特定計算資源。 根據內容和設定，環境可以是本機電腦、Azure 上的 Linux VM，也可以是在 Azure Container Service 中執行的 Kubernetes 叢集。 您的模型會裝載在 (於這些環境中執行的) Docker 容器中，並顯示為 REST API 端點。


- **模型管理：**可讓您將模型部署成 Web 服務、管理各種模型版本，以及監視其效能和計量。 受管理的模型會向「Azure Machine Learning 模型管理」帳戶註冊。

- **資訊清單：**當模型管理系統將模型部署到生產環境時，它所包含的資訊清單中會有模型、相依性、計分指令碼、資料範例和結構描述。 此資訊清單是用來建立 Docker 容器映像的配方。 您可以使用模型管理來自動產生資訊清單、建立不同的版本，並管理這些資訊清單。 


- **映像：**您可以使用資訊清單來產生 (和重新產生) Docker 映像。 容器化的 Docker 映像可彈性地在雲端、本機機器或 IoT 裝置上執行。 映像各自獨立，並且會包含在模型中對新資料評分所需的所有相依性。 

- **服務：**模型管理可讓您將模型部署為 Web 服務。 系統會將 Web 服務的邏輯和相依性封裝到映像內。 每個 Web 服務都是一組容器，這些容器會以已可向給定 URL 提供要求的映像為基礎。 系統會將 Web 服務計算為單一部署。
