---
title: "使用 Azure CLI 1.0 上傳自訂 Linux 映像 | Microsoft Docs"
description: "使用 Resource Manager 部署模型與 Azure CLI 1.0 搭配自訂 Linux 映像，來建立虛擬硬碟 (VHD) 並上傳至 Azure。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: iainfou
ms.openlocfilehash: ca4c6cb9296028275b2b032af0c94baabeec1223
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-image-by-using-the-azure-cli-10"></a>使用 Azure CLI 1.0 從自訂磁碟映像上傳並建立 Linux VM
本文說明如何使用 Resource Manager 部署模型，將虛擬硬碟 (VHD) 上傳至 Azure，並從這個自訂映像建立 Linux VM。 這項功能可讓您安裝和設定 Linux 散發版本以符合您的需求，然後使用該 VHD 快速建立 Azure 虛擬機器 (VM)。


## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本
您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0](#quick-commands) – 適用於傳統和資源管理部署模型的 CLI (本文章)
- [Azure CLI 2.0](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 適用於資源管理部署模型的新一代 CLI


## <a name="quick-commands"></a>快速命令
如果您需要快速完成工作，下列章節詳細說明上傳 VM 至 Azure 的基本命令。 每個步驟的詳細資訊和內容可在文件其他地方找到，[從這裡開始](#requirements)。

確定您已登入 [Azure CLI 1.0](../../cli-install-nodejs.md)，並且使用的是 Resource Manager 模式：

```azurecli
azure config mode arm
```

在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含 `myResourceGroup`、`mystorageaccount` 和 `myimages`。

首先，建立資源群組。 下列範例會在 `WestUs` 位置建立名為 `myResourceGroup` 的資源群組：

```azurecli
azure group create myResourceGroup --location "WestUS"
```

建立的儲存體帳戶以放置虛擬磁碟。 下列範例會建立名為 `mystorageaccount` 的儲存體帳戶：

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

列出儲存體帳戶的存取金鑰。 記下 `key1`：

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

使用取得的儲存體金鑰在您的儲存體帳戶內建立容器。 下列範例會使用來自 `key1` 的儲存體金鑰值，建立名為 `myimages` 的容器：

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

最後，將您的 VHD 上傳至您所建立的容器。 在 `/path/to/disk/mydisk.vhd` 下方為您的 VHD 指定本機路徑：

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

您現在可以 [使用 Resource Manager 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd)，從上傳的虛擬磁碟建立 VM。 您也可以藉由指定磁碟 (`--image-urn`) 的 URI 來使用 CLI。 下列範例會使用先前上傳的虛擬磁碟來建立名為 `myVM` 的 VM：

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
```

目的地儲存體帳戶必須與您上傳虛擬磁碟的目的地帳戶相同。 您也需要指定或依據提示回答 `azure vm create` 命令所需的所有其他參數，例如虛擬網路、公用 IP 位址、使用者名稱及 SSH 金鑰。 您可以深入了解[可用的 CLI Resource Manager 參數](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines)。

## <a name="requirements"></a>需求
若要完成下列步驟，您需要：

* **以 .vhd 檔案安裝的 Linux 作業系統** - 以 VHD 格式將 [Azure 背書的 Linux 散發套件](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (或參閱[非背書散發套件的資訊](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) 安裝到虛擬磁碟。 有多項工具可用來建立 VM 和 VHD：
  * 安裝和設定 [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) 或 [KVM](http://www.linux-kvm.org/page/RunningKVM)，並小心使用 VHD 做為您的映像格式。 如有需要，您可以使用 `qemu-img convert` 來[轉換映像](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats)。
  * 您也可以在 [Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) 或 [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx) 上使用 Hyper-V。

> [!NOTE]
> Azure 不支援較新的 VHDX 格式。 當您建立 VM 時，請指定 VHD 做為格式。 如有需要，您可以使用 [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) 或 [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) PowerShell Cmdlet 將 VHDX 磁碟轉換為 VHD。 此外，Azure 不支援上傳動態 VHD，因此您必須將此類磁碟轉換成靜態 VHD 再上傳。 您可以在上傳至 Azure 的期間使用 [適用於 GO 的 Azure VHD 公用程式](https://github.com/Microsoft/azure-vhd-utils-for-go) 之類的工具來轉換動態磁碟。
> 
> 

* 從自訂映像建立的 VM 必須位於與映像本身相同的儲存體帳戶中
  * 建立儲存體帳戶和容器來存放您的自訂映像和所建立的 VM
  * 建立所有 VM 之後，您即可放心地刪除您的映像

確定您已登入 [Azure CLI 1.0](../../cli-install-nodejs.md)，並且使用的是 Resource Manager 模式：

```azurecli
azure config mode arm
```

在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含 `myResourceGroup`、`mystorageaccount` 和 `myimages`。

<a id="prepimage"> </a>

## <a name="prepare-the-image-to-be-uploaded"></a>備妥要上傳的映像
Azure 支援各種 Linux 散發套件 (請參閱 [背書的散發套件](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))。 下列文章會逐步引導您了解如何準備 Azure 上支援的各種 Linux 散發套件：

* **[CentOS 型散發套件](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES 和 openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[其他：非背書散發套件](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

如需有關為 Azure 準備 Linux 映像的更多一般秘訣，另請參閱 **[Linux 安裝注意事項](create-upload-generic.md#general-linux-installation-notes)**。

> [!NOTE]
> 只有在搭配[經 Azure 背書之 Linux 散發套件](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中＜支援的版本＞下指定的組態詳細資料來使用其中一個經背書的散發套件時，[Azure 平台 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 才適用於執行 Linux 的虛擬機器。


## <a name="create-a-resource-group"></a>建立資源群組
資源群組會以邏輯方式將所有 Azure 資源 (例如虛擬網路和儲存體) 結合在一起以支援您的虛擬機器。 從 [這裡](../../azure-resource-manager/resource-group-overview.md)深入了解 Azure 資源群組。 在上傳您的自訂磁碟映像並建立 VM 之前，您必須先建立資源群組。 

下列範例會在 `WestUS` 位置建立名為 `myResourceGroup` 的資源群組：

```azurecli
azure group create myResourceGroup --location "WestUS"
```

## <a name="create-a-storage-account"></a>建立儲存體帳戶
VM 會以分頁 Blob 的形式儲存在儲存體帳戶內。 從[這裡](../../storage/common/storage-introduction.md#blob-storage)深入了解 Azure Blob 儲存體。 您要為自訂磁碟映像和 VM 建立儲存體帳戶。 您從自訂磁碟映像建立的所有 VM 都必須位於與該映像相同的儲存體帳戶中。

下列範例會在先前建立的資源群組中建立名為 `mystorageaccount` 的儲存體帳戶：

```azurecli
azure storage account create mystorageaccount --resource-group myResourceGroup \
    --location "WestUS" --kind Storage --sku-name PLRS
```

## <a name="list-storage-account-keys"></a>列出儲存體帳戶金鑰
Azure 會為每個儲存體帳戶產生兩個 512 位元的存取金鑰。 對儲存體帳戶進行驗證時 (例如為了執行寫入作業)，就會使用這些存取金鑰。 從 [這裡](../../storage/common/storage-create-storage-account.md#manage-your-storage-account)深入了解如何管理對儲存體的存取。 您可以使用 `azure storage account keys list` 命令來檢視存取金鑰。

檢視您所建立儲存體帳戶的存取金鑰：

```azurecli
azure storage account keys list mystorageaccount --resource-group myResourceGroup
```

輸出如下：

```azurecli
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK

```
請記下 `key1` ，因為在接下來的步驟中，您將使用它來與您的儲存體帳戶互動。

## <a name="create-a-storage-container"></a>建立儲存體容器
您可以在儲存體帳戶內建立容器來組織您的虛擬磁碟和映像，方法與您建立不同目錄來以邏輯方式組織本機檔案系統相同。 儲存體帳戶可以包含任意數目的容器。 

下列範例會建立一個名為 `myimages` 的容器，其中指定前一個步驟中取得的存取金鑰 (`key1`)：

```azurecli
azure storage container create --account-name mystorageaccount \
    --account-key key1 --container myimages
```

## <a name="upload-vhd"></a>上傳 VHD
現在您可以實際上傳您的自訂磁碟映像。 與 VM 使用的所有虛擬磁碟相同，您會以分頁 Blob 的形式上傳及儲存您的自訂磁碟映像。

指定您的存取金鑰、您在上一個步驟中建立的容器，然後指定您本機電腦上自訂磁碟映像的路徑：

```azurecli
azure storage blob upload --blobtype page --account-name mystorageaccount \
    --account-key key1 --container myimages /path/to/disk/mydisk.vhd
```

## <a name="create-vm-from-custom-image"></a>從自訂映像建立 VM
當您從自訂磁碟映像建立 VM 時，請指定磁碟映像的 URI。 請確保目的地儲存體帳戶與儲存您自訂磁碟映像的儲存體帳戶相符。 您可以使用 Azure CLI 或 Resource Manager JSON 範本來建立 VM。

### <a name="create-a-vm-using-the-azure-cli"></a>使用 Azure CLI 來建立 VM
您需搭配 `azure vm create` 命令指定 `--image-urn` 參數，以指向您的自訂磁碟映像。 請確保 `--storage-account-name` 與儲存您自訂磁碟映像的儲存體帳戶相符。 您不需使用與自訂磁碟映像相同的容器來儲存您的 VM。 上傳您的自訂磁碟映像之前，請確定會使用與先前步驟中相同的方式來建立任何額外的容器。

下列範例會從您的自訂磁碟映像建立名為 `myVM` 的 VM：

```azurecli
azure vm create myVM -l "WestUS" --resource-group myResourceGroup \
    --image-urn https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd
    --storage-account-name mystorageaccount
```

您仍需指定或依據提示回答 `azure vm create` 命令所需的所有其他參數，例如虛擬網路、公用 IP 位址、使用者名稱及 SSH 金鑰。 深入了解 [可用的 CLI Resource Manager 參數](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines)。

### <a name="create-a-vm-using-a-json-template"></a>使用 JSON 範本來建立 VM
Azure Resource Manager 範本是「JavaScript 物件標記法」(JSON) 檔案，定義了您想要建置的環境。 範本會細分成不同的資源提供者，例如計算或網路。 您可以使用現有的範本或自行撰寫範本。 深入了解如何 [使用 Resource Manager 和範本](../../azure-resource-manager/resource-group-overview.md)。

在範本的 `Microsoft.Compute/virtualMachines` 提供者內，您會有一個包含 VM 組態詳細資料的 `storageProfile` 節點。 兩個要編輯的主要參數為 `image` 和 `vhd` URI，分別指向您的自訂磁碟映像和新 VM 的虛擬磁碟。 以下顯示使用自訂磁碟映像的 JSON 範例：

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/myimages/mydisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

您可以使用[這個現有的範本以從自訂映像建立 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) 或閱讀[建立您自己的 Azure Resource Manager 範本](../../azure-resource-manager/resource-group-authoring-templates.md)。 

設定完範本之後，您需使用 `azure group deployment create` 命令來建立 VM。 請使用 `--template-uri` 參數來指定您 JSON 範本的 URI︰

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-uri https://uri.to.template/mytemplate.json
```

如果您已在電腦本機儲存 JSON 檔案，則可以改用 `--template-file` 參數︰

```azurecli
azure group deployment create --resource-group myResourceGroup
    --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>後續步驟
在您備妥並上傳自訂虛擬磁碟之後，您可以深入了解如何 [使用 Resource Manager 和範本](../../azure-resource-manager/resource-group-overview.md)。 您也可以 [將資料磁碟新增](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 至您的新 VM。 如果您需要存取在您的 VM 上執行的應用程式，請務必 [開啟連接埠和端點](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

