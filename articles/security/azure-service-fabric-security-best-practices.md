---
title: "Azure Service Fabric 安全性最佳做法 | Microsoft Docs"
description: "本文提供一組 Azure Service Fabric 安全性的最佳做法。"
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 3132e42aac72c0132c7526ac56d80bc5eec269e7
ms.contentlocale: zh-tw
ms.lasthandoff: 08/09/2017

---
# <a name="azure-service-fabric-security-best-practices"></a>Azure Service Fabric 安全性最佳做法
在 Azure 上部署應用程式很快速、輕鬆且符合成本效益。 在生產環境中部署雲端應用程式之前，針對基本和建議的最佳做法清單，取得最佳做法以協助評估應用程式是很有幫助的。

Azure Service Fabric 是分散式系統平台，可讓您輕鬆封裝、部署及管理可調整和可信賴的微服務。 Service Fabric 也可解決開發與管理雲端應用程式時遭遇的重大挑戰。 開發人員與管理員能夠避免複雜的基礎結構問題，專注於實作關鍵且嚴格要求之可調整、可信賴且可管理的工作負載。 

針對每個最佳做法，我們會說明︰

-   最佳作法是什麼
-   您為何想要啟用該最佳作法
-   如果無法啟用最佳作法，結果可能為何
-   如何學習啟用最佳作法

我們目前有下列 Azure Service Fabric 安全性最佳做法：

-   使用 Azure Resource Manager (ARM) 範本和 Azure Service Fabric PowerShell 模組來建立安全的叢集
-   使用 X.509 憑證
-   設定安全性原則
-   Reliable Actors 安全性設定
-   設定 Azure Service Fabric 的 SSL
-   Azure Service Fabric 的網路隔離/安全性
-   設定金鑰保存庫以提供安全性
-   將使用者指派給角色


## <a name="best-practices-for-securing-your-cluster"></a>保護叢集的最佳做法

**概觀**

一律使用安全的叢集
-   叢集安全性 - 使用憑證
-   用戶端存取 (系統管理員和唯讀) - 使用 AAD

使用自動化部署
-   使用指令碼來產生、部署及變換密碼
-   將密碼保存在 KV 中，使用 AD 進行所有其他用戶端存取
-   未經驗證的任何人都不應該進行存取。

此外，請考慮下列作業：
-   使用網路安全性群組 (NSG) 建立 DMZ
-   使用跳躍伺服器 (Jump Server) 透過 RDP 連線到叢集 VM 或管理叢集

叢集必須受到安全保護，以防止未經授權使用者連線您的叢集，特別是當叢集上有生產工作負載正在執行時。 雖然可以建立不安全的叢集，但這樣做會在叢集向公用網際網路公開管理端點時，允許匿名使用者連線叢集。

用來實作這些案例的技術。 [叢集安全性案例](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)包括：

-   節點對節點安全性 - 這會保護 VM 與叢集中電腦之間的通訊。 這可確保只有獲得授權加入叢集的電腦可以參與裝載應用程式和叢集中的服務。
在 Azure 上執行的叢集或在 Windows 上執行的獨立叢集可以使用[憑證安全性](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)或針對 Windows Server 機器使用 [Windows 安全性](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-windows-security)。
-   用戶端對節點安全性 - 這會保護 Service Fabric 用戶端與叢集中個別節點之間的通訊。
-   角色型存取控制 (RBAC) - 您可以在建立叢集時，分別為系統管理員和使用者用戶端角色提供個別的身分識別 (憑證、AAD 等等)，來指定這兩個角色。
-   安全性建議 - 對於 Azure 叢集，建議您針對節點對節點安全性使用 AAD 安全性來驗證用戶端和憑證。

若要設定獨立 Windows 叢集，請參閱[設定獨立 Windows 叢集的設定](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest)。

使用 Azure Resource Manager 範本和 Azure Service Fabric PowerShell 模組來建立安全的叢集。
[此處](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)提供的逐步指南可逐步引導您使用 Azure Resource Manager 在 Azure 中設定安全的 Azure Service Fabric 叢集。

使用 Azure Resource Manager 範本來自訂叢集
-   設定 VM VHD 的受管理儲存體

使用 Azure Resource Manager 範本來驅動資源群組的變更
-   輕鬆進行設定管理
-   稽核

將叢集設定視為程式碼
-   徹底檢查您選擇部署的設定
-   避免使用隱含命令直接調校您的資源

[Service Fabric 應用程式生命週期](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) 的許多層面都可以自動化。 [Azure Service Fabric PowerShell 模組](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package)會將部署、升級、移除和測試 Azure Service Fabric 應用程式的常見工作自動化。 也可使用應用程式管理的 Managed 和 HTTP API。

## <a name="use-x509-certificates"></a>使用 X.509 憑證
請務必使用 X.509 憑證或 Windows 安全性來保護叢集。 只有在建立叢集時才會設定安全性，而且不可能在叢集建立之後啟用安全性。

如果您指定[叢集憑證](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)，請將 ClusterCredentialType 的值設定為 X509。 如果您指定外部連線的伺服器憑證，請將 ServerCredentialType 設定為 X509。

-   執行生產環境工作負載之叢集中所使用的憑證應該是使用已正確設定的 Windows Server 憑證服務來建立，或是從已核准的憑證授權單位 (CA) 取得。
-   絕對不要在生產環境中使用以 MakeCert.exe 這類工具建立的暫時或測試憑證。
-   您可以使用自我簽署憑證，但應該只用於測試叢集，不應該在生產環境中使用。

如果叢集不安全。 任何人都可以匿名方式連線並執行管理作業，所以一律要使用 X.509 憑證或 Windows 安全性來保護生產叢集。

若要深入了解如何在 Service Fabric 叢集中啟用憑證，請參閱[新增或移除 Service Fabric 叢集的憑證](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure)。

## <a name="configure-security-policies"></a>設定安全性原則
在以該使用者帳戶部署時，Service Fabric 也會協助保護應用程式所使用的資源，例如檔案、目錄和憑證。 如此一來，即使在共用主控環境中，執行中的應用程式就不會彼此干擾。

-   使用 Active Directory 網域群組或使用者：您可以根據 Active Directory 使用者或群組帳戶的認證來執行服務。 這是在您網域內的內部部署 Active Directory，與 Azure Active Directory (Azure AD) 無關。 然後，您可以使用網域使用者或群組，存取網域中已經被授與權限的其他資源 (例如檔案共用)。

-   為 HTTP 和 HTTPS 端點指派安全性存取原則：如果您將 RunAs 原則套用到服務，而且服務資訊清單宣告具有 HTTP 通訊協定的端點資源，則您必須指定 SecurityAccessPolicy，以確保配置給這些端點的連接埠都已針對用以執行服務的 RunAs 使用者帳戶，正確列入存取控制清單中。 否則，http.sys 無法存取服務，而且您從用戶端呼叫時將會失敗。
若要深入了解如何在 Service Fabric 中啟用安全性原則，請參閱[設定應用程式的安全性原則](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security)。

## <a name="reliable-actors-security-configuration"></a>Reliable Actors 安全性設定
Service Fabric Reliable Actors 是動作項目設計模式的實作。 如同任何軟體設計模式，是否要使用特定模式的決策是根據軟體設計問題是否符合模式來決定。

做為一般指導方針，請在符合下列情況時，考慮使用動作項目模式來建立您的問題或案例模型︰
-   您的問題領域涉及大量 (數千個或更多) 小型、獨立且隔離的狀態和邏輯單位。
-   您想要使用單一執行緒的物件，這類物件不需要與外部元件進行顯著的互動，包括跨一組動作項目查詢狀態。
-   您的動作項目執行個體將不會藉由發出 I/O 作業，使用無法預期的延遲來封鎖呼叫端。

在 Service Fabric 中，動作項目是在 Reliable Actors 架構中實作的︰以動作項目模式為基礎的應用程式架構是建置於 [Service Fabric Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction)的頂端。 您撰寫的每個 Reliable Actor 服務實際上都是已資料分割的具狀態可靠服務。
每個動作項目都會定義為動作項目類型的執行個體，與 .NET 物件是 .NET 類型的執行個體的方式完全相同。 例如，有的動作項目類型會實作計算機的功能，而該類型會有許多動作項目分散到叢集的各種節點上。 每一個這類的動作項目都有唯一的動作項目識別碼識別。

[複寫器安全性設定](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration)用來保護在複寫期間使用的通訊通道。 這表示服務將無法看到彼此的複寫流量，並且也會確保高度可用資料的安全。 依預設，空白的安全性組態區段會妨礙複寫安全性。
複寫器組態會設定負責讓動作項目狀態提供者狀態高度可靠的複寫器。

## <a name="configure-ssl-for-azure-service-fabric"></a>設定 Azure Service Fabric 的 SSL

伺服器驗證：向管理用戶端[驗證](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)叢集管理端點，管理用戶端就能知道它正在交談的對象是真正的叢集。 此憑證也會為 HTTPS 管理 API 及透過 HTTPS 使用的 Service Fabric Explorer 提供 [SSL](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm)。
您必須為您的叢集取得自訂網域名稱。 當您向 CA 要求憑證時，憑證的主體名稱必須與用於您叢集的自訂網域名稱相符。

若要設定應用程式的 SSL，首先您需要取得已由憑證授權單位 (CA) (專門核發憑證的受信任第三方) 簽署的 SSL 憑證。 如果您還沒有此類憑證，則必須向銷售 SSL 憑證的公司取得。

憑證必須符合 Azure 中對於 SSL 憑證的下列要求：
-   憑證必須包含私密金鑰。
-   憑證必須是為了進行金鑰交換而建立，且可匯出成個人資訊交換檔 (.pfx)。
-   憑證的主體名稱必須符合用來存取雲端服務的網域。 您無法向憑證授權單位 (CA) 取得 cloudapp.net 網域的 SSL 憑證。 您必須取得要在存取您的服務時使用的自訂網域名稱。 當您向 CA 要求憑證時，憑證的主體名稱必須符合用來存取應用程式的自訂網域名稱。 例如，如果您的自訂網域名稱為 contoso.com，則您需向 CA 要求 **.contoso.com** 或 **www.contoso.com.** 憑證。
-   憑證至少必須以 2048 位元加密。

HTTP 不安全且容易受到竊聽攻擊，因為要從網頁瀏覽器轉送到 Web 伺服器或在其他端點之間轉送的資料，會以純文字傳送。 這表示攻擊者可以攔截並檢視敏感性資料，例如信用卡詳細資料和帳戶登入。 當資料使用 HTTPS 透過瀏覽器傳送或張貼時，SSL 可確保這類資訊經過加密及保護以避免遭到攔截。

如需詳細資訊，請參閱[設定 Azure 應用程式的 SSL](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate)。

## <a name="network-isolationsecurity-with-azure-service-fabric"></a>Azure Service Fabric 的網路隔離/安全性
使用 [Azure Resource Manager (ARM) 範本](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)作為設定三個節點類型安全叢集的範例，並透過網路安全性群組來控制輸入和輸出網路流量。

此範本針對每個虛擬機器擴展集 (VMSS) 各有一個網路安全性群組，可控制進出 VMSS 的流量。 預設會設定規則，允許範本中指定的系統服務和應用程式連接埠所需的所有流量。 請檢閱這些規則並進行變更以符合您的需求，包括為您的應用程式新增任何規則。

如需詳細資訊，請參閱 [Azure Service Fabric - 常見網路案例](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking)。

## <a name="set-up-a-key-vault-for-security"></a>設定金鑰保存庫以提供安全性
憑證是在 Service Fabric 中用來提供驗證與加密，以保護叢集和其應用程式的各個層面。

Service Fabric 會使用 X.509 憑證來保護叢集，並提供應用程式的安全性功能。 您可以使用 Key Vault 來[管理 Azure 中 Service Fabric 叢集的憑證](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure)。 在 Azure 中部署叢集時，負責建立 Service Fabric 叢集的 Azure 資源提供者會從 Key Vault 提取憑證，然後將它們安裝在叢集 VM 上。

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)、Service Fabric 叢集及 Azure 資源提供者之間的關係是在建立叢集時，使用金鑰保存庫中所儲存的憑證。

**建立資源群組**：第一個步驟是特別針對金鑰保存庫建立資源群組。 建議您將 Key Vault 放入其自己的資源群組中。 此動作可讓您移除計算和儲存體資源群組 (包括含有 Service Fabric 叢集的資源群組)，而不會遺失您的金鑰和密碼。 含有您金鑰保存庫的資源群組必須與正在使用它的叢集位於相同區域。

**在新的資源群組中建立金鑰保存庫**：金鑰保存庫必須經啟用為可供部署使用，才能讓計算資源提供者從它取得憑證，然後將它安裝在虛擬機器執行個體上。
若要深入了解如何設定 Azure Key Vault，請參閱[開始使用 Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)。

## <a name="assign-users-roles"></a>對使用者指派角色
建立應用程式來代表您的叢集之後，請將使用者指派給 Service Fabric 所支援的角色︰唯讀和系統管理員。 您可以使用 Azure 傳統入口網站來指派角色。

>[!Note]
> 如需 Service Fabric 中角色的詳細資訊，請參閱 [角色型存取控制 (適用於 Service Fabric 用戶端)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles)。

Azure Service Fabric 針對連線到 [Service Fabric 叢集](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)的用戶端，支援兩種不同的存取控制類型：系統管理員和使用者。 存取控制可讓叢集系統管理員針對不同的使用者群組限制特定叢集作業的存取權，讓叢集更加安全。

## <a name="next-steps"></a>後續步驟
- 設定 Service Fabric [開發環境](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)。
- 了解 [Service Fabric 支援選項](https://docs.microsoft.com/azure/service-fabric/service-fabric-support)。


