---
title: "管理 Azure Stack 儲存體帳戶 | Microsoft Docs"
description: "了解如何尋找、管理、復原及回收 Azure Stack 儲存體帳戶"
services: azure-stack
documentationcenter: 
author: AniAnirudh
manager: darmour
editor: 
ms.assetid: 627d355b-4812-45cb-bc1e-ce62476dab34
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/6/2017
ms.author: anirudha
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 6e14bd6312135b45984a82099e68a934ec2a4a70
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---
# <a name="manage-storage-accounts-in-azure-stack"></a>在 Azure Stack 中管理儲存體帳戶
了解如何在 Azure Stack 中管理儲存體帳戶，以便根據業務需求來尋找、復原及回收儲存體容量。

## <a name="find"></a>尋找儲存體帳戶
區域中的儲存體帳戶清單可在 Azure Stack 中，透過下列方式檢視：

1. 在網際網路瀏覽器中，瀏覽至 https://adminportal.local.azurestack.external。
2. 以雲端操作員身分 (使用您在部署期間所提供的認證)，登入 Azure Stack 系統管理入口網站
3. 在預設儀表板上，尋找 [區域管理] 清單，然後按一下您想要探索的區域。 例如 **(local**)。
   
   ![](media/azure-stack-manage-storage-accounts/image1.png)
4. 從 [資源提供者] 清單中選取 [儲存體]。
   
   ![](media/azure-stack-manage-storage-accounts/image2.png)
5. 現在，在儲存體資源提供者系統管理員刀鋒視窗中，向下捲動至 [儲存體帳戶] 索引標籤，然後按一下它。
   
   ![](media/azure-stack-manage-storage-accounts/image3.png)
   
   所產生的頁面就是在該區域中的儲存體帳戶清單。
   
   ![](media/azure-stack-manage-storage-accounts/image4.png)

預設會顯示前 10 個帳戶。 您可以按一下清單底部的 [載入更多] 連結，選擇擷取更多項目。

或

如果您只想看到特定儲存體帳戶，可以只**篩選並擷取相關的帳戶**。


**篩選帳戶：**

1. 按一下刀鋒視窗頂端的 [篩選]。
2. 在 [篩選] 刀鋒視窗上，您可以指定 [帳戶名稱]、[訂用帳戶識別碼] 或 [狀態]，以微調要顯示的儲存體帳戶清單。 請適當地指定。
3. 按一下 [更新] 。 清單應該會隨著重新整理。
   
    ![](media/azure-stack-manage-storage-accounts/image5.png)
4. 若要重設篩選：按一下 [篩選]、清除選取項目，然後更新。

[搜尋] 文字方塊 (在儲存體帳戶清單刀鋒視窗的頂端) 可讓您反白顯示帳戶清單中選取的文字。 如果無法輕易地取得完整名稱或識別碼，這真的很方便。

您可以在這裡使用任意文字，以協助找出您想看到的帳戶。

![](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>查看帳戶詳細資料
一旦找到您想檢視的帳戶之後，可以按一下特定的帳戶，以檢視特定的詳細資料。 新刀鋒視窗隨即開啟，其中包含帳戶詳細資料，例如：帳戶類型、建立時間、位置等等。

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>復原已刪除的帳戶
有時候，您必須復原已刪除的帳戶。

在 Azure Stack 中，有一個非常簡單的方式可以執行此作業：

1. 瀏覽至儲存體帳戶清單。 如需詳細資訊，請參閱本主題中的[尋找儲存體帳戶](#find)。
2. 在清單中找出該特定帳戶。 您可能需要篩選。
3. 請檢查帳戶的 [狀態]。 它應該指出 [已刪除]。
4. 按一下可開啟 [帳戶詳細資料] 刀鋒視窗的帳戶。
5. 在這個刀鋒視窗的頂端，找出 [復原] 按鈕，然後按一下它。
6. 按一下 [是] 以確認。
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. 復原現在處於 [處理中...請等待] 的狀態，正在等待復原成功的訊息出現。
   您也可以按一下入口網站頂端的「鈴鐺」圖示，以檢視進度指示。
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   一旦成功同步處理已復原的帳戶之後，就可以再次使用該帳戶。

### <a name="some-gotchas"></a>注意事項
* 已刪除的帳戶顯示 [不再保留] 狀態。
  
  這表示已刪除的帳戶已超出保留期限，可能無法復原。
* 已刪除的帳戶未顯示在帳戶清單中。
  
  這可能表示已刪除的帳戶已經被記憶體回收。 在此情況下，便無法復原該帳戶。 請參閱本主題中的[回收容量](#reclaim)。

## <a name="set-the-retention-period"></a>設定保留期限
保留期限設定可讓雲端操作員指定時間間隔天數 (介於 0 到 9999 天)，在此期間，任何已刪除的帳戶都可能復原。 預設保留期限設定為 15 天。 將值設定為 "0" 表示立即不保留任何已刪除的帳戶，並標示供定期記憶體回收。

**變更保留期限：**

1. 在網際網路瀏覽器中，瀏覽至 https://adminportal.local.azurestack.external。
2. 以雲端操作員身分 (使用您在部署期間所提供的認證)，登入 Azure Stack 系統管理入口網站
3. 在預設儀表板上，尋找 [區域管理] 清單，然後按一下您想要探索的區域，例如 **(local**)。
4. 從 [資源提供者] 清單中選取 [儲存體]。
5. 按一下頂端的 [設定]，開啟 [設定] 刀鋒視窗。
6. 按一下 [設定]，然後編輯保留期限值。

   設定天數，然後加以儲存。
   
   此值會立即生效，而且會設定您的整個區域。

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>回收容量
保留期限會有副作用，亦即已刪除的帳戶會繼續耗用容量，直到超出保留期限為止。 身為雲端操作員，即使保留期限尚未到期，您可能還是需要一個回收已刪除帳戶空間的方式。

您可以使用入口網站或 PowerShell 來回收容量。

**使用入口網站回收容量：**
1. 瀏覽至儲存體帳戶刀鋒視窗。 請參閱[尋找儲存體帳戶](#find)。
2. 按一下刀鋒視窗頂端的 [回收空間]。
3. 讀取訊息，然後按一下 [確定]。

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. 等候成功通知。請查看入口網站上的鈴鐺圖示。

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. 重新整理 [儲存體帳戶] 頁面。 已刪除的帳戶已遭到清除，因此不會再顯示在清單中。

您也可以使用 PowerShell 明確地覆寫保留期限，並立即回收容量。

**使用 PowerShell 回收容量：**   

1. 確認您已安裝並設定 Azure PowerShell。 否則，請使用下列指示： 
   * 若要安裝最新的 Azure PowerShell 版本，並將它與您的 Azure 訂用帳戶建立關聯，請參閱[如何安裝和設定 Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/)。
   如需有關 Azure Resource Manager Cmdlet 的詳細資訊，請參閱[搭配使用 Azure PowerShell 與 Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkId=394767)
2. 執行下列 Cmdlet：

> [!NOTE]
> 如果您執行這個 Cmdlet，將會永久刪除帳戶及其內容。 無法復原。 使用時請務必小心。


        Clear-ACSStorageAccount -ResourceGroupName system.local -FarmName <farm ID>


如需詳細資訊，請參閱 [Azure Stack PowerShell 文件](https://msdn.microsoft.com/library/mt637964.aspx) \(英文\)。
 

## <a name="migrate-a-container"></a>移轉容器
由於租用戶的儲存體用量不平均，因此雲端操作員可能會發現其中一個或多個基礎租用戶共用所使用的空間比其他租用戶更多。 如果發生這種情況，雲端操作員可以嘗試將部分 Blob 容器手動移轉至另一個共用，為使用量較大的共用釋放一些空間。 

您必須使用 PowerShell 來移轉容器。
> [!NOTE]
>Blob 容器移轉不支援即時移轉，而且目前是離線作業。 在移轉期間以及完成移轉之前，該容器中的基礎 Blob 都無法使用，而且是處於「離線」狀態。 

**使用 PowerShell 來移轉容器：**

1. 確認您已安裝並設定 Azure PowerShell。 否則，請使用下列指示：
    * 若要安裝最新的 Azure PowerShell 版本，並將它與您的 Azure 訂用帳戶建立關聯，請參閱[如何安裝和設定 Azure PowerShell](http://azure.microsoft.com/documentation/articles/powershell-install-configure/)。 如需有關 Azure Resource Manager Cmdlet 的詳細資訊，請參閱[搭配使用 Azure PowerShell 與 Azure Resource Manager](http://go.microsoft.com/fwlink/?LinkId=394767)
2. 取得伺服器陣列名稱： 
      
      `$farm = Get-ACSFarm -ResourceGroupName system.local`
3. 取得共用： 

   `$shares = Get-ACSShare -ResourceGroupName system.local -FarmName $farm.FarmName`

4. 取得指定共用的容器。 請注意，count 和 intent 都是選擇性參數：
            
   `$containers = Get-ACSContainer -ResourceGroupName system.local -FarmName $farm.FarmName -ShareName $shares[0].ShareName -Count 4 -Intent Migration`  

   接著，檢查 $containers：

   `$containers`

    ![](media/azure-stack-manage-storage-accounts/image13.png)
5. 取得用於容器移轉的最佳目的地共用：

    `$destinationshares= Get-ACSSharesForMigration  -ResourceGroupName system.local -FarmName $farm.farmname -SourceShareName $shares[0].ShareName`

    接著，檢查 $destinationshares：

    `$destinationshares`

    ![](media/azure-stack-manage-storage-accounts/image14.png)
6. 開始進行容器移轉。請注意，這是非同步實作，因此可以循環處理共用中的所有容器，並使用傳回的工作識別碼來追蹤狀態。

    `$jobId = Start-ACSContainerMigration -ResourceGroupName system.local -FarmName $farm.farmname -ContainerToMigrate $containers[1] -DestinationShareUncPath $destinationshares.UncPath`

    接著，檢查 $jobId：

   ```
   $jobId
   d1d5277f-6b8d-4923-9db3-8bb00fa61b65
   ```
7. 依工作識別碼檢查移轉工作的狀態。當容器移轉完成時，MigrationStatus 會設定為 「已完成」。

    `Get-ACSContainerMigrationStatus -ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image15.png)

8. 您可以取消進行中的移轉工作。 這仍然是非同步作業，而且可以使用 $jobid 來追蹤：

    `Stop-ACSContainerMigration-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId-Verbose`

    ![](media/azure-stack-manage-storage-accounts/image16.png)

    您可以再次檢查移轉取消的狀態：

    `Get-ACSContainerMigrationStatus-ResourceGroupName system.local -FarmName $farm.farmname -JobId $jobId`

    ![](media/azure-stack-manage-storage-accounts/image17.png)




  
  
