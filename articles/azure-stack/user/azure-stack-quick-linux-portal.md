---
title: "Azure Stack 快速入門 - 建立 VM 入口網站"
description: "Azure Stack 快速入門 - 使用入口網站來建立 Linux VM"
services: azure-stack
cloud: azure-stack
author: vhorne
manager: byronr
ms.service: azure-stack
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: victorh
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 5f815bafdcc7c05ec7f4149fb7c9df178f0f80e2
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-stack-portal"></a>使用 Azure Stack 入口網站來建立 Linux 虛擬機器

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

您可以透過 Azure Stack 入口網站來建立 Azure Stack 虛擬機器。 此方法提供一個瀏覽器型使用者介面，可建立和設定虛擬機器及所有相關的資源。 本快速入門說明如何快速建立 Linux 虛擬機器並在虛擬機器上安裝 Web 伺服器。

## <a name="prerequisites"></a>必要條件

* **Azure Stack 市集中的 Linux 映像**

   Azure Stack 市集預設並未包含 Linux 映像。 因此，您必須先使用[將市集項目從 Azure 下載到 Azure Stack](../azure-stack-download-azure-marketplace-item.md) 主題所述的步驟來確定 Azure Stack 操作員已下載 **Ubuntu Server 16.04 LT** 映像，才能建立 Linux 虛擬機器。

* **SSH 用戶端存取權**

   如果您使用「Azure Stack 開發套件」(ASDK)，可能會無法存取您環境中的 SSH 用戶端。 如果是這種情況，您可以從包含 SSH 用戶端的數個套件中做選擇。 例如，您可以安裝包含 SSH 用戶端和 SSH 金鑰產生器 (puttygen.exe) 的 PuTTY。 如需有關可能選項的詳細資訊，請參閱下列相關的 Azure 文章：[如何在 Azure 上搭配 Windows 使用 SSH 金鑰](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows#windows-packages-and-ssh-clients)。

   本快速入門使用 PuTTY 來產生 SSH 金鑰及連線到 Linux 虛擬機器。 若要下載並安裝 PuTTY，請移至 [http://www.putty.org/](http://www.putty.org)。

## <a name="create-an-ssh-key-pair"></a>建立 SSH 金鑰組

您必須要有 SSH 金鑰組，才能完成本快速入門的操作。 如果現有的 SSH 金鑰組，則可略過此步驟。

1. 瀏覽至 PuTTY 安裝資料夾 (預設位置為 ```C:\Program Files\PuTTY```) 並執行 ```puttygen.exe```。
2. 在 [PuTTY Key Generator] \(PuTTY 金鑰產生器\) 視窗中，確定 [Type of key to generate] \(要產生的金鑰類型\) 已設定為 [RSA]，並且 [Number of bits in a generated key] \(所產生金鑰中的位元數\) 已設定為 **2048**。 準備好時，按一下 [Generate] \(產生\)。

   ![puttygen.exe](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. 若要完成金鑰產生程序，請將您的滑鼠游標移至 [PuTTY 金鑰產生器] 視窗內。
4. 完成金鑰產生程序時，按一下 [Save public key] \(儲存公開金鑰\) 和 [Save private key] \(儲存私密金鑰\)，以將公開和私密金鑰儲存至檔案。

   ![PuTTY 金鑰](media/azure-stack-quick-linux-portal/Putty02.PNG)



## <a name="sign-in-to-the-azure-stack-portal"></a>登入 Azure Stack 入口網站

登入 Azure Stack 入口網站。 Azure Stack 入口網站的位址取決於您所連線的 Azure Stack 產品：

* 如果是「Azure Stack 開發套件」(ASDK)，請移至：https://portal.local.azurestack.external。
* 如果是 Azure Stack 整合系統，請移至您 Azure Stack 操作員所提供的 URL。

## <a name="create-the-virtual-machine"></a>建立虛擬機器

1. 按一下 Azure Stack 入口網站左上角的 [新增] 按鈕。

2. 選取 [計算]，然後選取 [Ubuntu Server 16.04 LTS]。
3. 按一下 [建立] 。

4. 輸入虛擬機器資訊。 針對 [驗證類型] 選取 [SSH 公開金鑰]。 當您貼上 SSH 公開金鑰 (先前已儲存至檔案中) 時 ，請謹慎地移除任何前置或尾端的空白字元。 完成時，按一下 [確定]。

   ![虛擬機器基本資訊](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. 針對虛擬機器，選取 [D1_V2]。

   ![機器大小](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. 在 [設定] 頁面上，保留預設值並按一下 [確定]。

7. 在 [摘要] 頁面上，按一下 [確定] 來開始進行虛擬機器部署。


## <a name="connect-to-the-virtual-machine"></a>連接至虛擬機器

1. 在虛擬機器頁面上，按一下 [連接]。 這會顯示可用來連線到虛擬機器的 SSH 連接字串。

   ![連接虛擬機器](media/azure-stack-quick-linux-portal/linux-03.PNG)

2. 開啟 PuTTY。
3. 在 [PuTTY Configuration] \(PuTTY 組態\) 畫面的 [Category] \(類別\) 底下，展開 [SSH]，然後按一下 [Auth] \(驗證\)。按一下 [Browse] \(瀏覽\)，然後選取您先前儲存的私密金鑰檔案。

   ![PuTTY 私密金鑰](media/azure-stack-quick-linux-portal/Putty03.PNG)
4. 在 [Category] \(類別\) 底下向上捲動，然後按一下 [Session] \(工作階段\)。
5. 在 [Host Name (or IP address)] \(主機名稱 (或 IP 位址)\) 方塊中，貼上 Azure Stack 入口網站中您先前看到的連接字串。 在此範例中，該字串為 ```asadmin@192.168.102.34```。
 
   ![PuTTY 工作階段](media/azure-stack-quick-linux-portal/Putty04.PNG)
6. 按一下 [Open] \(開啟\) 以開啟對虛擬機器的工作階段。

   ![Linux 工作階段](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-nginx"></a>安裝 NGINX

請使用下列 Bash 指令碼來更新套件來源，並在虛擬機器上安裝最新的 NGINX 套件。 

```bash 
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

完成時，請結束 SSH 工作階段，並返回 Azure Stack 入口網站中的虛擬機器 [概觀] 頁面。


## <a name="open-port-80-for-web-traffic"></a>針對 Web 流量開啟連接埠 80 

網路安全性群組 (NSG) 可保護輸入和輸出流量。 從 Azure Stack 入口網站建立虛擬機器時，會在連接埠 22 上建立 SSH 連線的輸入規則。 由於這個虛擬機器裝載 Web 伺服器，因此必須針對連接埠 80 建立 NSG 規則。

1. 在虛擬機器的 [概觀] 頁面上，按一下「資源群組」的名稱。
2. 選取虛擬機器的「網路安全性群組」。 使用 [類型] 資料行可以識別 NSG。 
3. 在左側功能表的 [設定] 底下，按一下 [輸入安全性規則]。
4. 按一下 [新增] 。
5. 在 [名稱] 中輸入 **http**。 確定 [連接埠範圍] 已設為 80 且 [動作] 已設為 [允許]。 
6. 按一下 [確定] 。


## <a name="view-the-nginx-welcome-page"></a>檢視 NGINX 歡迎使用頁面

在已於您虛擬機器上安裝 NGINX 並開啟連接埠 80 的情況下，現在便可透過虛擬機器的公用 IP 位址存取 Web 伺服器。 您可以在 Azure Stack 入口網站中的虛擬機器 [概觀] 頁面上找到該公用 IP 位址。

請開啟網頁瀏覽器，然後瀏覽至 ```http://<public IP address>```。

![預設 NGINX 網站](media/azure-stack-quick-linux-portal/linux-04.PNG)


## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、虛擬機器和所有相關資源。 若要這樣做，請從虛擬機器頁面選取資源群組，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已部署簡單的 Linux 虛擬機器、網路安全性群組規則，並已安裝 Web 伺服器。 若要深入了解 Azure Stack 虛擬機器，請繼續移至 [Azure Stack 中虛擬機器的考量](azure-stack-vm-considerations.md)。


