---
title: "在 Azure 上建立 Jenkins 伺服器"
description: "從 Jenkins 方案範本在 Azure Linux 虛擬機器上安裝 Jenkins，並建置範例 Java 應用程式。"
author: mlearned
manager: douge
ms.service: multiple
ms.workload: web
ms.devlang: java
ms.topic: hero-article
ms.date: 08/21/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 7bb74f297d52fb25171817175cce64187b397c38
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2017
---
# <a name="create-a-jenkins-server-on-an-azure-linux-vm-from-the-azure-portal"></a>從 Azure 入口網站在 Azure Linux VM 上建立 Jenkins 伺服器

本快速入門示範如何在 Ubuntu Linux VM 上安裝 [Jenkins](https://jenkins.io)，以及設定為使用 Azure 的工具和外掛程式。 當您完成時，您可讓在 Azure 中執行的 Jenkins 伺服器從 [GitHub](https://github.com) 建置範例 Java 應用程式。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶
* 在您電腦的命令列上存取SSH (例如 Bash 殼層或 [PuTTY](http://www.putty.org/))

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-jenkins-vm-from-the-solution-template"></a>從方案範本建立 Jenkins VM

在 Web 瀏覽器中開啟 [Jenkins 的 Marketplace 映像](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview)，然後從頁面左側選取 [立即取得]。 檢閱價格詳細資料並選取 [繼續]，然後選取 [建立] 以在 Azure 入口網站中設定 Jenkins 伺服器。 
   
![Azure 入口網站對話方塊](./media/install-jenkins-solution-template/ap-create.png)

在 [設定基本設定] 索引標籤中，填妥下列欄位：

![設定基本設定](./media/install-jenkins-solution-template/ap-basic.png)

* 使用 **Jenkins** 作為 [名稱]。
* 輸入 [使用者名稱]。 使用者名稱必須符合[特定需求](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm)。
* 選取 [密碼] 作為 [驗證類型] 並輸入密碼。 密碼必須包含大寫字元、數字和一個特殊字元。
* 使用 **myJenkinsResourceGroup** 作為 [資源群組]。
* 從 [位置] 下拉式清單選擇 [美國東部] [Azure 地區](https://azure.microsoft.com/regions/)。

選取 [確定] 繼續前往 [設定其他選項] 索引標籤。輸入唯一的網域名稱，以識別 Jenkins 伺服器並選取 [確定]。

![設定其他選項](./media/install-jenkins-solution-template/ap-addtional.png)  

 一旦通過驗證，再次從 [摘要] 索引標籤選取 [確定]。最後，選取 [購買] 以建立 Jenkins VM。 當您的伺服器準備就緒時，您會在 Azure 入口網站中收到通知：   

![Jenkins 已就緒通知](./media/install-jenkins-solution-template/jenkins-deploy-notification-ready.png)

## <a name="connect-to-jenkins"></a>連線到 Jenkins

在網頁瀏覽器中，瀏覽至您的虛擬機器 (例如，http://jenkins2517454.eastus.cloudapp.azure.com/)。 Jenkins 主控台無法透過不安全的 HTTP 存取，因此頁面上會提供指示，以便從您的電腦使用 SSH 通道安全地存取 Jenkins 主控台。

![將 Jenkins 解除鎖定](./media/install-jenkins-solution-template/jenkins-ssh-instructions.png)

在頁面從命令列使用 `ssh` 命令設定通道，並以先前從方案範本設定虛擬機器時選擇的虛擬機器系統管理員的使用者名稱取代 `username`。

```bash
ssh -L 127.0.0.1:8080:localhost:8080 jenkinsadmin@jenkins2517454.eastus.cloudapp.azure.com
```

啟動您的通道後，在本機電腦上瀏覽至 http://localhost:8080/。 

透過 SSH 連線到 Jenkins VM 時，在命令列中執行下列命令，以取得初始密碼。

```bash
`sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.
```

第一次使用此初始密碼時，請將 Jenkins 儀表板解除鎖定。

![將 Jenkins 解除鎖定](./media/install-jenkins-solution-template/jenkins-unlock.png)

在下一個頁面上選取 [安裝建議的外掛程式]，然後建立用來存取 Jenkins 儀表板的 Jenkins 系統管理使用者。

![Jenkins 已就緒！](./media/install-jenkins-solution-template/jenkins-welcome.png)

Jenkins 伺服器現在已準備要建置程式碼。

## <a name="create-your-first-job"></a>建立您的第一個作業

從 Jenkins 主控台選取 [建立新的作業]，然後將它命名為 **mySampleApp** 並選取 [自由樣式專案]，然後選取 [確定]。

![建立新的作業](./media/install-jenkins-solution-template/jenkins-new-job.png) 

選取 [原始程式碼管理] 索引標籤，啟用 [Git]，並且在 [存放庫 URL] 欄位中輸入下列 URL：`https://github.com/spring-guides/gs-spring-boot.git`

![建立 Git 存放庫](./media/install-jenkins-solution-template/jenkins-job-git-configuration.png) 

選取 [建置] 索引標籤，然後選取 [新增建置步驟]、[叫用 Gradle 指令碼]。 選取 [使用 Gradle 包裝函式]，然後在 [包裝函式位置] 中輸入 `complete` 並輸入 `build` 作為 [工作]。

![使用要建置的 Gradle 包裝函式](./media/install-jenkins-solution-template/jenkins-job-gradle-config.png) 

選取 [進階...] 然後在 [根組建指令碼] 欄位中輸入 `complete`。 選取 [ **儲存**]。

![在 Gradle 包裝函式建置步驟中設定進階設定](./media/install-jenkins-solution-template/jenkins-job-gradle-advances.png) 

## <a name="build-the-code"></a>建置程式碼

選取 [立即建置] 以編譯程式碼和封裝範例應用程式。 當您的組建完成時，選取專案的 [工作區] 連結。

![瀏覽至可從組建取得 JAR 檔案的工作區](./media/install-jenkins-solution-template/jenkins-access-workspace.png) 

瀏覽至 `complete/build/libs`，並確定 `gs-spring-boot-0.1.0.jar` 在那裡以確認您的組建成功。 您的 Jenkins 伺服器現在已準備要在 Azure 中建置自己的專案。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [新增 Azure VM 作為 Jenkins 代理程式](jenkins-azure-vm-agents.md)
