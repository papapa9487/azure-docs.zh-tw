---
title: "擷取 Azure Linux VM 作為範本使用 | Microsoft Docs"
description: "了解如何擷取以 Azure Resource Manager 部署模型建立的以 Linux 為基礎之 Azure 虛擬機器 (VM) 的映像，並將它一般化。"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: b1164fbd816eea5189786850f096438e32f8f802
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>擷取在 Azure 上執行的 Linux 虛擬機器
請遵循本文中的步驟，在 Resource Manager 部署模型中一般化和擷取 Azure Linux 虛擬機器 (VM)。 當您一般化 VM 時，需移除個人帳戶資訊，並準備要做為映像的 VM。 您接著擷取作業系統的一般化虛擬硬碟 (VHD) 映像、連接資料磁碟的 VHD 以及新 VM 部署的 [Resource Manager 範本](../../azure-resource-manager/resource-group-overview.md)。 本文詳細說明如何針對使用非受控磁碟的 VM，透過 Azure CLI 1.0 擷取 VM 映像。 您也可以[透過 Azure CLI 2.0 使用 Azure 受控磁碟擷取 VM](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 受控磁碟是由 Azure 平台處理，不需要任何準備或位置來儲存它們。 如需詳細資訊，請參閱 [Azure 受控磁碟概觀](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 

若要使用映像建立 VM、針對每個新的 VM 設定網路資源，並使用範本 (JavaScript 物件標記法 (亦稱為 JSON) 檔案) 從擷取的 VHD 映像部署它。 如此一來，您可以使用 VM 目前軟體的組態來複寫 VM，與您在 Azure Marketplace 中使用映像的方式類似。

> [!TIP]
> 如果您想要建立一份您現有 Linux VM 的複本，當中包含其特殊的備份或偵錯狀態，請參閱[建立在 Azure 上執行的 Linux 虛擬機器複本](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 如果您想要從內部部署 VM 上傳 Linux VHD，請參閱[上傳自訂磁碟映像並從這個映像建立 Linux VM](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。  

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本
您可以使用下列其中一個 CLI 版本來完成工作︰

- [Azure CLI 1.0](#before-you-begin) – 適用於傳統和資源管理部署模型的 CLI (本文章)
- [Azure CLI 2.0](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 適用於資源管理部署模型的新一代 CLI

## <a name="before-you-begin"></a>開始之前
請確保符合下列必要條件︰

* **在 Resource Manager 部署模型中建立的 Azure VM** - 如果您尚未建立 Linux VM，可以使用[入口網站](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、[Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 [Resource Manager 範本](create-ssh-secured-vm-from-template.md)。 
  
    視需要設定 VM。 例如，[新增資料磁碟](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、套用更新，並安裝應用程式。 
* **Azure CLI** - 在本機電腦上安裝 [Azure CLI](../../cli-install-nodejs.md)。

## <a name="step-1-remove-the-azure-linux-agent"></a>步驟 1：移除 Azure Linux 代理程式
首先，在 Linux VM 上執行 **waagent** 命令並搭配 **deprovision**參數。 此命令會刪除檔案與資料，使 VM 準備好進行一般化。 如需詳細資訊，請參閱 [Azure Linux 代理程式使用者指南](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

1. 使用 SSH 用戶端連線到 Linux VM。
2. 在 SSH 視窗中，輸入下列命令：
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > 只在您想要擷取作為映像的 VM 上執行這個命令。 這不能保證映像檔中的所有機密資訊都會清除完畢或適合轉散發。
 
3. 輸入 **y** 繼續。 您可以加入 **-force** 參數，便不用進行此確認步驟。
4. 在命令完成之後，請輸入 **exit**。 此步驟會關閉 SSH 用戶端。

## <a name="step-2-capture-the-vm"></a>步驟 2：擷取 VM
使用 Azure CLI 來一般化和擷取 VM。 在下列範例中，請以您自己的值取代範例參數名稱。 範例參數名稱包含 **myResourceGroup**、**myVnet** 和 **myVM**。

1. 從本機電腦，開啟 Azure CLI 並[登入您的 Azure 訂用帳戶](../../xplat-cli-connect.md)。 
2. 確定您處於 Resource Manager 模式。
   
    ```azurecli
    azure config mode arm
    ```
3. 使用下列命令，關閉您已經解除佈建的 VM：
   
    ```azurecli
    azure vm deallocate -g myResourceGroup -n myVM
    ```
4. 使用下列命令將 VM 一般化：
   
    ```azurecli
    azure vm generalize -g myResourceGroup -n myVM
    ```
5. 現在執行 **azure vm capture** 命令，它會擷取 VM。 在下列範例中，會使用開頭為 **MyVHDNamePrefix** 的名稱來擷取映像 VHD，而 **-t** 選項會指定範本 **MyTemplate.json** 的路徑。 
   
    ```azurecli
    azure vm capture -g myResourceGroup -n myVM -p myVHDNamePrefix -t myTemplate.json
    ```
   
   > [!IMPORTANT]
   > 在原始 VM 使用的相同儲存體帳戶中預設會建立映像 VHD 檔案。 使用相同儲存體帳戶來儲存從映像建立之所有新 VM 的 VHD。 

6. 若要尋找擷取之映像的位置，請在文字編輯器中開啟 JSON 範本。 在 **storageProfile** 中，尋找位於 **system** 容器的**映像**的 **uri**。 例如，OS 磁碟映像的 URI 類似於 `https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`

## <a name="step-3-create-a-vm-from-the-captured-image"></a>步驟 3：從擷取的映像建立 VM
現在使用具有範本的映像來建立 Linux VM。 下列步驟示範如何使用您擷取的 Azure CLI 和 JSON 檔案範本，在新的虛擬網路中建立 VM。

### <a name="create-network-resources"></a>建立網路資源
若要使用範本，您必須先為新的 VM 設定虛擬網路和 NIC。 建議您在儲存 VM 映像的位置中，為這些資源建立一個資源群組。 執行類似下列的命令，替換您的資源名稱和適當的 Azure 位置 (在這些命令列中為 "centralus")：

```azurecli
azure group create myResourceGroup1 -l "centralus"

azure network vnet create myResourceGroup1 myVnet -l "centralus"

azure network vnet subnet create myResourceGroup1 myVnet mySubnet

azure network public-ip create myResourceGroup1 myPublicIP -l "centralus"

azure network nic create myResourceGroup1 myNIC -k mySubnet -m myVnet -p myPublicIP -l "centralus"
```

### <a name="get-the-id-of-the-nic"></a>取得 NIC 的識別碼
若要使用您在擷取期間所儲存的 JSON，從映像部署 VM，您需要 NIC 的識別碼。 執行下列命令來取得識別碼：

```azurecli
azure network nic show myResourceGroup1 myNIC
```

輸出中的**識別碼**類似於 `/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic`

### <a name="create-a-vm"></a>建立 VM
現在執行下列命令，從擷取的 VM 映像建立 VM。 使用 **-f** 參數來指定您所儲存之範本 JSON 檔案的路徑。

```azurecli
azure group deployment create myResourceGroup1 MyDeployment -f MyTemplate.json
```

在命令輸出中，系統會提示您提供新的 VM 名稱、系統管理員使用者名稱和密碼，以及您先前建立的 NIC 識別碼。

```bash
info:    Executing command group deployment create
info:    Supply values for the following parameters
vmName: myNewVM
adminUserName: myAdminuser
adminPassword: ********
networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/myResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
```

下列範例會顯示您成功部署後所看到的內容︰

```bash
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment xxxxxxx
+ Waiting for deployment to complete
data:    DeploymentName     : MyDeployment
data:    ResourceGroupName  : MyResourceGroup1
data:    ProvisioningState  : Succeeded
data:    Timestamp          : xxxxxxx
data:    Mode               : Incremental
data:    Name                Type          Value

data:    ------------------  ------------  -------------------------------------

data:    vmName              String        myNewVM

data:    vmSize              String        Standard_D1

data:    adminUserName       String        myAdminuser

data:    adminPassword       SecureString  undefined

data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
info:    group deployment create command OK
```

### <a name="verify-the-deployment"></a>驗證部署
現在使用您建立的虛擬機器的 SSH 來驗證部署並開始使用新的 VM。 若要透過 SSH 連接，請尋找您藉由執行下列命令所建立的 VM 的 IP 位址：

```azurecli
azure network public-ip show myResourceGroup1 myPublicIP
```

公用 IP 位址會列在命令輸出中。 根據預設，您會經由連接埠 22 上的 SSH 連接到 Linux VM。

## <a name="create-additional-vms"></a>建立額外的 VM
依照前一節中的步驟，使用擷取的映像和範本來部署其他 VM。 從映像建立 VM 的其他選項包括使用快速入門範本，或執行 **azure vm create** 命令。

### <a name="use-the-captured-template"></a>使用擷取的範本
若要使用擷取的映像和範本，請遵循下列步驟 (上一節中詳細說明)︰

* 確保您的 VM 映像位於裝載 VM 之 VHD 的相同儲存體帳戶中。
* 複製範本 JSON 檔案，並指定新 VM 的 VHD (或 VHDs) 作業系統磁碟的唯一名稱。 例如，在 **storageProfile** 的 **vhd** 下，於 **uri** 中，指定 **osDisk** VHD 的唯一名稱，類似於 `https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`
* 在相同或不同的虛擬網路中建立 NIC。
* 使用修改過的範本 JSON 檔案，在您設定虛擬網路的資源群組中建立部署。

### <a name="use-a-quickstart-template"></a>使用快速入門範本
如果您要在從映像建立 VM 時自動設定網路，可以在範本中指定這些資源。 例如，請從 GitHub 參閱 [101-vm-from-user-image template](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)。 此範本會從自訂映像和必要的虛擬網路、公用 IP 位址和 NIC 資源建立 VM。 如需在 Azure 入口網站中使用範本的逐步解說，請參閱 [如何從使用 Resource Manager 範本的自訂映像建立虛擬機器](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/)。

### <a name="use-the-azure-vm-create-command"></a>使用 azure vm create 命令
通常最簡單的方式是使用 Resource Manager 範本從映像建立 VM。 不過，您可以使用 **azure vm create** 命令搭配 **-Q** (**--image-urn**) 參數，以*「命令方式」*建立 VM。 如果您使用此方法，您也要傳遞 **-d** (**--os-disk-vhd**) 參數來指定新 VM 的 OS .vhd 檔案位置。 此檔案必須位於儲存映像 VHD 檔案之儲存體帳戶的 vhds 容器中。 此命令會自動將新 VM 的 VHD 複製到 **vhds** 容器。

對映像執行 **azure vm create** 之前，請完成下列步驟：

1. 建立資源群組，或識別現有的資源群組以供部署。
2. 為新的 VM 建立公用 IP 位址資源和 NIC 資源。 如需使用 CLI 建立虛擬網路、公用 IP 位址及 NIC 的步驟，請參閱在本文中先前的說明。 (**azure vm create** 也可以建立 NIC，但您需要傳遞其他參數以取得虛擬網路和子網路。)

然後，執行將 URI 傳遞給新 OS VHD 檔案和現有映像的命令。 在此範例中，會在美國東部地區建立 Standard_A1 VM 大小。

```azurecli
azure vm create -g myResourceGroup1 -n myNewVM -l eastus -y Linux \
-z Standard_A1 -u myAdminname -p myPassword -f myNIC \
-d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" \
-Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.vhd"
```

如需其他命令選項，請執行 `azure help vm create`。

## <a name="next-steps"></a>後續步驟
若要使用 CIL 管理 VM，請參閱 [使用 Azure 資源管理員範本和 Azure CLI 部署和管理虛擬機器](create-ssh-secured-vm-from-template.md)中的工作。

