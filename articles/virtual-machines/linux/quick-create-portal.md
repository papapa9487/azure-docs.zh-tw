---
title: "Azure 快速入門 - 建立 VM 入口網站 | Microsoft Docs"
description: "Azure 快速入門 - 建立 VM 入口網站"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6ac6ed21f3cf363137381b82835a11d0920aee3b
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2017
---
# <a name="create-a-linux-virtual-machine-with-the-azure-portal"></a>使用 Azure 入口網站建立 Linux 虛擬機器

您可以透過 Azure 入口網站建立 Azure 虛擬機器。 此方法可提供以瀏覽器為基礎的使用者介面，以便建立和設定虛擬機器，以及所有相關的資源。 本快速入門會逐步說明如何建立虛擬機器，以及在 VM 上安裝 Web 伺服器。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="create-ssh-key-pair"></a>建立 SSH 金鑰組

您需要有 SSH 金鑰組才能完成本快速入門。 如果現有的 SSH 金鑰組，則可略過此步驟。

從 Bash 殼層，執行此命令並遵循畫面上的指示操作。 命令輸出包含公開金鑰檔案的檔案名稱。 將公開金鑰檔案的內容複製到剪貼簿。

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="log-in-to-azure"></a>登入 Azure 

登入 Azure 入口網站，網址是 http://portal.azure.com/。

## <a name="create-virtual-machine"></a>Create virtual machine

1. 按一下 Azure 入口網站左上角的 [新增] 按鈕。

2. 選取 [計算]，然後選取 [Ubuntu Server 16.04 LTS]。 

3. 輸入虛擬機器資訊。 針對 [驗證類型] 選取 [SSH 公開金鑰]。 貼上 [SSH 公開金鑰] 時，請謹慎地移除任何前置或尾端的空白字元。 完成時，按一下 [確定]。

    ![在入口網站刀鋒視窗中輸入 VM 的基本資訊](./media/quick-create-portal/create-vm-portal-basic-blade.png)

4. 選取 VM 的大小。 若要查看更多大小，請選取 [檢視全部] 或變更 [支援的磁碟類型] 篩選條件。 

    ![顯示 VM 大小的螢幕擷取畫面](./media/quick-create-portal/create-linux-vm-portal-sizes.png)  

5. 在 [設定] 之下，保留預設值並按一下 [確定]。

6. 在 [摘要] 頁面上，按一下 [確定] 來啟動虛擬機器部署。

7. 系統會將 VM 釘選到 Azure 入口網站儀表板。 一旦完成部署後，VM 摘要就會自動開啟。


## <a name="connect-to-virtual-machine"></a>連線至虛擬機器

建立虛擬機器的 SSH 連線。

1. 按一下虛擬機器屬性上的 [連線] 按鈕。 [連線] 按鈕顯示的 SSH 連接字串可用來連線至虛擬機器。

    ![入口網站 9](./media/quick-create-portal/portal-quick-start-9.png) 

2. 執行以下命令來建立 SSH 工作階段。 以您從 Azure 入口網站複製的連接字串取代此連接字串。

```bash 
ssh azureuser@40.112.21.50
```

## <a name="install-nginx"></a>安裝 NGINX

使用下列 bash 指令碼來更新套件來源及安裝最新的 NGINX 套件。 

```bash 
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

完成時，結束 SSH 工作階段並在 Azure 入口網站中傳回 VM 屬性。


## <a name="open-port-80-for-web-traffic"></a>針對 Web 流量開啟連接埠 80 

網路安全性群組 (NSG) 可保護輸入和輸出流量。 從 Azure 入口網站建立 VM 時，會在連接埠 22 上建立 SSH 連線的輸入規則。 因為此 VM 託管 Web 伺服器，所以必須針對連接埠 80 建立 NSG 規則。

1. 在虛擬機器上，按一下 [資源群組] 的名稱。
2. 選取 [網路安全性群組]。 使用 [類型] 資料行可以識別 NSG。 
3. 在左側功能表的 [設定] 之下，按一下 [輸入安全性規則]。
4. 按一下 [新增]。
5. 在 [名稱] 中輸入 **http**。 確定 [連接埠範圍] 已設為 80 且 [動作] 已設為 [允許]。 
6. 按一下 [確定] 。


## <a name="view-the-nginx-welcome-page"></a>檢視 NGINX 歡迎使用頁面

安裝 NGINX 後，且連接埠 80 對您的 VM 開啟，即可立即從網際網路存取 Web 伺服器。 開啟 Web 瀏覽器，並輸入 VM 的公用 IP 位址。 在 Azure 入口網站的 VM 屬性上可找到公用 IP 位址。

![預設 NGINX 網站](./media/quick-create-cli/nginx.png) 

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、虛擬機器和所有相關資源。 若要這樣做，請選取虛擬機器的資源群組，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已部署簡單的虛擬機器、網路安全性群組規則，並已安裝 Web 伺服器。 若要深入了解 Azure 虛擬機器，請繼續 Linux VM 的教學課程。

> [!div class="nextstepaction"]
> [Azure Linux 虛擬機器教學課程](./tutorial-manage-vm.md)
