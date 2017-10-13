---
title: "設定 Linux RDMA 叢集以執行 MPI 應用程式 | Microsoft Docs"
description: "建立由 H16r、H16mr、A8 或 A9 大小之 VM 所組成的 Linux 叢集，以使用 Azure RDMA 網路來執行 MPI 應用程式"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 01834bad-c8e6-48a3-b066-7f1719047dd2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.openlocfilehash: 4b2ceb64b1737918458f6d5c692fc2bfbc0f12ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>設定 Linux RDMA 叢集以執行 MPI 應用程式
了解如何使用[高效能運算 VM 大小](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)在 Azure 中設定 Linux RDMA 叢集，以執行平行訊息傳遞介面 (MPI) 應用程式。 本文提供準備 Linux HPC 映像以在叢集上執行 Intel MPI 的步驟。 準備之後，您會使用此映像和其中一個支援 RDMA 的 Azure VM 大小 (目前為 H16r、H16mr、A8 或 A9) 來部署 VM 的叢集。 請使用此叢集來執行透過低延遲、高輸送量網路有效率地進行通訊的 MPI 應用程式，此網路是以遠端直接記憶體存取 (RDMA) 技術為基礎。

> [!IMPORTANT]
> Azure 建立和處理資源的部署模型有兩種：[Azure Resource Manager](../../../resource-manager-deployment-model.md) 和傳統。 本文涵蓋之內容包括使用傳統部署模型。 Microsoft 建議讓大部分的新部署使用資源管理員模式。

## <a name="cluster-deployment-options"></a>叢集部署選項
您可以使用下列方法來建立包含或不含作業排程器的 Linux RDMA 叢集。

* **Azure CLI 指令碼**：如本文稍後所示，請使用 [Azure 命令列介面](../../../cli-install-nodejs.md) (CLI) 來處理支援 RDMA 之 VM 叢集的部署指令碼。 「服務管理」模式下的 CLI 會以傳統部署模型循序建立叢集節點，因此如果部署許多計算節點，可能需要花費數分鐘的時間。 使用傳統部署模型時，若要啟用 RDMA 網路連線，請將 VM 部署在相同的雲端服務中。
* **Azure Resource Manager 範本**：您也可以使用 Resource Manager 部署模型來部署支援 RDMA 的 VM 叢集，以連線到 RDMA 網路。 您可以[建立自己的範本](../../../resource-group-authoring-templates.md)，或檢查 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)，取得由 Microsoft 或社群貢獻的範本以部署想要的方案。 資源管理員範本可以提供快速可靠的方式來部署 Linux 叢集。 使用 Resource Manager 部署模型時，若要啟用 RDMA 網路連線，請將 VM 部署在相同的可用性設定組中。
* **HPC Pack**：在 Azure 中建立 Microsoft HPC Pack 叢集，並新增支援 RDMA 的計算節點來執行支援的 Linux 散發套件，以存取 RDMA 網路。 如需詳細資訊，請參閱[開始在 Azure 中的 HPC Pack 叢集使用 Linux 計算節點](hpcpack-cluster.md)。

## <a name="sample-deployment-steps-in-the-classic-model"></a>傳統模型中的範例部署步驟
下列步驟示範如何使用 Azure CLI 從 Azure Marketplace 部署 SUSE Linux Enterprise Server (SLES) 12 SP1 HPC VM、進行自訂，以及建立自訂 VM 映像。 然後，您可以使用映像來處理支援 RDMA 之 VM 叢集的部署指令碼。

> [!TIP]
> 請使用類似的步驟，根據 Azure Marketplace 中以 CentOS 為基礎的 HPC 映像來部署支援 RDMA 的 VM 叢集。 如所述，某些步驟會稍有不同。 
>
>

### <a name="prerequisites"></a>必要條件
* **用戶端電腦**：您需要一部 Mac、Linux 或 Windows 用戶端電腦來與 Azure 進行通訊。 這些步驟假設您使用 Linux 用戶端。
* **Azure 訂用帳戶**：如果您沒有訂用帳戶，只需要幾分鐘就可以建立[免費帳戶](https://azure.microsoft.com/free/)。 針對較大的叢集，請考慮隨用隨付訂用帳戶或其他購買選項。
* **VM 大小可用性**：下列執行個體大小有支援 RDMA︰H16r、H16mr、A8 及 A9。 如需了解 Azure 區域中的可用性，請查看 [依區域提供的產品](https://azure.microsoft.com/regions/services/) 。
* **核心配額**：您可能需要增加核心配額，才能部署計算密集型 VM 的叢集。 例如，如本文所示，如果您想要部署 8 個 A9 VM，將至少需要 128 個核心。 您的訂用帳戶可能也會限制您可以在特定 VM 大小系列 (包括 H 系列) 中部署的核心數目。 若要要求增加配額，可免費[開啟線上客戶支援要求](../../../azure-supportability/how-to-create-azure-support-request.md)。
* **Azure CLI**：[安裝](../../../cli-install-nodejs.md) Azure CLI 並從用戶端電腦[連接到您的 Azure 訂用帳戶](../../../xplat-cli-connect.md)。

### <a name="provision-an-sles-12-sp1-hpc-vm"></a>佈建 SLES 12 SP1 HPC VM
使用 Azure CLI 登入 Azure 之後，請執行 `azure config list` 來確認輸出顯示服務管理模式。 如果不是，請執行此命令來設定模式：

    azure config mode asm


請輸入下列命令來列出已授權您使用的所有訂用帳戶：

    azure account list

目前使用中訂用帳戶的 `Current` 會設定為 `true`。 如果此訂用帳戶不是您想要用來建立叢集的訂用帳戶，請將適當的訂用帳戶識別碼設定為使用中的訂用帳戶：

    azure account set <subscription-Id>

若要查看 Azure 中公開可用的 SLES 12 SP1 HPC 映像，請執行如下的命令，這是假設您的 Shell 環境支援 **grep**：

    azure vm image list | grep "suse.*hpc"

透過執行如下的命令，使用 SLES 12 SP1 HPC 映像來佈建支援 RDMA 的 VM：

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

其中：

* 大小 (此範例中為 A9) 是其中一個支援 RDMA 的 VM 大小。
* 外部 SSH 連接埠號碼 (在此範例中為 22，也就是 SSH 的預設值) 是任何有效的連接埠號碼。 內部 SSH 連接埠號是設定為 22。
* 在 location 所指定的 Azure 區域中會建立一個新雲端服務。 請指定一個有提供您選擇之 VM 大小的位置。
* 若為 SUSE 優先支援方案 (另外收費)，SLES 12 SP1 映像名稱目前可以是下列其中一個選項： 

 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824`

  `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824`


### <a name="customize-the-vm"></a>自訂 VM
VM 完成佈建之後，使用 VM 的外部 IP 位址 (或 DNS 名稱) 以及您設定的外部連接埠號碼，透過 SSH 傳送至 VM，然後加以自訂。 如需連接詳細資料，請參閱[如何登入執行 Linux 的虛擬機器](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 除非需要根目錄存取權才能完成步驟，否則請以您在 VM 上設定的使用者身分來執行命令。

> [!IMPORTANT]
> Microsoft Azure 不會提供 Linux VM 的根目錄存取權。 若要在以使用者身分連接至 VM 時取得系統管理存取權，請使用 `sudo` 執行命令。
>
>

* **更新**：使用 zypper 安裝更新。 您也可能會想要安裝 NFS 公用程式。

  > [!IMPORTANT]
  > 在 SLES 12 SP1 HPC VM 中，建議您不要套用核心更新，因為這可能會導致 Linux RDMA 驅動程式發生問題。
  >
  >
* **Intel MPI**：執行下列命令以完成 SLES 12 SP1 HPC VM 上的 Intel MPI 安裝︰

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
* **鎖定記憶體**：若要讓 MPI 程式碼鎖定可供 RDMA 使用的記憶體，請在 /etc/security/limits.conf 檔案中新增或變更下列設定。 您必須要有根目錄存取權才能編輯此檔案。

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

  > [!NOTE]
  > 基於測試目的，您也可以將 memlock 設定為無限制。 例如， `<User or group name>    hard    memlock unlimited`。 如需詳細資訊，請參閱[設定鎖定的記憶體大小的最佳已知方法](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size)。
  >
  >
* **SLES VM 的 SSH 金鑰**：產生 SSH 金鑰以在執行 MPI 作業時，於 SLES 叢集的所有計算節點之間建立使用者帳戶信任。 如果您部署 CentOS 型 HPC VM，請勿遵循此步驟。 請參閱本文稍後的指示，在您擷取映像並部署叢集之後，設定叢集節點間的無密碼 SSH 信任。

    若要建立 SSH 金鑰，請執行下列命令。 出現輸入提示時，選取 **Enter** 即可在預設位置產生金鑰，而不需要設定密碼。

        ssh-keygen

    將公開金鑰附加至已知公開金鑰的 authorized_keys 檔案。

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    在 ~/.ssh 目錄中，編輯或建立組態檔。 請提供您打算在 Azure 中使用的私人網路 IP 位址範圍 (在本範例中為 10.32.0.0/16)：

        host 10.32.0.*
        StrictHostKeyChecking no

    或者，列出叢集中每個 VM 的私人網路 IP 位址，如下所示：

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

  > [!NOTE]
  > 未指定特定 IP 位址或範圍時，設定 `StrictHostKeyChecking no` 可能會造成潛在的安全性風險。
  >
  >
* **應用程式**：在擷取映像之前，安裝您需要的任何應用程式，或執行其他自訂項目。

### <a name="capture-the-image"></a>擷取映像
若要擷取映像，請先在 Linux VM 上執行下列命令。 此命令會將 VM 取消佈建，但會保留您設定的使用者帳戶與 SSH 金鑰。

```
sudo waagent -deprovision
```

從用戶端電腦，執行下列 Azure CLI 命令來擷取映像。 如需詳細資訊，請參閱[如何將傳統 Linux 虛擬機器擷取成映像](capture-image.md)。  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

執行這些命令後，系統會擷取 VM 映像供您使用，接著將會刪除 VM。 現在您已準備好使用自訂映像來部署叢集。

### <a name="deploy-a-cluster-with-the-image"></a>使用映像部署叢集
針對您的環境，使用適當的值修改下列 Bash 指令碼，並從用戶端電腦執行。 因為 Azure 在傳統部署模型中循序部署 VM，系統會花費幾分鐘部署這段指令碼中建議的八個 A9 VM。

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>CentOS HPC 叢集的考量
如果您想要根據 Azure Marketplace 中的其中一個 CentOS 型 HPC 映像 (而不是 SLES 12 for HPC) 來設定叢集，請依照上一節中的一般步驟操作。 佈建及設定 VM 時，請注意下列差異：

- 已經在從 CentOS 型 HPC 映像佈建的 VM 上安裝 Intel MPI。
- 已經在 VM 的 /etc/security/limits.conf 檔案中新增鎖定記憶體設定。
- 請勿在您針對擷取佈建的 VM 上產生 SSH 金鑰。 取而代之的是，建議您在部署叢集之後，再設定以使用者為基礎的驗證。 如需詳細資訊，請參閱下列區度。  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>在叢集上設定無密碼 SSH 信任
在 CentOS 型 HPC 叢集上，有兩種可在計算節點之間建立信任的方法︰以主機為基礎的驗證和以使用者為基礎的驗證。 以主機為基礎的驗證超出本文的範圍，通常必須在部署期間透過擴充功能指令碼來完成。 以使用者為基礎的驗證便於在部署之後建立信任，而且需要在叢集中計算節點之間產生與共用 SSH 金鑰。 此命令通常稱為無密碼 SSH 登入，執行 MPI 工作時必須使用此命令。

從社群所貢獻的範例指令碼位於 [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) 上，以在 CentOS 型 HPC 叢集上啟用簡單使用者驗證。 請使用下列步驟來下載和使用此指令碼。 您也可以修改此指令碼，或使用任何其他方法來建立叢集計算節點之間的無密碼 SSH 驗證。

    wget https://raw.githubusercontent.com/tanewill/utils/master/ user_authentication.sh

若要執行此指令碼，您必須知道子網路 IP 位址的首碼。 請在其中一個叢集節點上執行下列命令來取得首碼。 您的輸出應該看起來像 10.1.3.5，而首碼是 10.1.3 部分。

    ifconfig eth0 | grep -w inet | awk '{print $2}'

現在請使用三個參數執行指令碼︰計算節點上的一般使用者名稱、該使用者在計算節點上的一般密碼，以及從上一個命令傳回的子網路首碼。

    ./user_authentication.sh <myusername> <mypassword> 10.1.3

此指令碼會執行下列動作︰

* 在名為 .ssh 的主機節點上建立目錄，此為無密碼登入的必要項目。
* 在 [.ssh] 目錄中建立組態檔，以指示無密碼登入允許從任何叢集節點進行登入。
* 建立包含所有叢集節點之節點名稱與節點 IP 位址的檔案。 在執行指令碼之後，會留下這些檔案以供日後參考。
* 為每個叢集節點 (包括主機節點) 建立私密和公開金鑰組，並在 authorized_keys 檔案中建立項目。

> [!WARNING]
> 執行這個指令碼可能會建立潛在的安全性風險。 請確定未散發 ~/.ssh 中的公開金鑰資訊。
>
>

## <a name="configure-intel-mpi"></a>設定 Intel MPI
若要在 Azure Linux RDMA 上執行 MPI 應用程式，您需要設定 Intel MPI 專用的特定環境變數。 以下是一個範例 Bash 指令碼，可用來設定執行應用程式所需的變數。 視 Intel MPI 的安裝需要來變更 mpivars.sh 路徑。

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

主機檔案的格式如下所示。 針對叢集中的每個節點加入一行指令碼。 從之前定義的虛擬網路指定私人 IP 位址，而非 DNS 名稱。 例如，對於 IP 位址為 10.32.0.1 和 10.32.0.2 的兩台主機，此檔案包含下列內容：

```
10.32.0.1:16
10.32.0.2:16
```

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>在基本的雙節點叢集上執行 MPI
如果您尚未這樣做，請先設定 Intel MPI 的環境。

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-an-mpi-command"></a>執行 MPI 命令
在其中一個計算節點上執行 MPI 命令，以顯示 MPI 已正確安裝而且可以在至少兩個計算節點之間通訊。 下列 **mpirun** 命令會在兩個節點上執行 **hostname** 命令。

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
您的輸出應該會列出您傳遞做為 `-hosts`之輸入的所有節點名稱。 例如，具有兩個節點的 **mpirun** 命令會傳回如下的輸出：

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>執行 MPI 基準測試
下列 Intel MPI 命令會執行 pingpong 基準測試，以驗證叢集組態和對 RDMA 網路的連線。

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

在含有兩個節點的工作中叢集上，您應該會看到如下的輸出。 在 Azure RDMA 網路上，大小在 512 個位元組以內的訊息預期會出現等於或小於 3 微秒的延遲。

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## <a name="next-steps"></a>後續步驟
* 在 Linux 叢集上部署並執行 Linux MPI 應用程式。
* 如需 Intel MPI 的指引，請參閱 [Intel MPI Library 文件](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/)。
* 請嘗試 [快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) 以使用 CentOS 型 HPC 映像建立 Intel Lustre 叢集。 如需詳細資訊，請參閱[在 Microsoft Azure 上部署 Lustre 的 Intel 雲端版本](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/)。
