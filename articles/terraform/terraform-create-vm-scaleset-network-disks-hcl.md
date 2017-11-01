---
title: "使用 Terraform 並利用 HCL 來建立 Azure VM 擴展集。"
description: "使用 Terraform 設定以虛擬網路和受管理的已連接磁碟完成的 Azure 虛擬機器擴展集並設定版本。"
keywords: "Terraform, DevOps, 擴展集, 虛擬機器, 網路, 儲存體, 模組"
ms.service: virtual-machines-linux
author: dcaro
ms.author: dcaro
ms.date: 10/04/2017
ms.topic: article
ms.openlocfilehash: 7a4e21d547b3d2b2399f9f68b1babd9f82a421b7
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="use-terraform-to-plan-and-create-a-networked-azure-vm-scale-set-with-managed-storage"></a>使用 Terraform 來規劃並建立具有受管理儲存體的網路型 Azure VM 擴展集

在本文中，您將使用 [Terraform](https://www.terraform.io/) \(英文\)，並利用 [Hashicorp 設定語言](https://www.terraform.io/docs/configuration/syntax.html) \(HCL\) \(英文\)，以建立及部署含受管理磁碟的 [Azure 虛擬機器擴展集](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview)。  

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 設定 Terraform 部署
> * 針對 Terraform 部署使用變數和輸出 
> * 建立和部署網路基礎結構
> * 建立和部署虛擬機器擴展集，並將它連接到網路
> * 建立和部署 Jumpbox 以透過 SSH 連線到 VM

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="before-you-begin"></a>開始之前

- [安裝 Terraform 並設定對 Azure 的存取權限](/azure/virtual-machines/linux/terraform-install-configure)
- [建立 SSH 金鑰組](/azure/virtual-machines/linux/mac-create-ssh-keys) (如果您還沒有金鑰組的話)。

## <a name="create-the-file-structure"></a>建立檔案結構

在空白目錄中建立使用下列三個名稱的新檔案：

- `variables.tf`：此檔案會保留範本中所使用的變數值。
- `output.tf`：此檔案用來描述將在部署後顯示的設定。
- `vmss.tf`：這是虛擬機器擴展集基礎結構的程式碼。

## <a name="create-the-variables-and-output-definitions"></a>建立變數和輸出定義

在此步驟中，您要定義可自訂 Terraform 所建立資源的變數。

編輯 `variables.tf` 檔案，複製下列程式碼，然後儲存變更。

```tf 
variable "location" {
  description = "The location where resources will be created"
  default     = "West US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources will be created"
  default     = "myResourceGroup"
}
```

編輯 `output.tf` 檔案並複製下列程式碼，以公開虛擬機器的完整網域名稱。 

```hcl 
output "vmss_public_ip" {
    value = "${azurerm_public_ip.vmss.fqdn}"
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>在範本中定義網路基礎結構

在此步驟中，您要在新的 Azure 資源群組中建立下列網路基礎結構： 

  - 一個具有 10.0.0.0/16 位址空間的 VNET 
  - 一個具有 10.0.2.0/24 位址空間的子網路
  - 兩個公用 IP 位址。 其中一個是用於虛擬機器擴展集負載平衡器，另一個用於連線到 SSH Jubmox。


編輯並複製 `vmss.tf` 中的下列程式碼： 

```tf
resource "azurerm_resource_group" "vmss" {
  name     = "${var.resource_group_name}"
  location = "${var.location}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = "${azurerm_resource_group.vmss.name}"
  virtual_network_name = "${azurerm_virtual_network.vmss.name}"
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> 建議標記已部署到 Azure 中的資源，以方便日後識別。

## <a name="create-the-network-infrastructure"></a>建立網路基礎結構

在建立 `.tf` 檔案的目錄中執行下列命令，以初始化 Terraform 環境：

```bash
terraform init 
```

接著執行下列命令，以在 Azure 中部署基礎結構。

```bash
terraform apply
```

確認公用 IP 位址的 FQDN 對應至您的設定：![公用 IP 位址的 VMSS TerraForm FQDN](./media/tf-create-vmss-step4-fqdn.png)


資源群組應具有下列資源：![VMSS Terraform 網路資源](./media/tf-create-vmss-step4-rg.png)


## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>編輯基礎結構以新增虛擬機器擴展集

在此步驟中，您會將下列資源新增到範本：

- 一個 Azure 負載平衡器和服務應用程式的規則，並且將負載平衡器連接到稍早設定的公用 IP 位址。
- Azure 後端位址集區，並將它指派給負載平衡器 
- 應用程式所使用且在負載平衡器上設定的健康情況探查連接埠 
- 位在負載平衡器幕後的虛擬機器擴展集，在稍早部署的 VNet 上執行
- 虛擬機器擴展集節點上的 [Nginx](http://nginx.org/) \(英文\)，會使用自訂指令碼延伸模組。

在 `vmss.tf` 檔案的結尾加入下列程式碼。

```tf
resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = "${azurerm_public_ip.vmss.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "ssh-running-probe"
  port                = "${var.application_port}"
}

resource "azurerm_lb_rule" "lbnatrule" {
    resource_group_name            = "${azurerm_resource_group.vmss.name}"
    loadbalancer_id                = "${azurerm_lb.vmss.id}"
    name                           = "http"
    protocol                       = "Tcp"
    frontend_port                  = "${var.application_port}"
    backend_port                   = "${var.application_port}"
    backend_address_pool_id        = "${azurerm_lb_backend_address_pool.bpepool.id}"
    frontend_ip_configuration_name = "PublicIPAddress"
    probe_id                       = "${azurerm_lb_probe.vmss.id}"
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_profile_os_disk {
    name              = ""
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_profile_data_disk {
    lun          = 0
    caching        = "ReadWrite"
    create_option  = "Empty"
    disk_size_gb   = 10
  }

  os_profile {
    computer_name_prefix = "vmlab"
    admin_username       = "azureuser"
    admin_password       = "Passwword1234"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = "${azurerm_subnet.vmss.id}"
      load_balancer_backend_address_pool_ids = ["${azurerm_lb_backend_address_pool.bpepool.id}"]
    }
  }

  extension { 
    name = "vmssextension"
    publisher = "Microsoft.OSTCExtensions"
    type = "CustomScriptForLinux"
    type_handler_version = "1.2"
    settings = <<SETTINGS
    {
        "commandToExecute": "sudo apt-get -y install nginx"
    }
    SETTINGS
  }

  tags {
    environment = "codelab"
  }
}
```

將下列程式碼加入 `variables.tf` 來自訂部署：

```tf 
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>在 Azure 中部署虛擬機器擴展集

執行下列命令以視覺化虛擬機器擴展集部署：

```bash
terraform plan
```

命令的輸出看起來應該如下所示。
![Terraform 新增 VMSS 計畫](./media/tf-create-vmss-step6-plan884d3aefd9708a711bc09a66e85eb149c23a3ccff959655ec00418168b2bd481.png)

接著在 Azure 中部署其他的資源： 

```bash
terraform apply 
```

資源群組的內容看起來應該像這樣：

![Terraform VM 擴展集資源群組](./media/tf-create-vmss-step6-apply.png)

開啟瀏覽器並連線到命令傳回的 FQDN。 

## <a name="add-an-ssh-jumpbox-to-the-existing-network"></a>將 SSH Jumpbox 新增到現有的網路 

在此步驟中，您要設定下列資源：
- 連線到與虛擬機器擴展集相同子網路的網路介面。
- 使用此網路介面連線的虛擬機器。 此 'jumpbox' 可從遠端存取。 一旦連線之後，您就可以透過 SSH 連線到擴展集中的任何虛擬機器。



在 `vmss.tf` 檔案的結尾加入下列程式碼：

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  public_ip_address_allocation = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = "${azurerm_subnet.vmss.id}"
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = "${azurerm_public_ip.jumpbox.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = "${var.location}"
  resource_group_name   = "${azurerm_resource_group.vmss.name}"
  network_interface_ids = ["${azurerm_network_interface.jumpbox.id}"]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "jumpbox-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  os_profile {
    computer_name  = "jumpbox"
    admin_username = "azureuser"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  tags {
    environment = "codelab"
  }
}
```

編輯 `outputs.tf` 並加入下列程式碼，以在部署完成時顯示 Jumpbox 的主機名稱：

```
output "jumpbox_public_ip" {
    value = "${azurerm_public_ip.jumpbox.fqdn}"
}
```

## <a name="deploy-the-jumpbox"></a>部署 Jumpbox

部署 Jumpbox。

```bash
terraform apply 
```

部署完成之後，資源群組的內容看起來像這樣：

![Terraform VM 擴展集資源群組](./media/tf-create-create-vmss-step8.png)

> [!NOTE]
> Jumpbox 和您所部署虛擬機器擴展集已停用使用密碼登入的功能。 請使用 SSH 登入來存取 VM。

## <a name="clean-up-the-environment"></a>清理環境

下列命令會刪除此教學課程中建立的資源：

```bash
terraform destroy
```

系統要求確認是否刪除資源時，輸入 `yes`。 解構程序需要幾分鐘的時間才能完成。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用 Terraform 在 Azure 上部署了虛擬機器擴展集。 您已了解如何︰

> [!div class="checklist"]
> * 初始化 Terraform 部署
> * 針對 Terraform 部署使用變數和輸出 
> * 建立和部署網路基礎結構
> * 建立和部署虛擬機器擴展集，並將它連接到現有的環境
> * 建立和部署 Jumpbox 以透過 SSH 連線到 VM 
