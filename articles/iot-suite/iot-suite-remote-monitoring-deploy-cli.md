---
title: "部署 Java 遠端監視解決方案架構 - Azure | Microsoft Docs"
description: "本教學課程說明如何使用 CLI 來佈建遠端監視預先設定的解決方案 Java 微服務。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 10/15/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 18e754697a7f2108b3095313dc47a65029863262
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution-using-the-cli"></a>使用 CLI 來部署遠端監視預先設定的解決方案

本教學課程示範如何佈建遠端監視預先設定的解決方案。 您將使用 CLI 來部署解決方案。 您也可以使用 azureiotsuite.com 的 Web 型 UI 來部署解決方案，若要了解此選項，請參閱[部署遠端監視預先設定的解決方案](iot-suite-remote-monitoring-deploy.md)。

## <a name="prerequisites"></a>必要條件

若要部署遠端監視預先設定的解決方案，您需要一個作用中的 Azure 訂用帳戶。

如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

若要執行 CLI，您必須在本機電腦上安裝 [Node.js](https://nodejs.org/)。

## <a name="install-the-cli"></a>安裝 CLI

若要安裝 CLI，請在您的命令列環境中執行下列命令：

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>登入 CLI

您必須依照下列方式使用 CLI 來登入 Azure 訂用帳戶，才能部署預先設定的解決方案：

```cmd/sh
pcs login
```

請依照畫面上的指示來完成登入程序。

## <a name="deployment-options"></a>部署選項

部署預先設定的解決方案時，有數個可設定部署程序的選項：

| 選項 | 值 | 說明 |
| ------ | ------ | ----------- |
| SKU    | `basic`、`standard` | _basic_ 部署適用於測試和示範環境，它會將所有微服務部署至單一虛擬機器。 _standard_ 部署適用於生產環境，它會將微服務部署至多部虛擬機器。 |
| 執行階段 | `dotnet`、`java` | 選取微服務的語言實作。 |

## <a name="deploy-the-preconfigured-solution"></a>部署預先設定的解決方案

### <a name="example-deploy-net-version"></a>範例：部署 .NET 版本

以下範例說明如何部署基本 .NET 版的遠端監視預先設定解決方案：

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>範例：部署 Java 版本

以下範例說明如何部署標準 Java 版的遠端監視預先設定解決方案：

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>pcs 命令選項

當您執行 `pcs` 命令來部署解決方案時，系統會要求您提供：

- 您解決方案的名稱。 此名稱必須是唯一的。
- 要使用的 Azure 訂用帳戶。
- 一個位置。
- 裝載微服務之虛擬機器帳戶的認證。 您可以使用這些認證來存取虛擬機器以進行疑難排解。

當 `pcs` 命令完成時，會顯示新的預先設定解決方案部署 URL。 `pcs` 命令也會建立一個檔案 `{deployment-name}-output.json`，其中含有額外的資訊，例如為您佈建的「IoT 中樞」名稱。

如需有關命令列參數的詳細資訊，請執行：

```cmd/sh
pcs -h
```

如需有關 CLI 的詳細資訊，請參閱 [如何使用 CLI](https://github.com/Azure/pcs-cli/blob/master/README.md) \(英文\)。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 設定預先設定的解決方案
> * 部署預先設定的解決方案
> * 登入預先設定的解決方案

既然您已部署遠端監視解決方案，下一步便是[探索解決方案儀表板的功能](./iot-suite-remote-monitoring-deploy.md)。

<!-- Next tutorials in the sequence -->