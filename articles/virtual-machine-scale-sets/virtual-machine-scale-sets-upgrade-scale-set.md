---
title: "升級 Azure 虛擬機器擴展集 | Microsoft Docs"
description: "升級 Azure 虛擬機器擴展集"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: guybo
ms.openlocfilehash: c7093e221ff8fe69ded1cfbce4f3ddeb1a195666
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-a-virtual-machine-scale-set"></a>升級虛擬機器擴展集
本文說明如何在不需停機的情況下，對 Azure 虛擬機器擴展集推出 OS 更新。 在此背景環境下，OS 更新包括變更 OS 的版本或 SKU，或是變更自訂映像的 URI。 在不需停機的情況下進行更新意謂著一次更新一部虛擬機器，或依群組 (例如一次一個容錯網域) 更新虛擬機器，而不是全部一起更新。 透過這種方式，任何非升級中的虛擬機器都可繼續執行。

為了避免混淆不清，讓我們區分您可能會想要執行的四種 OS 更新：

* 變更平台映像的版本或 SKU。 例如，將 Ubuntu 14.04.2-LTS 版本從 14.04.201506100 變更為 14.04.201507060，或將 Ubuntu 15.10/最新 SKU 變更為 16.04.0-LTS/最新 SKU。 本文涵蓋此案例。
* 變更指向您所建立的新版本自訂映像的 URI (**properties** > **virtualMachineProfile** > **storageProfile** > **osDisk** > **image** > **uri**)。 本文涵蓋此案例。
* 變更使用 Azure 受控磁碟所建立之擴展集的映像參考。
* 從虛擬機器內修補 OS (範例包括安裝安全性修補程式並執行 Windows Update)。 支援此案例，但本文並未涵蓋此案例。

這裡未涵蓋隨 [Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) 一起部署的虛擬機器擴展集。 如需修補 Service Fabric 的詳細資訊，請參閱[在 Service Fabric 叢集中修補 Windows OS](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-patch-orchestration-application)。

變更平台映像 OS 版本/SKU 或自訂映像 URI 的基本順序看起來如下：

1. 取得虛擬機器擴展集模型。
2. 變更模型中的版本、SKU、映像參考或 URI 值。
3. 更新模型。
4. 對擴展集中的虛擬機器進行 *manualUpgrade* 呼叫。 只有當您擴展集中的 *upgradePolicy* 設定為 [手動] 時，此步驟才相關。 如果它是設定為 [自動] ，則會同時升級所有虛擬機器，因而造成停機。

將這項資訊謹記在心，讓我們看看如何在 PowerShell 中使用 REST API 來更新擴展集的版本。 這些範例涵蓋平台映像的案例，但本文所提供的資訊已足以讓您將此程序應用在自訂映像上。

## <a name="powershell"></a>PowerShell
此範例會將 Windows 虛擬機器擴展集更新 (建立) 成新版本 4.0.20160229。 更新完模型之後，它會一次更新一個虛擬機器執行個體。

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the virtual machine scale set model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

如果您更新的是自訂映像的 URI，而不是變更平台映像版本，請以更新來源映像 URI 的命令取代 “set the new version” 行。 例如，如果擴展集不是使用 Azure 受控磁碟所建立，更新會如下所示：

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```

如果使用 Azure 受控磁碟建立了以自訂映像為基礎的擴展集，則會更新映像參考。 例如：

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.id = $newImageReference
```

## <a name="the-rest-api"></a>REST API
以下是幾個使用 Azure REST API 來推出 OS 版本更新的 Python 範例。 兩者都使用 Azure REST API 包裝函式的輕量型 [azurerm](https://pypi.python.org/pypi/azurerm) 程式庫對擴展集模型執行 GET，然後搭配更新的模型執行 PUT。 它們也會查看虛擬機器執行個體檢視，以依據更新網域識別虛擬機器。

### <a name="vmssupgrade"></a>Vmssupgrade
 [Vmssupgrade](https://github.com/gbowerman/vmsstools) 是一個 Python 指令碼，能夠用來一次以一個更新網域為範圍，對執行中的虛擬機器擴展集推出 OS 升級。

![選擇虛擬機器或更新網域的 vmssupgrade 指令碼](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

此指令碼能讓您選擇要更新的特定虛擬機器，或是指定更新網域。 它支援變更平台映像版本，或變更自訂映像的 URI。

### <a name="vmsseditor"></a>Vmsseditor
[Vmsseditor](https://github.com/gbowerman/vmssdashboard) 是虛擬機器擴展集的一般用途編輯器，能夠以熱力圖的形式顯示虛擬機器狀態，其中一個資料列代表一個更新網域。 除此之外，您還可以使用新的版本、SKU 或自訂映像 URI 來更新擴展集的模型，然後選擇要升級的容錯網域。 當您這麼做時，該更新網域中的所有虛擬機器都會升級成新模型。 或者，您也可以根據所選的批次大小執行輪流升級。  

下列螢幕擷取畫面顯示 Ubuntu 14.04-2LTS 版本 14.04.201507060 的擴展集模型。 自從拍攝此螢幕擷取畫面之後，有許多其他選項已經新增到此工具中。

![Ubuntu 14.04-2LTS 的擴展集 vmsseditor 模型](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

當您依序按一下 [升級] 和 [取得詳細資料] 之後，UD 0 中的虛擬機器將會開始更新。

![顯示更新進行中的 vmsseditor](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)

