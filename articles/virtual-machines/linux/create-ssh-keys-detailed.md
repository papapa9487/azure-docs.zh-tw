---
title: "在 Azure 上建立 Linux VM 之 SSH 金鑰組的詳細步驟 | Microsoft Docs"
description: "了解其他步驟，以在 Azure 中為 Linux VM 建立 SSH 公開和私密金鑰組，以及為不同使用案例建立特定憑證。"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 6/28/2017
ms.author: danlep
ms.openlocfilehash: d4548c6f21d04effd57ea36e4fc0d15f77568903
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="detailed-walk-through-to-create-an-ssh-key-pair-and-additional-certificates-for-a-linux-vm-in-azure"></a>在 Azure 中為 Linux VM 建立 SSH 金鑰組和其他憑證的詳細逐步解說
您可以利用 SSH 金鑰組，在預設使用 SSH 金鑰進行驗證的 Azure 上建立虛擬機器，進而免除登入密碼的需求。 密碼有可能被猜到，讓您的 VM 遭到持續不斷的暴力密碼破解嘗試來猜測您的密碼。 使用 Azure CLI 或 Resource Manager 範本建立的 VM，可以將 SSH 公開金鑰納入部署中，進而免於對 SSH 停用密碼登入進行後置部署設定。 針對用於 Linux 虛擬機器之類的憑證，本文提供產生憑證的詳細步驟和其他範例。 如果您想要快速建立和使用 SSH 金鑰組，請參閱[如何在 Azure 中建立 Linux VM 的 SSH 公開和私密金鑰組](mac-create-ssh-keys.md)。

## <a name="understanding-ssh-keys"></a>了解 SSH 金鑰

想要登入 Linux 伺服器，最簡單的方式是使用 SSH 公開和私密金鑰。 [公開金鑰密碼編譯](https://en.wikipedia.org/wiki/Public-key_cryptography) 會安全得多，因為密碼非常容易遭到暴力破解。

公開金鑰可以與任何人共用；但只有您 (或您的本機安全性基礎結構) 會擁有私密金鑰。  SSH 私密金鑰應該有[非常安全的密碼](https://www.xkcd.com/936/) (來源︰[xkcd.com](https://xkcd.com)) 來保護它。  此密碼只能用於存取 SSH 私密金鑰檔，而**不是**使用者帳戶密碼。  當您將密碼新增至 SSH 金鑰時，便會使用 128位元的 AES 來加密私密金鑰，而未利用密碼解密的私密金鑰沒有用處。  如果攻擊者竊取您的私密金鑰，而且該金鑰沒有密碼，他們就能使用該私密金鑰來登入有對應公開金鑰的伺服器。  如果私密金鑰受密碼保護，攻擊者就無法使用該金鑰，進而為您在 Azure 上的基礎結構提供額外的安全性。

本文會建立 SSH 通訊協定第 2 版的 RSA 公開和私密金鑰檔組合 (也稱為「ssh-rsa」金鑰)，這是使用 Azure Resource Manager 進行部署時的建議方式。 ssh-rsa  金鑰是在[入口網站](https://portal.azure.com)上進行傳統部署和 Resource Manager 部署時的必要項目。

## <a name="ssh-keys-use-and-benefits"></a>SSH 金鑰的使用和好處

Azure 需要至少 2048 位元、SSH 通訊協定第 2 版 RSA 格式的公開和私密金鑰；公開金鑰檔具有 `.pub` 容器格式  若要建立金鑰，請使用 `ssh-keygen`，在詢問一系列問題後，它便會編寫私密金鑰和對應的公開金鑰。 建立 Azure VM 時，Azure 會將公開金鑰複製到 VM 上的 `~/.ssh/authorized_keys` 資料夾。 `~/.ssh/authorized_keys` 中的 SSH 金鑰用於挑戰用戶端，以符合 SSH 登入連線上的對應私密金鑰。  使用用於驗證的 SSH 金鑰進行建立 Azure Linux VM 時，Azure 會將 SSHD 伺服器設定為不允許密碼登入，僅允許以 SSH 金鑰登入。  因此，建立具 SSH 金鑰的 Azure Linux VM，即可協助保護 VM 部署的安全，並免除在 **sshd_config** 檔中停用密碼的標準部署後設定步驟。

## <a name="using-ssh-keygen"></a>使用 ssh-keygen

此命令會使用 2048 位元 RSA 建立密碼保護 (加密) 的 SSH 金鑰組，並為其加上註解以供輕鬆識別。  

依預設，SSH 金鑰會保留在 `~/.ssh` 目錄中。  如果您沒有 `~/.ssh` 目錄，`ssh-keygen` 命令會使用正確的權限為您建立。

```bash
ssh-keygen \
    -t rsa \
    -b 2048 \
    -C "azureuser@myserver" \
    -f ~/.ssh/id_rsa \
    -N mypassword
```

*命令的說明*

`ssh-keygen` = 用來建立金鑰的程式

`-t rsa` = 要建立的金鑰類型 (採用 RSA 格式) [wikipedia][parens at end](`https://en.wikipedia.org/wiki/RSA_(cryptosystem) `)
`-b 2048` = 金鑰的位元

`-C "azureuser@myserver"` = 附加至公開金鑰檔案結尾以便輕鬆識別的註解。  通常會以一封電子郵件做為註解，但您可以使用任何最適合您的基礎結構的事物。

## <a name="classic-deploy-using-asm"></a>使用 `asm` 進行傳統部署

如果您使用傳統部署模型 (CLI 中的 `asm` 模式)，則可以在 pem 容器中使用 SSH-RSA 公開金鑰或 RFC4716 格式的金鑰。  SSH-RSA 公開金鑰是稍早在本文中使用 `ssh-keygen` 建立的金鑰。

若要從現有的 SSH 公開金鑰建立 RFC4716 格式的金鑰︰

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>ssh-keygen 的範例

```bash
ssh-keygen -t rsa -b 2048 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 azureuser@myserver
The keys randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

已儲存金鑰檔案：

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

本文中的金鑰組名稱。  預設會有名為 **id_rsa** 的金鑰組，而且有些工具可能會預期私密金鑰的檔案名稱為 **id_rsa**，所以最好有此金鑰組。 `~/.ssh/` 目錄是 SSH 金鑰組和 SSH 組態檔的預設位置。  如果未指定完整路徑，則 `ssh-keygen` 會在目前的工作目錄 (而不是預設 `~/.ssh`) 中建立金鑰。

`~/.ssh` 。

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

金鑰密碼︰

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` 將私密金鑰檔案的密碼稱為「複雜密碼」。  「強烈」建議為私密金鑰加上密碼。 若沒有使用密碼來保護金鑰檔，任何人只要擁有該檔案，就可以用它登入具有對應公開金鑰的任何伺服器。 新增密碼 (複雜密碼) 可提升防護能力，以防有人能夠取得您的私密金鑰檔案，讓您有時間變更用來對您進行驗證的金鑰。

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>使用 ssh-agent 來儲存您的私密金鑰密碼

若要避免在每次 SSH 登入時輸入私密金鑰檔案密碼，您可以使用 `ssh-agent` 來快取您的私密金鑰檔案密碼。 如果您使用 Mac，則 OSX 金鑰鏈會在您叫用 `ssh-agent`時安全地儲存私密金鑰密碼。

確認並使用 ssh-agent 和 ssh-add 將金鑰檔案通知 SSH 系統，才不需要以互動方式使用複雜密碼。

```bash
eval "$(ssh-agent -s)"
```

現在使用 `ssh-add` 命令將私密金鑰加入至 `ssh-agent`。

```bash
ssh-add ~/.ssh/id_rsa
```

私密金鑰密碼現已儲存在 `ssh-agent` 中。

## <a name="using-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>使用 `ssh-copy-id` 將金鑰複製到現有 VM
如果您已經建立 VM，您可以將新的 SSH 公開金鑰安裝至 Linux VM：

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub ahmet@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>建立和設定 SSH 組態檔

建議的最佳作法是建立及設定 `~/.ssh/config` 檔案來加速登入，並且最佳化您的 SSH 用戶端行為。

下列範例將示範標準組態。

### <a name="create-the-file"></a>建立檔案

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>編輯檔案以加入新的 SSH 組態：

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>範例 `~/.ssh/config` 檔案︰

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

此 SSH 組態可讓您將各個伺服器分段，讓它們各自擁有專用的金鑰組。 預設設定 (`Host *`) 適用於不符合組態檔中任何提升之特定主機的任何主機。

### <a name="config-file-explained"></a>組態檔的說明

`Host` = 在終端機上所呼叫的主機名稱。  `ssh fedora22` 會指示 `SSH` 使用設定區塊中標示為 `Host fedora22` 的值。注意︰這可以是符合您用途的任何標籤，並不代表任何伺服器的實際主機名稱。

`Hostname 102.160.203.241` = 所存取伺服器的 IP 位址或 DNS 名稱。

`User ahmet` = 登入伺服器時要使用的遠端使用者帳戶。

`PubKeyAuthentication yes` = 向 SSH 指出您想要使用 SSH 金鑰來登入。

`IdentityFile /home/ahmet/.ssh/id_id_rsa` = 要用於驗證的 SSH 私密金鑰和對應公開金鑰。

## <a name="ssh-into-linux-without-a-password"></a>使用 SSH 登入 Linux 而不提供密碼

您現在已擁有 SSH 金鑰組和設定好的 SSH 組態檔，因此能夠快速且安全地登入 Linux VM。 第一次使用 SSH 金鑰登入伺服器時，命令會提示您輸入該金鑰檔案的複雜密碼。

```bash
ssh fedora22
```

### <a name="command-explained"></a>命令的說明

在執行 `ssh fedora22` 後，SSH 會先從 `Host fedora22` 區塊中找出所有設定並加以載入，再從最後一個區塊 `Host *` 中載入所有其餘設定。

## <a name="next-steps"></a>後續步驟

接下來是使用新的 SSH 公開金鑰建立 Azure Linux VM。  相較於以預設登入方法密碼建立的 VM，以 SSH 公開金鑰作為登入所建立的 Azure VM 比較安全。  使用 SSH 金鑰建立的 Azure VM 預設會設定為停用密碼，避免遭到暴力猜測嘗試。

* [使用 Azure 範本建立安全的 Linux VM](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 Azure 入口網站建立安全的 Linux VM](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 Azure CLI 建立安全的 Linux VM](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
