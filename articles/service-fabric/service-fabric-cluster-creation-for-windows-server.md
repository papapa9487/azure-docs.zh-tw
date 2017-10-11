---
title: "建立獨立 Azure Service Fabric 叢集 | Microsoft Docs"
description: "在執行 Windows Server (無論是在內部部署或任何雲端) 的任何電腦 (實體或虛擬) 上建立 Azure Service Fabric 叢集。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: chackdan;maburlik;dekapur
ms.openlocfilehash: 6aa2905a97ec6b8c125f2ab9572a8e40bf525b27
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>建立在 Windows Server 上執行的獨立叢集
您可以使用 Azure Service Fabric 在執行 Windows Server 的任何虛擬機器或電腦上建立 Service Fabric 叢集。 這表示您能夠在包含一組互連式 Windows Server 電腦的任何環境中部署和執行 Service Fabric 應用程式，不論該環境是內部部署或是透過任何雲端提供者來提供。 Service Fabric 會提供一個安裝封裝來建立稱為獨立 Windows Server 封裝的 Service Fabric 叢集。

本文將逐步引導您完成建立 Service Fabric 獨立叢集的步驟。

> [!NOTE]
> 此獨立 Windows Server 套件已正式上市，可使用於生產部署。 此套件包含處於「預覽」狀態的新 Service Fabric 功能。 捲動至「[此封裝包含的預覽功能](#previewfeatures_anchor)」。 區段，以取得預覽功能的清單。 您可以立即[下載一份 EULA](http://go.microsoft.com/fwlink/?LinkID=733084)。
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>取得 Windows Server 套件的 Service Fabric 支援
* 請至 [Azure Service Fabric 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?)，向社群發問有關 Windows Server 的 Service Fabric 獨立封裝。
* 向 [Service Fabric 的專業支援](http://support.microsoft.com/oas/default.aspx?prid=16146)開立票證。  [在這裡](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)深入了解 Microsoft 的專業支援。
* 您也可以取得此封裝的支援做為 [Microsoft 頂級支援](https://support.microsoft.com/en-us/premier)的一部分。
* 如需詳細資訊，請參閱 [Azure Service Fabric 支援選項](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-support)。
* 若要針對支援用途收集記錄，請執行 [Service Fabric 獨立記錄收集器](service-fabric-cluster-standalone-package-contents.md)。

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>下載 Windows Server 套件的 Service Fabric
若要建立叢集，請使用適用於 Windows Server 套件 (Windows Server 2012 R2 及更新版本) 的 Service Fabric，可在這裡找到︰ <br>
[下載連結 - Service Fabric 獨立封裝 - Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690)

可在[這裡](service-fabric-cluster-standalone-package-contents.md)找到封裝內容的詳細資訊。

叢集建立時會自動下載 Service Fabric 執行階段封裝。 如果從未連線到網際網路的機器部署，請從這裡下載頻外執行階段封裝︰ <br>
[下載連結 - Service Fabric 執行階段 - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

獨立叢集組態範例在此︰ <br>
[獨立叢集組態範例](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>建立叢集
Service Fabric 可以使用[範例](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)中所含的 *ClusterConfig.Unsecure.DevCluster.json* 檔案，部署到一個電腦開發叢集。

將獨立封裝解除封裝到電腦，將範例組態檔複製到本機電腦，然後從獨立套件資料夾，透過系統管理員 PowerShell 工作階段，執行 *CreateServiceFabricCluster.ps1* 指令碼︰
### <a name="step-1a-create-an-unsecured-local-development-cluster"></a>步驟 1A：建立不安全的本機開發叢集
```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

如需疑難排解的詳細資料，請參閱[規劃及準備叢集部署](service-fabric-cluster-standalone-deployment-preparation.md)的「環境設定」一節。

如果您完成執行開發案例，您可以參閱「[移除叢集](#removecluster_anchor)」一節中的步驟，從電腦中移除 Service Fabric 叢集。 

### <a name="step-1b-create-a-multi-machine-cluster"></a>步驟 1B︰ 建立多部電腦的叢集
在您完成規劃和準備下面連結詳細列出的步驟之後，就可以開始使用您的叢集組態檔，建立生產叢集。 <br>
[規劃及準備叢集部署](service-fabric-cluster-standalone-deployment-preparation.md)

1. 從獨立封裝資料夾執行 *TestConfiguration.ps1* 指令碼，驗證您所撰寫的組態檔︰  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    您應該會看到如下的輸出： 如果底層欄位 "Passed" 傳回為 "True"，表示已通過例行性檢查，並可根據輸入組態來部署該叢集。

    ```
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. 建立叢集︰執行 *CreateServiceFabricCluster.ps1* 指令碼，以跨組態中的每一部機器部署 Service Fabric 叢集。 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> 部署追蹤會寫入您可以執行 CreateServiceFabricCluster.ps1 PowerShell 指令碼的 VM/電腦。 這些可以根據指令碼執行的目錄，在其子資料夾 DeploymentTraces 中找到。 若要查看是否已正確將 Service Fabric 部署到電腦，請在 FabricDataRoot 目錄中找到安裝的檔案，如叢集組態檔的 FabricSettings 區段 (預設為 c:\ProgramData\SF) 中所述。 同時，也要能在 [工作管理員] 看到 FabricHost.exe 和 Fabric.exe 處理序正在執行中。
> 
> 

### <a name="step-1c-create-an-offline-internet-disconnected-cluster"></a>步驟 1 C：建立離線 (網際網路中斷連線的) 叢集
叢集建立時會自動下載 Service Fabric 執行階段套件。 將叢集部署到未連線到網際網路的電腦時，您必須另外下載 Service Fabric 執行階段套件，並在建立叢集時提供指向它的路徑。
可以從另一部有連線到網際網路電腦，到[下載連結 - Service Fabric 執行階段 - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354) 另外下載執行階段套件。 將執行階段套件複製到您要部署離線叢集之處，然後執行 `CreateServiceFabricCluster.ps1` 搭配 `-FabricRuntimePackagePath` 參數建立叢集，如下所示： 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```
其中 `.\ClusterConfig.json` 和 `.\MicrosoftAzureServiceFabric.cab` 分別為叢集設定與執行階段 .cab 檔案的路徑。


### <a name="step-2-connect-to-the-cluster"></a>步驟 2：連接到叢集
若要連接至安全的叢集，請參閱 [Service Fabric 連線到安全的叢集](service-fabric-connect-to-secure-cluster.md)。

若要連線到不安全的叢集，請執行下列 PowerShell 命令：

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```
範例：
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```
### <a name="step-3-bring-up-service-fabric-explorer"></a>步驟 3：啟動 Service Fabric Explorer
現在，您可以使用 http://localhost:19080/Explorer/index.html 直接從其中一部電腦或使用 http://<*IPAddressofaMachine*>:19080/Explorer/index.html 從遠端利用 Service Fabric Explorer 連接到叢集。

## <a name="add-and-remove-nodes"></a>新增和移除節點
當您的商務需求改變時，您可以在獨立 Service Fabric 叢集中新增或移除節點。 如需詳細步驟，請參閱[在 Service Fabric 獨立叢集中新增或移除節點](service-fabric-cluster-windows-server-add-remove-nodes.md)。

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>刪除叢集
若要移除叢集，請從封裝資料夾執行 *RemoveServiceFabricCluster.ps1* PowerShell 指令碼，然後傳入 JSON 組態檔的路徑。 您可以選擇指定刪除作業的記錄檔位置。

此指令碼可以在以系統管理員身分存取叢集組態檔中列為節點的所有電腦的任何電腦上執行。 執行此指令碼所在的電腦不一定是叢集的一部分。

```
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>收集的遙測資料及如何選擇退出
根據預設，產品會收集 Service Fabric 使用情形的遙測來改善產品。 安裝過程執行的最佳做法分析會檢查能否連線到 [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1)。 如果無法連線，則安裝會失敗，除非您選擇退出遙測。

1. 遙測管線會嘗試將下列資料每天上傳一次到 [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1)。 這只是儘可能上傳，不會影響叢集功能。 只有執行主要容錯移轉管理員的節點才會傳送遙測。 沒有其他節點會傳送遙測。
2. 遙測是由下列項目所組成：

* 服務數
* ServiceTypes 數目
* Applications 的數目
* ApplicationUpgrades 的數目
* FailoverUnits 的數目
* InBuildFailoverUnits 的數目
* UnhealthyFailoverUnits 的數目
* Replicas 的數目
* InBuildReplicas 的數目
* StandByReplicas 的數目
* OfflineReplicas 的數目
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Nodes 的數目
* IsContextComplete: True/False
* ClusterId︰這是針對每個叢集隨機產生的 GUID。
* ServiceFabricVersion
* 遙測上傳來源虛擬機器的 IP 位址

若要停用遙測，請將下列命令新增至叢集組態中的 *properties*：*enableTelemetry: false*。

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>此封裝包含的預覽功能
無。


> [!NOTE]
> 從[適用於 Windows Server 的獨立叢集 GA 新版本 (5.3.204.x 版)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/)著手，您可以手動或自動將叢集升級至未來的版本。 請參閱[獨立 Service Fabric 叢集版本升級](service-fabric-cluster-upgrade-windows-server.md)文件，以取得詳細資訊。
> 
> 

## <a name="next-steps"></a>後續步驟
* [使用 PowerShell 部署與移除應用程式](service-fabric-deploy-remove-applications.md)
* [獨立 Windows 叢集的組態設定](service-fabric-cluster-manifest.md)
* [在獨立 Service Fabric 叢集中新增或移除節點](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [獨立 Service Fabric 叢集版本升級](service-fabric-cluster-upgrade-windows-server.md)
* [建立具有執行 Windows 之 Azure VM 的獨立 Service Fabric 叢集](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [使用 Windows 安全性保護 Windows 上的獨立叢集](service-fabric-windows-cluster-windows-security.md)
* [使用 X509 憑證保護 Windows 上的獨立叢集](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
