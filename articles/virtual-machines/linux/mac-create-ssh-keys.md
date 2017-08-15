---
title: "在 Azure 中建立和使用 Linux VM 的 SSH 金鑰組 | Microsoft Docs"
description: "如何在 Azure 中建立和使用 Linux VM 的 SSH 公開和私密金鑰組，以改善驗證程序的安全性。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/14/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 49541306c66e066a9d9f75d90bed2f4dfd21ce9d
ms.contentlocale: zh-tw
ms.lasthandoff: 06/23/2017

---

# <a name="how-to-create-and-use-an-ssh-public-and-private-key-pair-for-linux-vms-in-azure"></a>如何在 Azure 中建立和使用 Linux VM 的 SSH 公開和私密金鑰組
您可以利用安全殼層 (SSH) 金鑰組，在 Azure 上建立使用 SSH 金鑰來進行驗證的虛擬機器 (VM)，進而免除登入密碼的需求。 本文說明如何快速產生和使用 Linux VM 的 SSH 通訊協定第 2 版 RSA 公開和私密金鑰檔案組。 如需詳細步驟和其他範例，請參閱[建立 SSH 金鑰組和憑證的詳細步驟](create-ssh-keys-detailed.md)。

## <a name="create-an-ssh-key-pair"></a>建立 SSH 金鑰組
使用 `ssh-keygen` 命令可建立會預設建立在 `~/.ssh` 目錄中的 SSH 公開和私密金鑰檔案，但您可以在出現提示時指定不同位置和額外的複雜密碼 (用來存取私密金鑰檔案的密碼)。 從 Bash 殼層執行下列命令，並輸入您自己的資訊來回應提示。

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="use-the-ssh-key-pair"></a>使用 SSH 金鑰組
您放置在 Azure 之 Linux VM 上的公開金鑰預設會儲存在 `~/.ssh/id_rsa.pub`，除非您在建立時變更了位置。 如果您使用 [Azure CLI 2.0](/cli/azure) 來建立 VM，請在搭配使用 [az vm create](/cli/azure/vm#create) 與 `--ssh-key-path` 選項時指定此公開金鑰的位置。 如果您在 Azure 入口網站或 Resource Manager 範本中複製和貼上要使用之公開金鑰檔案的內容，請確定您並未複製任何額外的空白字元。 例如，如果您使用 OS X，您可以透過管道將公開金鑰檔案 (預設為 **~/.ssh/id_rsa.pub**) 傳送至 **pbcopy** 以複製內容 (另有其他 Linux 程式可執行相同作業，例如 `xclip`)。

如果您不熟悉 SSH 公開金鑰，您可以如下所示執行 `cat`，並以您自己的公開金鑰檔案位置取代 `~/.ssh/id_rsa.pub`，來看到您的公開金鑰︰

```bash
cat ~/.ssh/id_rsa.pub
```

有了 Azure VM 上的公開金鑰後，使用 VM 的 IP 位址或 DNS 名稱透過 SSH 連線到 VM (請記得將下面的 `azureuser` 和 `myvm.westus.cloudapp.azure.com` 取代為系統管理員使用者名稱和完整網域名稱或 IP 位址)：

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

如果您在建立金鑰組時提供了複雜密碼，請在登入程序期間出現提示時輸入複雜密碼  (伺服器會新增至 `~/.ssh/known_hosts` 資料夾，而且系統不會要求您重新連線，除非 Azure VM 上的公開金鑰有變更或伺服器名稱已從 `~/.ssh/known_hosts` 中移除)。

## <a name="next-steps"></a>後續步驟

使用 SSH 金鑰建立的 VM 預設會設定為停用密碼，以大幅提高暴力猜測嘗試的成本，進而讓攻擊難以進行。 本主題描述如何建立簡單的 SSH 金鑰組以供快速使用。 如果您需要更多關於建立 SSH 金鑰組的協助，或需要其他憑證，請參閱[建立 SSH 金鑰組和憑證的詳細步驟](create-ssh-keys-detailed.md)。

您可以使用 Azure 入口網站、CLI 和範本建立使用 SSH 金鑰組的 VM：

* [使用 Azure 入口網站建立安全的 Linux VM](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 Azure CLI 2.0 建立安全的 Linux VM](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [使用 Azure 範本建立安全的 Linux VM](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

