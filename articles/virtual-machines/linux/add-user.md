---
title: "對 Azure 上的 Linux VM 加入使用者 | Microsoft Docs"
description: "將使用者加入 Azure 上的 Linux VM。"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f8aa633b-8b75-45d7-b61d-11ac112cedd5
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/18/2016
ms.author: v-livech
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: ddc5446fa6a43bfff05ec251b30175ddd24052c9
ms.contentlocale: zh-tw
ms.lasthandoff: 08/09/2017

---
# <a name="add-a-user-to-an-azure-vm"></a>將使用者加入 Azure VM 中
在任何新 Linux VM 上的首要工作之一，就是建立新的使用者。  在本文中，我們會逐步建立 sudo 使用者帳戶、設定密碼、新增 SSH 公開金鑰，最後使用 `visudo` 來允許在沒有密碼的情況下使用 sudo。

必要條件︰[Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/)、[SSH 公開金鑰與私密金鑰](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、Azure 資源群組，且已安裝 Azure CLI 同時使用 `azure config mode arm` 切換至 Azure Resource Manager 模式。

## <a name="quick-commands"></a>快速命令
```bash
# Add a new user on RedHat family distros
sudo useradd -G wheel exampleUser

# Add a new user on Debian family distros
sudo useradd -G sudo exampleUser

# Set a password
sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully

# Copy the SSH Public Key to the new user
ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver

# Change sudoers to allow no password
# Execute visudo as root to edit the /etc/sudoers file
visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL

# Verify everything
# Verify the SSH keys & User account
bill@slackware$ ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
sudo top
```

## <a name="detailed-walkthrough"></a>詳細的逐步解說
### <a name="introduction"></a>簡介
新伺服器最常見的首要工作之一，就是新增使用者帳戶。  根登入應該一律停用，根帳戶本身不應與您的 Linux 伺服器搭配使用，只能與 sudo 搭配使用。  使用 sudo 來為使用者提供根擴大權限才是管理及使用 Linux 的正確方法。

我們會使用命令 `useradd` 來將使用者帳戶新增到 Linux VM。  執行 `useradd` 來修改 `/etc/passwd`、`/etc/shadow`、`/etc/group` 及 `/etc/gshadow`。  我們會在 `useradd` 命令中新增命令列旗標，以將新使用者一併新增到 Linux 上適當的 sudo 群組。  雖然 `useradd` 會在 `/etc/passwd` 中建立項目，但卻不會為新的使用者帳戶提供密碼。  我們會使用簡單的 `passwd` 命令，為新的使用者建立初始密碼。  最後一個步驟是修改 sudo 規則，以允許該使用者以 sudo 權限執行命令，而不必為每個命令輸入密碼。  使用私密金鑰登入時，我們會假設該使用者帳戶非意圖不良的執行者，因此會允許他不需密碼即可使用 sudo 存取。  

### <a name="adding-a-single-sudo-user-to-an-azure-vm"></a>將單一 sudo 使用者加入 Azure VM 中
使用 SSH 金鑰登入 Azure VM。  如果您尚未設定 SSH 公開金鑰存取權，請先閱讀完這篇文章： [在 Azure 使用公開金鑰驗證](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。  

`useradd` 命令會完成下列工作：

* 建立新的使用者帳戶
* 以相同的名稱建立新的使用者群組
* 在 `/etc/passwd`
* 在 `/etc/gpasswd`

`-G` 命令列旗標會將新的使用者帳戶新增到適當的 Linux 群組中，為該新使用者帳戶提供根擴大權限。

#### <a name="add-the-user"></a>加入使用者
```bash
# On RedHat family distros
sudo useradd -G wheel exampleUser

# On Debian family distros
sudo useradd -G sudo exampleUser
```

#### <a name="set-a-password"></a>設定密碼
`useradd` 命令會建立使用者，並將 `/etc/passwd` 和 `/etc/gpasswd` 中都新增項目，但不會實際設定密碼。  將密碼新增到項目中時是使用 `passwd` 命令。

```bash
sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```

我們在伺服器上已有具備 sudo 權限的使用者。

### <a name="add-your-ssh-public-key-to-the-new-user-account"></a>請將您的 SSH 公開金鑰加入新的使用者帳戶中
從您的電腦，使用 `ssh-copy-id` 命令搭配新密碼。

```bash
ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver
```

### <a name="using-visudo-to-allow-sudo-usage-without-a-password"></a>使用 visudo 以允許在沒有密碼的情況下使用 sudo
使用 `visudo` 來編輯 `/etc/sudoers` 檔案可增加幾層的保護，防止不正確地修改這個重要的檔案。  執行 `visudo` 時會鎖住 `/etc/sudoers` 檔案，以確保沒有任何其他使用者能在檔案處於編輯狀態時對檔案進行變更。  當您嘗試儲存或結束 `/etc/sudoers` 檔案時，`visudo` 也會檢查該檔案是否有錯誤，讓您儲存的不會是損毀的 sudoers 檔案。

我們在正確的預設群組中，已有使用者可進行 sudo 存取。  現在，我們將讓這些群組能夠在沒有密碼的情況下使用 sudo。

```bash
# Execute visudo as root to edit the /etc/sudoers file
visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL
```

### <a name="verify-the-user-ssh-keys-and-sudo"></a>驗證使用者、SSH 金鑰及 sudo
```bash
# Verify the SSH keys & User account
ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
sudo top
```

