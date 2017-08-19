---
title: "管理 Service Fabric 中的多個環境 |Microsoft Docs"
description: "Service Fabric 應用程式可以在任意大小 (從一部電腦至數千部電腦) 的叢集上執行。 在某些情況下，您會想要針對各種環境以不同的方式設定應用程式。 本文說明如何定義每個環境的不同應用程式參數。"
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: mikkelhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: eaf1daf8d9f973fe82ba9e82c60a2a82f2681786
ms.contentlocale: zh-tw
ms.lasthandoff: 06/09/2017

---
# <a name="manage-application-parameters-for-multiple-environments"></a>管理多個環境的應用程式參數
您可以使用任意數量的電腦 (從一至數千部) 來建立 Service Fabric 叢集。 雖然不需針對各種環境進行修改，即可執行應用程式二進位檔，但您通常會視您要部署的機器數目，以不同的方式設定應用程式。

簡單來說，請考慮無狀態服務的 `InstanceCount` 。 當您在 Azure 中執行應用程式時，您通常要將此參數設定為特殊值 "-1"。 這樣設定可確保您的服務在叢集中的每個節點上執行 (或節點中的每個節點，如果您已設定放置條件約束)。 不過，此設定不適用於單一電腦叢集，因為您不能有多個在單一電腦的相同端點上接聽的程序。 然而，您通常會將 `InstanceCount` 設定為 "1"。

## <a name="specifying-environment-specific-parameters"></a>指定環境特有的參數
此設定問題的解決方案是一組參數化預設服務和應用程式參數檔案，其中會填入指定之環境的參數值。 預設的服務和應用程式參數是在應用程式和服務資訊清單之中設定。 ServiceManifest.xml 和 ApplicationManifest.xml 檔案的結構描述定義是和 Service Fabric SDK 及工具一起安裝在 *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*。

### <a name="default-services"></a>預設服務
Service Fabric 應用程式是由服務執行個體集合所組成。 雖然您可以先建立一個空的應用程式，然後再動態建立所有的服務執行個體，但是大部分的應用程式都有一組應一律在應用程式具現化時建立的核心服務。 這些稱為「預設服務」。 其在應用程式資訊清單中指定，而方括號中包含每個環境組態的預留位置：

```xml
  <DefaultServices>
      <Service Name="Stateful1">
          <StatefulService
              ServiceTypeName="Stateful1Type"
              TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]"
              MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

              <UniformInt64Partition
                  PartitionCount="[Stateful1_PartitionCount]"
                  LowKey="-9223372036854775808"
                  HighKey="9223372036854775807"
              />
        </StatefulService>
    </Service>
  </DefaultServices>
```

必須在應用程式資訊清單的 [參數] 元素中定義每個具名參數：

```xml
    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="3" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>
```

DefaultValue 屬性指定當指定的環境缺少更特定的參數時所要使用的值。

> [!NOTE]
> 並非所有的服務執行個體參數都適用於每個環境組態。 在上述範例中，已針對服務的所有執行個體明確定義服務資料分割配置的 LowKey 和 HighKey 值，因為資料分割範圍是資料網域 (而不是環境) 的函數。
> 
> 

### <a name="per-environment-service-configuration-settings"></a>每個環境的服務組態設定
[Service Fabric 應用程式模型](service-fabric-application-model.md) 可讓服務包含組態封裝，內含可在執行階段讀取的自訂關鍵值組。 在應用程式資訊清單中指定 `ConfigOverride` ，也可依照環境區分這些設定的值。

假設您在 `Stateful1` 服務的 Config\Settings.xml 檔案中有下列設定：

```xml
  <Section Name="MyConfigSection">
     <Parameter Name="MaxQueueSize" Value="25" />
  </Section>
```
若要覆寫特定應用程式/環境組的這個值，請在應用程式資訊清單中匯入服務資訊清單時建立 `ConfigOverride` 。

```xml
  <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>
```
接著可如上所示，依照環境設定此參數。 在應用程式資訊清單的參數區段中加以宣告，並在應用程式參數檔案中指定環境特有的值，即可執行這項操作。

> [!NOTE]
> 在服務組態設定的情況下，有三個地方可以設定索引鍵的值：服務組態封裝、應用程式資訊清單和應用程式參數檔案。 Service Fabric 將一律先從應用程式參數檔案 (若已指定) 進行選擇，然後從應用程式資訊清單選擇，最後再從組態封裝選擇。
> 
> 

### <a name="setting-and-using-environment-variables"></a>設定及使用環境變數 
您可以在 ServiceManifest.xml 檔案中指定和設定環境變數，然後依據個別執行個體，在 ApplicationManifest.xml 檔案中覆寫這些變數。
以下範例顯示兩個環境變數，其中一個已設定值，另一個會被覆寫。 您可以使用應用程式參數來設定環境變數值，其方式與將這些用於組態覆寫時一樣。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
    <EnvironmentVariables>
      <EnvironmentVariable Name="MyEnvVariable" Value=""/>
      <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
    </EnvironmentVariables>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
```
若要覆寫 ApplicationManifest.xml 中的環境變數，請使用 `EnvironmentOverrides` 元素來參考 ServiceManifest 中的程式碼封裝。

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
    <EnvironmentOverrides CodePackageRef="MyCode">
      <EnvironmentVariable Name="MyEnvVariable" Value="mydata"/>
    </EnvironmentOverrides>
  </ServiceManifestImport>
 ``` 
 在具名服務執行個體建立之後，您便可以從程式碼存取環境變數。 例如，在 C# 中，您可以執行：

```csharp
    string EnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

### <a name="service-fabric-environment-variables"></a>Service Fabric 環境變數
Service Fabric 具有已針對每個服務執行個體設定的內建環境變數。 完整的環境變數清單如下，其中以粗體顯示的項目是您將在您的服務中使用的項目，而其他項目則是由 Service Fabric 執行階段使用。 

* Fabric_ApplicationHostId
* Fabric_ApplicationHostType
* Fabric_ApplicationId
* **Fabric_ApplicationName**
* Fabric_CodePackageInstanceId
* **Fabric_CodePackageName**
* **Fabric_Endpoint_[YourServiceName]TypeEndpoint**
* **Fabric_Folder_App_Log**
* **Fabric_Folder_App_Temp**
* **Fabric_Folder_App_Work**
* **Fabric_Folder_Application**
* Fabric_NodeId
* **Fabric_NodeIPOrFQDN**
* **Fabric_NodeName**
* Fabric_RuntimeConnectionAddress
* Fabric_ServicePackageInstanceId
* Fabric_ServicePackageName
* Fabric_ServicePackageVersionInstance
* FabricPackageFileName

下面的程式碼顯示如何列出 Service Fabric 環境變數
 ```csharp
    foreach (DictionaryEntry de in Environment.GetEnvironmentVariables())
    {
        if (de.Key.ToString().StartsWith("Fabric"))
        {
            Console.WriteLine(" Environment variable {0} = {1}", de.Key, de.Value);
        }
    }
```
以下是具有服務類型 `FrontEndService` 之應用程式類型 `GuestExe.Application` 的範例環境變數 (當在您的本機開發電腦上執行時)。

* **Fabric_ApplicationName = fabric:/GuestExe.Application**
* **Fabric_CodePackageName = Code**
* **Fabric_Endpoint_FrontEndServiceTypeEndpoint = 80**
* **Fabric_NodeIPOrFQDN = localhost**
* **Fabric_NodeName = _Node_2**

### <a name="application-parameter-files"></a>應用程式參數檔案
Service Fabric 應用程式專案可以包含一或多個應用程式參數檔案。 每個檔案會為應用程式資訊清單中定義的參數定義特定值：

```xml
    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="3" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>
```
根據預設，新的應用程式有三個應用程式參數檔案，名稱分別是 Local.1Node.xml、Local.5Node.xml、Cloud.xml：

![方案總管中的應用程式參數檔案][app-parameters-solution-explorer]

若要建立參數檔案，只需複製並貼上現有的參數檔案並為它提供新名稱。

## <a name="identifying-environment-specific-parameters-during-deployment"></a>在部署期間識別環境特有的參數
在部署階段，您需選擇要套用於您的應用程式的適當參數檔案。 您可以透過 Visual Studio 中的 [發佈] 對話方塊或透過 PowerShell 進行。

### <a name="deploy-from-visual-studio"></a>從 Visual Studio 部署
您可以在 Visual Studio 中發佈應用程式時，從可用的參數檔案清單進行選擇。

![在 [發佈] 對話方塊中選擇參數檔案][publishdialog]

### <a name="deploy-from-powershell"></a>從 PowerShell 部署
應用程式專案範本中包含的 `Deploy-FabricApplication.ps1` PowerShell 指令碼接受發行設定檔作為參數，而 PublishProfile 包含應用程式參數檔案的參考。

  ```PowerShell
    ./Deploy-FabricApplication -ApplicationPackagePath <app_package_path> -PublishProfileFile <publishprofile_path>
  ```

## <a name="next-steps"></a>後續步驟
若要深入了解本主題中討論的一些核心概念，請參閱 [Service Fabric 技術概觀](service-fabric-technical-overview.md)。 如需 Visual Studio 中其他可用的應用程式管理功能的相關資訊，請參閱 [在 Visual Studio 中管理 Service Fabric 應用程式](service-fabric-manage-application-in-visual-studio.md)。

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png

