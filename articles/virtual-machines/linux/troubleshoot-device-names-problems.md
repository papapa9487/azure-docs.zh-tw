---
title: "在 Azure 中 Linux VM 裝置名稱已變更 | Microsoft Docs"
description: "說明裝置名稱變更的原因，並提供此問題的解決方案。"
services: virtual-machines-linux
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: 
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 07/12/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: 789f4580901a22dc3aaae9599c7205c76f268403
ms.contentlocale: zh-tw
ms.lasthandoff: 07/13/2017

---

# <a name="troubleshooting-linux-vm-device-names-are-changed"></a>疑難排解：Linux VM 裝置名稱已變更

本文說明在您重新啟動 Linux 虛擬機器 (VM) 或重新連接磁碟之後，裝置名稱變更的原因。 它也會提供此問題的解決方案。

## <a name="symptom"></a>徵狀

在 Microsoft Azure 中執行 Linux VM 時，您可能會遇到下列問題。

- VM 在重新啟動之後無法開機。

- 如果中斷連結並重新連接資料磁碟，磁碟的裝置名稱即會變更。

- 使用裝置名稱參考磁碟的應用程式或指令碼就會失敗。 您發現磁碟的裝置名稱已變更。

## <a name="cause"></a>原因

Linux 中的裝置路徑不保證會在重新啟動之間保持一致。 裝置名稱是由主要 (字母) 和次要的數字所組成。  當 Linux 儲存裝置驅動程式偵測到新的裝置時，它會從可用範圍中指派主要和次要裝置號碼給它。 移除裝置時，即會釋放裝置號碼，以供稍後重複使用。

問題發生原因是 Linux 中由 SCSI 子系統所排定的裝置掃描以非同步方式執行。 最終的裝置路徑命名可能會在重新啟動之間有所不同。 

## <a name="solution"></a>方案

若要解決這個問題，請使用永續性命名。 有四種方法可用於永續性命名：依檔案系統標籤、依 uuid、依識別碼，以及依路徑。 我們建議針對 Azure Linux VM 使用檔案系統標籤和 UUID 方法。 

大多數的發行版本也提供 **nofail** 或 **nobootwait** fstab 選項。 即使磁碟在啟動時無法掛接，這些選項也能讓系統開機。 請檢查發行版本的文件，以取得這些參數的相關資訊。 如需如何在您新增資料磁碟時設定 Linux VM 以使用 UUID 的詳細資訊，請參閱[連接到 Linux VM 以掛接新磁碟](add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk)。 

在 VM 上安裝 Azure Linux 代理程式時，它會使用 Udev 規則，在 **/dev/disk/azure** 下方建構一組符號連結。 應用程式和指令碼可以使用這些 Udev 規則，來識別已將磁碟連接到 VM、其類型及 LUN。

## <a name="more-information"></a>詳細資訊

### <a name="identify-disk-luns"></a>識別磁碟 LUN

應用程式可以使用 LUN，來尋找所有連接的磁碟和建構符號連結。 Azure Linux 代理程式現在包含 udev 規則，可將符號連結從 LUN 設定到裝置，如下所示：

    $ tree /dev/disk/azure

    /dev/disk/azure
    ├── resource -> ../../sdb
    ├── resource-part1 -> ../../sdb1
    ├── root -> ../../sda
    ├── root-part1 -> ../../sda1
    └── scsi1
        ├── lun0 -> ../../../sdc
        ├── lun0-part1 -> ../../../sdc1
        ├── lun1 -> ../../../sdd
        ├── lun1-part1 -> ../../../sdd1
        ├── lun1-part2 -> ../../../sdd2
        └── lun1-part3 -> ../../../sdd3                                    
                                 

您也可以使用 lsscsi 或類似的工具，從 Linux 客體擷取 LUN 資訊，如下所示。

       $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

此客體 LUN 資訊可以與 Azure 訂用帳戶中繼資料搭配使用，在儲存分割區資料之 VHD 的 Azure 儲存體中識別位置。 例如，使用 az cli：

    $ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks                                        
    [                                                                                                                                                                  
    {                                                                                                                                                                  
    "caching": "None",                                                                                                                                              
      "createOption": "empty",                                                                                                                                         
    "diskSizeGb": 1023,                                                                                                                                             
      "image": null,                                                                                                                                                   
    "lun": 0,                                                                                                                                                        
    "managedDisk": null,                                                                                                                                             
    "name": "testVM-20170619-114353",                                                                                                                    
    "vhd": {                                                                                                                                                          
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"                                                       
    }                                                                                                                                                              
    },                                                                                                                                                                
    {                                                                                                                                                                   
    "caching": "None",                                                                                                                                               
    "createOption": "empty",                                                                                                                                         
    "diskSizeGb": 512,                                                                                                                                              
    "image": null,                                                                                                                                                   
    "lun": 1,                                                                                                                                                        
    "managedDisk": null,                                                                                                                                             
    "name": "testVM-20170619-121516",                                                                                                                    
    "vhd": {                                                                                                                                                           
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"                                                       
      }                                                                                                                                                             
      }                                                                                                                                                             
    ]

### <a name="discover-filesystem-uuids-by-using-blkid"></a>使用 blkid 探索 filesystem UUID

指令碼或應用程式可以讀取 blkid 的輸出或類似的資訊來源，並在 **/dev** 中建構符號連結以供使用。 輸出將會顯示連接至 VM 之所有磁碟的 UUID，以及與其相關聯的裝置檔案：

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Waagent udev 規則會在 **/dev/disk/azure** 下方建構一組符號連結：


    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1


應用程式可以使用此資訊來識別開機磁碟裝置和資源 (暫時) 磁碟。 在 Azure 中，應用程式應該參考 **/dev/disk/azure/root-part1** 或 **/dev/disk/azure-resource-part1**，以探索這些分割區。

如果有其他來自 blkid 清單的分割區，則它們會位於資料磁碟上。 應用程式可以維護這些分割區的 UUID，並使用如下的路徑，在執行階段探索裝置名稱：

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1

    
### <a name="get-the-latest-azure-storage-rules"></a>取得最新的 Azure 儲存體規則

若要取得最新的 Azure 儲存體規則，請執行下列命令：

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block


如需詳細資訊，請參閱下列文章。

- [Ubuntu：使用 UUID](https://help.ubuntu.com/community/UsingUUID) \(英文\)

- [Red Hat：永續性命名](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html) \(英文\)

- [Linux：UUID 可為您做些什麼](https://www.linux.com/news/what-uuids-can-do-you) \(英文\)

- [Udev：現代 Linux 系統中的裝置管理簡介](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system) \(英文\)


