---
title: "從自訂映像佈建 Azure Batch 集區 | Microsoft Docs"
description: "您可以從自訂映像建立 Batch 集區，以佈建含有您應用程式所需軟體與資料的計算節點。 自訂映像是設定計算節點以執行 Batch 工作負載的有效方式。"
services: batch
author: tamram
manager: timlt
ms.service: batch
ms.topic: article
ms.date: 08/07/2017
ms.author: tamram
ms.openlocfilehash: 3d655766b4f2a5efb0c8c29ffa81a89f84b3e17c
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="use-a-custom-image-to-create-a-pool-of-virtual-machines"></a>使用自訂映像來建立虛擬機器的集區

當您在 Azure Batch 中建立虛擬機器的集區時，可指定虛擬機器 (VM) 映像，以提供集區中每個計算節點的作業系統。 使用 Azure Marketplace 映像，或提供您已備妥的自訂 VHD 映像，即可建立虛擬機器的集區。 當您提供自訂映像時，您可以控制在佈建每個計算節點時的作業系統設定方式。 自訂映像也可以包含已佈建計算節點上可用的應用程式和參考資料。

使用自訂映像可以節省時間，讓您集區的計算節點可立即執行 Batch 工作負載。 雖然您一律可以使用 Azure Marketplace 映像，並且在每個已佈建的計算節點上安裝軟體，但相較於使用自訂映像，這種方法可能比較沒有效率。 

若要使用針對您情節設定的自訂映像，有一些原因，包括需要：

- **設定作業系統 (OS)** 可以在自訂映像上執行之作業系統的任何特別設定。 
- **安裝大型應用程式。** 相較於在佈建完成後，在每個計算節點上安裝應用程式，在自訂映像上安裝應用程式會更有效率。
- **複製大量資料。** 如果將資料複製到自訂映像，只需要複製一次，而不需要複製到每個計算節點，從而節省時間與頻寬。
- **在安裝過程中，重新啟動 VM。** 重新啟動 VM 可能是曠日耗時的流程，尤其是如果您有許多計算節點。

## <a name="prerequisites"></a>必要條件

- **使用「使用者訂用帳戶」集區配置模式建立的 Batch 帳戶。** 若要使用自訂映像來佈建虛擬機器集區，請以「使用者訂用帳戶」[集區配置模式](batch-api-basics.md#pool-allocation-mode)來建立 Batch 帳戶。 使用此模式時，Batch 集區會配置到帳戶所在的訂用帳戶。 如需有關在建立 Batch 帳戶時設定集區配置模式的資訊，請參閱[使用 Batch 開發大規模平行計算解決方案](batch-api-basics.md)中的[帳戶](batch-api-basics.md#account)一節。

- **Azure 儲存體帳戶**。 若要使用自訂映像來建立虛擬機器的集區，您需要在相同訂用帳戶和區域中的標準、一般用途 Azure 儲存體帳戶。 如果您要從 Azure VM 中建立自訂映像，就要將映像複製到 VM 之 OS 磁碟所在的儲存體帳戶，且不需要建立個別的儲存體帳戶。 
    
## <a name="prepare-a-custom-image"></a>準備自訂映像

若要準備自訂映像以便用於 Batch，您必須將現有的 Linux 或 Windows 安裝一般化。 將作業系統安裝一般化可移除機器專屬資訊。 結果會產生可安裝在其他電腦或 VM 上的映像。  

> [!IMPORTANT]
> Batch 目前不支援使用 Azure 管理的映像來佈建集區。 您用於佈建集區的自訂映像必須儲存在 Azure 儲存體中。 
>
> 準備您的自訂映像時，請記住下列幾點：
> - 請確認您用來佈建 Batch 集區的基本 OS 映像沒有任何預先安裝的 Azure 擴充，例如自訂指令碼擴充。 如果映像包含預先安裝的擴充，Azure 在部署 VM 時可能會遇到問題。
> - 請確定您提供的基本 OS 映像使用預設的暫存磁碟機，因為 Batch 節點代理程式目前需要有預設的暫存磁碟機。
>
>

您可以使用任何現有已備妥的 Windows 或 Linux 映像作為自訂映像。 例如，如果您需要使用本機映像，請使用 [AzCopy](../storage/storage-use-azcopy.md) 或另一個上傳工具，將映像上傳到位於相同訂用帳戶和區域中作為 Batch 帳戶的 Azure 儲存體帳戶。

您也可以從新的或現有的 Azure VM 準備自訂映像。 如果您要建立新的 VM，可以使用 Azure Marketplace 映像作為自訂映像的基礎映像，然後加以自訂。 若要自訂基礎映像，請建立 Azure VM 並在其中新增您的應用程式或資料。 然後將 VM 一般化以作為您的自訂映像，並將它儲存到 Azure 儲存體。 

若要從 Azure VM 準備自訂映像，請遵循下列步驟：

1. 從 Azure Marketplace 映像建立**未受管理的**的 Azure VM。 Azure Marketplace 包括 [Windows](../virtual-machines/windows/quick-create-portal.md) 和 [Linux](../virtual-machines/linux/quick-create-portal.md) 的映像。
    
    請在 VM 建立程序的步驟 3 中，確定您 [儲存體：受控磁碟] 的選項是選取 [否]。 另請記下 VM 之 OS 磁碟的儲存體帳戶名稱，因為這個儲存體帳戶也是 Azure 會在其中儲存自訂映像的位置：

    ![建立未受管理的 VM，並記下儲存體帳戶名稱](media/batch-custom-images/vm-create-storage.png)
 
2. 完成您的 VM 建立程序，並等候 Azure 將它進行配置。 以下映像顯示 Azure 入口網站在執行中狀態的 VM：

    ![從市集映像建立 VM](media/batch-custom-images/vm-status-running.png)

3. 一旦 VM 開始執行之後，請透過 RDP (適用於 Windows) 或 SSH (適用於 Linux) 向它連線。 安裝任何必要的軟體或複製所需的資料，然後將 VM 一般化。 請遵循[一般化 VM](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sa-copy-generalized.md#generalize-the-vm) 中所述的步驟操作。 
   
4. 請遵循下列步驟[登入 Azure PowerShell](../virtual-machines/windows/sa-copy-generalized.md#log-in-to-azure-powershell)。 若要安裝 Azure PowerShell，請參閱 [Azure PowerShell 概觀](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.2.0)。 

5. 接下來，請遵循下列步驟以[解除配置 VM 並將狀態設定為一般化](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sa-copy-generalized#deallocate-the-vm-and-set-the-state-to-generalized)。 

    在 Azure 入口網站中，請注意 VM 會解除配置：

    ![請確認 VM 已解除配置](media/batch-custom-images/vm-status-deallocated.png)

6.  使用 [Save-AzureRmVMImage](https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvmimage) PowerShell Cmdlet 來建立 VM 映像並加以儲存至 Azure 儲存體。 請遵循[建立映像](../virtual-machines/windows/sa-copy-generalized.md#create-the-image)中所述的指示。
    
    VM 映像會儲存到建立 VM 時所建立的 Azure 儲存體帳戶，如這個程序的步驟 1 中所示。 Save-AzureRmVMImage Cmdlet 會將映像儲存至該儲存體帳戶中的**系統**容器。 `-DestinationContainername` 參數會命名**系統**容器內的虛擬目錄。 `-VHDNamePrefix` 參數會指定 blob 名稱的前置詞。 blob 名稱前面會加上此前置詞並包含連字號。 

    例如，假設您使用下列參數呼叫 Save-AzureRmVMImage：  

        Save-AzureRmVMImage -ResourceGroupName sample-resource-group -Name vm-custom-image -DestinationContainerName batchimages -VHDNamePrefix custom -Path C:\Temp\Images\vm-custom-image.json

    所產生的影像會儲存到位置，blob 名稱如下所示：

    ![系統容器中儲存 VHD 的位置](media/batch-custom-images/vhd-in-vm-storage-account.png)

    > [!NOTE]
    > Azure 未受管理的 VM 會針對不同用途建立數個儲存體帳戶。 如果建立 VM 時您沒有記下作業系統磁碟的儲存體容器名稱，就請尋找包含**系統**容器的相關聯儲存體帳戶。 瀏覽**系統**容器，以使用您為 **Save-AzureRmVMImage** 命令指定的值來找出自訂映像。

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>在 Azure 入口網站中從自訂映像建立集區

一旦您儲存自訂映像且知道它的位置之後，就可以從該映像建立 Batch 集區。 請遵循下列步驟，從 Azure 入口網站建立集區：

1. 在 Azure 入口網站中瀏覽至您的 Batch 帳戶。 此帳戶必須與包含自訂映像的儲存體帳戶位於相同的訂用帳戶和區域中。 
2. 在左側的 [設定] 視窗中，選取 [集區] 功能表項目。
3. 在 [集區] 視窗中，選取 [新增] 命令。
4. 在 [新增集區] 視窗上，從 [映像類型] 下拉式清單選取 [自訂映像 (Linux/Windows)]。 入口網站會顯示 [自訂映像] 選擇器。 瀏覽至您自訂映像所在的儲存體帳戶，並從容器選取所需的 VHD。 
5. 針對您的自訂 VHD 選取正確的**發行者/提供項目/SKU**。
6. 指定其餘的必要設定，包括**節點大小**、**目標專用的節點**和**低優先權的節點**，以及所需的任何選擇性設定。

    例如，針對 Microsoft Windows Server Datacenter 2016 自訂映像，[新增集區] 視窗隨即顯示，如下所示：

    ![從自訂 Windows 映像新增集區](media/batch-custom-images/add-pool-custom-image.png)
  
若要檢查現有的集區是否以自訂映像作為基礎，請參閱 [集區] 視窗的資源摘要區段中的 **Operating System** 屬性。 如果已從自訂映像建立集區，它就會設定為**自訂 VM 映像**。

與集區相關聯的所有自訂 VHD 都會顯示在集區的 [屬性] 視窗中。
 
## <a name="next-steps"></a>後續步驟

- 如需 Batch 的深入概觀，請參閱[使用 Batch 開發大規模的平行計算解決方案](batch-api-basics.md)。
- 如需建立 Batch 帳戶的相關資訊，請參閱[使用 Azure 入口網站建立 Batch 帳戶](batch-account-create-portal.md)。