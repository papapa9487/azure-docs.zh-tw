---
title: "使用 Terraform 模組，在 Azure 上建立負載平衡的 Linux VM 基礎結構"
description: "了解如何使用 Terraform 模組，在 Azure 中建立含有負載平衡器的 Linux 虛擬機器叢集"
keywords: "terraform, devops, 虛擬機器, 網路, 模組"
author: rloutlaw
manager: justhe
ms.service: virtual-machines-linux
ms.custom: devops
ms.topic: article
ms.date: 10/19/2017
ms.author: routlaw
ms.openlocfilehash: 86fee5446065ffa8b5e8186ba8d8f56aa3efc2ea
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="create-a-vm-cluster-with-terraform-using-custom-hcl"></a>使用自訂 HCL 與 Terraform 建立 VM 叢集

本教學課程示範如何使用 [Hashicorp 設定語言](https://www.terraform.io/docs/configuration/syntax.html) (HCL) 來建立小型計算叢集。 此設定會在[可用性設定組](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)中建立一個負載平衡器、兩個 Linux VM，以及所有必要的網路資源。

在本教學課程中，您將：

> [!div class="checklist"]
> * 使用 Azure 設定驗證
> * 建立 Terraform 範本
> * 使用 plan 視覺化變更
> * 套用設定以建立叢集

## <a name="set-up-authentication-with-azure"></a>使用 Azure 設定驗證

> [!TIP]
> 如果您[使用 Terraform 環境變數](/azure/virtual-machines/linux/terraform-install-configure#set-environment-variables)或在 [Azure Cloud Shell](/azure/cloud-shell/overview) 中執行本教學課程，請略過此步驟。

 檢閱[安裝 Terraform 並設定對 Azure 的存取權限](/azure/virtual-machines/linux/terraform-install-configure)，來建立 Azure 服務主體。 使用這個服務主體與下列程式碼，將新檔案 `azureProviderAndCreds.tf` 植入空的目錄中：

```tf
variable subscription_id {}
variable tenant_id {}
variable client_id {}
variable client_secret {}

provider "azurerm" {
    subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    tenant_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    client_secret = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

## <a name="create-the-template"></a>建立範本

使用下列程式碼，建立名為 `main.tf` 的新 Terraform 範本： 

```tf
resource "azurerm_resource_group" "test" {
  name     = "acctestrg"
  location = "West US 2"
}

resource "azurerm_virtual_network" "test" {
  name                = "acctvn"
  address_space       = ["10.0.0.0/16"]
  location            = "${azurerm_resource_group.test.location}"
  resource_group_name = "${azurerm_resource_group.test.name}"
}

resource "azurerm_subnet" "test" {
  name                 = "acctsub"
  resource_group_name  = "${azurerm_resource_group.test.name}"
  virtual_network_name = "${azurerm_virtual_network.test.name}"
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "test" {
  name                         = "publicIPForLB"
  location                     = "${azurerm_resource_group.test.location}"
  resource_group_name          = "${azurerm_resource_group.test.name}"
  public_ip_address_allocation = "static"
}

resource "azurerm_lb" "test" {
  name                = "loadBalancer"
  location            = "${azurerm_resource_group.test.location}"
  resource_group_name = "${azurerm_resource_group.test.name}"

  frontend_ip_configuration {
    name                 = "publicIPAddress"
    public_ip_address_id = "${azurerm_public_ip.test.id}"
  }
}

resource "azurerm_lb_backend_address_pool" "test" {
  resource_group_name = "${azurerm_resource_group.test.name}"
  loadbalancer_id     = "${azurerm_lb.test.id}"
  name                = "BackEndAddressPool"
}

resource "azurerm_network_interface" "test" {
  count               = 2
  name                = "acctni${count.index}"
  location            = "${azurerm_resource_group.test.location}"
  resource_group_name = "${azurerm_resource_group.test.name}"

  ip_configuration {
    name                          = "testConfiguration"
    subnet_id                     = "${azurerm_subnet.test.id}"
    private_ip_address_allocation = "dynamic"
    load_balancer_backend_address_pools_ids = ["${azurerm_lb_backend_address_pool.test.id}"]
  }
}

resource "azurerm_managed_disk" "test" {
  count                = 2
  name                 = "datadisk_existing_${count.index}"
  location             = "${azurerm_resource_group.test.location}"
  resource_group_name  = "${azurerm_resource_group.test.name}"
  storage_account_type = "Standard_LRS"
  create_option        = "Empty"
  disk_size_gb         = "1023"
}

resource "azurerm_availability_set" "avset" {
  name                         = "avset"
  location                     = "${azurerm_resource_group.test.location}"
  resource_group_name          = "${azurerm_resource_group.test.name}"
  platform_fault_domain_count  = 2
  platform_update_domain_count = 2
  managed                      = true
}

resource "azurerm_virtual_machine" "test" {
  count                 = 2
  name                  = "acctvm${count.index}"
  location              = "${azurerm_resource_group.test.location}"
  availability_set_id   = "${azurerm_availability_set.avset.id}"
  resource_group_name   = "${azurerm_resource_group.test.name}"
  network_interface_ids = ["${element(azurerm_network_interface.test.*.id, count.index)}"]
  vm_size               = "Standard_DS1_v2"

  # Uncomment this line to delete the OS disk automatically when deleting the VM
  # delete_os_disk_on_termination = true

  # Uncomment this line to delete the data disks automatically when deleting the VM
  # delete_data_disks_on_termination = true

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "myosdisk${count.index}"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  # Optional data disks
  storage_data_disk {
    name              = "datadisk_new_${count.index}"
    managed_disk_type = "Standard_LRS"
    create_option     = "Empty"
    lun               = 0
    disk_size_gb      = "1023"
  }

  storage_data_disk {
    name            = "${element(azurerm_managed_disk.test.*.name, count.index)}"
    managed_disk_id = "${element(azurerm_managed_disk.test.*.id, count.index)}"
    create_option   = "Attach"
    lun             = 1
    disk_size_gb    = "${element(azurerm_managed_disk.test.*.disk_size_gb, count.index)}"
  }

  os_profile {
    computer_name  = "hostname"
    admin_username = "testadmin"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = false
  }

  tags {
    environment = "staging"
  }
}
```

## <a name="visualize-the-changes-with-plan"></a>使用 plan 視覺化變更

執行 `terraform plan`，預覽範本所建立的虛擬機器基礎結構。

![Terraform Plan](media/terraformPlanVmsWithModules.png)


## <a name="create-the-virtual-machines-with-apply"></a>使用 apply 建立虛擬機器

在 Azure 上執行 `terraform apply` 以佈建 VM 叢集。

![Terraform Apply](media/terraformApplyVmsWithModules.png)

## <a name="next-steps"></a>後續步驟

- 瀏覽 [Azure Terraform 模組](https://registry.terraform.io/modules/Azure)清單
- 使用 Terraform 建立[ 虛擬機器擴展集]()