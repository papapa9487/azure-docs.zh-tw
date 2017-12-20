---
title: "Azure Batch 上的容器工作負載 | Microsoft Docs"
description: "了解如何在 Azure Batch 上透過容器映像執行應用程式。"
services: batch
author: v-dotren
manager: timlt
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 12/01/2017
ms.author: v-dotren
ms.openlocfilehash: 1795bdde5506f599849a30d4e59ed7b916595ac4
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2017
---
# <a name="run-container-applications-on-azure-batch"></a>在 Azure Batch 上執行容器應用程式

Azure Batch 可讓您在 Azure 上執行及縮放大量批次運算作業。 到目前為止，Batch 工作已可直接在 Batch 集區中的虛擬機器 (VM) 上執行，但現在您可以將 Batch 集區設定為在 Docker 容器中執行工作。

使用容器可讓您輕鬆地執行 Batch 工作，無須管理應用程式套件和相依性。 容器會將應用程式部署為輕量、可攜且自給自足的單位，以便在各種環境中執行。 例如，您可以在本機建置及測試容器，然後將容器映像上傳至 Azure 或其他位置中的登錄。 容器部署模型可確保您應用程式的執行階段環境會正確地安裝和設定 (無論您的應用程式裝載在何處)。 本教學課程會示範如何使用 Batch .NET SDK 建立可支援執行容器工作的運算節點集區，以及如何在集區上執行容器工作。

本文假設您已熟悉 Docker 容器的概念，以及了解如何使用 .NET SDK 建立 Batch 集區和作業。 程式碼片段主要用於用戶端應用程式 (類似 [DotNetTutorial 範例](batch-dotnet-get-started.md))，並且是在 Batch 中支援容器應用程式所需的程式碼範例。


## <a name="prerequisites"></a>必要條件

* SDK 版本：以下版本的 Batch SDK 可支援容器映像：
    * Batch REST API 2017-09-01.6.0 版
    * Batch .NET SDK 8.0.0 版
    * Batch Python SDK 4.0 版
    * Batch Java SDK 3.0 版
    * Batch Node.js SDK 3.0 版

* 帳戶：在您的 Azure 帳戶中，您需要建立 Batch 帳戶以及選擇性地建立一般用途的儲存體帳戶。

* 支援的虛擬機器映像。 只有從下一節＜支援的虛擬機器映像＞中所述的映像，使用虛擬機器設定建立的集區，才可支援容器。

* 如果您提供自訂映像，您的應用程式必須使用 Azure Active Directory (Azure AD) 驗證，才能執行以容器為基礎的工作負載。 如果您使用 Azure Marketplace 映像，則不需要 Azure AD 驗證，因為系統會使用共用金鑰驗證。 Azure Batch 對於 Azure AD 的支援記載於[使用 Active Directory 驗證 Batch 服務解決方案](batch-aad-auth.md)中。


## <a name="supported-virtual-machine-images"></a>支援的虛擬機器映像

您必須提供 Windows 或 Linux 映像來建立虛擬機器計算節點的集區。

### <a name="windows-images"></a>Windows 映像

對於 Windows 容器的工作負載，Batch 目前支援的自訂映像需透過在 Windows 中執行 Docker 的虛擬機器來建立，或是您可以使用 Azure Marketplace 中具有容器映像的 Windows Server 2016 Datacenter。 此映像與 `batch.node.windows amd64` 節點代理程式 SKU 識別碼相容。 目前支援的容器類型僅限於 Docker。

### <a name="linux-images"></a>Linux 映像

針對 Linux 容器的工作負載，Batch 目前支援的自訂映像只能從在下列 Linux 發行版本上執行 Docker 的虛擬機器建立：Ubuntu 16.04 LTS 或 CentOS 7.3。 如果您選擇提供您自己自訂的 Linux 映像，請參閱[使用受管理自訂映像來建立虛擬機器的集區](batch-custom-images.md)中的指示。

您可以使用 [Docker Community Edition (CE)](https://www.docker.com/community-edition) 或 [Docker Enterprise Edition (EE)](https://www.docker.com/enterprise-edition)。

如果您想要充分利用 Azure NC 或 NV 虛擬機器大小的 GPU 效能，您需要在映像上安裝 NVIDIA 驅動程式。 此外，您需要安裝及執行適用於 NVIDIA GPU 的 Docker 引擎公用程式 [NVIDIA Docker](https://github.com/NVIDIA/nvidia-docker)。

若要存取 Azure RDMA 網路，請使用下列大小：A8、A9、H16r、H16mr 或 NC24r。 必要的 RDMA 驅動程式會安裝在 Azure Marketplace 中的 CentOS 7.3 HPC 和 Ubuntu 16.04 LTS 映像上。 若要執行 MPI 工作負載，則可能需要其他設定。 請參閱[在 Batch 集區中使用具備 RDMA 功能或已啟用 GPU 功能的執行個體](batch-pool-compute-intensive-sizes.md)。


## <a name="limitations"></a>限制

* Batch 只會對 Linux 集區上執行的容器提供 RDMA 支援。


## <a name="authenticate-using-azure-active-directory"></a>使用 Azure Active Directory 進行驗證

如果您使用自訂的虛擬機器映像來建立 Batch 集區，則用戶端應用程式必須使用 Azure AD 整合式驗證來進行驗證 (無法使用共用金鑰驗證)。 執行應用程式之前，請先確定您已在 Azure AD 中註冊該應用程式並為其建立身分識別，以及已指定其權限給其他應用程式。

此外，當您使用自訂的虛擬機器映像時，您需要授與應用程式可存取虛擬機器映像的 IAM 存取控制。 在 Azure 入口網站中，開啟 [所有資源] 並選取容器映像，然後在映像刀鋒視窗中的 [存取控制 (IAM)] 區段上按一下 [新增]。 在 [新增權限] 刀鋒視窗中，指定**角色**，在 [指派存取權] 中選取 [Azure AD 使用者、群組或應用程式]，然後在 [選取] 中輸入應用程式名稱。

當您在應用程式中使用 [BatchClient.Open](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_) 建立 Batch 用戶端時 (如[使用 Active Directory 驗證 Batch 服務解決方案](batch-aad-auth.md)中所述)，便會產生 Azure AD 驗證權杖。


## <a name="reference-a-vm-image-for-pool-creation"></a>參考虛擬機器映像以建立集區

在應用程式的程式碼中，提供用來建立集區計算節點的虛擬機器映像參考。 您可以透過建立 [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference) 物件來執行此動作。 您可以使用下列其中一個方式來指定要使用的映像：

* 如果您使用自訂映像，請提供虛擬機器映像的 Azure Resource Manager 資源識別碼。 映像識別碼的路徑格式如下列範例所示：

  ```csharp
  // Provide a reference to a custom image using an image ID
  ImageReference imageReference = new ImageReference("/subscriptions/<subscription-ID>/resourceGroups/<resource-group>/providers/Microsoft.Compute/images/<imageName>");
  ```

    若要從 Azure 入口網站中取得此映像識別碼，請開啟 [所有資源]，選取自訂映像，然後從映像刀鋒視窗中的 [概觀]區段中，複製 [資源識別碼] 中的路徑。

* 如果您使用 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?page=1&subcategories=windows-based) 映像，請提供描述映像的一組參數：提供項目的類型、發行者、SKU 和映像版本，如[虛擬機器映像清單](batch-linux-nodes.md#list-of-virtual-machine-images)中所示：

  ```csharp
  // Provide a reference to an Azure Marketplace image for
  // "Windows Server 2016 Datacenter with Containers"
  ImageReference imageReference = new ImageReference(
    publisher: "MicrosoftWindowsServer",
    offer: "WindowsServer",
    sku: "2016-Datacenter-with-Containers",
    version: "latest");
  ```


## <a name="container-configuration-for-batch-pool"></a>Batch 集區的容器設定

Batch 集區是作業中 Batch 執行工作所在的計算節點集合。 當您建立集區時，您可以使用計算節點的虛擬機器設定來提供該項目。 [VirtualMachineConfiguration](/dotnet/api/microsoft.azure.batch.virtualmachineconfiguration) 物件包含 [ContainerConfiguration](/dotnet/api/microsoft.azure.batch.containerconfiguration) 物件的參考。 若要在集區上啟用容器工作負載，請指定 `ContainerConfiguration` 設定。 虛擬機器設定也是您指定映像參考和映像節點代理程式 SKU 識別碼的地方，如下列範例所示。

您有多個選項可用來建立集區。 您可以使用或不使用預先擷取的容器映像，來建立一個集區。 

提取 (或預先擷取) 程序可讓您從 Docker Hub 或網際網路上的另一個容器登錄預先載入容器映像。 預先擷取容器映像的好處是，當工作第一次開始執行時，不必等待容器映像下載。 容器設定會在集區建立時將容器映像提取到虛擬機器中。 然後，在集區上執行的工作就可以參考容器映像清單和容器執行選項。



### <a name="pool-without-prefetched-container-images"></a>沒有預先擷取容器映像的集區

若要設定沒有預先擷取容器映像的集區，請定義 `ContainerConfiguration` 和 `VirtualMachineConfiguration` 物件，如下列範例所示。 此範例與下列範例皆假設您使用已安裝 Docker 引擎的自訂 Ubuntu 16.04 LTS 映像。

```csharp
// Specify container configuration
ContainerConfiguration containerConfig = new ContainerConfiguration();

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-for-container-configuration"></a>為容器設定預先擷取映像

若要在集區上預先擷取容器映像，請將容器映像清單 (`containerImageNames`) 新增至 `ContainerConfiguration`，並提供映像清單的名稱。 下列範例假設您使用自訂 Ubuntu 16.04 LTS 映像、從 [Docker Hub](https://hub.docker.com) 預先擷取 TensorFlow 映像，以及在啟動工作中啟動 TensorFlow。

```csharp
// Specify container configuration, prefetching Docker images
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> { "tensorflow/tensorflow:latest-gpu" } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Set a native command line start task
StartTask startTaskNative = new StartTask( CommandLine: "<native-host-command-line>" );

// Define container settings
TaskContainerSettings startTaskContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu");
StartTask startTaskContainer = new StartTask(
    CommandLine: "<docker-image-command-line>",
    TaskContainerSettings: startTaskContainerSettings);

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration, startTaskContainer);

// Commit pool creation
pool.Commit();
```


### <a name="prefetch-images-from-a-private-container-registry"></a>從私人容器登錄中預先擷取映像

您也可以透過驗證私人容器登錄伺服器，來預先擷取容器映像。 在下列範例中，`ContainerConfiguration`和 `VirtualMachineConfiguration` 物件會使用自訂 Ubuntu 16.04 LTS 映像，而且會從私人 Azure 容器登錄中預先提取私人 TensorFlow 映像。

```csharp
// Specify a container registry
ContainerRegistry containerRegistry = new ContainerRegistry (
    registryServer: "myContainerRegistry.azurecr.io",
    username: "myUserName",
    password: "myPassword");

// Create container configuration, prefetching Docker images from the container registry
ContainerConfiguration containerConfig = new ContainerConfiguration(
    containerImageNames: new List<string> {
        "myContainerRegistry.azurecr.io/tensorflow/tensorflow:latest-gpu" },
    containerRegistries: new List<ContainerRegistry> { containerRegistry } );

// VM configuration
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
    imageReference: imageReference,
    containerConfiguration: containerConfig,
    nodeAgentSkuId: "batch.node.ubuntu 16.04");

// Create pool
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 4,
    virtualMachineSize: "Standard_NC6",
    virtualMachineConfiguration: virtualMachineConfiguration);

// Commit pool creation
pool.Commit();
```


## <a name="container-settings-for-the-task"></a>工作的容器設定

當您設定要在計算節點上執行的工作時，您必須指定容器專屬的設定，例如工作執行選項、要使用的映像和登錄。

使用工作類別的 ContainerSettings 屬性來設定容器專屬設定。 這些設定會由 [TaskContainerSettings](/dotnet/api/microsoft.azure.batch.taskcontainersettings) 類別定義。

如果您在容器映像上執行工作，[雲端工作](/dotnet/api/microsoft.azure.batch.cloudtask)和[作業管理員工作](/dotnet/api/microsoft.azure.batch.cloudjob.jobmanagertask)會需要容器設定。 但是，[啟動工作](/dotnet/api/microsoft.azure.batch.starttask)、[作業準備工作](/dotnet/api/microsoft.azure.batch.cloudjob.jobpreparationtask)和[作業解除工作](/dotnet/api/microsoft.azure.batch.cloudjob.jobreleasetask)不需要容器設定 (也就是這些工作可以在容器內容中執行或直接在節點上執行)。

當您設定容器設定時，以遞迴方式列在 `AZ_BATCH_NODE_ROOT_DIR` (節點上的 Azure Batch 根目錄) 下方的目錄會對應至容器，所有工作環境變數會對應至容器，而工作命令列會在容器中執行。

[為容器設定預先擷取映像](#prefetch-images-for-container-configuration)中的程式碼範例會示範如何為啟動工作指定容器設定。 下列程式碼範例會示範如何為雲端工作指定容器設定：

```csharp
// Simple task command

string cmdLine = "<my-command-line>";

TaskContainerSettings cmdContainerSettings = new TaskContainerSettings (
    imageName: "tensorflow/tensorflow:latest-gpu",
    containerRunOptions: "--rm --read-only"
    );

CloudTask containerTask = new CloudTask (
    id: "Task1",
    containerSettings: cmdContainerSettings,
    commandLine: cmdLine); 
```


## <a name="next-steps"></a>後續步驟

* 如需 Batch 的深入概觀，請參閱[使用 Batch 開發大規模的平行計算解決方案](batch-api-basics.md)。

* 如需有關在 Linux 上安裝和使用 Docker CE 的詳細資訊，請參閱 [Docker](https://docs.docker.com/engine/installation/) 文件。

* 如需使用自訂映像的詳細資訊，請參閱[使用受管理的自訂映像來建立虛擬機器的集區](batch-custom-images.md)。
