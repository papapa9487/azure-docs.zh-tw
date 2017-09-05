---
title: "開始使用 Azure Service Fabric CLI (sfctl)"
description: "了解如何使用 Azure Service Fabric CLI。 了解如何連線到叢集，以及如何管理應用程式。"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 2faca2887f25b45d833dea7b2259277466290670
ms.contentlocale: zh-tw
ms.lasthandoff: 08/29/2017

---
# <a name="azure-service-fabric-command-line"></a>Azure Service Fabric 命令列

Azure Service Fabric CLI (sfctl) 是一個命令列公用程式，用於操作和管理 Azure Service Fabric 實體。 Sfctl 可以搭配 Windows 或 Linux 叢集使用。 Sfctl 可在支援 python 的任何平台上執行。

## <a name="prerequisites"></a>必要條件

安裝之前，請確定您的環境已安裝 python 和 pip。 如需詳細資訊，請參閱 [pip 快速入門文件](https://pip.pypa.io/en/latest/quickstart/)和正式的 [python 安裝文件](https://wiki.python.org/moin/BeginnersGuide/Download)。

雖然 python 2.7 和 3.6 都受支援，但建議使用 python 3.6。 下一節說明如何安裝所有必要條件和 CLI。

## <a name="install-pip-python-and-sfctl"></a>安裝 pip、python 和 sfctl

雖然有許多方法可在您的平台上安裝 pip 和 python，但下列步驟可針對主要作業系統快速設定 python 3.6 和 pip：

### <a name="windows"></a>Windows

對於 Windows 10、Server 2016 和 Server 2012R2，您可以使用標準官方安裝指示。 Python 安裝程式預設也會安裝 pip。

- 瀏覽至正式 [python 下載頁面](https://www.python.org/downloads/)並下載最新版的 python 3.6
- 啟動安裝程式
- 選取位於 `Add Python 3.6 to PATH` 提示底部的選項
- 選取 `Install Now`
- 完成安裝

您現在應該可開啟新的命令視窗，並取得 python 和 pip 的版本：

```bat
python --version
pip --version
```

然後執行下列命令以安裝 Service Fabric CLI

```
pip install sfctl
sfctl -h
```

### <a name="ubuntu"></a>Ubuntu

對於 Ubuntu 16.04 Desktop，您可以使用第三方 PPA 來安裝 python 3.6：

從終端機執行下列命令：

```bash
sudo add-apt-repository ppa:jonathonf/python-3.6
sudo apt-get update
sudo apt-get install python3.6
sudo apt-get install python3-pip
```

然後，若要安裝 sfctl 以便您安裝 python 3.6，請執行下列命令：

```bash
python3.6 -m pip install sfctl
sfctl -h
```

這些步驟不會影響系統安裝的 python 3.5 和 2.7。 除非您很熟悉 Ubuntu，否則請勿嘗試修改這些安裝。

### <a name="macos"></a>MacOS

對於 MacOS，建議使用 [HomeBrew 套件管理員](https://brew.sh)。 執行下列命令，以安裝 HomeBrew (如果尚未安裝)：

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

然後從終端機安裝 python 3.6、pip 和 sfctl

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

這些步驟不會修改系統安裝的 python 2.7。

## <a name="cli-syntax"></a>CLI 語法

命令前面一律會加上 `sfctl`。 如需有關所有命令的一般資訊，請使用 `sfctl -h`。 如需單一命令的說明，請使用 `sfctl <command> -h`。

命令會遵循可重複的結構，而命令的目標前面會加上動詞命令或動作：

```azurecli
sfctl <object> <action>
```

在此範例中，`<object>` 是 `<action>` 的目標。

## <a name="select-a-cluster"></a>選取叢集

在您執行任何作業之前，必須選取要連線的叢集。 例如，執行下列命令以選取並連線到名為 `testcluster.com` 的叢集。

> [!WARNING]
> 請勿於生產環境中使用不安全的 Service Fabric 叢集。

```azurecli
sfctl cluster select --endpoint http://testcluster.com:19080
```

叢集端點前面必須加上 `http` 或 `https`。 它必須包含 HTTP 閘道的連接埠。 此連接埠和位址等同於 Service Fabric Explorer URL。

對於使用憑證保護的叢集，您可以指定 PEM 編碼的憑證。 此憑證可以指定為單一檔案或憑證和金鑰組。

```azurecli
sfctl cluster select --endpoint https://testsecurecluster.com:19080 --pem ./client.pem
```

如需詳細資訊，請參閱[連線到 Azure Service Fabric 叢集](service-fabric-connect-to-secure-cluster.md)。

## <a name="basic-operations"></a>基本作業

叢集連線資訊會跨多個 sfctl 工作階段保存。 選取 Service Fabric 叢集後，您可以在此叢集上執行任何 Service Fabric 命令。

例如，若要取得 Service Fabric 叢集健康情況，請使用下列命令：

```azurecli
sfctl cluster health
```

此命令會導致下列輸出：

```json
{
  "aggregatedHealthState": "Ok",
  "applicationHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "name": "fabric:/System"
    }
  ],
  "healthEvents": [],
  "nodeHealthStates": [
    {
      "aggregatedHealthState": "Ok",
      "id": {
        "id": "66aa824a642124089ee474b398d06a57"
      },
      "name": "_Test_0"
    }
  ],
  "unhealthyEvaluations": []
}
```

## <a name="tips-and-troubleshooting"></a>秘訣與疑難排解

用於解決一般問題的某些建議和秘訣。

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>將憑證從 PFX 轉換為 PEM 格式

Service Fabric CLI 支援以 PEM (.pem 副檔名) 檔案作為用戶端憑證。 如果您從 Windows 使用 PFX 檔案，則必須將這些憑證轉換成 PEM 格式。 若要將 PFX 檔案轉換為 PEM 檔案，請使用下列命令︰

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

如需詳細資訊，請參閱 [OpenSSL 文件](https://www.openssl.org/docs/)。

### <a name="connection-issues"></a>連接問題

某些作業可能會產生下列訊息：

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

確認指定的叢集端點可以使用且正在接聽。 另外確認 Service Fabric Explorer 的 UI 可在該主機和連接埠上使用。 若要更新端點，請使用 `sfctl cluster select`。

### <a name="detailed-logs"></a>詳細記錄

當您偵錯或回報問題時，詳細記錄通常很有幫助。 有一個全域 `--debug` 旗標可增加記錄檔的詳細程度。

### <a name="command-help-and-syntax"></a>命令的說明和語法

如需特定命令或一組命令的說明，請使用 `-h` 旗標：

```azurecli
sfctl application -h
```

另一個範例：

```azurecli
sfctl application create -h
```

## <a name="next-steps"></a>後續步驟

* [透過 Azure Service Fabric CLI 部署應用程式](service-fabric-application-lifecycle-sfctl.md)
* [在 Linux 上開始使用 Service Fabric](service-fabric-get-started-linux.md)

