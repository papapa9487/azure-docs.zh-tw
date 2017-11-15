---
title: "建立及上傳 FreeBSD VM 映像 | Microsoft Docs"
description: "了解如何建立及上傳包含 FreeBSD 作業系統的虛擬硬碟 (VHD)，以建立 Azure 虛擬機器。"
services: virtual-machines-linux
documentationcenter: 
author: thomas1206
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
ms.author: huishao
ms.openlocfilehash: 7b41826f071174df8f00af56a228e0f31c3cfe2f
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2017
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

這項工作包含下列四個步驟：

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

## <a name="step-2-prepare-the-connection-to-azure"></a>步驟 2：準備與 Azure 的連接
確定您是在傳統部署模型中使用 Azure CLI (`azure config mode asm`)，然後登入您的帳戶︰

```azurecli
azure login
```


<a id="upload"> </a>


## <a name="step-3-upload-the-vhd-file"></a>步驟 3：上傳 .vhd 檔案

您需要一個可供上傳 VHD 檔案的儲存體帳戶。 您可以選取現有的儲存體帳戶或[建立新帳戶](../../../storage/common/storage-create-storage-account.md)。

在上傳 .vhd 檔案時，可以將 .vhd 檔案放在 Blob 儲存體中的任一處。 以下是您上傳檔案時將使用的一些詞彙︰

* **BlobStorageURL** 是您在步驟 2 建立的儲存體帳戶的 URL。
* **YourImagesFolder** 是您要用來儲存映像之 Blob 儲存體中的容器。
* **VHDName** 是 Azure 傳統入口網站中，用來識別虛擬硬碟的顯示標籤。
* **PathToVHDFile** 是 .vhd 檔案的完整路徑和名稱。

從您在上一個步驟使用的 Azure PowerShell 視窗中，輸入：

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-4-create-a-vm-with-the-uploaded-vhd-file"></a>步驟 4：以上傳的 .vhd 檔案建立 VM
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
