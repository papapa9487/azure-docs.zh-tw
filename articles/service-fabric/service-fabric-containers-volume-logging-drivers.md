---
title: "Azure Service Fabric Docker Compose (預覽) | Microsoft Docs"
description: "Azure Service Fabric 接受 Docker Compose 格式，可讓您更輕鬆地使用 Service Fabric 來協調現有容器。 Docker Compose 的支援目前為預覽狀態。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 433424a6700d3e8940e3d1142ce2ff579a92067c
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2017
---
# <a name="use-docker-volume-plug-ins-and-logging-drivers-in-your-container"></a>使用容器的 Docker 磁碟區外掛程式和記錄驅動程式
Azure Service Fabric 支援為容器服務指定 [Docker 磁碟區外掛程式](https://docs.docker.com/engine/extend/plugins_volume/)和 [Docker 記錄驅動程式](https://docs.docker.com/engine/admin/logging/overview/)。 當您的容器移動了或在不同主機上重新啟動時，您可將資料保存在 [Azure 檔案](https://azure.microsoft.com/services/storage/files/)中。

目前僅支援 Linux 容器的磁碟區驅動程式。 如果您是使用 Windows 容器，則無需磁碟區驅動程式就可以將磁碟區對應至 Azure 檔案 [SMB3 共用](https://blogs.msdn.microsoft.com/clustering/2017/08/10/container-storage-support-with-cluster-shared-volumes-csv-storage-spaces-direct-s2d-smb-global-mapping/)。 針對此對應，將您叢集中的虛擬機器 (VM) 更新至最新的 Windows Server 1709 版本。


## <a name="install-the-docker-volumelogging-driver"></a>安裝 Docker 磁碟區/記錄驅動程式

如果電腦上未安裝 Docker 磁碟區/記錄驅動程式，可以使用 RDP/SSH 通訊協定來手動安裝它。 您可以透過[虛擬機器擴展集啟動指令碼](https://azure.microsoft.com/resources/templates/201-vmss-custom-script-windows/)或 [SetupEntryPoint 指令碼](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-model#describe-a-service)，使用這些通訊協定執行安裝。

安裝[適用於 Azure 的 Docker 磁碟區驅動程式](https://docs.docker.com/docker-for-azure/persistent-data-volumes/)的指令碼範例如下所示：

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

## <a name="specify-the-plug-in-or-driver-in-the-manifest"></a>在資訊清單中指定外掛程式或驅動程式
外掛程式是在應用程式資訊清單中指定，如下所示：

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
        <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
        <LogConfig Driver="etwlogs" >
          <DriverOption Name="test" Value="vale"/>
        </LogConfig>
        <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
        <Volume Source="d:\myfolder" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
        <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
        </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

[磁碟區] 元素的 [來源] 標記是指來源資料夾。 來源資料夾可以是裝載容器或持續性遠端存放區的 VM 中的資料夾。 [目的地] 標記是 [來源] 在執行容器內所對應的位置。 因此，您的目的地不能是您容器內的現有位置。

指定磁碟區外掛程式時，Service Fabric 會使用指定的參數自動建立磁碟區。 [來源] 標記是磁碟區的名稱，而 [驅動程式] 標記會指定磁碟區驅動程式的外掛程式。 您可以使用 [DriverOption] 標記來指定選項，如下所示：

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```
如果指定了 Docker 記錄驅動程式，您就必須在叢集中部署代理程式 (或容器) 來處理記錄。 [DriverOption] 標記可以用來指定記錄驅動程式的選項。

## <a name="next-steps"></a>後續步驟
若要將容器部署到 Service Fabric 叢集，請參閱[在 Service Fabric 上部署容器](service-fabric-deploy-container.md)。
