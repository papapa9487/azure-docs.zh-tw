---
title: "在 Azure 入口網站中建立 Service Fabric 叢集 | Microsoft Docs"
description: "本文說明如何使用 Azure 入口網站和 Azure 金鑰保存庫在 Azure 中建立安全的 Service Fabric 叢集。"
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: vturecek
ms.assetid: 426c3d13-127a-49eb-a54c-6bde7c87a83b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/21/2017
ms.author: chackdan
ms.openlocfilehash: 3dd4f3494bb9ed70549f41e22c58666cada8da07
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>使用 Azure 入口網站在 Azure 中建立 Service Fabric 叢集
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure 入口網站](service-fabric-cluster-creation-via-portal.md)
> 
> 

這是一個逐步指南，可逐步引導您使用 Azure 入口網站在 Azure 中設定安全的 Service Fabric 叢集。 本指南將逐步引導您完成下列步驟：

* 設定金鑰保存庫來管理叢集安全性的金鑰。
* 透過 Azure 入口網站在 Azure 中建立安全的叢集。
* 使用憑證驗證系統管理員。

> [!NOTE]
> 如需更進階的安全性選項 (例如使用 Azure Active Directory 進行使用者驗證及設定應用程式安全性的憑證)，請參閱[使用 Azure Resource Manager 建立您的叢集][create-cluster-arm]。
> 
> 

安全的叢集是可以防止未經授權存取管理作業的叢集，那些作業包括部署、升級及刪除應用程式、服務和它們包含的資料。 不安全的叢集是任何人都可以隨時連線並執行管理作業的叢集。 雖然可以建立不安全的叢集，但 **強烈建議您建立安全的叢集**。 不安全的叢集 **無法在事後保護其安全** - 必須建立新的叢集。

不論叢集是 Linux 叢集或 Windows 叢集，建立安全叢集的概念都一樣。 如需建立安全 Linux 叢集的詳細資訊和協助程式指令碼，請參閱[在 Linux 上建立安全叢集](service-fabric-cluster-creation-via-arm.md#secure-linux-clusters)。 由所提供的協助程式指令碼所取得的參數可以直接輸入到入口網站，如[在 Azure 入口網站中建立叢集](#create-cluster-portal)一節所述。

## <a name="log-in-to-azure"></a>登入 Azure
本指南使用 [Azure PowerShell][azure-powershell]。 開始新的 PowerShell 工作階段時，請先登入您的 Azure 帳戶並選取您的訂用帳戶，然後再執行 Azure 命令。

登入您的 Azure 帳戶：

```powershell
Login-AzureRmAccount
```

選取您的訂用帳戶：

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-key-vault"></a>設定金鑰保存庫
這部分的指南將逐步引導您為 Azure 中的 Service Fabric 叢集和為 Service Fabric 應用程式建立金鑰保存庫。 如需 Key Vault 的完整指引，請參閱 [Key Vault入門指南][key-vault-get-started]。

Service Fabric 會使用 X.509 憑證來保護叢集。 Azure 金鑰保存庫可用來管理 Azure 中 Service Fabric 叢集的憑證。 在 Azure 中部署叢集時，負責建立 Service Fabric 叢集的 Azure 資源提供者會從金鑰保存庫提取憑證，並將它們安裝在叢集 VM 上。

下圖說明金鑰保存庫、Service Fabric 叢集，以及 Azure 資源提供者 (會在建立叢集時使用金鑰保存庫中所存憑證) 之間的關係：

![憑證安裝][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>建立資源群組
第一個步驟是為金鑰保存庫建立一個專用的新資源群組。 建議將金鑰保存庫放入它自己的資源群組，您就可以移除計算和儲存體資源群組 (例如擁有您 Service Fabric 叢集的資源群組) 而不會遺失您的金鑰和密碼。 擁有您金鑰保存庫的資源群組必須和正在使用它的叢集位於相同區域。

```powershell

    PS C:\Users\vturecek> New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet will be modified in a future release.

    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### <a name="create-key-vault"></a>建立金鑰保存庫
在新的資源群組中建立金鑰保存庫。 金鑰保存庫 **必須啟用以用於部署** ，才能讓 Service Fabric 資源提供者從中取得憑證並安裝在叢集節點上：

```powershell

    PS C:\Users\vturecek> New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment


    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all


    Tags                             :
```

如果您有現有金鑰保存庫，可以使用 Azure CLI 將它啟用以用於部署：

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```


## <a name="add-certificates-to-key-vault"></a>新增憑證至金鑰保存庫
憑證是在 Service Fabric 中用來提供驗證與加密，以保護叢集和其應用程式的各個層面。 如需如何在 Service Fabric 中使用憑證的詳細資訊，請參閱 [Service Fabric 叢集安全性案例][service-fabric-cluster-security]。

### <a name="cluster-and-server-certificate-required"></a>叢集和伺服器憑證 (必要)
需要此憑證來保護叢集安全及防止未經授權存取叢集。 它會透過幾種方式提供叢集安全性：

* **叢集驗證：** 驗證叢集同盟的節點對節點通訊。 只有可使用此憑證提供其身分識別的節點可以加入叢集。
* **伺服器驗證：** 向管理用戶端驗證叢集管理端點，管理用戶端就能知道它正在交談的對象是真正的叢集。 此憑證也會為 HTTPS 管理 API，以及為透過 HTTPS 使用的 Service Fabric Explorer 提供 SSL。

為用於這些用途，憑證必須符合下列要求：

* 憑證必須包含私密金鑰。
* 憑證必須是為了進行金鑰交換而建立，且可匯出成個人資訊交換檔 (.pfx)。
* 憑證的主體名稱必須符合用來存取 Service Fabric 叢集的網域。 這是必要的，以便為叢集的 HTTPS 管理端點和 Service Fabric Explorer 提供 SSL。 您無法向憑證授權單位 (CA) 取得 `.cloudapp.azure.com` 網域的 SSL 憑證。 為您的叢集取得自訂網域名稱。 當您向 CA 要求憑證時，憑證的主體名稱必須符合用於您叢集的自訂網域名稱。

### <a name="client-authentication-certificates"></a>用戶端驗證憑證
其他用戶端憑證會驗證系統管理員以執行叢集管理工作。 Service Fabric 有兩個存取層級：[系統管理員] 和 [唯讀使用者]。 您至少應使用一個單一憑證以用於進行系統管理存取。 若要進行其他使用者層級存取，則必須提供個別憑證。 如需存取角色的詳細資訊，請參閱[角色型存取控制 (適用於 Service Fabric 用戶端)][service-fabric-cluster-security-roles]。

若要使用 Service Fabric，您並不需要將用戶端驗證憑證上傳至金鑰保存庫。 這些憑證只需要提供給獲得授權來管理叢集的使用者。 

> [!NOTE]
> 建議使用 Azure Active Directory 驗證用戶端以執行叢集管理作業。 若要使用 Azure Active Directory，您必須[使用 Azure Resource Manager 建立叢集][create-cluster-arm]。
> 
> 

### <a name="application-certificates-optional"></a>應用程式憑證 (選用)
您可以針對應用程式安全性目的，在叢集上安裝任何數目的其他憑證。 在建立您的叢集之前，請考量需要在節點上安裝憑證的應用程式安全性案例，例如：

* 加密和解密應用程式組態值
* 在複寫期間跨節點加密資料 

透過 Azure 入口網站建立叢集時無法設定應用程式憑證。 若要在建立叢集時設定應用程式憑證，您必須[使用 Azure Resource Manager 建立叢集][create-cluster-arm]。 您也可以在建立叢集之後將應用程式憑證新增到叢集。

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>設定憑證格式以供 Azure 資源提供者使用
私密金鑰檔案 (.pfx) 可以直接透過金鑰保存庫來新增及使用。 但是，Azure 資源提供者需要以特殊 JSON 格式儲存金鑰，該格式包含 .pfx 作為 Base-64 編碼字串和私密金鑰密碼。 為符合這些要求，金鑰必須放入 JSON 字串中，然後在金鑰保存庫中儲存為密碼  。

若要讓這個程序更容易，可使用 PowerShell 模組 ([GitHub 上有提供][service-fabric-rp-helpers])。 請依照這些步驟使用模組：

1. 將儲存機制的完整內容下載到本機目錄中。 
2. 在您的 PowerShell 視窗中匯入模組：

```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

此 PowerShell 模組中的 `Invoke-AddCertToKeyVault` 命令會自動將憑證私密金鑰的格式設定為 JSON 字串，並將它上傳到金鑰保存庫。 請用它來將叢集憑證與任何其他應用程式憑證新增到金鑰保存庫。 請為您想在叢集中安裝的任何其他憑證重複這個步驟。

```powershell
PS C:\Users\vturecek> Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet will be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault


Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

這些是設定 Service Fabric 叢集 Resource Manager 範本時的所有金鑰保存庫必要條件，該範本會安裝用於節點驗證、管理端點安全性和驗證，以及使用 X.509 憑證的任何其他應用程式安全性功能的憑證。 此時，您應該已經在 Azure 中建立以下項目：

* 金鑰保存庫資源群組
  * 金鑰保存庫
    * 叢集伺服器驗證憑證

</a "create-cluster-portal" ></a>

## <a name="create-cluster-in-the-azure-portal"></a>在 Azure 入口網站中建立叢集
### <a name="search-for-the-service-fabric-cluster-resource"></a>搜尋 Service Fabric 叢集資源
![在 Azure 入口網站上搜尋 Service Fabric 叢集範本。][SearchforServiceFabricClusterTemplate]

1. 登入 [Azure 入口網站][azure-portal]。
2. 按一下 [新增]  來新增資源範本。 在 [全部內容] 下方的 [Marketplace] 中搜尋 Service Fabric 叢集範本。
3. 選取清單中的 [Service Fabric 叢集]  。
4. 瀏覽至 [Service Fabric 叢集] 刀鋒視窗，按一下 [建立]，
5. [建立 Service Fabric 叢集]  刀鋒視窗具有下列四個步驟。

#### <a name="1-basics"></a>1.基本概念
![建立新資源群組的螢幕擷取畫面。][CreateRG]

在 [基本] 刀鋒視窗中，您必須提供您的叢集的基本詳細資料。

1. 輸入您的叢集名稱。
2. 輸入 VM 遠端桌面的 [使用者名稱] 和 [密碼]。
3. 請務必選取您要部署叢集的 [訂用帳戶]  ，尤其是在您擁有多個訂用帳戶時。
4. 建立 **新的資源群組**。 最好讓它與叢集同名，因為這有助於稍後尋找它們，尤其是當您嘗試變更您的部署及刪除您的叢集時，特別有用。
   
   > [!NOTE]
   > 雖然您可以決定使用現有的資源群組，但最好還是建立新的資源群組。 這可讓您輕鬆地刪除您不需要的叢集。
   > 
   > 
5. 選取您要在其中建立叢集的 [區域] 。 您必須使用金鑰保存庫所在的相同區域。

#### <a name="2-cluster-configuration"></a>2.叢集組態
![建立節點類型][CreateNodeType]

設定您的叢集節點。 可用來定義定義 VM 的大小、VM 的數目，以及 VM 的屬性。 您的叢集可以有多個節點類型，但主要節點類型 (您在入口網站定義的第一個節點類型) 必須至少有 5 個 VM。這是 Service Fabric 系統服務放置所在的節點類型。 請勿設定 [放置屬性]，因為會自動新增 "NodeTypeName" 預設放置屬性。

> [!NOTE]
> 多個節點類型的常見案例是包含前端服務和後端服務的應用程式。 您想要將「前端」服務放在連接埠對網際網路開放的較小型 VM (D2 等 VM 大小) 上，但想要將「後端」服務放在沒有對網際網路開放連接埠的較大型 VM (D4、D6、D15 等 VM 大小) 上。
> 
> 

1. 選擇節點類型的名稱 (1 到 12 個字元，只能包含字母和數字)。
2. 主要節點類型的 VM **大小**下限取決於您為叢集選擇的**持久性**層級。 持久性層級的預設值為 Bronze。 如需持久性的詳細資訊，請參閱[如何選擇 Service Fabric 叢集可靠性和持久性][service-fabric-cluster-capacity]。
3. 選取 VM 大小和定價層。 D 系列 VM 擁有 SSD 磁碟機，且強烈建議用於具狀態應用程式。 請勿使用只有部分核心或可用磁碟容量少於 7 GB 的任何 VM SKU。 
4. 主要節點類型的 VM **數目**下限取決於您選擇的**可靠性**層級。 可靠性層級的預設值為 Silver。 如需可靠性的詳細資訊，請參閱[如何選擇 Service Fabric 叢集可靠性和持久性][service-fabric-cluster-capacity]。
5. 選擇節點類型的 VM 數目。 您可以在稍後相應增加或相應減少節點類型中的 VM 數目，但在主要節點類型上，數目下限取決於您選擇的可靠性層級。 其他節點類型可以有 1 個 VM 的下限。
6. 設定自訂端點。 此欄位可讓您輸入以逗號區隔的連接埠清單，您可以透過 Azure Load Balancer 針對您的應用程式向公用網際網路公開這些連接埠。 例如，如果您計劃對您的叢集部署 Web 應用程式，請在這裡輸入「80」來允許連接埠 80 的流量進入您的叢集。 如需端點的詳細資訊，請參閱[與應用程式通訊][service-fabric-connect-and-communicate-with-services]
7. 設定叢集**診斷**。 預設會在您的叢集上啟用診斷功能，以協助排解疑難問題。 如果您要停用診斷，請將其 [狀態] 切換至 [關閉]。 **不**建議將診斷關閉。
8. 選取您想要為叢集設定的 Fabric 升級模式。 如果您要讓系統自動挑選最新可用的版本，並嘗試將叢集升級到此版本，請選取 [自動] 。 如果您想要選擇支援的版本，將模式設定為 [手動] 。

> [!NOTE]
> 我們支援的叢集限於執行支援的 Service Fabric 版本。 如果選取 [手動]  模式，您必須負責將叢集升級到支援的版本。 如需 Fabric 升級模式的詳細資訊，請參閱 [service-fabric-cluster-upgrade 文件][service-fabric-cluster-upgrade]。
> 
> 

#### <a name="3-security"></a>3.安全性
![Azure 入口網站中安全性組態的螢幕擷取畫面。][SecurityConfigs]

最後一個步驟是使用金鑰保存庫和稍早建立的憑證資訊，提供憑證資訊來保護叢集。

* 將使用 **PowerShell 命令把** 叢集憑證 `Invoke-AddCertToKeyVault` 上傳至金鑰保存庫時取得的輸出填入主要憑證欄位。

```powershell
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47
```

* 選取 [設定進階設定] 核取方塊來輸入**系統管理用戶端**和**唯讀用戶端**的用戶端憑證。 在這些欄位中，輸入系統管理用戶端憑證的指紋和唯讀使用者用戶端憑證的指紋 (如果適用)。 當系統管理員嘗試連線叢集時，只有在他們的憑證指紋和這裡輸入的指紋值相符時，才會被授與存取權。  

#### <a name="4-summary"></a>4.摘要

若要完成叢集建立程序，請按一下 [摘要]  來查看您提供的組態，或是下載用來部署叢集的 Azure Resource Manager 範本。 在您提供必要的設定之後，[確定]  按鈕會變成綠色，您只要按一下該按鈕就可以啟動叢集建立程序。

您可以在通知功能中看到叢集的建立進度。 (請按一下畫面右上角狀態列附近的鈴噹圖示。)如果您在建立叢集時按了 [釘選到「開始面板」]，您將會看到 [部署 Service Fabric 叢集] 已釘選到 [開始] 面板。

### <a name="view-your-cluster-status"></a>檢視叢集狀態
![顯示叢集詳細資料的儀表板螢幕擷取畫面。][ClusterDashboard]

建立叢集之後，您就可以在入口網站檢查您的叢集：

1. 移至 [瀏覽]，然後按一下 [Service Fabric 叢集]。
2. 找出您的叢集，然後按一下它。
3. 現在儀表板會顯示叢集的詳細資料，包括叢集的公用端點和 Service Fabric Explorer 的連結。

叢集之儀表板刀鋒視窗上的 [節點監視器]  區段會指出健康狀態良好和不良的 VM 數目。 如需進一步了解叢集健康狀態，請參閱 [Service Fabric 健康狀態模型簡介][service-fabric-health-introduction]。

> [!NOTE]
> Service Fabric 叢集需要有一定數量的節點保持運作中，以維護可用性並維持狀態 - 稱為「維持仲裁」。 因此，除非您已先執行[狀態的完整備份][service-fabric-reliable-services-backup-restore]，否則關閉叢集中的所有電腦通常並不安全。
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>遠端連接到虛擬機器擴展集執行個體或叢集節點
您在叢集中指定的每個 NodeTypes 都會形成虛擬機器擴展集。 <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>後續步驟
此時，您擁有一個使用憑證來管理驗證的安全叢集。 接下來，請[連線到您的叢集](service-fabric-connect-to-secure-cluster.md)並了解如何[管理應用程式密碼](service-fabric-application-secret-management.md)。  同時，了解 [Service Fabric 支援選項](service-fabric-support.md)。

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-get-started.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
<!--[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md#remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node -->
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
