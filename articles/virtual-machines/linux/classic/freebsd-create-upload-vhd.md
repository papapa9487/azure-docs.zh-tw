---
title: "建立及上傳 FreeBSD VM 映像 | Microsoft Docs"
description: "了解如何建立及上傳包含 FreeBSD 作業系統的虛擬硬碟 (VHD)，以建立 Azure 虛擬機器。"
services: virtual-machines-linux
documentationcenter: 
author: KylieLiang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: kyliel
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 918f454784a9676297077c2e94c3e49ab2872d2f
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---
# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>建立並上傳 FreeBSD VHD 到 Azure
本文說明如何建立及上傳包含 FreeBSD 作業系統的虛擬硬碟 (VHD)。 上傳之後，您可以使用它做為您自己的映像在 Azure 中建立虛擬機器 (VM)。

> [!IMPORTANT] 
> Azure 建立和處理資源的部署模型有二種： [資源管理員和傳統](../../../resource-manager-deployment-model.md)。 本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議讓大部分的新部署使用資源管理員模式。 如需使用 Resource Manager 模型上傳 VHD 的詳細資訊，請參閱[這裡](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="prerequisites"></a>必要條件
本文假設您具有下列項目：

* **Azure 訂用帳戶**-- 如果您沒有，只需要幾分鐘的時間就可以建立帳戶。 如果您有 MSDN 訂用帳戶，請參閱 [Visual Studio 訂閱者的每月 Azure 點數](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。 否則，請參閱 [建立免費試用帳戶](https://azure.microsoft.com/pricing/free-trial/)。  
* **Azure PowerShell 工具**-- 必須已安裝 Azure PowerShell 模組，並設定為使用您的訂用帳戶。 若要下載此模組，請參閱 [Azure 下載](https://azure.microsoft.com/downloads/)。 這裡有一個說明如何安裝和設定此模組的教學課程。 使用 [Azure Downloads](https://azure.microsoft.com/downloads/) Cmdlet 上傳 VHD。
* **安裝在 .vhd 檔案中的 FreeBSD 作業系統** -- 支援的 FreeBSD 作業系統必須已安裝到虛擬硬碟中。 有多項工具可用來建立 .vhd 檔案。 例如，您可以使用虛擬化解決方案 (例如 Hyper-V) 建立 .vhd 檔案，並安裝作業系統。 如需相關指示，請參閱 [安裝 Hyper-V 和建立虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。

> [!NOTE]
> Azure 不支援較新的 VHDX 格式。 您可以使用 Hyper-V 管理員或 [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx)Cmdlet，將磁碟轉換為 VHD 格式。 此外，還有 [MSDN 上有關如何使用 FreeBSD 搭配 Hyper-V 的教學課程](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx)。
>
>

這項工作包含下列五個步驟：

## <a name="step-1-prepare-the-image-for-upload"></a>步驟 1：準備要上傳的映像
在您已安裝 FreeBSD 作業系統的虛擬機器上，完成下列程序：

1. 啟用 DHCP。

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart
2. 啟用 SSH。

    確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。 根據預設，它從 FreeBSD 光碟安裝之後就會啟用。 
3. 設定序列主控台。

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf
4. 安裝 sudo。

    在 Azure 中已停用 root 帳戶。 這表示您必須利用未授權的使用者 sudo 從較高權限執行命令。

        # pkg install sudo
   
5. Azure 代理程式的必要條件。

        # pkg install python27  
        # pkg install Py27-setuptools  
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git
6. 安裝 Azure 代理程式。

    最新版的 Azure 代理程式一律可以在 [github](https://github.com/Azure/WALinuxAgent/releases)上找到。 2.0.10 + 版正式支援 FreeBSD 10 和 10.1，2.1.4 版 (包括 2.2.x) 正式支援 FreeBSD 10.2 和更新版本。

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    針對 2.0 版，以下使用 2.0.16 做為範例：

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    針對 2.1 版，以下使用 2.1.4 做為範例：

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

   > [!IMPORTANT]
   > 安裝 Azure 代理程式之後，最好先確認它正在執行︰
   >
   >

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # ps auxw | grep waagent
        root   639   0.0  0.5 104620 17520 u0- I    05:17    0:00.20 python /usr/local/sbin/waagent -daemon (python2.7)
        # cat /var/log/waagent.log
7. 取消佈建系統。

    取消佈建系統以清理系統，使之適合重新佈建。 下列命令也會刪除最後佈建的使用者帳戶和相關聯的資料：

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    現在您可以關閉您的 VM。

## <a name="step-2-create-a-storage-account-in-azure"></a>步驟 2：在 Azure 中建立儲存體帳戶
必須要有 Azure 中的儲存體帳戶才能上傳 .vhd 檔案，以用來建立虛擬機器。 您可以使用 Azure 傳統入口網站來建立儲存體帳戶。

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。
2. 在命令列上選取 [新增] 。
3. 選取 [資料服務] > [儲存體]  > [快速建立]。

    ![快速建立儲存體帳戶](./media/freebsd-create-upload-vhd/Storage-quick-create.png)
4. 依照下列方式填入欄位：

   * 在 [URL]  欄位中，輸入要在儲存體帳戶 URL 中使用的子網域名稱。 此項目可以包含 3 至 24 個數字和小寫字母。 這個名稱會成為 URL 內用來為訂用帳戶的 Azure Blob 儲存體、Azure 佇列儲存體、或Azure 表格儲存體資源定址的主機名稱。
   * 從 [位置/同質群組] 下拉式清單中，選取儲存體帳戶的 [位置或同質群組]。 同質群組可讓您將雲端服務和儲存體放在相同的資料中心。
   * 在 [複寫] 欄位中，決定儲存體帳戶是否要使用 [異地備援] 複寫。 依預設會開啟異地複寫。 此選項可讓您免費將資料複寫至次要位置，使您在主要位置發生重大錯誤時，可將儲存體容錯移轉至該位置。 次要位置會自動指派，且無法變更。 如果您因為法律規定或組織原則而需要更充分掌控您以雲端為基礎的儲存體所在的位置，您可以關閉地理複寫。 但請注意，如果您後續又開啟異地複寫，在您將現有的資料複寫至次要位置時，將會產生一次性的資料傳輸費用。 不含異地複寫的儲存服務會有相對的折扣。 如需深入了解如何管理儲存體帳戶的異地複寫，請參閱：[Azure 儲存體複寫](../../../storage/common/storage-redundancy.md)。

     ![輸入儲存體帳戶詳細資料](./media/freebsd-create-upload-vhd/Storage-create-account.png)
5. 選取 [建立儲存體帳戶] 。 帳戶現在會出現在 [儲存體] 下方。

    ![已成功建立儲存體帳戶](./media/freebsd-create-upload-vhd/Storagenewaccount.png)
6. 接下來，為您上傳的 .vhd 檔案建立容器。 選取儲存體帳戶名稱，然後選取 [容器] 。

    ![儲存體帳戶詳細資訊](./media/freebsd-create-upload-vhd/storageaccount_detail.png)
7. 選取 [建立容器] 。

    ![儲存體帳戶詳細資訊](./media/freebsd-create-upload-vhd/storageaccount_container.png)
8. 在 [名稱]  欄位中，輸入容器的名稱。 然後，在 [存取]  下拉式選單中，選取您想要的存取原則的類型。

    ![容器名稱](./media/freebsd-create-upload-vhd/storageaccount_containervalues.png)

   > [!NOTE]
   > 容器預設為私人，且只能由帳戶擁有者存取。 若要允許對容器中的 Blob 進行公開讀取存取，但不允許存取容器屬性和中繼資料，請使用 [公用 Blob] 選項。 若要允許對容器和 Blob 進行完整的公開讀取存取，請使用 [ **公開容器** ] 選項。
   >
   >

## <a name="step-3-prepare-the-connection-to-azure"></a>步驟 3：準備 Azure 的連線
您必須先在電腦與 Azure 訂用帳戶之間建立安全連線，才能上傳 .vhd 檔案。 您可以使用 Azure Active Directory (Azure AD) 方法或憑證方法來這樣做。

### <a name="use-the-azure-ad-method-to-upload-a-vhd-file"></a>使用 Azure AD 方法上傳 .vhd 檔案
1. 開啟 Azure PowerShell 主控台。
2. 輸入以下命令：  
    `Add-AzureAccount`

    這個命令會開啟登入視窗，您可在此以您的工作或學校帳戶登入。

    ![PowerShell Window](./media/freebsd-create-upload-vhd/add_azureaccount.png)
3. Azure 會驗證並儲存認證資訊。 然後關閉視窗。

### <a name="use-the-certificate-method-to-upload-a-vhd-file"></a>使用憑證方法上傳 .vhd 檔案
1. 開啟 Azure PowerShell 主控台。
2. 輸入：`Get-AzurePublishSettingsFile`。
3. 隨即會開啟瀏覽器視窗，並提示您下載 .publishsettings 檔案。 此檔案包含您 Azure 訂用帳戶的資訊和憑證。

    ![瀏覽器下載頁面](./media/freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)
4. 儲存 .publishsettings 檔案。
5. 輸入：`Import-AzurePublishSettingsFile <PathToFile>`，其中的 `<PathToFile>` 是 .publishsettings 檔案的完整路徑。

   如需詳細資訊，請參閱 [開始使用 Azure Cmdlet](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx)。

   如需安裝和設定 PowerShell 的詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](/powershell/azure/overview)。

## <a name="step-4-upload-the-vhd-file"></a>步驟 4：上傳 .vhd 檔案
在上傳 .vhd 檔案時，可以將 .vhd 檔案放在 Blob 儲存體中的任一處。 以下是您上傳檔案時將使用的一些詞彙︰

* **BlobStorageURL** 是您在步驟 2 建立的儲存體帳戶的 URL。
* **YourImagesFolder** 是您要用來儲存映像之 Blob 儲存體中的容器。
* **VHDName** 是 Azure 傳統入口網站中，用來識別虛擬硬碟的顯示標籤。
* **PathToVHDFile** 是 .vhd 檔案的完整路徑和名稱。

從您在上一個步驟使用的 Azure PowerShell 視窗中，輸入：

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-5-create-a-vm-with-the-uploaded-vhd-file"></a>步驟 5：以上傳的 .vhd 檔案建立 VM
上傳 .vhd 檔案之後，您可以將其新增為與訂用帳戶相關之自訂映像清單中的映像，並使用此自訂映像建立虛擬機器。

1. 從您在上一個步驟使用的 Azure PowerShell 視窗中，輸入：

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

   > [!NOTE]
   > 使用 Linux 做為作業系統類型。 目前的 Azure PowerShell 版本只接受 "Linux" 或 "Windows" 為參數。
   >
   >
2. 完成前面的步驟之後，當您在 Azure 傳統入口網站上選擇 [映像]  索引標籤時，將會列出新的映像。  

    ![Choose an image](./media/freebsd-create-upload-vhd/addfreebsdimage.png)
3. 從資源庫建立虛擬機器。 這個新映像現在會出現在 [我的映像] 下。
4. 選取新映像。 接著，依照提示設定主機名稱、密碼、SSH 金鑰等項目。

    ![自訂映像](./media/freebsd-create-upload-vhd/createfreebsdimageinazure.png)
5. 佈建完成後，您會看到您的 FreeBSD VM 在 Azure 中執行。

    ![Azure 中的 FreeBSD 映像](./media/freebsd-create-upload-vhd/freebsdimageinazure.png)

