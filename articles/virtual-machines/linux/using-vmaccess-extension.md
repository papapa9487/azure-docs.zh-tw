---
title: "將存取重設為 Azure Linux VM | Microsoft Docs"
description: "如何使用 VMAccess 擴充功能和 Azure CLI 2.0 在 Linux VM 上管理使用者及重設存取"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 08/04/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 587c73278a9a92776276a811c5c4c8d3db773de3
ms.contentlocale: zh-tw
ms.lasthandoff: 08/16/2017

---
# <a name="manage-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-20"></a>使用 VMAccess 擴充功能搭配 Azure CLI 2.0 在 Linux VM 上管理使用者、SSH 及檢查或修復磁碟
Linux VM 的磁碟顯示錯誤。 您不知怎麼重設 Linux VM的根密碼，或不小心刪除了 SSH 私密金鑰。 如果是過去資料中心的時代發生此狀況，您必須親赴現場，然後再開啟 KVM 才能存取伺服器主控台。 請將 Azure VMAccess 擴充功能想成 KVM 交換器，在此可以存取主控台重設 Linux 存取或執行磁碟等級維護。

本文將說明如何使用 Azure VMAccess 擴充功能來檢查或修復磁碟、重設使用者存取、管理使用者帳戶或重設 Linux 上的 SSH 組態。 您也可以使用 [Azure CLI 1.0](using-vmaccess-extension-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 來執行這些步驟。


## <a name="ways-to-use-the-vmaccess-extension"></a>使用 VMAccess 擴充功能的方式
您可以透過兩種方式在 Linux VM 上使用「VMAccess 擴充功能」：

* 使用 Azure CLI 2.0 和必要的參數。
* [使用 VMAccess 擴充功能會處理然後作為行動依據的原始 JSON 檔案](#use-json-files-and-the-vmaccess-extension)。

下列範例會使用 [az vm user](/cli/azure/vm/user) 命令。 若要執行這些步驟，您需要安裝最新的 [Azure CLI 2.0](/cli/azure/install-az-cli2)，並且使用 [az login](/cli/azure/#login) 來登入 Azure 帳戶。

## <a name="reset-ssh-key"></a>重設 SSH 金鑰
下列範例會重設名為 `myVM` 的 VM 上使用者 `azureuser` 的 SSH 金鑰：

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="reset-password"></a>重設密碼
下列範例會重設名為 `myVM` 的 VM 上使用者 `azureuser` 的密碼：

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>重新啟動 SSH
下列範例會重新啟動 SSH 精靈，並將 SSH 組態重設成名為 `myVM` 的 VM 上之預設值：

```azurecli
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-a-user"></a>建立使用者
下列範例會在名為 `myVM` 的 VM 上，建立一個使用 SSH 金鑰進行驗證、名為 `myNewUser` 的使用者：

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>刪除使用者
下列範例會刪除名為 `myVM` 之 VM 上名為 `myNewUser` 的使用者：

```azurecli
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```


## <a name="use-json-files-and-the-vmaccess-extension"></a>使用 JSON 檔案和 VMAccess 擴充功能
下列範例會使用原始 JSON 檔案。 請使用 [az vm extension set](/cli/azure/vm/extension#set) 來接著呼叫您的 JSON 檔案。 您也可以從 Azure 範本呼叫這些 JSON 檔案。 

### <a name="reset-user-access"></a>重設使用者存取
如果您已無法存取 Linux VM 上的根，可以啟動 VMAccess 指令碼來重設使用者的 SSH 金鑰或密碼。

若要重設使用者的 SSH 公開金鑰，請建立名為 `reset_ssh_key.json` 的檔案，並以下列格式新增設定。 將您自己的值取代為 `username` 和 `ssh_key` 參數：

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

執行 VMAccess 指令碼搭配︰

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_ssh_key.json
```

若要重設使用者密碼，請建立名為 `reset_user_password.json` 的檔案，並以下列格式新增設定。 將您自己的值取代為 `username` 和 `password` 參數：

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

執行 VMAccess 指令碼搭配︰

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>重新啟動 SSH
若要重新啟動 SSH 精靈並將 SSH 組態重設為預設值，請建立名為 `reset_sshd.json` 的檔案。 新增下列內容：

```json
{
  "reset_ssh": true
}
```

執行 VMAccess 指令碼搭配︰

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-users"></a>管理使用者

若要建立使用 SSH 金鑰進行驗證的使用者，請建立名為 `create_new_user.json` 的檔案，並以下列格式新增設定。 將您自己的值取代為 `username` 和 `ssh_key` 參數：

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

執行 VMAccess 指令碼搭配︰

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

若要刪除使用者，請建立名為 `delete_user.json` 的檔案並新增下列內容。 將您自己的值取代為 `remove_user` 參數：

```json
{
  "remove_user":"myNewUser"
}
```

執行 VMAccess 指令碼搭配︰

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>檢查或修復磁碟
您也可以使用 VMAccess 來檢查並修復您新增至 Linux VM 的磁碟。

若要檢查然後修復磁碟，請建立名為 `disk_check_repair.json` 的檔案，並以下列格式新增設定。 將您自己的值取代為 `repair_disk` 的名稱：

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

執行 VMAccess 指令碼搭配︰

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```

## <a name="next-steps"></a>後續步驟
使用 Azure VMAccess 擴充功能來更新 Linux，是一種對執行中 Linux VM 進行變更的方法。 您也可以使用 cloud-init 和 Azure Resource Manager 範本之類的工具，在開機時修改您的 Linux VM。

[適用於 Linux 的虛擬機器擴充功能和功能](extensions-features.md)

[使用 Linux VM 擴充功能編寫 Azure Resource Manager 範本](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[在建立期間使用 cloud-init 自訂 Linux VM](using-cloud-init.md)


