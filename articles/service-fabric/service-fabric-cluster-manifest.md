---
title: "設定 Azure Service Fabric 獨立叢集 | Microsoft Docs"
description: "了解如何設定獨立或私人的 Service Fabric 叢集。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 0c5ec720-8f70-40bd-9f86-cd07b84a219d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/02/2017
ms.author: dekapur
ms.openlocfilehash: 660e7b59ae0e92692121620341562e412a6e8eae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="configuration-settings-for-standalone-windows-cluster"></a>獨立 Windows 叢集的組態設定
本文說明如何使用 ClusterConfig.JSON 檔案來設定獨立 Service Fabric 叢集。 您可以使用此檔案針對獨立叢集指定如下的資訊：Service Fabric 節點及其 IP 位址、叢集上不同類型的節點、安全性組態，以及關於容錯/升級網域的網路拓撲。

當您[下載獨立 Service Fabric 套件](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)時，即會將 ClusterConfig.JSON 檔案的一些範例下載至您的工作電腦。 名稱中有「DevCluster」的範例將可協助您在同一部電腦上建立三個節點皆具的叢集，例如邏輯節點。 在這三個節點中，至少必須將一個節點標示為主要節點。 此叢集可用於開發或測試環境，但不支援做為生產叢集。 名稱中有「MultiMachine」的範例將可協助您建立生產品質叢集，其中的每個節點會建立在不同電腦上。 這些叢集的主要節點數目將以[可靠性層級](#reliability)為基礎。 在 API 05-2017 版的 5.7 版次中，我們移除了可靠性層級屬性。 但是，我們的程式碼將計算叢集的最佳化可靠性層級。 請不要在 5.7 和更新的程式碼版本中使用這個屬性。


1. *ClusterConfig.Unsecure.DevCluster.JSON* 和 *ClusterConfig.Unsecure.MultiMachine.JSON* 示範如何分別建立不安全的測試或生產叢集。 
2. *ClusterConfig.Windows.DevCluster.JSON* 和 *ClusterConfig.Windows.MultiMachine.JSON* 示範如何使用 [Windows 安全性](service-fabric-windows-cluster-windows-security.md)建立受保護的測試或生產叢集。
3. *ClusterConfig.X509.DevCluster.JSON* 和 *ClusterConfig.X509.MultiMachine.JSON* 示範如何使用 [X509 憑證型安全性](service-fabric-windows-cluster-x509-security.md)建立受保護的測試或生產叢集。 

現在，我們將檢視 ***ClusterConfig.JSON*** 檔案的不同區段，如下所示。

## <a name="general-cluster-configurations"></a>一般叢集組態
這涵蓋廣泛的叢集特定組態，如以下 JSON 片段所示。

    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",

若要為 Service Fabric 叢集提供任何易記名稱，您可以將它指派給 **name** 變數。 **ClusterConfigurationVersion** 是叢集的版本號碼，您應該在每次升級 Service Fabric 叢集時上調此號碼。 不過，您應該讓 **apiVersion** 保持使用預設值。

<a id="clusternodes"></a>

## <a name="nodes-on-the-cluster"></a>叢集上的節點
您可以使用 **nodes** 區段，在 Service Fabric 叢集上設定節點，如下列程式碼片段所示。

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

Service Fabric 叢集至少必須包含 3 個節點。 您可以根據安裝程式，在此區段中新增更多節點。 下表說明每個節點的組態設定。

| **節點組態** | **說明** |
| --- | --- |
| nodeName |您可以為節點提供易記名稱。 |
| iPAddress |開啟命令視窗並輸入 `ipconfig`，以找出節點的 IP 位址。 記下 IPV4 位址，並將它指派給 **iPAddress** 變數。 |
| nodeTypeRef |每個節點都可以指派不同的節點類型。 [節點類型](#nodetypes) 將於下一節中定義。 |
| faultDomain |容錯網域可讓叢集管理員定義實體節點，這類節點可能會在相同時間因為共用實體的相依性 (例如電源和網路來源) 而發生錯誤。 |
| upgradeDomain |升級網域說明大約會在相同時間關閉以進行 Service Fabric 升級的節點集。 因為它們不會受到任何實體需求所限制，您可以選擇要將哪些節點指派給哪些升級網域。 |

## <a name="cluster-properties"></a>叢集屬性
ClusterConfig.JSON 中的 **properties** 區段用來設定叢集，如下所示。

<a id="reliability"></a>

### <a name="reliability"></a>可靠性
**reliabilityLevel** 的概念會定義可以在叢集主要節點上執行之 Service Fabric 系統服務的複本或執行個體數目。 它會決定這些服務以及叢集的可靠性。 其值會由系統在建立和升級叢集時計算。

### <a name="diagnostics"></a>診斷
**diagnosticsStore** 區段可讓您設定參數，以啟用診斷以及疑難排解節點或叢集的失敗，如下列程式碼片段所示。 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

**metadata** 是叢集診斷的說明，而且可根據您的安裝程式來設定。 這些變數有助於收集 ETW 追蹤記錄檔、損毀傾印，以及效能計數器。 如需 ETW 追蹤記錄檔的詳細資訊，請參閱 [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) 和 [ETW 追蹤](https://msdn.microsoft.com/library/ms751538.aspx)。 包含[損毀傾印](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/)和[效能計數器](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx)的所有記錄檔可導向至電腦上的 **connectionString** 資料夾。 您也可以使用 *AzureStorage* 儲存診斷。 請參閱下面的範例程式碼片段。

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>安全性
**security** 區段對於安全獨立的 Service Fabric 叢集是必要的項目。 下列程式碼片段示範此區段的一部分。

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

**metadata** 是安全叢集的說明，而且可根據您的安裝程式來設定。 **ClusterCredentialType** 和 **ServerCredentialType** 決定叢集和節點將實作的安全性類型。 如果是憑證式安全性，可設定為 *X509*，如果是以 Azure Active Directory 為基礎的安全性，可設定為 *Windows*。 其餘的 **security** 區段則是根據安全性類型。 如需如何填滿其餘 **security** 區段的相關資訊，請參閱[獨立叢集中的憑證式安全性](service-fabric-windows-cluster-x509-security.md)或[獨立叢集中的 Windows 安全性](service-fabric-windows-cluster-windows-security.md)。

<a id="nodetypes"></a>

### <a name="node-types"></a>節點類型
**nodeTypes** 區段說明叢集所擁有的節點類型。 至少必須針對叢集指定一個節點類型，如下列程式碼片段所示。 

    "nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpointPort": "19001",
        "leaseDriverEndpointPort": "19002"
        "serviceConnectionEndpointPort": "19003",
        "httpGatewayEndpointPort": "19080",
        "reverseProxyEndpointPort": "19081",
        "applicationPorts": {
            "startPort": "20575",
            "endPort": "20605"
        },
        "ephemeralPorts": {
            "startPort": "20606",
            "endPort": "20861"
        },
        "isPrimary": true
    }]

**name** 是此特定節點類型的易記名稱。 若要建立此節點類型的節點，請將其易記名稱指派給該節點的 **nodeTypeRef** 變數，如[上面](#clusternodes)所述。 為每個節點類型定義將會使用的連接端點。 您可以為這些連接端點選擇任意的連接埠號碼，只要它們不會與此叢集中的任何其他端點發生衝突即可。 視 [**reliabilityLevel**](#reliability) 而定，多節點叢集中會有一或多個主要節點 (也就是 **isPrimary** 設為 *true*)。 如需 **nodeTypes** 和 **reliabilityLevel** 的詳細資訊，以及為了了解主要和非主要節點類型，請參閱 [Service Fabric 叢集容量規劃考量](service-fabric-cluster-capacity.md)。 

#### <a name="endpoints-used-to-configure-the-node-types"></a>用來設定節點類型的端點
* clientConnectionEndpointPort 是在使用用戶端 API 時，用戶端用來連線到叢集的連接埠。 
* clusterConnectionEndpointPort 是節點用來彼此通訊的連接埠。
* leaseDriverEndpointPort 是叢集租用驅動程式用來了解節點是否仍在作用中的連接埠。 
* serviceConnectionEndpointPort 是節點上部署的應用程式和服務用來與該特定節點的 Service Fabric 用戶端通訊的連接埠。
* httpGatewayEndpointPort 是 Service Fabric Explorer 用來連線到叢集的連接埠。
* ephemeralPorts 會覆寫 [OS 所使用的動態連接埠](https://support.microsoft.com/kb/929851)。 Service Fabric 會使用一部分連接埠做為應用程式連接埠，其餘連接埠則可供 OS 使用。 它也會將此範圍對應至 OS 中存在的現有範圍，因此不論用途為何，您都可以使用範例 JSON 檔案中給定的範圍。 您必須確保頭尾連接埠之間相差至少 255。 如果這項差異太低，您可能會遇到衝突，因為這個範圍會與作業系統共用。 請參閱設定的動態連接埠範圍，方法是執行 `netsh int ipv4 show dynamicport tcp`。
* applicationPorts 是 Service Fabric 應用程式將使用的連接埠。 應用程式連接埠範圍應該足以涵蓋應用程式的端點需求。 此範圍應該排除於電腦上的動態連接埠範圍 (也就是在組態中設定的 *ephemeralPorts* 範圍) 之外。  Service Fabric 會在每當需要新連接埠時使用這些連接埠，以及負責開啟這些連接埠的防火牆。 
* reverseProxyEndpointPort 是選擇性的反向 Proxy 端點。 如需詳細資訊，請參閱 [Service Fabric 反向 Proxy](service-fabric-reverseproxy.md)。 

### <a name="log-settings"></a>記錄設定
**fabricSettings** 區段可讓您設定 Service Fabric 資料和記錄檔的根目錄。 您只能在初始叢集建立期間自訂這些項目。 如需這個區段的範例程式碼片段，請參閱下列內容。

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

建議您使用非作業系統磁碟機做為 FabricDataRoot 和 FabricLogRoot，因為這類磁碟機比較不會讓作業系統當機。 請注意，如果您只自訂資料根目錄，則記錄根目錄將會以資料根目錄的下一個層級來取代。

### <a name="stateful-reliable-service-settings"></a>具狀態可靠服務設定
**KtlLogger** 區段可讓您設定 Reliable Services 的全域組態設定。 如需這些設定的詳細資訊，請參閱[設定具狀態可靠服務](service-fabric-reliable-services-configuration.md)。
下列範例示範如何變更所建立的共用交易記錄，以備份具狀態服務的任何可靠集合。

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="add-on-features"></a>附加元件功能
若要設定附加元件的功能，應將 apiVersion 設定為 ' 04-2017' 或更高版本，而且 addonFeatures 必須設定為：

    "apiVersion": "04-2017",
    "properties": {
      "addOnFeatures": [
          "DnsService",
          "RepairManager"
      ]
    }

### <a name="container-support"></a>容器支援
若要啟用獨立叢集的 Windows Server 容器和 Hyper-V 容器的容器支援，必須啟用 'DnsService' 附加元件功能。


## <a name="next-steps"></a>後續步驟
當您根據獨立叢集安裝程式設定完整的 ClusterConfig.JSON 檔案之後，就可以遵循[建立獨立 Service Fabric 叢集](service-fabric-cluster-creation-for-windows-server.md)一文來部署叢集，然後繼續[使用 Service Fabric Explorer 視覺化叢集](service-fabric-visualizing-your-cluster.md)。

