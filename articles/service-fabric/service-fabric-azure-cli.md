---
title: "開始使用 Azure Service Fabric XPlat CLI"
description: "開始使用 Azure Service Fabric XPlat CLI"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: c3ec8ff3-3b78-420c-a7ea-0c5e443fb10e
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 9190b01b6ce42ea67ec7806c55a0013834d24211
ms.contentlocale: zh-tw
ms.lasthandoff: 07/04/2017

---

# <a name="using-the-xplat-cli-to-interact-with-a-service-fabric-cluster"></a>使用 XPlat CLI 與 Service Fabric 叢集進行互動

您可以使用 Linux 上的 XPlat CLI，從 Linux 機器與 Service Fabric 叢集進行互動。

第一個步驟是從 git rep 取得最新版本的 CLI，並使用下列命令將它設定在您的路徑中︰

```sh
 git clone https://github.com/Azure/azure-xplat-cli.git
 cd azure-xplat-cli
 npm install
 sudo ln -s \$(pwd)/bin/azure /usr/bin/azure
 azure servicefabric
```

對於它支援的每個命令，您可以輸入命令的名稱以取得該命令的說明。
支援自動完成命令。 例如，下列命令可讓您取得所有應用程式命令的說明。 

```sh
 azure servicefabric application 
```

您可以進一步篩選出特定命令的說明，如下列範例所示︰

```sh
 azure servicefabric application  create
```

若要在 CLI 中啟用自動完成，請執行下列命令︰

```sh
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.sh\_profile
source ~/azure.completion.sh
```

下列命令會連線到叢集，並顯示叢集中的節點︰

```sh
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric node show
```

若要使用具名參數並了解其用途，您可以在命令後面輸入 --help。 例如：

```sh
 azure servicefabric node show --help
 azure servicefabric application create --help
```

從 **不屬於叢集**的電腦連接到多電腦叢集時，請使用下列命令︰

```sh
 azure servicefabric cluster connect http://PublicIPorFQDN:19080
```

適當地以實際 IP 或 FQDN 取代 PublicIPorFQDN 標記。 從 **屬於叢集**的電腦連接到多電腦叢集時，請使用下列命令︰

```sh
 azure servicefabric cluster connect --connection-endpoint http://localhost:19080 --client-connection-endpoint PublicIPorFQDN:19000
```

您可以使用 PowerShell 或 CLI，與透過 Azure 入口網站建立的 Linux Service Fabric 叢集互動。

> [!WARNING]
> 這些叢集不安全，因此，您可能因為在叢集資訊清單中新增公用 IP 位址，而造成單機系統門戶洞開。

## <a name="using-the-xplat-cli-to-connect-to-a-service-fabric-cluster"></a>使用 XPlat CLI 來連線至 Service Fabric 叢集

下列 Azure CLI 命令說明如何連線到安全的叢集。 憑證詳細資料必須與叢集節點上的憑證相符。

```sh
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert
```

如果您的憑證有憑證授權單位 (CA)，則您需要新增 --ca-cert-path 參數，如下所示︰ 

```sh
 azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```

如果您有多個 CA，請使用逗號做為分隔符號。

如果憑證中的一般名稱不符合連接端點，您可以使用 `--strict-ssl-false` 參數略過驗證，如下列命令所示︰

```sh
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl-false 
```

如果您想要略過 CA 驗證，您可以新增 --reject-unauthorized-false 參數，如下列命令所示︰ 

```sh
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized-false 
```

連線之後，您應該能夠執行其他 CLI 命令來與叢集互動。

## <a name="deploying-your-service-fabric-application"></a>部署 Service Fabric 應用程式

執行下列命令來複製、註冊和啟動 Service Fabric 應用程式︰

```sh
azure servicefabric application package copy [applicationPackagePath] [imageStoreConnectionString] [applicationPathInImageStore]
azure servicefabric application type register [applicationPathinImageStore]
azure servicefabric application create [applicationName] [applicationTypeName] [applicationTypeVersion]
```

## <a name="upgrading-your-application"></a>升級您的應用程式

處理程序類似於 [Windows 中的處理程序](service-fabric-application-upgrade-tutorial-powershell.md))。

從專案根目錄中建置、複製、註冊和建立您的應用程式。 如果您的應用程式執行個體名為 `fabric:/MySFApp`，且類型為 MySFApp，則命令會如下所示︰

```sh
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
 azure servicefabric application create fabric:/MySFApp MySFApp 1.0
```

變更您的應用程式，並重建已修改的服務。  以服務 (和程式碼或組態或資料，視情況而定) 已更新的版本，更新已修改之服務的資訊清單檔案 (ServiceManifest.xml)。 同時，也以應用程式已更新的版本號碼及已修改的服務，修改應用程式的資訊清單 (ApplicationManifest.xml)。  現在，使用下列命令來複製並註冊已更新的應用程式︰

```sh
 azure servicefabric cluster connect http://localhost:19080>
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
```

現在，您可以使用下列命令來啟動應用程式升級︰

```sh
 azure servicefabric application upgrade start -–application-name fabric:/MySFApp -–target-application-type-version 2.0 --rolling-upgrade-mode UnmonitoredAuto
```

您現在可以使用 SFX 來監視應用程式升級。 應用程式在幾分鐘內會完成更新。 您也可以用錯誤動作來測試已更新的應用程式，並檢查 Service Fabric 中的自動回復功能。

## <a name="converting-from-pfx-to-pem-and-vice-versa"></a>從 PFX 轉換為 PEM (反之亦然)

您可能需要在本機電腦 (採用 Windows 或 Linux) 上安裝憑證，以存取可能位於不同環境中的安全叢集。 比方說，從 Windows 電腦存取安全的 Linux 叢集 (反之亦然) 時，您可能需要將憑證從 PFX 轉換為 PEM。 

若要從 PEM 檔案轉換為 PFX 檔案，請使用下列命令︰

```bash
openssl pkcs12 -export -out certificate.pfx -inkey mycert.pem -in mycert.pem -certfile mycert.pem
```

若要從 PFX 檔案轉換為 PEM 檔案，請使用下列命令︰

```bash
openssl pkcs12 -in certificate.pfx -out mycert.pem -nodes
```

請參閱[OpenSSL 文件](https://www.openssl.org/docs/man1.0.1/apps/pkcs12.html)以取得詳細資訊。

<a id="troubleshooting"></a>

## <a name="troubleshooting"></a>疑難排解


### <a name="copying-of-the-application-package-does-not-succeed"></a>未成功複製應用程式封裝

檢查是否已安裝 `openssh` 。 根據預設，Ubuntu 桌面不會安裝此軟體。 使用下列命令安裝它：

```sh
sudo apt-get install openssh-server openssh-client**
```

如果問題持續發生，請使用下列命令來變更 `sshd_config` 檔案，以嘗試對 ssh 停用 PAM：

```sh
sudo vi /etc/ssh/sshd_config
#Change the line with UsePAM to the following: UsePAM no
sudo service sshd reload
```

如果問題還是持續發生，請執行下列命令來嘗試增加 ssh 工作階段數目︰

```sh
sudo vi /etc/ssh/sshd\_config
# Add the following to lines:
# MaxSessions 500
# MaxStartups 300:30:500
sudo service sshd reload
```

尚不支援使用金鑰 (而不是密碼) 進行 ssh 驗證 (因為平台會使用 ssh 來複製套件)，請改為使用密碼驗證。

## <a name="next-steps"></a>後續步驟

[設定開發環境，並將 Service Fabric 應用程式部署到 Linux 叢集。](service-fabric-get-started-linux.md)

## <a name="related-articles"></a>相關文章

* [開始使用 Service Fabric 和 Azure CLI 2.0](service-fabric-azure-cli-2-0.md)

