---
title: "在 HDInsight 上使用 R 伺服器安裝 RStudio - Azure | Microsoft Docs"
description: "如何在 HDInsight 上使用 R 伺服器安裝 RStudio。"
services: hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 918abb0d-8248-4bc5-98dc-089c0e007d49
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: 416420d855505508735ebd8526e93efdb230ad53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="installing-rstudio-with-r-server-on-hdinsight"></a>在 HDInsight 上使用 R 伺服器安裝 RStudio

此文章說明如何使用自訂指令碼，在叢集的邊緣節點上安裝 [RStudio Server](https://www.rstudio.com/products/rstudio-server/) 的社群 (免費) 版本。 RStudio Server 提供瀏覽器型 IDE 供遠端用戶端使用，而且在 Linux 上廣為使用。 目前有多種適用於 R 的整合式開發環境 (IDE)，包括：

- Microsoft 的 [Visual Studio R 工具](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS) 
- [RStudio Server](https://www.rstudio.com/products/rstudio-server/) 
- Walware 的 Eclipse 型 [StatET](http://www.walware.de/goto/statet)。

在 HDInsight 叢集的邊緣節點上安裝 RStudio Server 的優點在於可提供完整的 IDE 體驗，供透過叢集上的 R 伺服器來開發及執行 R 指令碼。 相較於預設使用 R 主控台的情況，此設定可大幅提高生產力。

> [!NOTE]
> 本文中描述的程序僅適用於在佈建叢集時未選擇安裝 RStudio Server 社群版的情況。 如果您已在佈建期間新增它，則可以按一下叢集入口 Azure 入口網站中的 [R 伺服器儀表板] 圖格，然後在 [R Studio Server] 圖格上存取它。 

如果您偏好使用 RStudio Server 的商業授權 Pro 版本，請依照 [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/) 的安裝指示執行。

> [!NOTE]
> 若您使用 HDInsight 叢集，且 R 是使用[安裝 R 指令碼動作](hdinsight-hadoop-r-scripts-linux.md)安裝的，則此文件中的步驟將無法正確執行，因為它們要求 HDInsight 叢集上必須有 R 伺服器。
>
> 

## <a name="prerequisites"></a>先決條件

* 具有 R 伺服器的 Azure HDInsight 叢集已安裝。 如需相關指示，請參閱[開始在 HDInsight 叢集上使用 R 伺服器](hdinsight-hadoop-r-server-get-started.md)。
* SSH 用戶端。 針對 Linux 和 Unix 發行版本或 Macintosh OS X，`ssh` 命令是隨作業系統提供。 針對 Windows，我們建議使用 [Cygwin](http://www.redhat.com/services/custom/cygwin/) 與 [OpenSSH 選項](https://www.youtube.com/watch?v=CwYSvvGaiWU)，或 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。  

## <a name="install-rstudio-on-the-cluster-using-a-custom-script"></a>使用自訂指令碼在叢集上安裝 RStudio

步驟如下：

1. 識別叢集的邊緣節點。 針對具有 R 伺服器的 HDInsight 叢集，以下是前端節點和邊緣節點的命名慣例。
   * 前端節點 `CLUSTERNAME-ssh.azurehdinsight.net`
   * 邊緣節點 `CLUSTERNAME-ed-ssh.azurehdinsight.net` 

2. 使用步驟 1 中提供的命名模式以 SSH 方式連接至叢集的邊緣節點。 如需詳細資訊，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md)。

3. 一旦連線，就會變成叢集上的根使用者。 在 SSH 工作階段中，使用下列命令：

        sudo su -

4. 下載自訂指令碼以安裝 RStudio。 使用下列命令：

        wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. 變更自訂指令碼檔案的權限，然後執行指令碼。 使用下列命令：

        chmod 755 InstallRStudio.sh
        ./InstallRStudio.sh

6. 如果您在建立具有 R 伺服器的 HDInsight 叢集時使用 SSH 密碼，您可以略過此步驟，並繼續進行下一步。 如果您是使用 SSH 金鑰建立叢集，您必須為您的 SSH 使用者設定密碼。 連線到 RStudio 時，您會需要此密碼。 執行下列命令：

        passwd USERNAME
        Current Kerberos password:
        New password:
        Retype new password:
        Current Kerberos password:


7. 當系統提示您輸入**目前的 Kerberos 密碼**時，請按 **Enter**。  請注意，您必須將 `USERNAME` 取代為您的 HDInsight 叢集的 SSH 使用者。 如果已成功設定您的密碼，應該會看到下列訊息：

        passwd: password updated successfully

    結束 SSH 工作階段。

8. 建立到叢集的 SSH 通道，方法是將 HDInsight 叢集上的 `ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` 對應至用戶端電腦。 您必須在開啟新的瀏覽器工作階段之前，建立 SSH 通道。

   * 在已安裝 [Cygwin](http://www.redhat.com/services/custom/cygwin/) 的 Linux 用戶端或 Windows 用戶端上，開啟終端機工作階段並且使用下列命令：

             ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

       以您 HDInsight 叢集的 SSH 使用者取代 **USERNAME**，並以您 HDInsight 叢集的名稱取代 **CLUSTERNAME**。
       您也可以透過新增 `-i id_rsa_key` 來使用 SSH 金鑰而非密碼。        
   * 如果使用 Windows 用戶端與 PuTTY，則

     1. 開啟 PuTTY，並輸入連線資訊。
     2. 在對話方塊左側的 [類別] 區段中，依序展開 [連線] 和 [SSH]，然後選取 [通道]。
     3. 在 [ **控制 SSH 連接埠轉送的選項** ] 表單中提供下列資訊：

        * **來源連接埠** - 您想要轉送之用戶端上的連接埠。 例如， **8787**。
        * **目的地** - 必須對應至本機用戶端電腦的目的地。 例如，**localhost:8787**。

            ![建立 SSH 通道](./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "建立 SSH 通道")

     4. 按一下 新增 以新增設定，然後按一下開啟 以開啟 SSH 連線。
     5. 當系統顯示提示時，請登入伺服器以建立 SSH 工作階段並啟用該通道。

9. 開啟網頁瀏覽器，並根據您針對通道輸入的連接埠輸入下列 URL：

        http://localhost:8787/ 

10. 系統將會提示您輸入 SSH 使用者名稱與密碼以連線到叢集。 如果您在建立叢集時使用 SSH 金鑰，則必須輸入在步驟 5 中所建立的密碼。

    ![連線至 R Studio](./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "建立 SSH 通道")

11. 若要測試 RStudio 安裝是否成功，您可以執行測試指令碼，該指令碼會在叢集上執行以 R 為基礎的 MapReduce 與 Spark 作業。 若要下載要在 RStudio 中執行的測試指令碼，請返回 SSH 主控台並輸入下列命令：

    *    如果您建立具有 R 的 Hadoop 叢集，請使用此命令：

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r
    *    如果您已建立具有 R 的 Spark 叢集，請使用此命令：

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

12. 在 RStudio 中，會看到您所下載的測試指令碼。 按兩下以開啟該檔案，選取檔案的內容，然後按一下執行。 您應該可以在 [主控台] 窗格中看到輸出：

   ![測試安裝](./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "測試安裝")

另一個做法是輸入 `source(testhdi.r)` 或 `source(testhdi_spark.r)` 以執行指令碼。

## <a name="see-also"></a>另請參閱

* [適用於 HDInsight 叢集上的 R 伺服器的計算內容選項](hdinsight-hadoop-r-server-compute-contexts.md)
* [適用於 HDInsight 上 R 伺服器的 Azure 儲存體選項](hdinsight-hadoop-r-server-storage.md)

