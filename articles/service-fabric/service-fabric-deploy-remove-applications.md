---
title: "Azure Service Fabric 應用程式部署 | Microsoft Docs"
description: "如何使用 PowerShell 部署和移除 Service Fabric 中的應用程式。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/05/2017
ms.author: ryanwi
ms.openlocfilehash: 6d0f85a839171c43d226741f54e0dc954b85601d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-and-remove-applications-using-powershell"></a>使用 PowerShell 部署與移除應用程式
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Visual Studio](service-fabric-publish-app-remote-cluster.md)
> * [FabricClient API](service-fabric-deploy-remove-applications-fabricclient.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)

<br/>

[封裝應用程式類型][10]後，即可將它部署至 Azure Service Fabric 叢集中。 部署涉及下列三個步驟：

1. 將應用程式封裝上傳至映像存放區
2. 註冊應用程式類型
3. 建立應用程式執行個體

在應用程式中部署且個體開始在叢集中執行之後，您就可以刪除應用程式執行個體和其應用程式類型。 從叢集完全移除應用程式需要執行下列步驟︰

1. 移除 (或刪除) 執行中的應用程式執行個體
2. 取消註冊不再需要的應用程式類型
3. 移除映像存放區中的應用程式封裝

如果您在本機開發叢集上使用 [Visual Studio 部署和偵錯應用程式](service-fabric-publish-app-remote-cluster.md)，則先前所有步驟都會透過 PowerShell 指令碼自動處理。  在應用程式專案的 [指令碼] 資料夾中可找到這個指令碼。 本文提供該指令碼的背景資料，讓您可以在 Visual Studio 之外執行相同的作業。 
 
## <a name="connect-to-the-cluster"></a>連接到叢集
執行本文中的任何 PowerShell 命令之前，請一律透過使用 [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) 連接至 Service Fabric 叢集的方式啟動。 若要連線至本機開發叢集，請執行下列命令︰

```powershell
PS C:\>Connect-ServiceFabricCluster
```

針對連線至遠端叢集，或連線至使用 Azure Active Directory、X509 憑證或 Windows Active Directory 保護的叢集，如需相關的範例，請參閱[連線至安全的叢集](service-fabric-connect-to-secure-cluster.md)。

## <a name="upload-the-application-package"></a>上傳應用程式封裝
上傳應用程式封裝會將它放在一個可由內部 Service Fabric 元件存取的位置。
如果您想要在本機確認應用程式封裝，使用 [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) cmdlet。

[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 命令會將應用程式封裝上傳至叢集映像存放區。

假設您在 Visual Studio 2015 中建置並封裝名為 *MyApplication* 的應用程式。 根據預設，ApplicationManifest.xml 中列出的應用程式類型名稱會是 "MyApplicationType"。  應用程式封裝 (其中包含必要的應用程式資訊清單、服務資訊清單和程式碼/組態/資料封裝) 位於 *C:\Users\<username\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*。 

下列命令會列出應用程式封裝的內容︰

```powershell
PS C:\> $path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
PS C:\> tree /f $path
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

如果應用程式封裝是大型且/或有許多檔案，您可以[壓縮](service-fabric-package-apps.md#compress-a-package)。 壓縮會減少檔案大小和數目。
副作用是註冊和取消註冊應用程式類型會比較快。 目前上傳時間可能會變慢，特別是當您包含壓縮封裝的時間。 

若要壓縮封裝，請使用相同的 [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 命令。 壓縮可與上傳分開進行，方法為使用 `SkipCopy` 旗標，或是與上傳作業共同進行。 在壓縮封裝上套用壓縮為無作業。
若要將已壓縮的封裝解壓縮，請使用相同的 [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 命令搭配 `UncompressPackage` 參數。

下列 Cmdlet 會在不將封裝複製到映像存放區的情況下壓縮封裝。 該套件現在包含 `Code` 及 `Config` 封裝的 ZIP 壓縮檔案。 應用程式和服務資訊清單不會壓縮，因為有許多內部作業都需要它們 (例如特定驗證的封裝共用、應用程式類型名稱及版本擷取)。 對資訊清單進行壓縮，將會使這些作業效率不佳。

```
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
PS C:\> tree /f $path
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
|   ApplicationManifest.xml
|
└───Stateless1Pkg
       Code.zip
       Config.zip
       ServiceManifest.xml
```

針對大型應用程式封裝，壓縮會很花時間。 為了獲得最佳結果，請使用快速的 SSD 磁碟機。 壓縮時間和已壓縮封裝的大小也會根據封裝內容而有所不同。
例如，以下是一些封裝的壓縮統計資料，其顯示初始和壓縮的封裝大小，以壓縮時間。

|初始大小 (MB)|檔案計數|壓縮時間|壓縮的封裝大小 (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

一旦壓縮封裝後，可以視需要將它上傳到一或多個 Service Fabric 叢集。 已壓縮及未壓縮套件的部署機制皆相同。 如果封裝已壓縮，它會以壓縮的形式儲存在叢集映像存放區中，並在應用程式執行之前於節點上解壓縮。


下列範例會將套件上傳至映像存放區中的 "MyApplicationV1" 資料夾︰

```powershell
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

如果您未指定 -ApplicationPackagePathInImageStore 參數，應用程式套件會複製到映像存放區中的 "Debug" 資料夾。

>[!NOTE]
>如果 PowerShell 工作階段連線到 Service Fabric 叢集，**Copy-ServiceFabricApplicationPackage** 將會自動偵測適當的映像存放區連接字串。 針對 5.6 之前的 Service Fabric 版本，必須明確地提供 **-ImageStoreConnectionString** 引數。
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>**Get ImageStoreConnectionStringFromClusterManifest** Cmdlet 是 Service Fabric SDK PowerShell 模組的一部分，可用來取得映像存放區連接字串。  若要匯入 SDK 模組，請執行︰
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>請參閱[了解映像存放區連接字串](service-fabric-image-store-connection-string.md)，以取得有關映像存放區和映像存放區連接字串的補充資訊。
>
>
>

上傳封裝的時間會根據多個因素而有所不同。 這些因素有些是封裝中的檔案數目、封裝大小和檔案大小。 在來源電腦及 Service Fabric 叢集之間的網路速度也會影響上傳時間。 [Copy-servicefabricapplicationpackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 的預設逾時值為 30 分鐘。
根據所述的因素，您可能必須增加逾時。 如果要在複製呼叫中壓縮封裝，您需要同時考慮壓縮時間。



## <a name="register-the-application-package"></a>註冊應用程式封裝
註冊應用程式套件時，應用程式類型和應用程式資訊清單中宣告的版本可供使用。 系統會讀取在上一個步驟上傳的套件，請確認套件、處理套件內容，然後將處理過的套件複製至內部系統位置。  

執行 [Register-servicefabricapplicationtype](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) Cmdlet，將應用程式類型註冊在叢集，使它可供部署使用︰

```powershell
PS C:\> Register-ServiceFabricApplicationType MyApplicationV1
Register application type succeeded
```

"MyApplicationV1" 是應用程式套件所在映像存放區中的資料夾。 名稱為 "MyApplicationType" 和版本為 "1.0.0" (兩者都在應用程式資訊清單中) 的應用程式類型，現在已註冊在叢集中。

[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) 命令只有在系統成功註冊應用程式封裝之後才會返回。 註冊所需時間取決於應用程式封裝的大小和內容。 **-TimeoutSec** 參數可在必要時用來提供較長的逾時 (預設逾時為 60 秒)。

如果您有大型應用程式套件或如果您遇到逾時，使用 **-Async** 參數。 當叢集接受暫存器命令時會傳回此命令，並視需要繼續處理。
[Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) 命令會列出所有成功註冊的應用程式類型版本和其註冊狀態。 您可以使用此命令以判斷何時會完成註冊。

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>從映像存放區移除應用程式封裝
建議您在成功註冊應用程式之後，移除應用程式套件。  從映像存放區刪除應用程式套件會釋放系統資源。  保留未使用的應用程式套件會耗用磁碟儲存空間，並會導致應用程式效能問題。

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>建立應用程式
您可以使用 [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) Cmdlet，從任何已成功註冊的應用程式類型版本，將應用程式具現化。 每個應用程式名稱的開頭必須為 "fabric:" 配置，而且必須是每個應用程式執行個體的唯一名稱。 如果已在目標應用程式類型的應用程式資訊清單中定義預設服務，也會一併建立這些服務。

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
多個應用程式執行個體可以針對任何指定的已註冊應用程式類型版本來建立。 每個應用程式執行個體將在隔離狀態下執行，包含本身的工作目錄和程序。

若要查看哪些具名的應用程式和服務正在叢集中執行，請執行 [Get-servicefabricapplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) 和 [Get-servicefabricservice](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps) Cmdlet︰

```powershell
PS C:\> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}

PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>移除應用程式
當不再需要應用程式執行個體時，您可以使用 [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) Cmdlet，依名稱永久移除它。 [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) 也會自動移除屬於應用程式的所有服務，永久地移除所有服務狀態。 

> [!WARNING]
> 此作業無法回復，且應用程式狀態無法復原。

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>取消註冊應用程式類型
當不再需要應用程式類型的特定版本時，您應該使用 [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) Cmdlet 取消註冊該應用程式類型。 取消註冊未使用的應用程式類型會透過移除應用程式二進位檔，來釋放映像存放區所使用的儲存空間。 取消註冊應用程式類型並不會移除應用程式套件。 只要沒有對應的應用程式針對應用程式類型進行具現化，且沒有擱置中的應用程式升級進行參考該類性，便可取消註冊該應用程式類型。

執行 [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) Cmdlet，查看目前註冊在叢集中的應用程式類型：

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

執行 [Unregister-servicefabricapplicationtype](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps)，取消註冊特定應用程式類型︰

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>疑難排解
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage 要求 ImageStoreConnectionString
Service Fabric SDK 環境應已正確設定預設值。 但若有需要，所有命令的 ImageStoreConnectionString都應符合 Service Fabric 叢集正在使用的值。 您可以在使用 [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) 和 Get-ImageStoreConnectionStringFromClusterManifest 命令擷取的叢集資訊清單 ImageStoreConnectionString 中找到此值：

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

**Get ImageStoreConnectionStringFromClusterManifest** Cmdlet 是 Service Fabric SDK PowerShell 模組的一部分，可用來取得映像存放區連接字串。  若要匯入 SDK 模組，請執行︰

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

會在叢集資訊清單中找到 ImageStoreConnectionString：

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

請參閱[了解映像存放區連接字串](service-fabric-image-store-connection-string.md)，以取得有關映像存放區和映像存放區連接字串的補充資訊。

### <a name="deploy-large-application-package"></a>部署大型應用程式封裝
問題︰大型應用程式封裝 (GB 的順序) 的 [Copy-servicefabricapplicationpackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 逾時。
請嘗試︰
- 使用`TimeoutSec`參數指定 [Copy-servicefabricapplicationpackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) 命令的較大逾時。 此逾時預設為 30 分鐘。
- 檢查來源電腦與叢集之間的網路連線。 如果連線速度變慢，請考慮使用更佳網路連線的電腦。
如果用戶端電腦與叢集在不同的區域中，請考慮使用與叢集接近或相同區域中的用戶端電腦。
- 請檢查是否到達外部節流。 例如，當映像存放區設定為使用 Azure 儲存體時，上傳可能受到節流控制。

問題︰上傳封裝順利完成，但 [Register-servicefabricapplicationtype](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) 逾時。請嘗試︰
- 複製到映像存放區之前[壓縮封裝](service-fabric-package-apps.md#compress-a-package)。
壓縮會減少檔案的大小和數目，而後者則可減少資料傳輸量和 Service Fabric 必須執行的工作。 上傳作業可能會變慢 (尤其是如果您包含壓縮時間)，但註冊和取消註冊應用程式類型會比較快。
- 使用 `TimeoutSec` 參數指定 [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) 的較大逾時。
- 指定 [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) 的 `Async` 參數。 當叢集接受命令時會傳回此命令，而且會繼續以非同步方式進行應用程式類型的註冊。 基於這個理由，在此情況下不需要指定較高的逾時。 [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) 命令會列出所有成功註冊的應用程式類型版本和其註冊狀態。 您可以使用此命令以判斷何時會完成註冊。

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>部署具有很多檔案的應用程式封裝
問題︰具有很多檔案的應用程式封裝 (以千計的順序) [Register-servicefabricapplicationtype](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) 的逾時。
請嘗試︰
- 複製到映像存放區之前[壓縮封裝](service-fabric-package-apps.md#compress-a-package)。 壓縮會減少檔案的數目。
- 使用 `TimeoutSec` 參數指定 [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) 的較大逾時。
- 指定 [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) 的 `Async` 參數。 當叢集接受命令時會傳回此命令，而且會繼續以非同步方式進行應用程式類型的註冊。
基於這個理由，在此情況下不需要指定較高的逾時。 [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) 命令會列出所有成功註冊的應用程式類型版本和其註冊狀態。 您可以使用此命令以判斷何時會完成註冊。

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>後續步驟
[Service Fabric 應用程式升級](service-fabric-application-upgrade.md)

[Service Fabric 健康狀態簡介](service-fabric-health-introduction.md)

[診斷和疑難排解 Service Fabric 服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[在 Service Fabric 中模型化應用程式](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md
