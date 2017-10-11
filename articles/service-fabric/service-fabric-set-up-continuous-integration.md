---
title: "設定 Azure 微服務的連續整合 |Microsoft Docs"
description: "取得如何使用 Visual Studio Team Services (VSTS) 設定 Service Fabric 應用程式之持續整合和部署的概觀。"
services: service-fabric
documentationcenter: na
author: mthalman-msft
manager: timlt
editor: 
ms.assetid: 3e8c2290-9e7a-456a-9b2c-db44d1b3988d
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2016
ms.author: mthalman;mikhegn
ms.openlocfilehash: 76a1e013e824910c7a489e345b6563ae3951378f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="set-up-service-fabric-continuous-integration-and-deployment-with-visual-studio-team-services"></a>使用 Visual Studio Team Services 設定 Service Fabric 連續整合和部署
本文說明使用 Visual Studio Team Services (VSTS) 設定 Azure Service Fabric 應用程式之持續整合和部署的步驟。

這份文件反映目前的程序，而且應該隨著時間而改變。

## <a name="prerequisites"></a>必要條件
若要開始，請遵循下列步驟︰

1. 確定您有 Team Services 帳戶的存取權或 [建立一個](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) 。
2. 確定您有 Team Services 小組專案的存取權或 [建立一個](https://www.visualstudio.com/docs/setup-admin/create-team-project) 。
3. 確定您擁有可部署應用程式的 Service Fabric 叢集，或使用 [Azure 入口網站](service-fabric-cluster-creation-via-portal.md)、[Azure Resource Manager 範本](service-fabric-cluster-creation-via-arm.md)或 [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md) 建立一個。
4. 確定您已經建立 Service Fabric 應用程式 (.sfproj) 專案。 您必須具有使用 Service Fabric SDK 2.1 或更新版本建立或升級的專案 (.sfproj 檔案須包含 1.1 或更高的 ProjectVersion 屬性值)。

> [!NOTE]
> 自訂組建代理程式已不再需要。 Team Services 裝載的代理程式現在都預先安裝 Service Fabric 叢集管理軟體，可讓您直接從這些代理程式部署應用程式。
> 
> 

## <a name="configure-and-share-your-source-files"></a>設定和共用來源檔案
您想要執行的第一步是準備部署程序使用的發行設定檔 (於 Team Services 內執行)。  發行設定檔應設定為以先前準備的叢集為目標。

1. 在您的應用程式專案內選擇您想要使用於持續整合工作流程的發行設定檔。 遵循如何將應用程式發佈至遠端叢集的[發佈指示](service-fabric-publish-app-remote-cluster.md)。 但是，實際上您不需要發佈應用程式。 適當地設定各項後，即可按一下 [發佈] 對話方塊中的 [儲存] 超連結。
2. 如果您想針對 Team Services 中的每個部署升級應用程式，您可以設定發行設定檔以啟用升級。 在步驟 1 使用的同一個 [發行] 對話方塊中，確認已核取 [升級應用程式]  核取方塊。  深入了解 [設定其他升級設定](service-fabric-visualstudio-configure-upgrade.md)。 按一下 [儲存]  超連結，將設定儲存至發行設定檔。
3. 確定您已將變更儲存至發行設定檔，並取消 [發行] 對話方塊。
4. 現在是時候與 Team Services [共用應用程式專案來源檔案](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#vs) 。 當您的來源檔案可以在 Team Services 中存取之後，就可以繼續產生組建的下一個步驟。 

## <a name="create-a-build-definition"></a>建立組建定義
Team Services 組建定義描述由一組循序執行的組建步驟所組成的工作流程。 建立組建定義的目的是要產生 Service Fabric 應用程式套件，以及可用於部署應用程式的其他構件。 深入了解 Team Services [組建定義](https://www.visualstudio.com/docs/build/define/create)。

### <a name="create-a-definition-from-the-build-template"></a>從組建範本建立定義
1. 在 Visual Studio Team Services 中開啟您的小組專案。
2. 選取 [組建]  索引標籤。
3. 選取綠色 **+** 符號以建立新的組建定義。
4. 在開啟的對話方塊中，選取 [組建] 範本類別中的 [Azure Service Fabric 應用程式]。
5. 選取 [下一步] 。
6. 選取和您的 Service Fabric 應用程式關聯的儲存機制和分支 。
7. 選取您想要使用的代理程式佇列。 支援裝載的代理程式。
8. 選取 [ **建立**]。
9. 儲存組建定義並提供名稱。
10. 下一段是範本所產生之組建步驟的說明︰

| 組建步驟 | 說明 |
| --- | --- |
| NuGet 還原 |還原方案的 NuGet 套件。 |
| 組建方案 \*.sln |組建整個方案。 |
| 組建方案 \*.sfproj |產生用來部署應用程式的 Service Fabric 應用程式套件。 應用程式套件位置會指定為組建的構件目錄內。 |
| 更新 Service Fabric 應用程式版本 |更新應用程式套件資訊清單檔案中包含的版本值以提供升級支援。 如需詳細資訊，請參閱 [工作文件頁面](https://go.microsoft.com/fwlink/?LinkId=820529) 。 |
| 複製檔案 |將發行設定檔和應用程式參數檔案複製到組建的構件，以供部署取用。 |
| 發行構件 |發行組建的構件。 可讓發行定義取用組建的構件。 |

### <a name="verify-the-default-set-of-tasks"></a>確認預設工作集
1. 確認 [NuGet 還原] 的 [方案]輸入欄位和 [組建方案] 組建步驟。  根據預設，這些組建步驟會針對相關儲存機制中包含的所有方案檔案執行。  如果您只想在這些方案檔案的其中之一執行組建定義，您需要明確地更新該檔案的路徑。
2. 確認 [封裝應用程式] 組建步驟的 [方案] 輸入欄位。  根據預設，這個組建步驟會假設儲存機制中只有一個 Service Fabric 應用程式專案 (.sfproj)。  如果您的儲存機制中有多個這類檔案，並只想以其中之一作為此組建定義的目標，您需要明確地更新該檔案的路徑。  如果您想將多個應用程式專案封裝在您的儲存機制中，您需要在組建定義中建立分別以一個應用程式專案為目標的額外 **Visual Studio 組建**步驟。  您也需要更新這些組建步驟的 [MSBuild 引數] 欄位，讓它們各自擁有唯一的封裝位置。
3. 確認 **更新 Service Fabric 應用程式版本** 組建步驟中定義的版本控制行為。  根據預設，這個組建步驟會將組建編號附加至應用程式套件資訊清單檔案中的所有版本值。 如需詳細資訊，請參閱 [工作文件頁面](https://go.microsoft.com/fwlink/?LinkId=820529) 。 這有助於支援您的應用程式的升級，因為每個升級部署需要的版本值不同於先前的部署。 如果您不想在工作流程中使用應用程式升級，您可以考慮停用這個組建步驟。 如果您打算產生可用來覆寫現有 Service Fabric 應用程式的組建，則必須予以停用。 如果組建所產生的應用程式版本與叢集中的應用程式版本不相符，則會部署失敗。
4. 如果您的方案包含 .NET Core 專案，則必須確定您的組建定義包含還原相依性的組建步驟：
   1. 選取 [新增組件步驟...] 。
   2. 找出 [公用程式] 索引標籤內的 [命令列] 工作，並按一下其 [新增] 按鈕。
   3. 對於工作的輸入欄位，請使用下列值︰
   4. 工具︰dotnet
   5. 引數︰restore
   6. 拖曳工作，將它就放在 [NuGet 還原]  步驟之後。
5. 儲存對組建定義所做的任何變更。

### <a name="try-it"></a>試試看
選取 [佇列組建]  以手動啟動組建。 推送或簽入時也會觸發組建。 一旦您確認組建順利執行後，就可以繼續定義可將應用程式部署至叢集的發行定義。

## <a name="create-a-release-definition"></a>建立發行定義
Team Services 發行定義描述由一組循序執行的工作所組成的工作流程。 建立發行定義的目的是取得應用程式套件，並將它部署至叢集。 一起使用時，組建定義和發行定義可以執行整個工作流程，從來源檔案開始，並以叢集中的執行中應用程式結束。 深入了解 Team Services [發行定義](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)。

### <a name="create-a-definition-from-the-release-template"></a>從發行範本建立定義
1. 在 Visual Studio Team Services 中開啟您的專案。
2. 選取 [發行]  索引標籤。
3. 選取綠色 **+** 符號以建立新的發行定義，並選取功能表中的 [建立發行定義]。
4. 在開啟的對話方塊中，選取 [部署] 範本類別中的 [Azure Service Fabric 部署]。
5. 選取 [下一步] 。
6. 選取做為這個發行定義的來源的組建定義。  發行定義會參考所選組建定義所產生的構件。
7. 如果您想要讓 Team Services 在每次組建完成時自動建立新的發行並部署 Service Fabric 應用程式，請核取 [連續部署]  核取方塊。
8. 選取您想要使用的代理程式佇列。 支援裝載的代理程式。
9. 選取 [ **建立**]。
10. 按一下頁面頂端的鉛筆圖示以編輯定義名稱。
11. 從工作的 [叢集連線]  輸入欄位中，選取您的應用程式應部署到哪個叢集。 叢集連線提供讓部署工作連線到叢集所需的資訊。 如果您的叢集還沒有叢集連線，請選取欄位旁的 [管理]  超連結新增一個。 在開啟的頁面上執行下列步驟：
    1. 選取 [新增服務端點]，然後從功能表中選取 [Azure Service Fabric]。
    2. 選取此端點的目標叢集所使用的驗證類型。
    3. 在 [連線名稱]  欄位中定義連線的名稱。  一般而言，您可使用您的叢集名稱。
    4. 在 [叢集端點]  欄位中，定義用戶端連線端點 URL。  範例︰tcp://contoso.westus.cloudapp.azure.com:19000。
    5. 對於 Azure Active Directory 認證，請在 [使用者名稱] 和 [密碼] 欄位中定義連線到叢集所需的認證。
    6. 對於憑證式驗證，請在 [用戶端憑證]  欄位中定義用戶端憑證檔案的 Base64 編碼。  如需如何取得該值的資訊，請參閱該欄位的說明快顯。  如果您的憑證受密碼保護，請在 [密碼]  欄位中定義密碼。
    7. 按一下 [確定] 確認變更。 回到發行定義後，按一下 [叢集連線]  欄位上的重新整理圖示以查看您剛才新增的端點。
12. 儲存發行定義。

> [!NOTE]
> Microsoft 帳戶 (例如，@hotmail.com 或 @outlook.com) 不支援 Azure Active Directory 驗證。
> 
> 

建立的定義包含一項工作類型 **Service Fabric 應用程式部署**。 如需此工作的詳細資訊，請參閱 [工作文件頁面](https://go.microsoft.com/fwlink/?LinkId=820528) 。

### <a name="verify-the-template-defaults"></a>確認範本預設值
1. 確認 [部署 Service Fabric 應用程式] 工作的 [發行設定檔] 輸入欄位。 根據預設，此欄位會參考組建的構件中包含的發行設定檔 (名為 Cloud.xml)。 如果您想要參考不同的發行設定檔，或如果組建的構件中包含多個應用程式套件，您必須適當地更新路徑。
2. 確認 [部署 Service Fabric 應用程式] 工作的 [應用程式套件] 輸入欄位。 根據預設，此欄位會參考組建定義範本中使用的預設應用程式套件路徑。  如果您已經修改組建定義中的預設應用程式套件路徑，您也必須在此適當地更新路徑。

### <a name="try-it"></a>試試看
從 [發行] 按鈕功能表上選取 [建立發行] 以手動建立發行。 在開啟的對話方塊中，選取做為發行基準的組建，然後按一下 [建立] 。 如果您啟用連續部署，當關聯的組建定義完成組建時，便會自動建立發行。

## <a name="next-steps"></a>後續步驟
若要深入了解與 Service Fabric 應用程式的連續整合，請閱讀下列文章：

* [Team Services 文件首頁](https://www.visualstudio.com/docs/overview)
* [Team Services 中的組建管理](https://www.visualstudio.com/docs/build/overview)
* [Team Services 中的發行管理](https://www.visualstudio.com/docs/release/overview)

