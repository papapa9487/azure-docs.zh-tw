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
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 1248aeeaa159789b008eb003c2cd7babe0432919
ms.contentlocale: zh-tw
ms.lasthandoff: 08/09/2017

---

# <a name="use-a-custom-image-to-create-a-pool"></a>使用自訂映像來建立集區

當您在 Azure Batch 中建立的集區時，您可指定虛擬機器 (VM) 映像，以提供集區中每個計算節點的作業系統組態。 使用 Azure Marketplace 映像，或提供您已備妥的自訂映像，即可建立集區。 當您提供自訂映像時，您可以控制在佈建每個計算節點時的作業系統設定方式。 自訂映像也可以包含已佈建計算節點上可用的應用程式和參考資料。

使用自訂映像可以節省時間，讓您集區的計算節點可立即執行 Batch 工作負載。 雖然您一律可以使用 Azure Marketplace 映像，並且在每個已佈建的計算節點上安裝軟體，但相較於使用自訂映像，這種方法可能比較沒有效率。 如果您必須安裝大型應用程式、複製大量資料，或在安裝程序期間重新啟動 VM，請考慮使用針對您的需求所設定的自訂映像。  

## <a name="prerequisites"></a>必要條件

- **使用「使用者訂用帳戶」集區配置模式建立的 Batch 帳戶。** 若要使用自訂映像來佈建虛擬機器集區，請以「使用者訂用帳戶」[集區配置模式](batch-api-basics.md#pool-allocation-mode)來建立 Batch 帳戶。 使用此模式時，Batch 集區會配置到帳戶所在的訂用帳戶。 如需有關在建立 Batch 帳戶時設定集區配置模式的資訊，請參閱[使用 Batch 開發大規模平行計算解決方案](batch-api-basics.md)中的[帳戶](batch-api-basics.md#account)一節。

- **Azure 儲存體帳戶**。 若要使用自訂映像來建立虛擬機器設定集區，您需要一或多個標準 Azure 儲存體帳戶來儲存自訂的 VHD 映像。 自訂映像會儲存為 blob。 建立集區時若要參考自訂映像，請針對 [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/add-a-pool-to-an-account#bk_vmconf) 屬性的 [osDisk](https://docs.microsoft.com/rest/api/batchservice/add-a-pool-to-an-account#bk_osdisk) 屬性，指定自訂映像 VHD blob 的 URI。

    請確定您的儲存體帳戶符合下列準則：   
    
    - 包含自訂映像 VHD blob 的儲存體帳戶必須與 Batch 帳戶 (使用者訂用帳戶) 位於相同的訂用帳戶。
    - 指定的儲存體帳戶必須與 Batch 帳戶位於相同區域中。
    - 目前僅支援標準一般用途的儲存體帳戶。 未來將會支援 Azure 高階儲存體。
    - 您可以指定一個具有多個自訂 VHD blob 的儲存體帳戶，或是多個儲存體帳戶，每個皆具有單一的 blob。 建議您使用多個儲存體帳戶，以取得更佳的效能。
    - 一個唯一自訂映像 VHD blob 最多可支援 40 個 Linux VM 執行個體，或 20 個 Windows VM 執行個體。 您必須建立 VHD blob 的複本，才能建立具有多個 VM 的集區。 例如，具有 200 個 Windows VM 的集區，需要針對 **osDisk** 屬性指定 10 個唯一的 VHD blob。
    
## <a name="prepare-a-custom-image"></a>準備自訂映像

若要準備自訂映像以便用於 Batch，您必須將現有的 Linux 或 Windows 安裝一般化。 將作業系統安裝一般化可移除機器專屬資訊。 結果會產生可安裝在其他電腦或 VM 上的映像。  

您可以使用 Azure Marketplace 映像作為自訂映像的基礎映像。 若要自訂基礎映像，請建立 Azure VM 並在其中新增您的應用程式或資料。 然後將 VM 一般化以作為您的自訂映像。   

如需有關從 Azure VM 準備自訂 Linux 映像的資訊，請參閱[如何建立虛擬機器或 VHD 的映像](../virtual-machines/linux/capture-image.md)。 

如需有關從 Azure VM 準備自訂 Windows 映像的資訊，請參閱[使用 Azure PowerShell 建立自訂 VM 映像](../virtual-machines/windows/tutorial-custom-images.md)和 [Sysprep (系統準備) 概觀](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)。 

> [!IMPORTANT]
> 準備您的自訂映像時，請記住下列事項：
> - 請確認您用來佈建 Batch 集區的基本 OS 映像沒有任何預先安裝的 Azure 擴充，例如自訂指令碼擴充。 如果映像包含預先安裝的擴充，Azure 在部署 VM 時可能會遇到問題。
> - 請確定您提供的基本 OS 映像使用預設的暫存磁碟機，因為 Batch 節點代理程式目前需要有預設的暫存磁碟機。
>
>
    
## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>在 Azure 入口網站中從自訂映像建立集區

若要使用 Azure 入口網站從自訂映像建立集區：

1. 在 Azure 入口網站中瀏覽至您的 Batch 帳戶。
2. 在 [設定] 刀鋒視窗上，選取 [集區] 功能表項目。
3. 在 [集區] 刀鋒視窗上，選取 [新增] 命令；[新增集區] 刀鋒視窗隨即顯示。
4. 從 [映像類型] 下拉式清單選取 [自訂映像 (Linux/Windows)]。 入口網站會顯示 [自訂映像] 選擇器。 從相同的容器中選擇一或多個 VHD，然後按一下 [選取] 按鈕。 
   未來版本將提供從不同儲存體帳戶和不同容器選取 VHD 的支援。
5. 針對您自訂的 VHD 選取正確的 **發行者/優惠/SKU**選取所需的**快取**模式，然後填入集區的所有其他參數。
6. 若要檢查集區是否以自訂映像作為基礎，請參閱 [集區] 刀鋒視窗的資源摘要區段中的 **Operating System** 屬性。 這個屬性的值應該是**自訂 VM 映像**。
7. 與集區相關聯的所有自訂 VHD 都會顯示在集區的 [屬性] 刀鋒視窗中。
 
## <a name="next-steps"></a>後續步驟

- 如需 Batch 的深入概觀，請參閱[使用 Batch 開發大規模的平行計算解決方案](batch-api-basics.md)。
- 如需建立 Batch 帳戶的相關資訊，請參閱[使用 Azure 入口網站建立 Batch 帳戶](batch-account-create-portal.md)。
