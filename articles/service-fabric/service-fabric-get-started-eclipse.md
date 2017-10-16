---
title: "適用於 Eclipse 的 Azure Service Fabric 外掛程式 | Microsoft Docs"
description: "開始使用適用於 Eclipse 的 Service Fabric 外掛程式。"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/21/2016
ms.author: saysa
ms.openlocfilehash: 4fa77da8665908553072792d7f2ede47bf5567dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>適用於 Eclipse Java 應用程式開發的 Service Fabric 外掛程式
Eclipse 是 Java 開發人員最普遍使用的整合式的開發環境 (IDE) 之一。 在本文中，我們將說明如何設定 Eclipse 開發環境來使用 Azure Service Fabric。 了解如何安裝 Service Fabric 外掛程式、建立 Service Fabric 應用程式，以及將 Service Fabric 應用程式部署到 Eclipse Neon 中的本機或遠端 Service Fabric 叢集。

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse-neon"></a>在 Eclipse Neon 安裝或更新 Service Fabric 外掛程式
您可以在 Eclipse 中安裝 Service Fabric 外掛程式。 外掛程式可協助簡化建置和部署 Java 服務的程序。

1.  請確定您已安裝最新版的 Eclipse Neon 和最新版的 Buildship (1.0.17 或更新版本)︰
    -   若要檢查已安裝的元件版本，請在 Eclipse Neon 中，前往選擇 [說明] > [安裝詳細資料]。
    -   若要更新 Buildship，請參閱 [Eclipse Buildship：適用於 Gradle 的 Eclipse 外掛程式][buildship-update]。
    -   若要檢查並安裝 Eclipse Neon 的更新，前往 [說明] > [檢查更新]。

2.  若要安裝 Service Fabric 外掛程式，在 Eclipse Neon 中，前往 [說明] > [安裝新軟體]。
  1.    在 [使用] 文字方塊中，輸入 **http://dl.microsoft.com/eclipse**。
  2.    按一下 [新增] 。

         ![適用於 Eclipse Neon 的 Service Fabric 外掛程式][sf-eclipse-plugin-install]
  3.    選取 Service Fabric 外掛程式，然後按 [下一步]。
  4.    完成安裝步驟，然後接受 Microsoft 軟體授權條款。

如果您已安裝 Service Fabric 外掛程式，請確定您擁有的是最新版本。 若要檢查可用的更新，前往 [說明] > [安裝詳細資料]。 在已安裝外掛程式的清單中，選取 Service Fabric，然後按一下更新。 將安裝可用的更新。

> [!NOTE]
> 如果安裝或更新 Service Fabric 外掛程式時很緩慢，可能是因為 Eclipse 設定。 Eclipse 會收集所有變更的中繼資料，以更新使用您 Eclipse 執行個體註冊的網站。 若要加速檢查及安裝 Service Fabric 外掛程式更新的程序，前往 [可用的軟體站台]。 清除所有網站的核取方塊，除了指向 Service Fabric 外掛程式位置 (http://dl.microsoft.com/eclipse/azure/servicefabric) 的網站以外。

> [!NOTE]
>如果 Eclipse 在 Mac 上未如預期一般運作，或需要您以進階使用者身分執行，請移至 **ECLIPSE_INSTALLATION_PATH** 資料夾，並瀏覽至子資料夾 **Eclipse.app/Contents/MacOS**。 執行 `./eclipse` 來啟動 Eclipse。


## <a name="create-a-service-fabric-application-in-eclipse"></a>在 Eclipse 中建立 Service Fabric 應用程式

1.  在 Eclipse Neon 中，前往 [檔案] > [新增] > [其他]。 選取 **Service Fabric 外掛程式**，然後按 [下一步]。

    ![Service Fabric 新專案第 1 頁][create-application/p1]

2.  輸入專案的名稱，然後按 [下一步]。

    ![Service Fabric 新專案第 2 頁][create-application/p2]

3.  在範本清單中，選取 [服務範例]。 選取服務範本類型 (執行者、無狀態、容器或來賓二進位)，然後按 [下一步]。

    ![Service Fabric 新專案第 3 頁][create-application/p3]

4.  輸入服務名稱和服務詳細資訊，然後按一下完成。

    ![Service Fabric 新專案第 4 頁][create-application/p4]

5. 當您建立第一個 Service Fabric 專案時，在 [開啟相關聯的觀點] 對話方塊中，按一下 [是]。

    ![Service Fabric 新專案第 5 頁][create-application/p5]

6.  新的專案看起來像這樣︰

    ![Service Fabric 新專案第 6 頁][create-application/p6]

## <a name="build-and-deploy-a-service-fabric-application-in-eclipse"></a>在 Eclipse 中建置和部署 Service Fabric 應用程式

1.  以滑鼠右鍵按一下新的 Service Fabric 應用程式，然後選取 [Service Fabric]。

    ![Service Fabric 快顯功能表][publish/RightClick]

2. 在子功能表中，選取您想要的選項︰
    -   若要建置應用程式而不進行清除，按一下 [建置應用程式]。
    -   若要對應用程式執行清除建置，按一下 [重新建置應用程式]。
    -   若要清除所建置構件的應用程式，按一下 [清除應用程式]。

3.  您也可以從這個功能表選擇部署、解除部署和發佈應用程式：
    -   若要部署到您的本機叢集，按一下 [部署應用程式]。
    -   在 [發佈應用程式] 對話方塊方塊中，選取 [發佈設定檔]︰
        -  **Local.json**
        -  **Cloud.json**

     這些 JavaScript 物件標記法 (JSON) 檔案會用來儲存連線到本機或雲端 (Azure) 叢集所需的資訊 (例如連線端點和安全性資訊)。

  ![Service Fabric 發佈功能表][publish/Publish]

另一種方式是使用 Eclipse 執行組態來部署 Service Fabric 應用程式。

  1.    前往 [執行] > [執行組態]。
  2.    在 [等級專案] 下，選取 **ServiceFabricDeployer** 執行組態。
  3.    在右窗格中，[引數] 索引標籤底上，選取 [本機] 或 [雲端] 來做為 **publishProfile**。  預設值為 [本機]。 若要部署至遠端或雲端叢集，請選取 [雲端]。
  4.    若要確保在發佈設定檔中填入適當的資訊，請視需要編輯 **Local.json** 或 **Cloud.json**。 您可以新增或更新端點詳細資料和安全性認證。
  5.    確定**工作目錄**指向您要部署的應用程式。 如果要變更應用程式，請按一下 [工作區] 按鈕，然後選取您要的應用程式。
  6.    按一下 套用，然後按一下執行。

幾分鐘內即可建置和部署您的應用程式。 您可以在 Service Fabric Explorer 中監視部署狀態。  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>將 Service Fabric 服務新增至 Service Fabric 應用程式

若要將 Service Fabric 服務新增至現有 Service Fabric 應用程式，請執行下列步驟︰

1.  以滑鼠右鍵按一下您要新增服務的專案，然後按一下Service Fabric。

    ![Service Fabric 新增服務第 1 頁][add-service/p1]

2.  按一下 [新增 Service Fabric 服務]，並完成步驟集合以將服務新增至專案。
3.  選取要新增至專案的服務範本，然後按 [下一步]。

    ![Service Fabric 新增服務第 2 頁][add-service/p2]

4.  輸入服務名稱 (以及視需要其他詳細資料)，然後按一下新增服務 按鈕。  

    ![Service Fabric 新增服務第 3 頁][add-service/p3]

5.  新增服務之後，您的整體專案結構看起來類似下列專案︰

    ![Service Fabric 新增服務第 4 頁][add-service/p4]

## <a name="edit-manifest-versions-of-your-service-fabric-java-application"></a>編輯 Service Fabric Java 應用程式的資訊清單版本

若要編輯資訊清單版本，以滑鼠右鍵按一下專案，移至 **Service Fabric**，然後從功能表下拉式清單中選取 [編輯資訊清單版本...]。 在精靈中，您可以更新應用程式資訊清單、服務資訊清單的資訊清單版本，以及 **Code**、**Config** 和 **Data** 套件的版本。

如果您勾選 [自動更新應用程式和服務版本] 選項，然後更新版本，則會自動更新資訊清單版本。 若要提供範例，您先選取核取方塊，然後將 **Code** 的版本從 0.0.0 更新為 0.0.1 並按一下 [完成]，然後服務資訊清單版本和應用程式資訊清單版本將會自動更新為 0.0.1。

## <a name="upgrade-your-service-fabric-java-application"></a>升級 Service Fabric Java 應用程式

在升級案例中，假設您在 Eclipse 中使用 Service Fabric 外掛程式建立 **App1** 專案。 您使用外掛程式部署它，建立名為 **fabric:/App1Application** 的應用程式。 應用程式類型是 **App1AppicationType**，應用程式版本為 1.0。 現在您想要升級應用程式，但不要中斷可用性。

首先，變更您的應用程式，然後重建已修改的服務。 以服務 (和程式碼、組態或資料，視相關性而定) 已更新的版本，更新已修改之服務的資訊清單檔案 (ServiceManifest.xml)。 同時，也以應用程式已更新的版本號碼及已修改的服務，修改應用程式的資訊清單 (ApplicationManifest.xml)。  

若要使用 Eclipse Neon 升級您的應用程式，您可以建立重複的執行組態設定檔。 然後，視需要使用它升級您的應用程式。

1.  前往 [執行] > [執行組態]。 在左窗格中，按一下 [等級專案] 左邊的小箭號。
2.  以滑鼠右鍵按一下 **ServiceFabricDeployer**，然後選取 [重複]。 輸入此設定的新名稱，例如，**ServiceFabricUpgrader**。
3.  在右窗格的 引數 索引標籤上，將 **-Pconfig='deploy'** 變更為 **-Pconfig='upgrade'**，然後按一下套用。

此程序會建立並儲存您可以隨時用來升級應用程式的執行組態設定檔。 這也會從應用程式資訊清單檔中取得最新版更新應用程式類型版本的程序。

應用程式升級會花費幾分鐘的時間。 您可以在 Service Fabric Explorer 中監視應用程式升級。

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>移轉要搭配 Maven 使用的舊版 Service Fabric Java 應用程式
我們最近已將 Service Fabric Java 程式庫從 Service Fabric Java SDK 移至 Maven 存放庫。 雖然您使用 Eclipse 產生的新應用程式將產生最新的專案 (能夠搭配 Maven 運作)，但您可以更新現有的 Service Fabric 無狀態或動作項目 Java 應用程式 (先前使用 Service Fabric Java SDK)，以使用 Maven 提供的 Service Fabric Java 相依性。 請遵循[這裡](service-fabric-migrate-old-javaapp-to-use-maven.md)所述的步驟，確保舊版應用程式能搭配 Maven 運作。

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-eclipse/service-fabric-eclipse-plugin.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
