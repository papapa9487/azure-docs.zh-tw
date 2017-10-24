---
title: "針對刪除 Azure 儲存體帳戶、容器或 VHD 時所發生的錯誤進行疑難排解 | Microsoft Docs"
description: "針對刪除 Azure 儲存體帳戶、容器或 VHD 時所發生的錯誤進行疑難排解"
services: storage
documentationcenter: 
author: passaree
manager: cshepard
editor: na
tags: storage
ms.assetid: 17403aa1-fe8d-45ec-bc33-2c0b61126286
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/13/2017
ms.author: genli
ms.openlocfilehash: 174ab97ac14f4c05690306691df5ee4b6d33dd93
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-storage-delete-errors-in-resource-manager-deployment"></a>針對 Resource Manager 部署中的儲存體刪除錯誤進行疑難排解
本文章提供疑難排解指引，協助您排解在 Azure Resource Manager 部署中嘗試刪除 Azure 儲存體帳戶、容器或 Blob 時發生的以下任一錯誤。

>**無法刪除儲存體帳戶 'StorageAccountName'。錯誤︰無法刪除儲存體帳戶，因為正在使用其構件。**

>**無法刪除 # 個容器 (共 # 個)：<br>vhds：目前容器上有租用，但要求中沒有指定任何租用識別碼。**

>**無法刪除 # 個 Blob (共 # 個)：<br>：目前 Blob 上有租用，但要求中沒有指定任何租用識別碼。**

Azure VM 中使用的虛擬硬碟是以分頁 Blob 儲存在 Azure 標準或進階儲存體帳戶中的 .vhd 檔案。  如需 Azure 磁碟的詳細資訊，請參閱[](../../virtual-machines/windows/about-disks-and-vhds.md)。 Azure 能預防刪除已連接 VM 的磁碟，以避免損毀。 它也能預防刪除分頁 Blob 已連接 VM 的容器和儲存體帳戶。 

## <a name="solution"></a>方案
收到前述任一錯誤時，刪除儲存體帳戶、容器或 Blob 的程序如下： 
1. [識別連接 VM 的 Blob](#step-1-identify-blobs-attached-to-a-vm)
2. [刪除已連接**作業系統磁碟**的 VM](#step-2-delete-vm-to-detach-os-disk)
3. [中斷連結其餘 VM 的所有**資料磁碟**](#step-3-detach-data-disk-from-the-vm)

完成上述步驟之後，請再嘗試刪除儲存體帳戶、容器或 Blob。

### <a name="step-1-identify-blob-attached-to-a-vm"></a>步驟 1：識別連接 VM 的 Blob

#### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>案例 1：刪除 Blob - 識別連接的 VM
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [中樞] 功能表中，選取 [所有資源]。 移至儲存體帳戶，在 [Blob 服務] 下方選取 [容器]，然後瀏覽到要刪除的 Blob。
3. 如果 Blob **租用狀態**是**已租用**，請以滑鼠右鍵按一下，然後選取 [編輯中繼資料] 開啟 Blob 中繼資料窗格。 

    ![入口網站的螢幕擷取畫面，反白顯示儲存體帳戶 Blob 及按一下滑鼠右鍵 > [編輯中繼資料]](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-edit-metadata-sm.png)

4. 在 Blob 中繼資料窗格中，檢查和記下 **MicrosoftAzureCompute_VMName** 的值。 這個值是連接 VHD 的目標 VM 名稱。 (如果這個欄位不存在，請參閱**重要事項**)
5. 在 Blob 中繼資料窗格中，檢查和記下 **MicrosoftAzureCompute_DiskType** 的值。 它能識別連接的磁碟是作業系統磁碟或資料磁碟 (如果這個欄位不存在，請參閱**重要事項**)。 

     ![開啟儲存體「Blob 中繼資料」窗格的入口網站螢幕擷取畫面](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-blob-metadata-sm.png)

6. 如果 Blob 磁碟類型是 **OSDisk**，請遵循[步驟 2：刪除 VM 以中斷作業系統磁碟的連線](#step-2-delete-vm-to-detach-os-disk)。 否則，如果 Blob 磁碟類型是 **DataDisk**，請遵循[步驟 3：中斷資料磁碟與 VM 的連線](#step-3-detach-data-disk-from-the-vm)。 

> [!IMPORTANT]
> 如果 **MicrosoftAzureCompute_VMName** 和 **MicrosoftAzureCompute_DiskType** 未出現在 Blob 中繼資料內，代表 Blob 已明確租用，而且未連接 VM。 已租用的 Blob 必須先中斷租用後才能予以刪除。 若要中斷租用，請在 Blob 上按一下滑鼠右鍵，然後選取 [中斷租用]。 即使未連接 VM 的已租用 Blob 無法刪除，不過它無法預防容器或儲存體帳戶遭到刪除。

#### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>案例 2：刪除容器 - 識別連接 VM 之容器內的所有 Blob
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [中樞] 功能表中，選取 [所有資源]。 移至儲存體帳戶，在 [Blob 服務] 下方選取 [容器]，然後找出要刪除的容器。
3. 按一下開啟容器，容器內的 Blob 清單將會出現。 在清單中識別 Blob 類型 = **分頁 Blob** 且租用狀態 = **已租用**的所有 Blob。 遵循[案例 1](#step-1-identify-blobs-attached-to-a-vm) 以辨識分別與這些 Blob 相關聯的 VM。

    ![入口網站的螢幕擷取畫面，反白顯示儲存體帳戶 Blob、[租用狀態] 和 [已租用]](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-disks-sm.png)

4. 遵循[步驟 2](#step-2-delete-vm-to-detach-os-disk) 和[步驟 3](#step-3-detach-data-disk-from-the-vm) 來刪除連接 **OSDisk** 的 VM 及中斷連結 **DataDisk**。 

#### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>案例 3：刪除儲存體帳戶 - 識別儲存體帳戶內已連接 VM 的所有 Blob
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [中樞] 功能表中，選取 [所有資源]。 移至儲存體帳戶，在 [Blob 服務] 下方選取 [容器]。

    ![入口網站的螢幕擷取畫面，反白顯示儲存體帳戶容器、[租用狀態] 和 [已租用]](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-containers-sm.png)

3. 在 [容器] 刀鋒視窗中，識別所有 [租用狀態] 為 [已租用] 的容器，然後遵循[案例 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) 來處理每個**已租用**的容器。
4. 遵循[步驟 2](#step-2-delete-vm-to-detach-os-disk) 和[步驟 3](#step-3-detach-data-disk-from-the-vm) 來刪除連接 **OSDisk** 的 VM 及中斷連結 **DataDisk**。 

### <a name="step-2-delete-vm-to-detach-os-disk"></a>步驟 2：刪除 VM 以中斷作業系統磁碟的連線
如果 VHD 是作業系統磁碟，您必須先刪除 VM 才能刪除連接的 VHD。 完成下列步驟後，您就不需要針對連接相同 VM 的資料磁碟執行額外動作：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [中樞] 功能表上，選取 [虛擬機器]。
3. 選取連接 VHD 的 VM。
4. 確定沒有正在使用虛擬機器的項目，而且您不再需要虛擬機器。
5. 在 虛擬機器詳細資料 刀鋒視窗頂端，選取 刪除，然後按一下是 進行確認。
6. VM 應該會遭到刪除，但 VHD 應該會保留下來。 不過，VHD 應該不會再連接 VM 或再有租用。 釋放租用可能需要幾分鐘的時間。 若要確認租用已釋放，請瀏覽至 Blob 位置；在 [Blob 屬性] 窗格中，[租用狀態] 應該會是 [可用]。

### <a name="step-3-detach-data-disk-from-the-vm"></a>步驟 3：中斷資料磁碟與 VM 的連線
如果 VHD 是資料磁碟，請從 VM 中斷連結 VHD，以便移除租用：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [中樞] 功能表上，選取 [虛擬機器]。
3. 選取連接 VHD 的 VM。
4. 在 [虛擬機器詳細資料] 刀鋒視窗中，選取 [磁碟]。
5. 選取連接 VHD 且要刪除的資料磁碟。 您可以藉由檢查 VHD 的 URL 來判斷磁碟中連接的 Blob。
6. 若要確認 Blob 位置，您可以按一下磁碟以查看 [VHD URI] 欄位中的路徑。
7. 在 [磁碟] 刀鋒視窗頂端，選取 [編輯]。
8. 針對要刪除的資料磁碟按一下**中斷連結圖示**。

     ![開啟儲存體「Blob 中繼資料」窗格的入口網站螢幕擷取畫面](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-vm-disks-edit.png)

9. 選取 [ **儲存**]。 磁碟應該會立即與 VM 中斷連結，而且 VHD 上應該不會再有租用。 釋放租用可能需要幾分鐘的時間。 若要確認租用已釋放，請瀏覽至 Blob 位置；在 [Blob 屬性] 窗格中，[租用狀態] 值應該會是 [已解除鎖定] 或 [可用]。

## <a name="next-steps"></a>後續步驟
再次嘗試刪除先前未能刪除的儲存體物件。

