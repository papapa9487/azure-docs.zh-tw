---
title: "Linux 與 Windows 之間的 Azure Service Fabric 差異 | Microsoft Docs"
description: "Linux 上的 Azure Service Fabric 與 Windows 上的 Azure Service Fabric 之間的差異。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/19/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 25976ba919454e26f1dd7965de5db7c4f80b9355
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="differences-between-service-fabric-on-linux-and-windows"></a>Linux 與 Windows 上的 Service Fabric 差異

Windows 上支援的一些功能，在 Linux 上尚未提供支援。 最終，功能集會處於同等地位，而隨著每次發行，此功能差距將會縮小。 可用的最新版本之間 (也就是 Windows 上的 6.0 版和 Linux 上的 6.0 版之間) 存在下列差異： 

* 所有程式設計模型都處於預覽狀態 (Java/C# Reliable Actors、Reliable Stateless Services 和 Reliable Stateful Services)
* Envoy (ReverseProxy) 在 Linux 上處於預覽狀態
* 適用於 Linux 的獨立安裝程式還無法在 Linux 上使用
* 主控台重新導向 (Linux 或 Windows 生產叢集中不支援)
* Linux 上的錯誤分析服務 (FAS)
* Service Fabric 服務的 DNS 服務 (Linux 上的容器支援 DNS 服務)
* 特定 Powershell 命令的 CLI 命令對等項目 (在下面的清單中，大多數項目只適用於獨立叢集)

Windows 與 Linux 上的開發工具也不同。 在 Windows 上使用 Visual Studio、Powershell、VSTS 和 ETW，而在 Linux 上使用 Yeoman、Eclipse、Jenkins 和 LTTng。

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>無法針對 Linux Service Fabric 叢集運作的 Powershell Cmdlet

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy



## <a name="next-steps"></a>後續步驟
* [在 Linux 上準備您的開發環境](service-fabric-get-started-linux.md)
* [在 OSX 上準備您的開發環境](service-fabric-get-started-mac.md)
* [使用 Yeoman 在 Linux 上建立和部署第一個 Service Fabric Java 應用程式](service-fabric-create-your-first-linux-application-with-java.md)
* [在 Linux 上使用適用於 Eclipse 的 Service Fabric 外掛程式建立和部署第一個 Service Fabric Java 應用程式](service-fabric-get-started-eclipse.md)
* [在 Linux 上建立第一個 CSharp 應用程式](service-fabric-create-your-first-linux-application-with-csharp.md)
* [使用 Service Fabric CLI 管理應用程式](service-fabric-application-lifecycle-sfctl.md)

