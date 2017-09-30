---
title: "設定 Azure Service Fabric 容器服務的網路模式 | Microsoft Docs"
description: "了解如何設定 Azure Service Fabric 支援的不同網路模式。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 1ecded3af6396f50e67dc5d2a9ef8337699046ea
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="service-fabric-container-networking-modes"></a>Service Fabric 容器網路模式

容器服務之 Service Fabric 叢集中提供的預設網路模式是 `nat` 網路模式。 使用 `nat` 網路模式，讓一個以上的容器服務接聽相同連接埠會導致部署錯誤。 為了執行在相同通訊埠上接聽的數個服務，Service Fabric 支援 `open` 網路模式 (5.7 版或更高版本)。 使用 `open` 網路模式，每個容器服務會取得動態指派 IP 位址，在內部允許多個服務接聽相同的連接埠。   

因此，使用單一服務類型 (具有在服務資訊清單中定義的靜態端點)，就可以使用 `open` 網路模式建立和刪除新的服務，而不會造成部署錯誤。 同樣地，可以使用相同 `docker-compose.yml` 檔案 (具有靜態連接埠對應) 來建立多個服務。

不建議使用動態指派 IP 來探索服務，因為當服務重新啟動或移至其他節點時，IP 位址會變更。 只使用 **Service Fabric 命名服務**或 **DNS 服務**來進行服務探索。 


> [!WARNING]
> 在 Azure 中每個 vNET 僅允許總計 4096 個 IP。 因此，節點數目和容器服務執行個體數目的總和 (使用 `open` 網路) 在 vNET 中不能超過 4096 個。 對於這類高密度案例，建議使用 `nat` 網路模式。
>

## <a name="setting-up-open-networking-mode"></a>設定開啟網路模式

1. 藉由啟用 `fabricSettings` 下的 DNS 服務和 IP 提供者，設定 Azure Resource Manager 範本。 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name":  "Trace/Etw", 
                    "parameters": [
                    {
                            "name": "Level",
                            "value": "5"
                    }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```

2. 設定網路設定檔區段，以允許在叢集的每個節點上設定多個 IP 位址。 下列範例會針對 Windows/Linux Service Fabric 叢集，在每個節點上設定五個 IP 位址 (因此您可以有五個服務執行個體，接聽每個節點上的連接埠)。

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
   ```
 

3. 僅適用於 Windows 叢集，使用下列值設定 NSG 規則，以開啟 vNET 的連接埠 UDP/53：

   | 優先順序 |    名稱    |    來源      |  目的地   |   服務    | 動作 |
   |:--------:|:----------:|:--------------:|:--------------:|:------------:|:------:|
   |     2000 | Custom_Dns | VirtualNetwork | VirtualNetwork | DNS (UDP/53) | 允許  |


4. 在應用程式資訊清單中為每個服務指定網路模式 `<NetworkConfig NetworkType="open">`。  模式 `open` 會導致服務取得專用 IP 位址。 如果未指定模式，則會預設為基本 `nat` 模式。 因此，在下列資訊清單範例中，`NodeContainerServicePackage1` 和 `NodeContainerServicePackage2` 可以分別接聽相同通訊埠 (這兩個服務都會在 `Endpoint1` 上接聽)。

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="open"/>
           <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="open"/>
            <PortBinding ContainerPort="8910" EndpointRef="Endpoint1"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```
您可以跨 Windows 叢集之應用程式內的服務，混合和比對不同網路模式。 因此，您可以讓某些服務在 `open` 模式中，以及某些服務在 `nat` 網路模式中。 當使用 `nat` 設定服務時，接聽的連接埠必須是唯一的。 在 Linux 叢集上不支援針對不同服務混合網路模式。 


## <a name="next-steps"></a>後續步驟
在本文中，您會了解 Service Fabric 提供的網路模式。  

* [Service Fabric 應用程式模型](service-fabric-application-model.md)
* [Service Fabric 服務資訊清單資源](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-service-manifest-resources)
* [將 Windows 容器部署至 Windows Server 2016 上的 Service Fabric](service-fabric-get-started-containers.md)
* [將 Docker 容器部署至 Linux 上的 Service Fabric](service-fabric-get-started-containers-linux.md)

