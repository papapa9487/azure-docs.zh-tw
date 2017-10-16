---
title: "設定獨立 Azure Service Fabric 叢集 | Microsoft Docs"
description: "建立開發獨立叢集，其中包含在同部電腦上執行的三個節點。 完成此設定之後，您就可以開始建立包含多部電腦的叢集。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/06/2017
ms.author: dekapur
ms.openlocfilehash: 5c8f4c784eed7b64810a3dd1c36c043d22a66936
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-service-fabric-standalone-cluster"></a>建立您的第一個 Service Fabric 獨立叢集
您可以在內部部署環境或雲端上，在執行 Windows Server 2012 R2 或 Windows Server 2016 的虛擬機器或電腦上建立 Service Fabric 獨立叢集。 本快速入門可協助您在短短幾分鐘內建立開發獨立叢集。  當您完成時，您會具備有一個包含三個節點的叢集，其在您可部署應用程式的單一電腦上執行。

## <a name="before-you-begin"></a>開始之前
Service Fabric 會提供一個安裝套件以建立 Service Fabric 獨立叢集。  [下載安裝套件](http://go.microsoft.com/fwlink/?LinkId=730690)。  將安裝套件解壓縮至您在其中設定開發叢集之電腦或虛擬機器上的資料夾。  [這裡](service-fabric-cluster-standalone-package-contents.md)有安裝套件內容的詳細說明。

部署和設定叢集的叢集系統管理員必須具有電腦的系統管理員權限。 您無法在網域控制站上安裝 Service Fabric。

## <a name="validate-the-environment"></a>驗證環境
系統會使用獨立套件中的 *TestConfiguration.ps1* 指令碼作為最佳做法分析程式，以驗證是否可以在指定的環境上部署叢集。 [部署準備](service-fabric-cluster-standalone-deployment-preparation.md)會列出必要條件和環境需求。 執行指令碼來確認您是否可以建立開發叢集︰

```powershell
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
```
## <a name="create-the-cluster"></a>建立叢集
已使用安裝套件安裝數個範例叢集組態檔。 *ClusterConfig.Unsecure.DevCluster.json* 是最簡單的叢集組態︰在單一電腦上執行的不安全叢集 (包含三個節點)。  其他組態檔說明使用 X.509 憑證或 Windows 安全性保護之單一或多重電腦的叢集。  您不需要修改本教學課程的任何預設組態設定，但請瀏覽組態檔並熟悉設定。  **nodes** 區段描述叢集中的三個節點︰名稱、IP 位址、[節點類型、容錯網域和升級網域](service-fabric-cluster-manifest.md#nodes-on-the-cluster)。  **properties** 區段定義叢集的[安全性、可靠性層級、診斷集合和節點類型](service-fabric-cluster-manifest.md#cluster-properties)。

此叢集並不安全。  任何人都可以匿名方式連線並執行管理作業，所以一律要使用 X.509 憑證或 Windows 安全性來保護生產叢集。  只有在建立叢集時才會設定安全性，而且不可能在叢集建立之後啟用安全性。  若要深入了解 Service Fabric 叢集安全性，請閱讀[保護叢集](service-fabric-cluster-security.md)。  

若要建立包含三個節點的開發叢集，請從系統管理員 PowerShell 工作階段執行 *CreateServiceFabricCluster.ps1* 指令碼：

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

建立叢集時，會自動下載並安裝 Service Fabric 執行階段套件。

## <a name="connect-to-the-cluster"></a>連接到叢集
包含三個節點的開發叢集現在正在執行中。 ServiceFabric PowerShell 模組會隨著執行階段套件一起安裝。  您可以使用 Service Fabric 執行階段套件，確認叢集是從同部電腦或從遠端電腦執行。  [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) Cmdlet 會建立叢集連線。   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
如需連線到叢集的其他範例，請參閱[連線到安全的叢集](service-fabric-connect-to-secure-cluster.md)。 連線到叢集之後，使用 [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) Cmdlet 來顯示叢集中的節點清單以及每個節點的狀態資訊。 每個節點的 **HealthState** 應該為「正常」。

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>使用 Service Fabric Explorer 將叢集視覺化
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 是一個理想的工具，可將叢集視覺化及管理應用程式。  Service Fabric Explorer 是在叢集中執行的一項服務，您可以使用瀏覽器瀏覽至 [http://localhost:19080/Explorer](http://localhost:19080/Explorer) 來存取該服務。 

叢集儀表板會提供您叢集的概觀，包括應用程式和節點健康情況的摘要。 節點檢視會顯示叢集的實體配置。 對於指定的節點，您可以檢查已經在該節點上部署程式碼的應用程式。

![Service Fabric Explorer][service-fabric-explorer]

## <a name="remove-the-cluster"></a>移除叢集
若要移除叢集，請從套件資料夾執行 *RemoveServiceFabricCluster.ps1* PowerShell 指令碼，然後傳入 JSON 組態檔的路徑。 您可以選擇指定刪除作業的記錄檔位置。

```powershell
# Removes Service Fabric cluster nodes from each computer in the configuration file.
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -Force
```

若要從電腦中移除 Service Fabric 執行階段，請從套件資料夾執行下列 PowerShell 指令碼。

```powershell
# Removes Service Fabric from the current computer.
.\CleanFabric.ps1
```

## <a name="next-steps"></a>後續步驟
您已設定好開發獨立叢集，請嘗試下列文章︰
* [設定包含多部電腦的獨立叢集](service-fabric-cluster-creation-for-windows-server.md)並啟用安全性。
* [使用 PowerShell 部署應用程式](service-fabric-deploy-remove-applications.md)

[service-fabric-explorer]: ./media/service-fabric-get-started-standalone-cluster/sfx.png
