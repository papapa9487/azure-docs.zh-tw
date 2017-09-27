---
title: "針對刪除 Azure 儲存體帳戶、容器或 VHD 時所發生的錯誤進行疑難排解 | Microsoft Docs"
description: "針對刪除 Azure 儲存體帳戶、容器或 VHD 時所發生的錯誤進行疑難排解"
services: storage
documentationcenter: 
author: genlin
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
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 11944dd38b1cc30106c0b76a108480c018ca39d4
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds"></a>針對刪除 Azure 儲存體帳戶、容器或 VHD 時所發生的錯誤進行疑難排解

當您嘗試刪除 [Azure 入口網站](https://portal.azure.com)中的 Azure 儲存體帳戶、容器或虛擬硬碟 (VHD) 時，可能會收到錯誤。 本文提供可協助您解決 Azure Resource Manager 部署問題的疑難排解指引。

若本文未能解決您的 Azure 問題，請造訪 [MSDN 與 Stack Overflow](https://azure.microsoft.com/support/forums/) 上的 Azure 論壇。 您可以在這些論壇上張貼您的問題，或將問題貼到 Twitter 上的 @AzureSupport。 此外，您也可以選取 [Azure 支援](https://azure.microsoft.com/support/options/)網站上的 [取得支援]，來提出 Azure 支援要求。

## <a name="symptoms"></a>徵兆
### <a name="scenario-1"></a>案例 1
當您嘗試在 Resource Manager 部署的儲存體帳戶中刪除 VHD 時，您會收到下列錯誤訊息︰

**無法刪除 Blob 'vhds/BlobName.vhd'。錯誤：目前 blob 上沒有租用，且要求中沒有指定任何租用識別碼。**

虛擬機器 (VM) 在您嘗試刪除的 VHD 上有租用，所以會發生此問題。

### <a name="scenario-2"></a>案例 2
當您嘗試在 Resource Manager 部署的儲存體帳戶中刪除容器時，您會收到下列錯誤訊息︰

**無法刪除儲存體容器 'vhds'。錯誤：目前容器上沒有租用，且要求中沒有指定任何租用識別碼。**

容器中有 VHD 鎖定在租用狀態，所以會發生此問題。

### <a name="scenario-3"></a>案例 3
當您嘗試在 Resource Manager 部署中刪除儲存體帳戶時，您會收到下列錯誤訊息︰

**無法刪除儲存體帳戶 'StorageAccountName'。錯誤︰無法刪除儲存體帳戶，因為正在使用其構件。**

儲存體帳戶含有處於租用狀態的 VHD，所以會發生此問題。

## <a name="solution"></a>方案 
若要解決這些問題，您必須識別造成錯誤的 VHD 和相關聯的 VM。 接著，將 VHD 與 VM 中斷連結 (若為資料磁碟)，或刪除使用 VHD 的 VM (若為 OS 磁碟)。 這會從 VHD 移除租用，讓 VHD 得以被刪除。 

若要這麼做，請使用下列其中一種方法：

### <a name="method-1---use-azure-storage-explorer"></a>方法 1 - 使用 Azure 儲存體總管

### <a name="step-1-identify-the-vhd-that-prevent-deletion-of-the-storage-account"></a>步驟 1 識別導致無法刪除儲存體帳戶的 VHD

1. 您刪除儲存體帳戶時，將收到以下訊息對話方塊： 

    ![刪除儲存體帳戶時的訊息](././media/storage-resource-manager-cannot-delete-storage-account-container-vhd/delete-storage-error.png) 

2. 檢查**磁碟 URL**，以找出導致無法刪除儲存體帳戶的儲存體帳戶和 VHD。 在下列範例中，“.blob.core.windows.net “ 是儲存體帳戶名稱，"SCCM2012-2015-08-28.vhd" 是 VHD 名稱。  

        https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd

### <a name="step-2-delete-the-vhd-by-using-azure-storage-explorer"></a>步驟 2 使用 Azure 儲存體總管刪除 VHD

1. 下載並安裝最新版 [Azure 儲存體總管](http://storageexplorer.com/)。 此工具是免費的獨立應用程式，可讓您在 Windows、MacOS 和 Linux 上輕鬆處理 Azure 儲存體資料。
2. 開啟 [Azure 儲存體總管]，選取 ![帳戶圖示](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/account.png) 在左列中，選取 Azure 環境中，然後登入。

3. 選取所有訂用帳戶，或選取含有您想要刪除的儲存體帳戶的訂用帳戶。

    ![新增訂用帳戶](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/addsub.png)

4. 移至稍早從磁碟 URL 取得的儲存體帳戶，並選取 [Blob 容器] > [VHD]，然後搜尋導致無法刪除儲存體帳戶的 VHD。
5. 如果找到 VHD，請檢查 [VM 名稱] 資料行，找出使用此 VHD 的 VM。

    ![檢查 VM](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/check-vm.png)

6. 使用 Azure 入口網站從 VHD 移除租用。 如需詳細資訊，請參閱[從 VHD 移除租用](#remove-the-lease-from-the-vhd)。 

7. 移至 [Azure 儲存體總管]，以滑鼠右鍵按一下 VHD，然後選取 [刪除]。

8. 刪除儲存體帳戶。

### <a name="method-2---use-azure-portal"></a>方法 2 - 使用 Azure 入口網站 

#### <a name="step-1-identify-the-vhd-that-prevent-deletion-of-the-storage-account"></a>步驟 1：識別導致無法刪除儲存體帳戶的 VHD

1. 您刪除儲存體帳戶時，將收到以下訊息對話方塊： 

    ![刪除儲存體帳戶時的訊息](././media/storage-resource-manager-cannot-delete-storage-account-container-vhd/delete-storage-error.png) 

2. 檢查**磁碟 URL**，以找出導致無法刪除儲存體帳戶的儲存體帳戶和 VHD。 在下列範例中，“.blob.core.windows.net “ 是儲存體帳戶名稱，"SCCM2012-2015-08-28.vhd" 是 VHD 名稱。  

        https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd

2. 登入 [Azure 入口網站](https://portal.azure.com)。
3. 在 [中樞] 功能表中，選取 [所有資源]。 移至儲存體帳戶，然後選取 [Blob] > [VHD]。

    ![入口網站的螢幕擷取畫面，醒目提示儲存體帳戶與「vhds」容器](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)

4. 找出稍早從磁碟 URL 取得的 VHD。 然後，判斷是哪個 VM 在使用 VHD。 通常來說，您可以藉由檢查 VHD 的名稱來判斷是哪個 VM 握有 VHD︰

資源管理員開發模型中的 VM

   * 作業系統磁碟一般會遵循此命名慣例︰VMName-YYYY-MM-DD-HHMMSS.vhd
   * 資料磁碟一般會遵循此命名慣例︰VMName-YYYY-MM-DD-HHMMSS.vhd

傳統開發模型中的 VM

   * 作業系統磁碟一般會遵循此命名慣例︰CloudServiceName-VMName-YYYY-MM-DD-HHMMSS.vhd
   * 資料磁碟一般會遵循此命名慣例︰CloudServiceName-VMName-YYYY-MM-DD-HHMMSS.vhd

#### <a name="step-2-remove-the-lease-from-the-vhd"></a>步驟 2︰從 VHD 移除租用

[從 VHD 移除租用](#remove-the-lease-from-the-vhd)，然後刪除儲存體帳戶。

## <a name="what-is-a-lease"></a>租用是什麼？
租用是可用來控制 Blob (例如 VHD) 存取權的鎖定。 租用 Blob 時，只有租用的擁有者可以存取 Blob。 租用非常重要，原因如下︰

* 如果多名擁有者嘗試同時對 Blob 的相同部分寫入資料，租用可防止資料損毀。
* 租用可防止正在使用中的 Blob 遭到 (例如 VM) 刪除。
* 租用可防止正在使用中的儲存體帳戶遭到 (例如 VM) 刪除。

### <a name="remove-the-lease-from-the-vhd"></a>從 VHD 移除租用
如果 VHD 是作業系統磁碟，您必須刪除 VM 才能移除租用：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [中樞] 功能表上，選取 [虛擬機器]。
3. 選取在 VHD 上握有租用的 VM。
4. 確定沒有正在使用虛擬機器的項目，而且您不再需要虛擬機器。
5. 在 [VM 詳細資料] 刀鋒視窗頂端，選取 [刪除]，然後按一下 [是] 進行確認。
6. VM 應該會遭到刪除，但 VHD 應該會保留下來。 不過，VHD 上應該不會再有租用。 釋放租用可能需要幾分鐘的時間。 若要確認租用已遭到釋放，請移至 [所有資源] > [儲存體帳戶名稱] > [Blob] > [VHD]。 在 Blob 屬性 窗格中，租用狀態 值應該是 已解除鎖定。

如果 VHD 是資料磁碟，請從 VM 中斷連結 VHD，以便移除租用：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [中樞] 功能表上，選取 [虛擬機器]。
3. 選取在 VHD 上握有租用的 VM。
4. 選取 [VM 詳細資料] 刀鋒視窗上的 [磁碟]。
5. 選取在 VHD 上握有租用的資料磁碟。 您可以藉由檢查 VHD 的 URL 來判斷磁碟中連接的 VHD。
6. 確實判斷沒有任何項目正在使用資料磁碟。
7. 按一下 [磁碟詳細資料] 刀鋒視窗上的 [中斷連結]。
8. 磁碟應該會立即與 VM 中斷連結，而且 VHD 上應該不會再有租用。 釋放租用可能需要幾分鐘的時間。 若要確認租用已遭到釋放，請移至 [所有資源] > [儲存體帳戶名稱] > [Blob] > [VHD]。 在 Blob 屬性 窗格中，租用狀態 值應該是 已解除鎖定。

## <a name="next-steps"></a>後續步驟
* [刪除儲存體帳戶](storage-create-storage-account.md#delete-a-storage-account)
* [如何在 Microsoft Azure (PowerShell) 中止 blob 儲存體的鎖定租用](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

