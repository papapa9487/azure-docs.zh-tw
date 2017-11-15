---
title: "適用於 Linux 的 N 系列驅動程式設定 | Microsoft Docs"
description: "如何針對 Azure 中執行 Linux 的 N 系列 VM 設定 NVIDIA GPU 驅動程式"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 46f8b2c20d9ce31ef3f782d098de09952701bbcc
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/08/2017
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>在執行 Linux 的 N 系列 VM 上安裝 NVIDIA GPU 驅動程式

若要利用 Azure N 系列 VM (執行 Linux) 的 GPU 功能，您必須安裝支援的 NVIDIA 圖形驅動程式。 本文提供您在部署 N 系列 VM 後的驅動程式安裝步驟。 驅動程式設定資訊也適用於 [Windows VM](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。


如需 N 系列 VM 規格、儲存體容量與磁碟的詳細資料，請參閱 [GPU Linux VM 大小](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 



[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-grid-drivers-for-nv-vms"></a>安裝 NV VM 的 GRID 驅動程式

若要在 NV VM 上安裝 NVIDIA GRID 驅動程式，請將 SSH 連線至每個 VM，並遵循您 Linux 散發套件的步驟。 

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. 執行 `lspci` 命令。 請確認 NVIDIA M60 卡可以顯示為 PCI 裝置。

2. 安裝更新。

  ```bash
  sudo apt-get update

  sudo apt-get upgrade -y

  sudo apt-get dist-upgrade -y

  sudo apt-get install build-essential ubuntu-desktop -y
  ```
3. 停用與 NVIDIA 驅動程式不相容的 Nouveau 核心驅動程式。 (僅在 NV VM 上使用 NVIDIA 驅動程式。)若要這樣做，使用下列內容，在名為 `nouveau.conf` 的 `/etc/modprobe.d ` 中建立檔案︰

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```


4. 重新啟動 VM，並重新連線。 結束 X 伺服器：

  ```bash
  sudo systemctl stop lightdm.service
  ```

5. 下載並安裝 GRID 驅動程式：

  ```bash
  wget -O NVIDIA-Linux-x86_64-367.106-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-367.106-grid.run

  sudo ./NVIDIA-Linux-x86_64-367.106-grid.run
  ``` 

6. 當系統詢問您是否要執行 nvidia-xconfig 公用程式來更新您的 X 組態檔時，選取 [是]。

7. 安裝完成後，請將 /etc/nvidia/gridd.conf.template 複製到位於 /etc/nvidia/ 的新檔案 gridd.conf

  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```

8. 將下面內容新增至 `/etc/nvidia/gridd.conf`：
 
  ```
  IgnoreSP=TRUE
  ```
9. 重新啟動 VM 並繼續確認安裝。


### <a name="centos-based-73-or-red-hat-enterprise-linux-73"></a>以 CentOS 作為基礎的 7.3 或 Red Hat Enterprise Linux 7.3

> [!IMPORTANT]
> 請勿執行 `sudo yum update` 以更新 CentOS 7.3 或 Red Hat Enterprise Linux 7.3 上的核心版本。 目前，驅動程式的安裝及更新無法在核心已更新的情況下運作。
>

1. 更新核心和 DKMS。
 
  ```bash  
  sudo yum update
 
  sudo yum install kernel-devel
 
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
  sudo yum install dkms
  ```

2. 停用與 NVIDIA 驅動程式不相容的 Nouveau 核心驅動程式。 (僅在 NV VM 上使用 NVIDIA 驅動程式。)若要這樣做，使用下列內容，在名為 `nouveau.conf` 的 `/etc/modprobe.d ` 中建立檔案︰

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```
 
3. 重新啟動 VM、重新連線，然後安裝最新的 Linux Integration Services for Hyper-V：
 
  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3.tar.gz

  tar xvzf lis-rpms-4.2.3.tar.gz

  cd LISISO

  sudo ./install.sh

  sudo reboot

  ```
 
4. 重新連線至 VM 並執行 `lspci` 命令。 請確認 NVIDIA M60 卡可以顯示為 PCI 裝置。
 
5. 下載並安裝 GRID 驅動程式：

  ```bash
  wget -O NVIDIA-Linux-x86_64-367.106-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-367.106-grid.run

  sudo ./NVIDIA-Linux-x86_64-367.106-grid.run
  ``` 
6. 當系統詢問您是否要執行 nvidia-xconfig 公用程式來更新您的 X 組態檔時，選取 [是]。

7. 安裝完成後，請將 /etc/nvidia/gridd.conf.template 複製到位於 /etc/nvidia/ 的新檔案 gridd.conf
  
  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```
  
8. 將下面內容新增至 `/etc/nvidia/gridd.conf`：
 
  ```
  IgnoreSP=TRUE
  ```
9. 重新啟動 VM 並繼續確認安裝。

### <a name="verify-driver-installation"></a>確認驅動程式安裝


若要查詢 GPU 裝置狀態，請透過 SSH 連線至 VM 並執行與驅動程式一起安裝的 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 命令列公用程式。 

您會看到類似以下的輸出：

![NVIDIA 裝置狀態](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11 伺服器
如果您需要 X11 伺服器遠端連線到 NV VM，建議使用 [x11vnc](http://www.karlrunge.com/x11vnc/)，因為它可讓圖形進行硬體加速。 必須以手動方式將 M60 裝置的 BusID 新增至 xconfig 檔案 (Ubuntu 16.04 LTS 上的 `etc/X11/xorg.conf`、CentOS 7.3 或 Red Hat Enterprise Server 7.3 上的 `/etc/X11/XF86config`)。 新增類似下列的 `"Device"` 區段：
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "your-BusID:0:0:0"
EndSection
```
 
此外，更新您的 `"Screen"` 區段，即可使用此裝置。
 
可以找到 BusID，方法為執行

```bash
/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1
```
 
重新配置或重新啟動 VM 時，可以變更 BusID。 因此，重新啟動 VM 時，建議您使用指令碼來更新 X11 設定中的 BusID。 例如：

```bash 
#!/bin/bash
BUSID=$((16#`/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1`))

if grep -Fxq "${BUSID}" /etc/X11/XF86Config; then     echo "BUSID is matching"; else   echo "BUSID changed to ${BUSID}" && sed -i '/BusID/c\    BusID          \"PCI:0@'${BUSID}':0:0:0\"' /etc/X11/XF86Config; fi
```

可以叫用這個檔案作為開機時的根目錄，方法是在 `/etc/rc.d/rc3.d` 中為其建立項目。


## <a name="install-cuda-drivers-for-nc-vms"></a>安裝適用於 NC VM 的 CUDA 驅動程式

以下是從 NVIDIA CUDA Toolkit 在 Linux NC VM 上安裝 NVIDIA 驅動程式的步驟。 

C 和 C++ 開發人員可以選擇性地安裝完整 Toolkit，以建置 GPU 加速的應用程式。 如需詳細資訊，請參閱 [CUDA 安裝指南](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)。


> [!NOTE]
> 這裡所提供的 CUDA 驅動程式下載連結，為發行當時的最新驅動程式。 如需最新的 CUDA 驅動程式，請瀏覽 [NVIDIA](https://developer.nvidia.com/cuda-zone) 網站。
>

若要安裝 CUDA Toolkit，請透過 SSH 連線至每部 VM。 若要確認系統有 CUDA 功能的 GPU，請執行下列命令︰

```bash
lspci | grep -i NVIDIA
```
您會看到類似下列範例 (顯示 NVIDIA Tesla K80 卡) 的輸出︰

![lspci 命令輸出](./media/n-series-driver-setup/lspci.png)

然後執行您的配送映像特有的安裝命令。

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. 下載並安裝 CUDA 驅動程式。
  ```bash
  CUDA_REPO_PKG=cuda-9-0_9.0.176-1_amd64.deb

  wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

  sudo dpkg -i /tmp/${CUDA_REPO_PKG}

  sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo apt-get update

  sudo apt-get install cuda-drivers

  ```

  安裝可能需要數分鐘的時間。

2. 若要選擇性地安裝完整的 CUDA Toolkit，請輸入︰

  ```bash
  sudo apt-get install cuda
  ```

3. 重新啟動 VM 並繼續確認安裝。

#### <a name="cuda-driver-updates"></a>CUDA 驅動程式更新

我們建議您在部署後定期更新 CUDA 驅動程式。

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-based-73-or-red-hat-enterprise-linux-73"></a>以 CentOS 作為基礎的 7.3 或 Red Hat Enterprise Linux 7.3

1. 請安裝最新的 Linux Integration Services for Hyper-V。

  > [!IMPORTANT]
  > 如果您在 NC24r VM 上安裝了 CentOS 架構的 HPC 映像，請跳至步驟 3。 因為 HPC 映像中預先安裝了 Azure RDMA 驅動程式和 Linux Integration Services，所以不應該升級 LIS，並且依預設會停用核心更新。
  >

  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-1.tar.gz
 
  tar xvzf lis-rpms-4.2.3-1.tar.gz
 
  cd LISISO
 
  sudo ./install.sh
 
  sudo reboot
  ```
 
3. 重新連線至 VM，並使用下列命令繼續安裝：

  ```bash
  sudo yum install kernel-devel

  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

  sudo yum install dkms

  CUDA_REPO_PKG=cuda-repo-rhel7-9.0.176-1.x86_64.rpm

  wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

  sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo yum install cuda-drivers
  ```

  安裝可能需要數分鐘的時間。 

4. 若要選擇性地安裝完整的 CUDA Toolkit，請輸入︰

  ```bash
  sudo yum install cuda
  ```

5. 重新啟動 VM 並繼續確認安裝。


### <a name="verify-driver-installation"></a>確認驅動程式安裝


若要查詢 GPU 裝置狀態，請透過 SSH 連線至 VM 並執行與驅動程式一起安裝的 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 命令列公用程式。 

您會看到類似以下的輸出：

![NVIDIA 裝置狀態](./media/n-series-driver-setup/smi.png)



## <a name="rdma-network-for-nc24r-vms"></a>適用於 NC24r VM 的 RDMA 網路

可以在部署於相同可用性設定組中的 NC24r VM 上啟用 RDMA 網路連線能力。 RDMA 網路可針對搭配 Intel MPI 5.x 或更新版本執行的應用程式，支援訊息傳遞介面 (MPI) 流量。 其他需求如下：

### <a name="distributions"></a>散發

從 Azure Marketplace 中支援 RDMA 連線的下列其中一個映像，部署 NC24r VM：
  
* **Ubuntu**：Ubuntu Server 16.04 LTS。 設定 VM 上的 RDMA 驅動程式，並向 Intel 註冊以下載 Intel MPI：

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS 型 HPC**：CentOS 型 7.3 HPC。 已在 VM 上安裝 RDMA 驅動程式和 Intel MPI 5.1。 


## <a name="troubleshooting"></a>疑難排解

* 執行具有 4.4.0-75 Linux 核心之 Ubuntu 16.04 LTS 的 Azure N 系列 VM 具有和 CUDA 驅動程式相關的已知問題。 如果您要從較早的核心版本升級，請升級到最少核心版本 4.4.0-77。

* 您可以使用 nvidia-smi 設定持續性模式，如此當您需要查詢卡片時，命令的輸出更快。 若要設定持續性模式，請執行 `nvidia-smi -pm 1`。 請注意，如果重新啟動 VM，模式設定就會消失。 您一律可以編寫指令碼在啟動時執行模式設定。


## <a name="next-steps"></a>後續步驟

* 如需 N 系列 VM 上 NVIDIA GPU 的詳細資訊，請參閱︰
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (適用於 Azure NC VM)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (適用於 Azure NV VM)

* 若要擷取已安裝 NVIDIA 驅動程式的 Linux VM 映像，請參閱[如何一般化和擷取 Linux 虛擬機器](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
