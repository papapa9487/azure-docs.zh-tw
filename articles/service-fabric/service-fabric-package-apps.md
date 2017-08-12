---
title: "對 Azure Service Fabric 應用程式進行封裝 | Microsoft Docs"
description: "如何在將 Service Fabric 應用程式部署至叢集之前對它進行封裝。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/27/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 48e4ad774164b87d0cacb42f709e54af1d6f07b9
ms.contentlocale: zh-tw
ms.lasthandoff: 06/30/2017

---
# <a name="package-an-application"></a>封裝應用程式
本文說明如何對 Service Fabric 應用程式進行封裝，並使其準備好進行部署。

## <a name="package-layout"></a>封裝版面配置
應用程式資訊清單、一或多個服務資訊清單及其他必要的封裝檔案必須以特定的配置組織後，才能部署到 Service Fabric 叢集。 在本文中的範例資訊清單必須組織成下列目錄結構：

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

命名資料夾以符合每個對應元素的 **名稱** 屬性。 例如，如果服務資訊清單包含名稱為 **MyCodeA** 和 **MyCodeB** 的兩個程式碼封裝，則同名的兩個資料夾會包含每個程式碼封裝所需的二進位檔。

## <a name="use-setupentrypoint"></a>使用 SetupEntryPoint
使用 **SetupEntryPoint** 的一般案例，是當您必須在服務啟動之前執行可執行檔，或必須使用提高的權限來執行作業時。 例如：

* 設定及初始化服務可執行檔需要的環境變數。 這不僅限於透過 Service Fabric 程式設計模型撰寫的可執行檔。 例如，npm.exe 部署 node.js 應用程式，需要設定某些環境變數。
* 透過安裝安全性憑證設定存取控制。

如需有關如何設定 **SetupEntryPoint** 的詳細資訊，請參閱[設定服務設定進入點的原則](service-fabric-application-runas-security.md)

<a id="Package-App"></a>
## <a name="configure"></a>設定
### <a name="build-a-package-by-using-visual-studio"></a>使用 Visual Studio 建置封裝
如果您使用 Visual Studio 2015 來建立您的應用程式，您可以使用 [封裝] 命令來自動建立符合上述版面配置的封裝。

若要建立封裝，請以滑鼠右鍵按一下方案總管中的應用程式專案，然後選擇 [封裝] 命令，如下所示：

![使用 Visual Studio 封裝應用程式][vs-package-command]

封裝完成時，您會在 [輸出]  視窗中找到封裝的位置。 當您在 Visual Studio 中部署或偵錯應用程式時，封裝步驟會自動執行。

### <a name="build-a-package-by-command-line"></a>透過命令列建置封裝
使用 `msbuild.exe` 以程式設計方式封裝您的應用程式也是可行的。 深究其背後的原理，由於 Visual Studio 執行它，因此輸出也會相同。

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>測試封裝
您可以使用 [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) 命令，透過 PowerShell 在本機上驗證封裝結構。
此命令會檢查有無資訊清單剖析問題，並驗證所有參考。 這個命令只會驗證封裝中目錄與檔案的結構正確性。
除了檢查所有必要檔案是否都在之外，它不會驗證任何程式碼或資料封裝內容。

```
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

這個錯誤顯示程式碼封裝中遺漏服務資訊清單 *SetupEntryPoint* 中參考的 **MySetup.bat** 檔案。 加入遺漏的檔案之後，應用程式驗證就會通過：

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
```

如果您的應用程式已定義[應用程式參數](service-fabric-manage-multiple-environment-app-configuration.md)，您可以在 [Test-ServiceFabricApplicationPackage (英文)](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) 中傳遞它們以進行適當的驗證。

如果您知道將部署應用程式的叢集，建議您傳送 `ImageStoreConnectionString` 參數。 在此情況下，該封裝也會針對已在叢集中執行的舊版應用程式進行驗證。 例如，驗證可以偵測具有相同版本但不同內容的封裝是否已經部署。  

一旦應用程式已完成封裝並通過驗證，請根據檔案的大小及數目，評估是否需要壓縮。

## <a name="compress-a-package"></a>壓縮封裝
當封裝很大或有許多檔案時，您可以壓縮它以加快部署速度。 壓縮會減少檔案數目並降低封裝大小。
就已壓縮的應用程式套件而言，[上傳應用程式套件](service-fabric-deploy-remove-applications.md#upload-the-application-package)可能比上傳未壓縮的套件需要花費更多的時間 (特別是如果將壓縮時間也計算在內的話)，但在[註冊](service-fabric-deploy-remove-applications.md#register-the-application-package)和[取消應用程式類型註冊](service-fabric-deploy-remove-applications.md#unregister-an-application-type)方面，已壓縮的應用程式套件所花費的時間則較短。

已壓縮和未壓縮套件的部署機制都相同。 如果封裝已壓縮，它會以壓縮的形式儲存在叢集映像存放區中，並在應用程式執行之前於節點上解壓縮。
壓縮會將有效的 Service Fabric 封裝以壓縮的版本取代。 該資料夾必須允許寫入權限。 在已經壓縮的封裝上執行壓縮將不會產生任何變化。

您可以執行 PowerShell 命令 [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps)，並搭配 `CompressPackage` 參數來壓縮封裝。 您可以使用相同的命令，並搭配 `UncompressPackage` 參數來將封裝解壓縮。

下列命令會在不將封裝複製到映像存放區的情況下壓縮封裝。 您可以在不搭配 `SkipCopy` 旗標的情況下使用 [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps)，視需求將壓縮的封裝複製到一或多個 Service Fabric 叢集。
該套件現在包含 `code`、`config` 及 `data` 套件的 ZIP 壓縮檔案。 應用程式資訊清單和服務資訊清單不會壓縮，因為有許多內部作業都需要它們 (例如特定驗證的封裝共用、應用程式類型名稱及版本擷取)。
對資訊清單進行壓縮，將會使這些作業效率不佳。

```
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy

PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

除此之外，您可以使用 [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps)，同時壓縮並複製封裝。
如果封裝很大，請提供足夠的逾時，使封裝可以完成壓縮並上傳至叢集。
```
PS D:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Service Fabric 會在內部針對驗證計算應用程式封裝的總和檢查碼。 使用壓縮時，會針對每個封裝的壓縮版本計算總和檢查碼。
若您已複製應用程式套件的未壓縮版本，而想要將壓縮用於同一個套件，您就必須變更 `code`、`config` 和 `data` 套件的版本，以避免總和檢查碼不符。 若封裝未變更，則您可不變更版本而使用 [差異佈建](service-fabric-application-upgrade-advanced.md)。 使用此選項時，請勿包含未變更的套件，而是要從服務資訊清單參考它。

同樣地，若您已上傳封裝的壓縮版本，且想要使用未壓縮的封裝，則您必須更新版本以避免總和檢查碼不符。

封裝現已正確完成封裝、驗證及壓縮 (若有需要)，因此已準備好[部署](service-fabric-deploy-remove-applications.md)至一或多個 Service Fabric 叢集。

### <a name="compress-packages-when-deploying-using-visual-studio"></a>使用 Visual Studio 進行部署時壓縮套件
您可以將 `CopyPackageParameters` 元素新增到發行設定檔，並將 `CompressPackage` 屬性設定為 `true`，來指示 Visual Studio 在部署時壓縮套件。

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="next-steps"></a>後續步驟
[部署與移除應用程式][10]說明如何使用 PowerShell 來管理應用程式執行個體。

[管理多個環境的應用程式參數][11]說明如何為不同的應用程式執行個體設定參數和環境變數。

[設定應用程式的安全性原則][12]說明如何依據安全性原則執行服務來限制存取。

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md

