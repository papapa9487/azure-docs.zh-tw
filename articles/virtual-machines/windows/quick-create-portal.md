---
title: "Azure 快速入門 - 建立 Windows VM 入口網站 | Microsoft Docs"
description: "Azure 快速入門 - 建立 Windows VM 入口網站"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 7ccc9694f74b655d5b9497378632f05defdbcbf0
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2017
---
# <a name="create-a-windows-virtual-machine-with-the-azure-portal"></a>使用 Azure 入口網站建立 Windows 虛擬機器

您可以透過 Azure 入口網站建立 Azure 虛擬機器。 此方法可提供以瀏覽器為基礎的使用者介面，以便建立和設定虛擬機器，以及所有相關的資源。 本快速入門會逐步說明如何建立虛擬機器，以及在 VM 上安裝 Web 伺服器。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="log-in-to-azure"></a>登入 Azure

登入 Azure 入口網站，網址是 http://portal.azure.com/。

## <a name="create-virtual-machine"></a>Create virtual machine

1. 按一下 Azure 入口網站左上角的 [新增] 按鈕。

2. 選取 [計算]，然後選取 [Windows Server 2016 Datacenter]。 

3. 輸入虛擬機器資訊。 在此輸入的使用者名稱和密碼用於登入虛擬機器。 完成時，按一下 [確定]。

    ![在入口網站刀鋒視窗中輸入 VM 的基本資訊](./media/quick-create-portal/create-windows-vm-portal-basic-blade.png)  

4. 選取 VM 的大小。 若要查看更多大小，請選取 [檢視全部] 或變更 [支援的磁碟類型] 篩選條件。 

    ![顯示 VM 大小的螢幕擷取畫面](./media/quick-create-portal/create-windows-vm-portal-sizes.png)  

5. 在 [設定] 之下，保留預設值並按一下 [確定]。 

6. 在 [摘要] 頁面上，按一下 [確定] 來啟動虛擬機器部署。

7. 系統會將 VM 釘選到 Azure 入口網站儀表板。 一旦完成部署後，VM 摘要就會自動開啟。


## <a name="connect-to-virtual-machine"></a>連線至虛擬機器

建立與虛擬機器的遠端桌面連線。

1. 按一下虛擬機器屬性上的 [連線] 按鈕。 隨即建立並下載遠端桌面通訊協定檔案 (.rdp 檔案)。

    ![入口網站 9](./media/quick-create-portal/quick-create-portal/portal-quick-start-9.png) 

2. 若要連線至您的 VM，請開啟下載的 RDP 檔案。 出現提示時，按一下 [連線]。 在 Mac 上，您需要 RDP 用戶端，例如來自 Mac App Store 的[遠端桌面用戶端](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12)。

3. 輸入在建立虛擬機器時指定的使用者名稱和密碼，然後按一下 [確定]。

4. 您可能會在登入過程中收到憑證警告。 按一下 [是] 或 [繼續] 以繼續進行連線。


## <a name="install-iis-using-powershell"></a>使用 PowerShell 安裝 IIS

在虛擬機器上，啟動 PowerShell 工作階段並執行下列命令來安裝 IIS。

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

完成時，結束 RDP 工作階段並在 Azure 入口網站中傳回 VM 屬性。

## <a name="open-port-80-for-web-traffic"></a>針對 Web 流量開啟連接埠 80 

網路安全性群組 (NSG) 可保護輸入和輸出流量。 從 Azure 入口網站建立 VM 時，會在連接埠 3389 上建立 RDP 連線的輸入規則。 因為此 VM 託管 Web 伺服器，所以必須針對連接埠 80 建立 NSG 規則。

1. 在虛擬機器上，按一下 [資源群組] 的名稱。
2. 選取 [網路安全性群組]。 使用 [類型] 資料行可以識別 NSG。 
3. 在左側功能表的 [設定] 之下，按一下 [輸入安全性規則]。
4. 按一下 [新增]。
5. 在 [名稱] 中輸入 **http**。 確定 [連接埠範圍] 已設為 80 且 [動作] 已設為 [允許]。 
6. 按一下 [確定] 。


## <a name="view-the-iis-welcome-page"></a>檢視 IIS 歡迎使用頁面

安裝 IIS 後，且連接埠 80 對您的 VM 狀態為開啟，即可立即從網際網路存取 Web 伺服器。 開啟 Web 瀏覽器，並輸入 VM 的公用 IP 位址。 在 Azure 入口網站的 [虛擬機器] 之下可找到公用 IP 位址。

![IIS 預設網站](./media/quick-create-powershell/default-iis-website.png) 

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、虛擬機器和所有相關資源。 若要這樣做，請選取 VM 的資源群組，然後按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已部署簡單的虛擬機器、網路安全性群組規則，並已安裝 Web 伺服器。 若要深入了解 Azure 虛擬機器，請繼續 Windows VM 的教學課程。

> [!div class="nextstepaction"]
> [Azure Windows 虛擬機器教學課程](./tutorial-manage-vm.md)
