---
title: "Azure Service Fabric Docker Compose 預覽 | Microsoft Docs"
description: "Azure Service Fabric 接受 Docker Compose 格式，讓您能更輕鬆地使用 Service Fabric 協調現有容器。 這項支援目前只能預覽。"
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
ms.openlocfilehash: 955f84e5656bbf568234cbaf69faa4dd0a741206
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2017
---
# <a name="using-volume-plugins-and-logging-drivers-in-your-container"></a>使用容器的磁碟區外掛程式和記錄驅動程式
Service Fabric 支援為容器服務指定 [Docker 磁碟區外掛程式](https://docs.docker.com/engine/extend/plugins_volume/)和 [Docker 記錄驅動程式](https://docs.docker.com/engine/admin/logging/overview/)。  即使您的容器移動了或在不同主機上重新啟動，這樣可讓您將資料保存在 [Azure 檔案](https://azure.microsoft.com/en-us/services/storage/files/)中。

目前只有適用於 Linux 容器的磁碟區驅動程式，如下所示。  如果您使用 Windows 容器，可使用最新的 1709 版本 Windows Server 將磁碟區對應至 Azure 檔案 [SMB3 共用](https://blogs.msdn.microsoft.com/clustering/2017/08/10/container-storage-support-with-cluster-shared-volumes-csv-storage-spaces-direct-s2d-smb-global-mapping/)，不需要磁碟區驅動程式。 這麼做需要將您叢集中的虛擬機器更新至 Windows Server 1709 版本。


## <a name="install-volumelogging-driver"></a>安裝磁碟區/記錄驅動程式

如果電腦未安裝 Docker 磁碟區/記錄驅動程式，請透過 RDP/SSH 登入電腦，然後透過 [VMSS 啟動指令碼](https://azure.microsoft.com/en-us/resources/templates/201-vmss-custom-script-windows/)或使用 [SetupEntryPoint](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-model#describe-a-service) 指令碼手動安裝。 選擇上述的其中一個方法，您可以撰寫指令碼來安裝 [Azure 的 Docker 磁碟區驅動程式](https://docs.docker.com/docker-for-azure/persistent-data-volumes/)：


```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

## <a name="specify-the-plugin-or-driver-in-the-manifest"></a>在資訊清單中指定外掛程式或驅動程式
外掛程式是在應用程式資訊清單中指定，如下列資訊清單中所示：

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

在上述範例中，`Volume` 的 `Source` 標記指的是來源資料夾。 來源資料夾可以是裝載容器或持續性遠端存放區的 VM 中的資料夾。 `Destination` 標記是 `Source` 在執行容器內所對應的位置。  因此，您的目的地不能是在您容器內的現有位置。

指定磁碟區外掛程式時，Service Fabric 會使用指定的參數自動建立磁碟區。 `Source` 標記是磁碟區的名稱，而 `Driver` 標記會指定磁碟區驅動程式的外掛程式。 您可以使用 `DriverOption` 標記指定選項，如下列程式碼片段所示：

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```
如果指定了 Docker 記錄驅動程式，就必須在叢集中部署代理程式 (或容器) 來處理記錄檔。  `DriverOption` 標記也可用來指定記錄驅動程式選項。

請參閱下列文章將容器部署至 Service Fabric 叢集：


[將容器部署在 Service Fabric 上](service-fabric-deploy-container.md)

