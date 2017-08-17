---
title: "在 Azure 上建立第一個 Linux (Ubuntu) VM 上的 Jenkins Master"
description: "利用解決方案範本來部署 Jenkins。"
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 8bacfe3e-7f0b-4394-959a-a88618cb31e1
ms.service: multiple
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.translationtype: HT
ms.sourcegitcommit: 80fd9ee9b9de5c7547b9f840ac78a60d52153a5a
ms.openlocfilehash: 06d6d305eb9711768dc62a04726359e6280d1b69
ms.contentlocale: zh-tw
ms.lasthandoff: 08/14/2017

---

# <a name="create-your-first-jenkins-master-on-a-linux-ubuntu-vm-on-azure"></a>在 Azure 上建立第一個 Linux (Ubuntu) VM 上的 Jenkins Master

本快速入門示範如何在 Linux (Ubuntu 14.04 LTS) VM 上安裝最新的穩定 Jenkins 版本，以及設定為使用 Azure 的工具和外掛程式。 這些工具包括：
<ul>
<li>Git，以取得原始檔控制</li>
<li>Azure 認證外掛程式，可獲得安全連線</li>
<li>Azure VM 代理程式外掛程式，可進行彈性組建、測試和持續整合</li>
<li>Azure 儲存體外掛程式，以儲存成品</li>
<li>Azure CLI，可使用指令碼來部署應用程式</li>
</ul>

在本教學課程中，您了解如何：

> [!div class="checklist"]
> * 建立 Azure 免費帳戶。
> * 使用解決方案範本在 Azure VM 上建立 Jenkins Master。 
> * 執行 Jenkins 的初始設定。
> * 安裝建議的外掛程式。

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

## <a name="create-the-vm-in-azure-by-deploying-the-solution-template-for-jenkins"></a>在 Azure 中建立 VM，方法是部署 Jenkins 的解決方案範本

Azure 快速入門範本可讓您快速且可靠地在 Azure 上部署複雜的技術。  Azure Resource Manager 可讓您使用[宣告式範本](https://azure.microsoft.com/en-us/resources/templates/?term=jenkins)佈建應用程式。 在單一的範本中，您可以部署多個服務及其相依性。 您可以使用相同的範本，在應用程式生命週期的每個階段重複部署應用程式。

檢視這個範本的[方案和定價](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-oss.jenkins?tab=Overview)資訊以了解成本選項。

移至[Jenkins 的市集映像](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/azure-oss.jenkins?tab=Overview)，按一下 [立即取得]  

在 Azure 入口網站中，按一下 [建立]。  這個範本需要使用 Resource Manager，因此會停用範本模型下拉式清單。
   
![Azure 入口網站對話方塊](./media/install-jenkins-solution-template/ap-create.png)

在 [設定基本設定] 索引標籤中：

![設定基本設定](./media/install-jenkins-solution-template/ap-basic.png)

* 提供您 Jenkins 執行個體的名稱。
* 選取 VM 磁碟類型。  針對生產工作負載，選擇較大的 VM 和 SSD 以獲得較佳的效能。  您可以在[這裡](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage)深入了解 Azure 磁碟類型。
* 使用者名稱：必須符合長度需求，且不能包含保留的字組或不支援的字元。 不允許諸如 "admin" 等名稱。  如需詳細資訊，請參閱[這裡](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/faq)的使用者名稱和密碼需求。
* 驗證類型：建立受密碼保護的執行個體或[SSH 公開金鑰](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/ssh-from-windows)。 如果您是使用密碼，則必須符合下列需求的其中 3 項：1 個小寫字元、1 個大寫字元、1 個數字和 1 個特殊字元。
* 將 Jenkins 版本類型保留為 **LTS**
* 選取一個訂用帳戶。
* 建立資源群組，或使用現有空的資源群組。 
* 選取位置。

在 [設定其他選項] 索引標籤中：

![設定其他選項](./media/install-jenkins-solution-template/ap-addtional.png)

* 提供可唯一識別 Jenkins Master 的網域名稱標籤。

按一下 [確定] 移至下一個步驟。 

一旦驗證成功後，按一下 [確定] 可下載範本和參數。 

接下來，選取 [購買] 可佈建所有資源。

## <a name="setup-ssh-port-forwarding"></a>設定 SSH 連接埠轉送

根據預設，Jenkins 執行個體是使用 http 通訊協定，並且接聽通訊埠 8080。 使用者不應該透過不安全的通訊協定進行驗證。
    
設定連接埠轉送可在本機電腦上檢視 Jenkins UI。

### <a name="if-you-are-using-windows"></a>如果您是使用 Windows：

如果您是使用密碼來保護 Jenkins，請安裝 PuTTY 並執行此命令：
```
putty.exe -ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* 如需登入，請輸入密碼。

![如需登入，請輸入密碼。](./media/install-jenkins-solution-template/jenkins-pwd.png)

如果您是使用 SSH，請執行此命令：
```
putty -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

### <a name="if-you-are-using-linux-or-mac"></a>如果您是使用 Linux 或 Mac：

如果您是使用密碼來保護 Jenkins Master，請執行此命令：
```
ssh -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```
* 如需登入，請輸入密碼。

如果您是使用 SSH，請執行此命令：
```
ssh -i <private key file including path> -L 8080:localhost:8080 <username>@<Domain name label>.<location>.cloudapp.azure.com
```

## <a name="connect-to-jenkins"></a>連線到 Jenkins
啟動您的通道後，在本機電腦上瀏覽至 http://localhost:8080/。

第一次使用時，請使用初始管理密碼將 Jenkins 儀表板解除鎖定。

![將 Jenkins 解除鎖定](./media/install-jenkins-solution-template/jenkins-unlock.png)

若要取得權杖，請 SSH 至 VM 並執行 `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`。

![將 Jenkins 解除鎖定](./media/install-jenkins-solution-template/jenkins-ssh.png)

系統會要求您安裝建議的外掛程式。

接下來，建立 Jenkins Master 的管理使用者。

Jenkins 執行個體現在可供使用！ 您可以存取唯讀檢視，方法是前往 http://\<您剛才建立的執行個體之公用 DNS 名稱\>。

![Jenkins 已就緒！](./media/install-jenkins-solution-template/jenkins-welcome.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您：

> [!div class="checklist"]
> * 使用解決方案範本建立了 Jenkins Master。
> * 執行了 Jenkins 的初始設定。
> * 安裝了外掛程式。

請遵循此連結，了解如何使用 Azure VM 代理程式與 Jenkins 持續整合。

> [!div class="nextstepaction"]
> [Azure VM 作為 Jenkins 代理程式](jenkins-azure-vm-agents.md)

