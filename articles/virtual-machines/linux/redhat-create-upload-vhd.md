---
title: "建立及上傳 Red Hat Enterprise Linux VHD 以在 Azure 中使用 | Microsoft Docs"
description: "了解如何建立及上傳包含 Red Hat Linux 作業系統的 Azure 虛擬硬碟 (VHD)。"
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 6c6b8f72-32d3-47fa-be94-6cb54537c69f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/28/2017
ms.author: szark
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: b753c76b8c3d789c681d7fbff6aa07590b860be5
ms.contentlocale: zh-tw
ms.lasthandoff: 08/09/2017

---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>準備適用於 Azure 的 Red Hat 型虛擬機器
在本文中，您將學習如何準備 Red Hat Enterprise Linux (RHEL) 虛擬機器以在 Azure 中使用。 本文涵蓋的 RHEL 版本為 6.7 和 7.1。 本文章所述之準備作業使用 Hyper-V、核心為基礎之虛擬機器 (KVM) 及 VMware 等 Hypervisor。 如需參加 Red Hat 雲端存取方案之資格需求的詳細資訊，請參閱 [Red Hat 雲端存取網站](http://www.redhat.com/en/technologies/cloud-computing/cloud-access)與[在 Azure 上執行 RHEL](https://access.redhat.com/ecosystem/ccsp/microsoft-azure)。

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>從 Hyper-V 管理員準備 Red Hat 型虛擬機器

### <a name="prerequisites"></a>必要條件
本節假設您已經從 Red Hat 網站取得 ISO 檔案並將 RHEL 映像安裝至虛擬硬碟 (VHD)。 如需有關如何使用 Hyper-V 管理員來安裝作業系統映像的詳細資訊，請參閱[安裝 Hyper-V 角色和設定虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。

**RHEL 安裝注意事項**

* Azure 不支援 VHDX 格式。 Azure 只支援固定 VHD。 您可以使用 Hyper-V 管理員將磁碟轉換為 VHD 格式，或者使用 convert-vhd Cmdlet。 如果您使用 VirtualBox，請選取 [固定大小] ，而不是預設在建立磁碟時動態配置的選項。
* Azure 僅支援第 1 代虛擬機器。 您可以將第 1 代虛擬機器從 VHDX 轉換為 VHD 檔案格式，並從動態擴充轉換為固定大小的磁碟。 您無法變更虛擬機器的世代。 如需詳細資訊，請參閱[應該在 Hyper-V 中建立第 1 代還是第 2 代的虛擬機器？](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。
* 允許的 VHD 大小上限為 1,023 GB。
* 安裝 Linux 作業系統時，我們建議您使用標準磁碟分割而不是邏輯磁碟區管理員 (LVM)，其經常是許多安裝的預設設定。 此練習可避免 LVM 名稱與複製的虛擬機器發生衝突，特別是為了疑難排解而需要將作業系統磁碟連結至另一部相同虛擬機器時。 如果願意，您可以在資料磁碟上使用 [LVM](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 [RAID](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* 需要掛接通用磁碟格式 (UDF) 檔案系統的核心支援。 在 Azure 上第一次開機時，連結至客體的 UDF 格式媒體會將佈建組態傳遞至 Linux 虛擬機器。 Azure Linux 代理程式必須能夠掛接 UDF 檔案系統，才能讀取其組態並佈建虛擬機器。
* 2.6.37 以前的 Linux 核心版本在具有較大虛擬機器大小的 Hyper-V 上不支援非統一記憶體存取 (NUMA)。 這個問題主要會影響使用上游 Red Hat 2.6.32 kernel 的較舊散發套件，RHEL 6.6 (kernel-2.6.32-504) 已加以修正。 執行 2.6.37 以前自訂核心或 2.6.32-504 以前 RHEL 核心的系統必須在 grub.conf 的核心命令列上設定 `numa=off` 開機參數。 如需詳細資訊，請參閱 Red Hat [KB 436883](https://access.redhat.com/solutions/436883)。
* 請勿在作業系統磁碟上設定交換磁碟分割。 您可以設定 Linux 代理程式在暫存資源磁碟上建立交換檔。  在下列步驟中可以找到與此有關的詳細資訊。
* 所有 VHD 的大小都必須是 1 MB 的倍數。

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>從 Hyper-V 管理員準備 RHEL 6 虛擬機器

1. 在 Hyper-V 管理員中，選取虛擬機器。

2. 按一下 [連接] ，以開啟虛擬機器的主控台視窗。

3. 在 RHEL 6 中，NetworkManager 可能會對 Azure Linux 代理程式造成干擾。 執行下列命令以將此套件解除安裝：
   
        # sudo rpm -e --nodeps NetworkManager

4. 建立或編輯 `/etc/sysconfig/network` 檔案，然後新增下列文字：
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. 建立或編輯 `/etc/sysconfig/network-scripts/ifcfg-eth0` 檔案，然後新增下列文字：
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6. 移動 (或移除) udev 規則可防止產生乙太網路介面的靜態規則。 在 Microsoft Azure 或 Hyper-V 中複製虛擬機器時，這些規則會造成問題：

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

7. 若要確保開機時會啟動網路服務，可執行下列命令：

        # sudo chkconfig network on

8. 透過執行以下命令來註冊 Red Hat 訂用帳戶，以便從 RHEL 儲存機制安裝封裝：

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

9. WALinuxAgent 套件 `WALinuxAgent-<version>` 已推送至 Red Hat extras 儲存機制。 執行下列命令以啟用 extras 儲存機制：

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

10. 修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。 若要執行此修改，請在文字編輯器中開啟 `/boot/grub/menu.lst`，並確定預設核心包含以下參數：
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    這也將確保所有主控台訊息都會傳送給第一個序列埠，以協助 Azure 支援團隊進行問題偵錯程序。
    
    此外，我們還建議您移除下列參數：
    
        rhgb quiet crashkernel=auto
    
    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。  您可以視需要保留 `crashkernel` 選項的設定。 請注意，此參數會減少虛擬機器中約 128 MB 或以上的可用記憶體數量。 此組態可能會對小型虛擬機器造成問題。

    >[!Important]
    RHEL 6.5 與較舊版本也必須設定 `numa=off` 核心參數。 請參閱 Red Hat [KB 436883](https://access.redhat.com/solutions/436883)。

11. 確定已安裝安全殼層 (SSH) 伺服器，並已設定為在開機時啟動 (這通常為預設值)。 修改 /etc/ssh/sshd_config 以包含下面一行：

        ClientAliveInterval 180

12. 執行以下命令來安裝 Azure Linux 代理程式：

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    如果 NetworkManager 和 NetworkManager-gnome 套件並未在步驟 3 中遭到移除，則在安裝 WALinuxAgent 套件時會將這兩個套件移除。

13. 請勿在作業系統磁碟上建立交換空間。

    Azure Linux 代理程式可在虛擬機器佈建於 Azure 後，使用連結至虛擬機器的本機資源磁碟自動設定交換空間。 請注意，本機資源磁碟是暫存磁碟，可能會在虛擬機器取消佈建時清空。 在上一個步驟安裝 Azure Linux 代理程式後，請在 /etc/waagent.conf 中適當修改下列參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

14. 執行下列命令以取消註冊訂用帳戶 (如有必要)：

        # sudo subscription-manager unregister

15. 執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

16. 在 Hyper-V 管理員中，按一下 [動作] > [關閉]。 您現在可以將 Linux VHD 上傳至 Azure。


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>從 Hyper-V 管理員準備 RHEL 7 虛擬機器

1. 在 Hyper-V 管理員中，選取虛擬機器。

2. 按一下 [連接]  ，以開啟虛擬機器的主控台視窗。

3. 建立或編輯 `/etc/sysconfig/network` 檔案，然後新增下列文字：
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4. 建立或編輯 `/etc/sysconfig/network-scripts/ifcfg-eth0` 檔案，然後新增下列文字：
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

5. 若要確保開機時會啟動網路服務，可執行下列命令：

        # sudo chkconfig network on

6. 透過執行以下命令來註冊 Red Hat 訂用帳戶，以便從 RHEL 儲存機制安裝封裝：

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7. 修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。 若要執行此修改，請在文字編輯器中開啟 `/etc/default/grub`，然後編輯 `GRUB_CMDLINE_LINUX` 參數。 例如：
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   這也將確保所有主控台訊息都會傳送給第一個序列埠，以協助 Azure 支援團隊進行問題偵錯程序。 此組太也會關閉新的 RHEL 7 對 NIC 的命名慣例。 此外，我們還建議您移除下列參數：
   
        rhgb quiet crashkernel=auto
   
    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。 您可以視需要保留 `crashkernel` 選項的設定。 請注意，此參數會減少虛擬機器中約 128 MB 或以上的可用記憶體數量，這可能會對小型虛擬機器造成問題。

8. 完成 `/etc/default/grub`的編輯之後，請執行下列命令以重建 grub 組態：

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9. 確定已安裝 SSH 伺服器，並已設定為在開機時啟動 (這通常為預設值)。 修改 `/etc/ssh/sshd_config` 以包含下面一行：

        ClientAliveInterval 180

10. WALinuxAgent 套件 `WALinuxAgent-<version>` 已推送至 Red Hat extras 儲存機制。 執行下列命令以啟用 extras 儲存機制：

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

11. 執行以下命令來安裝 Azure Linux 代理程式：

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

12. 請勿在作業系統磁碟上建立交換空間。

    Azure Linux 代理程式可在虛擬機器佈建於 Azure 後，使用連結至虛擬機器的本機資源磁碟自動設定交換空間。 請注意，本機資源磁碟是暫存磁碟，可能會在虛擬機器取消佈建時清空。 在上一個步驟安裝 Azure Linux 代理程式後，請在 `/etc/waagent.conf` 中適當修改下列參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. 如果您要取消註冊訂用帳戶，請執行下列命令：

        # sudo subscription-manager unregister

14. 執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

15. 在 Hyper-V 管理員中，按一下 [動作] > [關閉]。 您現在可以將 Linux VHD 上傳至 Azure。


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>從 KVM 準備 Red Hat 型虛擬機器
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>從 KVM 準備 RHEL 6 虛擬機器

1. 從 Red Hat 網站下載 RHEL 6 的 KVM 映像。

2. 設定根密碼。

    產生加密的密碼，並複製命令的輸出：

        # openssl passwd -1 changeme

    使用 guestfish 設定根密碼：
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   將根使用者的第二個欄位從 "!!" 變更為加密的密碼。

3. 在 KVM 中從 qcow2 映像建立虛擬機器。 將磁碟類型設定為 **qcow2**，再將虛擬網路介面裝置模型設定為 **virtio**。 然後啟動虛擬機器並以 root 身分登入。

4. 建立或編輯 `/etc/sysconfig/network` 檔案，然後新增下列文字：
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. 建立或編輯 `/etc/sysconfig/network-scripts/ifcfg-eth0` 檔案，然後新增下列文字：
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6. 移動 (或移除) udev 規則可防止產生乙太網路介面的靜態規則。 在 Azure 或 Hyper-V 中複製虛擬機器時，這些規則會造成問題：

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

7. 若要確保開機時會啟動網路服務，可執行下列命令：

        # chkconfig network on

8. 透過執行以下命令來註冊 Red Hat 訂用帳戶，以便從 RHEL 儲存機制安裝封裝：

        # subscription-manager register --auto-attach --username=XXX --password=XXX

9. 修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。 若要進行此設定，請在文字編輯器中開啟 `/boot/grub/menu.lst`，並確定預設核心包含以下參數：
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    這也將確保所有主控台訊息都會傳送給第一個序列埠，以協助 Azure 支援團隊進行問題偵錯程序。
    
    此外，我們還建議您移除下列參數：
    
        rhgb quiet crashkernel=auto
    
    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。 您可以視需要保留 `crashkernel` 選項的設定。 請注意，此參數會減少虛擬機器中約 128 MB 或以上的可用記憶體數量，這可能會對小型虛擬機器造成問題。

    >[!Important]
    RHEL 6.5 與較舊版本也必須設定 `numa=off` 核心參數。 請參閱 Red Hat [KB 436883](https://access.redhat.com/solutions/436883)。

10. 將 Hyper-V 模組新增至 initramfs：  

    編輯 `/etc/dracut.conf` 檔案並新增下列內容：

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    重建 initramfs：

        # dracut -f -v

11. 解除安裝 cloud-init：

        # yum remove cloud-init

12. 確定您已安裝 SSH 伺服器，並已設定為在開機時啟動：

        # chkconfig sshd on

    修改 /etc/ssh/sshd_config 以包含下面一行：

        PasswordAuthentication yes
        ClientAliveInterval 180

13. WALinuxAgent 套件 `WALinuxAgent-<version>` 已推送至 Red Hat extras 儲存機制。 執行下列命令以啟用 extras 儲存機制：

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

14. 執行以下命令來安裝 Azure Linux 代理程式：

        # yum install WALinuxAgent

        # chkconfig waagent on

15. Azure Linux 代理程式可在虛擬機器佈建於 Azure 後，使用連結至虛擬機器的本機資源磁碟自動設定交換空間。 請注意，本機資源磁碟是暫存磁碟，可能會在虛擬機器取消佈建時清空。 在上一個步驟安裝 Azure Linux 代理程式後，請在 **/etc/waagent.conf** 中適當修改下列參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. 執行下列命令以取消註冊訂用帳戶 (如有必要)：

        # subscription-manager unregister

17. 執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

18. 在 KVM 中關閉虛擬機器。

19. 將 qcow2 映像轉換成 VHD 格式。

    先將映像轉換成原始格式：

        # qemu-img convert -f qcow2 -O raw rhel-6.8.qcow2 rhel-6.8.raw

    確認原始映像的大小符合 1 MB， 否則將大小四捨五入為 1 MB︰

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.8.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.8.raw $rounded_size

    將原始磁碟轉換成固定大小的 VHD：

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.8.raw rhel-6.8.vhd


### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>從 KVM 準備 RHEL 7 虛擬機器

1. 從 Red Hat 網站下載 RHEL 7 的 KVM 映像。 此程序會使用 RHEL 7 做為範例。

2. 設定根密碼。

    產生加密的密碼，並複製命令的輸出：

        # openssl passwd -1 changeme

    使用 guestfish 設定根密碼：

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   將根使用者的第二個欄位從 "!!" 變更為加密的密碼。

3. 在 KVM 中從 qcow2 映像建立虛擬機器。 將磁碟類型設定為 **qcow2**，再將虛擬網路介面裝置模型設定為 **virtio**。 然後啟動虛擬機器並以 root 身分登入。

4. 建立或編輯 `/etc/sysconfig/network` 檔案，然後新增下列文字：
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. 建立或編輯 `/etc/sysconfig/network-scripts/ifcfg-eth0` 檔案，然後新增下列文字：
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

6. 若要確保開機時會啟動網路服務，可執行下列命令：

        # chkconfig network on

7. 透過執行以下命令來註冊 Red Hat 訂用帳戶，以便從 RHEL 儲存機制安裝封裝：

        # subscription-manager register --auto-attach --username=XXX --password=XXX

8. 修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。 若要進行此設定，請在文字編輯器中開啟 `/etc/default/grub`，然後編輯 `GRUB_CMDLINE_LINUX` 參數。 例如：
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   此命令也會確保所有主控台訊息都會傳送給第一個序列埠，以協助 Azure 支援團隊進行問題偵錯程序。 而且也會關閉新的 RHEL 7 對 NIC 的命名慣例。 此外，我們還建議您移除下列參數：
   
        rhgb quiet crashkernel=auto
   
    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。 您可以視需要保留 `crashkernel` 選項的設定。 請注意，此參數會減少虛擬機器中約 128 MB 或以上的可用記憶體數量，這可能會對小型虛擬機器造成問題。

9. 完成 `/etc/default/grub`的編輯之後，請執行下列命令以重建 grub 組態：

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10. 將 Hyper-V 模組新增至 initramfs。

    編輯 `/etc/dracut.conf` 並加入內容：

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    重建 initramfs：

        # dracut -f -v

11. 解除安裝 cloud-init：

        # yum remove cloud-init

12. 確定您已安裝 SSH 伺服器，並已設定為在開機時啟動：

        # systemctl enable sshd

    修改 /etc/ssh/sshd_config 以包含下面一行：

        PasswordAuthentication yes
        ClientAliveInterval 180

13. WALinuxAgent 套件 `WALinuxAgent-<version>` 已推送至 Red Hat extras 儲存機制。 執行下列命令以啟用 extras 儲存機制：

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

14. 執行以下命令來安裝 Azure Linux 代理程式：

        # yum install WALinuxAgent

    啟用 waagent 服務：

        # systemctl enable waagent.service

15. 請勿在作業系統磁碟上建立交換空間。

    Azure Linux 代理程式可在虛擬機器佈建於 Azure 後，使用連結至虛擬機器的本機資源磁碟自動設定交換空間。 請注意，本機資源磁碟是暫存磁碟，可能會在虛擬機器取消佈建時清空。 在上一個步驟安裝 Azure Linux 代理程式後，請在 `/etc/waagent.conf` 中適當修改下列參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. 執行下列命令以取消註冊訂用帳戶 (如有必要)：

        # subscription-manager unregister

17. 執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

18. 在 KVM 中關閉虛擬機器。

19. 將 qcow2 映像轉換成 VHD 格式。

    先將映像轉換成原始格式：

        # qemu-img convert -f qcow2 -O raw rhel-7.3.qcow2 rhel-7.3.raw

    確認原始映像的大小符合 1 MB， 否則將大小四捨五入為 1 MB︰

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.8.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.8.raw $rounded_size

    將原始磁碟轉換成固定大小的 VHD：

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.3.raw rhel-7.3.vhd

## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>從 VMware 準備 Red Hat 型虛擬機器
### <a name="prerequisites"></a>必要條件
本節假設您已在 VMware 中安裝 RHEL 虛擬機器。 如需有關如何在 VMware 中安裝作業系統的詳細資訊，請參閱 [VMware 客體作業系統安裝指南](http://partnerweb.vmware.com/GOSIG/home.html)。

* 安裝 Linux 作業系統時，我們建議您使用標準磁碟分割而不是 LVM (這通常是許多安裝的預設設定)。 這可避免 LVM 名稱與複製的虛擬機器發生衝突，特別是為了疑難排解而需要將作業系統磁碟連結至另一部虛擬機器時。 如果願意，您可以在資料磁碟上使用 LVM 或 RAID。
* 請勿在作業系統磁碟上設定交換磁碟分割。 您可以設定 Linux 代理程式以在暫存資源磁碟上建立交換檔。 您可以在以下步驟中找到與此相關的詳細資訊。
* 建立虛擬硬碟時，請選取 [將虛擬磁碟儲存為單一檔案] 。

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>從 VMWare 準備 RHEL 6 虛擬機器
1. 在 RHEL 6 中，NetworkManager 可能會對 Azure Linux 代理程式造成干擾。 執行下列命令以將此套件解除安裝：
   
        # sudo rpm -e --nodeps NetworkManager

2. 在 /etc/sysconfig/ 目錄中，建立名為 **network** 、且包含下列文字的檔案：

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

3. 建立或編輯 `/etc/sysconfig/network-scripts/ifcfg-eth0` 檔案，然後新增下列文字：
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

4. 移動 (或移除) udev 規則可防止產生乙太網路介面的靜態規則。 在 Azure 或 Hyper-V 中複製虛擬機器時，這些規則會造成問題：

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

5. 若要確保開機時會啟動網路服務，可執行下列命令：

        # sudo chkconfig network on

6. 透過執行以下命令來註冊 Red Hat 訂用帳戶，以便從 RHEL 儲存機制安裝封裝：

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7. WALinuxAgent 套件 `WALinuxAgent-<version>` 已推送至 Red Hat extras 儲存機制。 執行下列命令以啟用 extras 儲存機制：

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

8. 修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。 若要這樣做，請在文字編輯器中開啟 `/etc/default/grub` 並編輯 `GRUB_CMDLINE_LINUX` 參數。 例如：
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   這也將確保所有主控台訊息都會傳送給第一個序列埠，以協助 Azure 支援團隊進行問題偵錯程序。 此外，我們還建議您移除下列參數：
   
        rhgb quiet crashkernel=auto
   
    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。 您可以視需要保留 `crashkernel` 選項的設定。 請注意，此參數會減少虛擬機器中約 128 MB 或以上的可用記憶體數量，這可能會對小型虛擬機器造成問題。

9. 將 Hyper-V 模組新增至 initramfs：

    編輯 `/etc/dracut.conf` 檔案並新增下列內容：

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    重建 initramfs：

        # dracut -f -v

10. 確定已安裝 SSH 伺服器，並已設定為在開機時啟動 (這通常為預設值)。 修改 `/etc/ssh/sshd_config` 以包含下面一行：

    ClientAliveInterval 180

11. 執行以下命令來安裝 Azure Linux 代理程式：

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

12. 請勿在作業系統磁碟上建立交換空間。

    Azure Linux 代理程式可在虛擬機器佈建於 Azure 後，使用連結至虛擬機器的本機資源磁碟自動設定交換空間。 請注意，本機資源磁碟是暫存磁碟，可能會在虛擬機器取消佈建時清空。 在上一個步驟安裝 Azure Linux 代理程式後，請在 `/etc/waagent.conf` 中適當修改下列參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. 執行下列命令以取消註冊訂用帳戶 (如有必要)：

        # sudo subscription-manager unregister

14. 執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

15. 關閉虛擬機器，然後將 VMDK 檔案轉換成 .vhd 檔案。

    先將映像轉換成原始格式：

        # qemu-img convert -f vmdk -O raw rhel-6.8.vmdk rhel-6.8.raw

    確認原始映像的大小符合 1 MB， 否則將大小四捨五入為 1 MB︰

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.8.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.8.raw $rounded_size

    將原始磁碟轉換成固定大小的 VHD：

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.8.raw rhel-6.8.vhd

### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>從 VMWare 準備 RHEL 7 虛擬機器
1. 建立或編輯 `/etc/sysconfig/network` 檔案，然後新增下列文字：
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2. 建立或編輯 `/etc/sysconfig/network-scripts/ifcfg-eth0` 檔案，然後新增下列文字：
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

3. 若要確保開機時會啟動網路服務，可執行下列命令：

        # sudo chkconfig network on

4. 透過執行以下命令來註冊 Red Hat 訂用帳戶，以便從 RHEL 儲存機制安裝封裝：

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5. 修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。 若要執行此修改，請在文字編輯器中開啟 `/etc/default/grub`，然後編輯 `GRUB_CMDLINE_LINUX` 參數。 例如：
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   此組態也會確保所有主控台訊息都會傳送給第一個序列埠，以協助 Azure 支援團隊進行問題偵錯程序。 也會關閉新的 RHEL 7 對 NIC 的命名慣例。 此外，我們還建議您移除下列參數：
   
        rhgb quiet crashkernel=auto
   
    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。 您可以視需要保留 `crashkernel` 選項的設定。 請注意，此參數會減少虛擬機器中約 128 MB 或以上的可用記憶體數量，這可能會對小型虛擬機器造成問題。

6. 完成 `/etc/default/grub`的編輯之後，請執行下列命令以重建 grub 組態：

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7. 將 Hyper-V 模組新增至 initramfs。

    編輯 `/etc/dracut.conf`，新增內容：

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    重建 initramfs：

        # dracut -f -v

8. 確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。 此設定通常是預設值。 修改 `/etc/ssh/sshd_config` 以包含下面一行：

        ClientAliveInterval 180

9. WALinuxAgent 套件 `WALinuxAgent-<version>` 已推送至 Red Hat extras 儲存機制。 執行下列命令以啟用 extras 儲存機制：

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

10. 執行以下命令來安裝 Azure Linux 代理程式：

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

11. 請勿在作業系統磁碟上建立交換空間。

    Azure Linux 代理程式可在虛擬機器佈建於 Azure 後，使用連結至虛擬機器的本機資源磁碟自動設定交換空間。 請注意，本機資源磁碟是暫存磁碟，可能會在虛擬機器取消佈建時清空。 在上一個步驟安裝 Azure Linux 代理程式後，請在 `/etc/waagent.conf` 中適當修改下列參數：

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12. 如果您要取消註冊訂用帳戶，請執行下列命令：

        # sudo subscription-manager unregister

13. 執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

14. 關閉虛擬機器，然後將 VMDK 檔案轉換成 VHD 格式。

    先將映像轉換成原始格式：

        # qemu-img convert -f vmdk -O raw rhel-7.3.vmdk rhel-7.3.raw

    確認原始映像的大小符合 1 MB， 否則將大小四捨五入為 1 MB︰

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.8.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.8.raw $rounded_size

    將原始磁碟轉換成固定大小的 VHD：

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.3.raw rhel-7.3.vhd

## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>使用 kickstart 檔案自動從 ISO 準備 Red Hat 型虛擬機器
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>從 kickstart 檔案準備 RHEL 7 虛擬機器

1.  建立包含以下內容的 kickstart 檔案，然後儲存此檔案。 如需有關 kickstart 安裝的詳細資訊，請參閱 [Kickstart 安裝指南](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html)。

        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
          auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff

        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

2. 將 kickstart 檔案放在可存取它的安裝系統。

3. 在 Hyper-V 管理員中建立新的虛擬機器。 在 [連線虛擬硬碟] 頁面上，選取 [稍後連接虛擬硬碟]，並完成 [新增虛擬機器精靈]。

4. 開啟虛擬機器設定：

    a.  將新的虛擬硬碟連結至虛擬機器。 請務必選取 [VHD 格式] 和 [固定大小]。

    b.這是另一個 C# 主控台應用程式。  將安裝 ISO 連接到 DVD 光碟機。

    c.  將 BIOS 設定成從 CD 開機。

5. 啟動虛擬機器。 當安裝指南出現時，請按 **Tab** 鍵以設定開機選項。

6. 在開機選項結尾輸入 `inst.ks=<the location of the kickstart file>` ，然後按 **Enter**鍵。

7. 等待安裝完成。 完成後，虛擬機器將會自動關閉。 您現在可以將 Linux VHD 上傳至 Azure。

## <a name="known-issues"></a>已知問題
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>使用非 Hyper-V Hypervisor 時，初始 RAM 磁碟未包含 Hyper-V 驅動程式

在某些情況下，Linux 安裝程式可能不會在初始 RAM 磁碟 (initrd 或 initramfs) 中包含 Hyper-V 的驅動程式，除非 Linux 偵測到自己在 Hyper-V 環境中執行。

使用不同的虛擬化系統 (即 Virtualbox、Xen 等) 來準備 Linux 映像時，您可能需要重新建置 initrd，以確保在初始 RAM 磁碟上至少有 hv_vmbus 和 hv_storvsc 核心模組可以使用。 目前至少已知在以上游 Red Hat 散發套件為基礎的系統上有此問題。

若要解決這個問題，請將 Hyper-V 模組新增至 initramfs 並加以重建︰

編輯 `/etc/dracut.conf` 檔案並新增下列內容：

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

重建 initramfs：

        # dracut -f -v

如需詳細資訊，請參閱 [重建 initramfs](https://access.redhat.com/solutions/1958)的相關資訊。

## <a name="next-steps"></a>後續步驟
您現在可以開始使用您的 Red Hat Enterprise Linux 虛擬硬碟在 Azure 建立新的虛擬機器。 若這是您第一次將該 .vhd 檔案上傳到 Azure，請參閱 [建立及上傳包含 Linux 作業系統的虛擬硬碟](classic/create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)中的步驟 2 和步驟 3。

如需已通過認證可執行 Red Hat Enterprise Linux 之 Hypervisor 的詳細資訊，請參閱 [Red Hat 網站](https://access.redhat.com/certified-hypervisors)。

