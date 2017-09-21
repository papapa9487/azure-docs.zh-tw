---
title: "開始使用 Azure Service Fabric CLI"
description: "了解如何使用 Azure Service Fabric CLI。 了解如何連線到叢集，以及如何管理應用程式。"
services: service-fabric
author: samedder
manager: timlt
ms.service: service-fabric
ms.topic: get-started-article
ms.date: 08/22/2017
ms.author: edwardsa
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 851f04c8b5eee762ec43060f02c8b83f00c1782e
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---
# <a name="azure-service-fabric-cli"></a>Azure Service Fabric CLI

Azure Service Fabric 命令列介面 (CLI) 是一個命令列公用程式，用於操作和管理 Azure Service Fabric 實體。 Service Fabric CLI 可以搭配 Windows 或 Linux 叢集使用。 Service Fabric CLI 可在支援 Python 的任何平台上執行。

## <a name="prerequisites"></a>必要條件

安裝之前，請確定您的環境已安裝 Python 和 pip。 如需詳細資訊，請參閱 [pip 快速入門文件](https://pip.pypa.io/en/latest/quickstart/)和正式的 [Python 安裝文件](https://wiki.python.org/moin/BeginnersGuide/Download)。

雖然 Python 2.7 和 3.6 都能支援，但建議您使用 Python 3.6。 下一節說明如何安裝所有的必要條件和 CLI。

## <a name="install-pip-python-and-the-service-fabric-cli"></a>安裝 pip、Python 和 Service Fabric CLI

 有許多方法可在您的平台上安裝 pip 和 Python。 以下這些步驟可以使用 Python 3.6 與 pip 快速設定主要作業系統。

### <a name="windows"></a>Windows

針對 Windows 10、Windows Server 2016 和 Windows Server 2012 R2，請使用標準官方安裝指示。 Python 安裝程式預設也會安裝 pip。

1. 前往正式 [Python 下載頁面](https://www.python.org/downloads/)並下載最新版的 Python 3.6。

2. 開始安裝程式。

3. 在提示字元的底部，選取 [將 Python 3.6 新增至路徑]。

4. 選取 [立即安裝]，並完成安裝。

現在您可以開啟新的命令視窗，並取得 Python 和 pip 的版本。

```bat
python --version
pip --version
```

然後執行下列命令以安裝 Service Fabric CLI：

```
pip install sfctl
sfctl -h
```

### <a name="ubuntu"></a>Ubuntu

對於 Ubuntu 16.04 桌面，您可以使用第三方個人套件封存 (PPA) 來安裝 Python 3.6。

從終端機執行下列命令：

```bash
sudo add-apt-repository ppa:jonathonf/python-3.6
sudo apt-get update
sudo apt-get install python3.6
sudo apt-get install python3-pip
```

然後，若要安裝 Service Fabric CLI 以便您安裝 Python 3.6，請執行下列命令：

```bash
python3.6 -m pip install sfctl
sfctl -h
```

這些步驟不會影響系統安裝的 Python 3.5 和 2.7。 除非您很熟悉 Ubuntu，否則請勿嘗試修改這些安裝。

### <a name="macos"></a>MacOS

對於 MacOS，建議使用 [HomeBrew 套件管理員](https://brew.sh)。 如果尚未安裝 HomeBrew，可執行下列命令來加以安裝：

```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

然後從終端機安裝 Python 3.6、pip 和 Service Fabric CLI，方法是執行下列命令：

```bash
brew install python3
pip3 install sfctl
sfctl -h
```

這些步驟不會修改系統安裝的 Python 2.7。

## <a name="cli-syntax"></a>CLI 語法

命令前面一律會加上 `sfctl`。 如需有關所有可用命令的一般資訊，請使用 `sfctl -h`。 如需單一命令的說明，請使用 `sfctl <command> -h`。

命令會遵循可重複的結構，而命令的目標前面會加上動詞命令或動作。

```azurecli
sfctl <object> <action>
```

在此範例中，`<object>` 是 `<action>` 的目標。

## <a name="select-a-cluster"></a>選取叢集

在您執行任何作業之前，必須選取要連線的叢集。 例如，若要選取並連線到名為 `testcluster.com` 的叢集，請執行下列命令：

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

叢集連線資訊會跨多個 Service Fabric CLI 工作階段保存。 選取 Service Fabric 叢集後，您可以在此叢集上執行任何 Service Fabric 命令。

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

以下是用於解決一般問題的某些建議和秘訣。

### <a name="convert-a-certificate-from-pfx-to-pem-format"></a>將憑證從 PFX 轉換為 PEM 格式

Service Fabric CLI 支援以 PEM (.pem 副檔名) 檔案作為用戶端憑證。 如果您從 Windows 使用 PFX 檔案，則必須將這些憑證轉換成 PEM 格式。 若要將 PFX 檔案轉換為 PEM 檔案，請使用下列命令︰

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

如需詳細資訊，請參閱 [OpenSSL 文件](https://www.openssl.org/docs/)。

### <a name="connection-problems"></a>連線問題

某些作業可能會產生下列訊息：

`Failed to establish a new connection: [Errno 8] nodename nor servname provided, or not known`

確認指定的叢集端點可以使用且正在接聽。 另外確認 Service Fabric Explorer 的 UI 可在該主機和連接埠上使用。 若要更新端點，請使用 `sfctl cluster select`。

### <a name="detailed-logs"></a>詳細記錄

當您偵錯或回報問題時，詳細記錄通常很有幫助。 全域 `--debug` 旗標可增加記錄檔的詳細程度。

### <a name="command-help-and-syntax"></a>命令的說明和語法

如需特定命令或一組命令的說明，請使用 `-h` 旗標。

```azurecli
sfctl application -h
```

以下是另一個範例︰

```azurecli
sfctl application create -h
```

## <a name="next-steps"></a>後續步驟

* [透過 Azure Service Fabric CLI 部署應用程式](service-fabric-application-lifecycle-sfctl.md)
* [在 Linux 上開始使用 Service Fabric](service-fabric-get-started-linux.md)

