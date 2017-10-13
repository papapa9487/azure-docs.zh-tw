---
title: "從範本建立 Azure Service Fabric 叢集 | Microsoft Docs"
description: "本文說明如何使用 Azure Resource Manager、Azure Key Vault 及 Azure Active Directory (Azure AD) 來進行用戶端驗證，在 Azure 中設定安全的 Service Fabric 叢集。"
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: chackdan
ms.openlocfilehash: 47152d05eb7e31e7fe1f35e33a10fe8e903e21e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>使用 Azure Resource Manager 來建立 Service Fabric 叢集
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure 入口網站](service-fabric-cluster-creation-via-portal.md)
>
>

此逐步指南可逐步引導您使用 Azure Resource Manager 在 Azure 中設定安全的 Azure Service Fabric 叢集。 我們承認本文很長。 不過，除非您已經徹底熟悉內容，否則請務必仔細遵循每個步驟。

本指南涵蓋下列程序：

* 設定 Azure Key Vault 以上傳叢集和應用程式安全性的憑證
* 使用 Azure Resource Manager 在 Azure 中建立受保護的叢集
* 使用適用於叢集管理的 Azure Active Directory (Azure AD) 來驗證使用者

安全的叢集是可防止以未經授權的方式存取管理作業的叢集。 這包括部署、升級和刪除應用程式、服務及其包含的資料。 不安全的叢集是任何人都可以隨時連線並執行管理作業的叢集。 雖然可以建立不安全的叢集，但強烈建議您從一開始就建立安全的叢集。 由於無法在稍後再針對不安全的叢集進行保護，因此必須建立新叢集。

不論是 Linux 叢集還是 Windows 叢集，建立安全叢集的概念都是相同的。 如需建立安全 Linux 叢集的詳細資訊和協助程式指令碼，請參閱[在 Linux 上建立安全叢集](#secure-linux-clusters)。

## <a name="sign-in-to-your-azure-account"></a>登入您的 Azure 帳戶
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

## <a name="set-up-a-key-vault"></a>設定 Key Vault
本節說明如何為 Azure 中的 Service Fabric 叢集和為 Service Fabric 應用程式建立 Key Vault。 如需 Azure Key Vault 的完整指南，請參閱 [Key Vault 入門指南][key-vault-get-started]。

Service Fabric 會使用 X.509 憑證來保護叢集，並提供應用程式的安全性功能。 您可以使用 Key Vault 來管理 Azure 中 Service Fabric 叢集的憑證。 在 Azure 中部署叢集時，負責建立 Service Fabric 叢集的 Azure 資源提供者會從 Key Vault 提取憑證，然後將它們安裝在叢集 VM 上。

下圖說明 Azure Key Vault、Service Fabric 叢集及 Azure 資源提供者 (會在建立叢集時使用 Key Vault 中所存憑證) 之間的關係：

![憑證安裝圖][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>建立資源群組
第一個步驟是特別針對 Key Vault 建立資源群組。 建議您將 Key Vault 放入其自己的資源群組中。 此動作可讓您移除計算和儲存體資源群組 (包括含有 Service Fabric 叢集的資源群組)，而不會遺失您的金鑰和密碼。 含有您 Key Vault 的資源群組必須與正在使用它的叢集位於「相同區域」。

如果您打算在多個區域中部署叢集，建議您在命名資源群組和 Key Vault 時，使用能指出其所屬區域的方式。  

```powershell

    New-AzureRmResourceGroup -Name westus-mykeyvault -Location 'West US'
```
輸出應該會看起來如下：

```powershell

    WARNING: The output object type of this cmdlet is going to be modified in a future release.

    ResourceGroupName : westus-mykeyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/westus-mykeyvault

```
<a id="new-key-vault"></a>

### <a name="create-a-key-vault-in-the-new-resource-group"></a>在新的資源群組中建立 Key Vault
Key Vault 「必須經啟用為可供部署使用」，才能讓計算資源提供者從它取得憑證，然後將它安裝在虛擬機器執行個體上：

```powershell

    New-AzureRmKeyVault -VaultName 'mywestusvault' -ResourceGroupName 'westus-mykeyvault' -Location 'West US' -EnabledForDeployment

```

輸出應該會看起來如下：

```powershell

    Vault Name                       : mywestusvault
    Resource Group Name              : westus-mykeyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault
    Vault URI                        : https://mywestusvault.vault.azure.net
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
<a id="existing-key-vault"></a>

## <a name="use-an-existing-key-vault"></a>使用現有的 Key Vault

若要使用現有的 Key Vault，您「必須將它啟用為可供部署使用」，才能讓計算資源提供者從它取得憑證，然後將它安裝在叢集節點上：

```powershell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

```

<a id="add-certificate-to-key-vault"></a>

## <a name="add-certificates-to-your-key-vault"></a>將憑證新增至 Key Vault

憑證是在 Service Fabric 中用來提供驗證與加密，以保護叢集和其應用程式的各個層面。 如需如何在 Service Fabric 中使用憑證的詳細資訊，請參閱 [Service Fabric 叢集安全性案例][service-fabric-cluster-security]。

### <a name="cluster-and-server-certificate-required"></a>叢集和伺服器憑證 (必要)
需要此憑證來保護叢集安全及防止未經授權存取叢集。 它會透過兩種方式提供叢集安全性：

* 叢集驗證：驗證叢集同盟的節點對節點通訊。 只有可使用此憑證提供其身分識別的節點可以加入叢集。
* 伺服器驗證：向管理用戶端驗證叢集管理端點，讓管理用戶端知道正在交談的對象是真正的叢集。 此憑證也會為 HTTPS 管理 API 及透過 HTTPS 使用的 Service Fabric Explorer 提供 SSL。

為用於這些用途，憑證必須符合下列要求：

* 憑證必須包含私密金鑰。
* 憑證必須是為了進行金鑰交換而建立，且可匯出成個人資訊交換 (.pfx) 檔案。
* 憑證的主體名稱必須與您用來存取 Service Fabric 叢集的網域相符。 必須如此相符，才能為叢集的 HTTPS 管理端點和 Service Fabric Explorer 提供 SSL。 您無法從憑證授權單位 (CA) 取得 .cloudapp.azure.com 網域的 SSL 憑證。 您必須為您的叢集取得自訂網域名稱。 當您向 CA 要求憑證時，憑證的主體名稱必須與用於您叢集的自訂網域名稱相符。

### <a name="application-certificates-optional"></a>應用程式憑證 (選用)
您可以針對應用程式安全性目的，在叢集上安裝任何數目的其他憑證。 在建立您的叢集之前，請考量需要在節點上安裝憑證的應用程式安全性案例，例如：

* 將應用程式組態值加密和解密。
* 在複寫期間將資料跨節點加密。

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>格式化憑證以供 Azure 資源提供者使用
您可以透過 Key Vault 來直接新增和使用私密金鑰檔案 (.pfx)。 不過，Azure 計算資源提供者要求必須以特殊「JavaScript 物件標記法」(JSON) 格式儲存金鑰。 此格式包含 .pfx 檔案作為 Base-64 編碼字串和私密金鑰密碼。 為了符合這些要求，金鑰必須放在 JSON 字串中，然後在 Key Vault 中儲存為「密碼」。

若要讓這個程序更容易，可使用 PowerShell 模組 ([GitHub 上有提供][service-fabric-rp-helpers])。 若要使用該模組，請執行下列操作：

1. 將儲存機制的完整內容下載到本機目錄中。
2. 移至本機目錄。
2. 在您的 PowerShell 視窗中匯入 ServiceFabricRPHelpers 模組：

```powershell

 Import-Module "C:\..\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

```

此 PowerShell 模組中的 `Invoke-AddCertToKeyVault` 命令會自動將憑證私密金鑰的格式設定為 JSON 字串，並將它上傳到 Key Vault。 請使用此命令將叢集憑證及任何其他應用程式憑證新增到 Key Vault。 請為您想在叢集中安裝的任何其他憑證重複這個步驟。

#### <a name="uploading-an-existing-certificate"></a>上傳現有的憑證

```powershell

 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName westus-mykeyvault -Location "West US" -VaultName mywestusvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

```

如果您收到如這裡顯示的錯誤，通常表示有資源 URL 發生衝突的情形。 為了解決衝突，請變更 Key Vault 名稱。

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

解決衝突之後，輸出看起來應該會像這樣：

```

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup westus-mykeyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing value mywestusvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to mywestusvault in vault mywestusvault


Name  : CertificateThumbprint
Value : E21DBC64B183B5BF355C34C46E03409FEEAEF58D

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault

Name  : CertificateURL
Value : https://mywestusvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

>[!NOTE]
>您需要 CertificateThumbprint、SourceVault 及 CertificateURL 這前三個字串，才能設定安全的 Service Fabric 叢集及取得您可能用於應用程式安全性的任何應用程式憑證。 如果您未儲存這些字串，可能很難在稍後透過查詢 Key Vault 來擷取它們。

<a id="add-self-signed-certificate-to-key-vault"></a>

#### <a name="creating-a-self-signed-certificate-and-uploading-it-to-the-key-vault"></a>建立自我簽署憑證並上傳到 Key Vault

如果您已將憑證上傳到 Key Vault，請略過此步驟。 此步驟是用來產生新的自我簽署憑證，並將其上傳到 Key Vault。 在變更下列指令碼中的參數並執行它之後，系統應該會提示您輸入憑證密碼。  

```powershell

$ResourceGroup = "chackowestuskv"
$VName = "chackokv2"
$SubID = "6c653126-e4ba-42cd-a1dd-f7bf96ae7a47"
$locationRegion = "westus"
$newCertName = "chackotestcertificate1"
$dnsName = "www.mycluster.westus.mydomain.com" #The certificate's subject name must match the domain used to access the Service Fabric cluster.
$localCertPath = "C:\MyCertificates" # location where you want the .PFX to be stored

 Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResourceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath

```

如果您收到如這裡顯示的錯誤，通常表示有資源 URL 發生衝突的情形。 為了解決衝突，請變更 Key Vault 名稱、RG 名稱等。

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

解決衝突之後，輸出看起來應該會像這樣：

```
PS C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers> Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResouceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -Password $certPassword -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath
Switching context to SubscriptionId 6c343126-e4ba-52cd-a1dd-f8bf96ae7a47
Ensuring ResourceGroup chackowestuskv in westus
WARNING: The output object type of this cmdlet will be modified in a future release.
Creating new vault westuskv1 in westus
Creating new self signed certificate at C:\MyCertificates\chackonewcertificate1.pfx
Reading pfx file from C:\MyCertificates\chackonewcertificate1.pfx
Writing secret to chackonewcertificate1 in vault westuskv1


Name  : CertificateThumbprint
Value : 96BB3CC234F9D43C25D4B547sd8DE7B569F413EE

Name  : SourceVault
Value : /subscriptions/6c653126-e4ba-52cd-a1dd-f8bf96ae7a47/resourceGroups/chackowestuskv/providers/Microsoft.KeyVault/vaults/westuskv1

Name  : CertificateURL
Value : https://westuskv1.vault.azure.net:443/secrets/chackonewcertificate1/ee247291e45d405b8c8bbf81782d12bd

```

>[!NOTE]
>您需要 CertificateThumbprint、SourceVault 及 CertificateURL 這前三個字串，才能設定安全的 Service Fabric 叢集及取得您可能用於應用程式安全性的任何應用程式憑證。 如果您未儲存這些字串，可能很難在稍後透過查詢 Key Vault 來擷取它們。

 此時，您應該已經備妥下列元素：

* Key Vault 資源群組。
* Key Vault 及其 URL (在先前的 PowerShell 輸出中稱為 SourceVault)。
* 在 Key Vault 中的叢集伺服器驗證憑證及其 URL。
* 在 Key Vault 中的應用程式憑證及其 URL。


<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>設定用戶端驗用的 Azure Active Directory

Azure AD 可讓組織 (稱為租用戶) 管理使用者對應用程式的存取。 應用程式分成具有 Web 型登入 UI 的應用程式，以及具有原生用戶端體驗的應用程式。 在本文中，我們假設您已經建立租用戶。 如果您尚未建立租用戶，請從閱讀[如何取得 Azure Active Directory 租用戶][active-directory-howto-tenant]開始進行。

Service Fabric 叢集提供其管理功能的各種進入點 (包括 Web 型 [Service Fabric Explorer][service-fabric-visualizing-your-cluster] 和 [Visual Studio][service-fabric-manage-application-in-visual-studio])。 因此，您將建立兩個 Azure AD 應用程式來控制對叢集的存取：一個 Web 應用程式和一個原生應用程式。

為了簡化與設定 Azure AD 搭配 Service Fabric 叢集相關的一些步驟，我們建立了一組 Windows PowerShell 指令碼。

> [!NOTE]
> 建立叢集之前，您必須先完成下列步驟。 由於指令碼會預期叢集名稱和端點，因此這些值應該是計劃的值，而不是您已經建立的值。

1. [下載指令碼][sf-aad-ps-script-download]到您的電腦。
2. 在 zip 檔案上按一下滑鼠右鍵，選取 屬性、選取 解除封鎖 核取方塊，然後按一下套用。
3. 解壓縮 zip 檔案。
4. 執行 `SetupApplications.ps1`，並且提供 TenantId、ClusterName 和 WebApplicationReplyUrl 作為參數。 例如：

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    您可以執行 PowerShell 命令 `Get-AzureSubscription` 來找出您的 TenantId。 執行此命令會顯示每個訂用帳戶的 TenantId。

    ClusterName 是用來加在指令碼所建立 Azure AD 應用程式的前面。 它不需要與實際叢集名稱完全相符。 其用意只是要讓您更容易將 Azure AD 構件對應到與之搭配使用的 Service Fabric 叢集。

    WebApplicationReplyUrl 是在您的使用者完成登入之後，Azure AD 傳回給他們的預設端點。 請將此端點設定為您叢集的 Service Fabric Explorer 端點，預設為︰

    https://&lt;cluster_domain&gt;:19080/Explorer

    系統會提示您登入具有 Azure AD 租用戶系統管理權限的帳戶。 在您登入之後，指令碼會建立 Web 和原生應用程式來代表 Service Fabric 叢集。 如果您在 [Azure 傳統入口網站][azure-classic-portal]中查看租用戶的應用程式，則應該會看到兩個新項目︰

   * *ClusterName*\_叢集
   * *ClusterName*\_用戶端

   此指令碼會列印您在下一節建立叢集時 Azure Resource Manager 範本所需的 JSON，因此建議讓 PowerShell 視窗保持開啟。

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>建立 Service Fabric 叢集 Resource Manager 範本
在本節中，Service Fabric 叢集 Resource Manager 範本中會使用上述 PowerShell 命令的輸出。

您可以從 [GitHub 上的 Azure 快速啟動範本資源庫][azure-quickstart-templates]取得範例 Resource Manager 範本。 這些範本可以用作叢集範本的起點。

### <a name="create-the-resource-manager-template"></a>建立 Resource Manager 範本
本指南使用[五節點安全叢集][service-fabric-secure-cluster-5-node-1-nodetype]範例範本和範本參數。 下載 `azuredeploy.json` 和 `azuredeploy.parameters.json` 到您的電腦並在您最愛的文字編輯器中開啟這兩個檔案。

### <a name="add-certificates"></a>新增憑證
您可以藉由參考包含憑證金鑰的 Key Vault，將憑證新增到叢集 Resource Manager 範本。 建議您將 Key Vault 值置於 Resource Manager 範本參數檔案中。 這麼做會讓 Resource Manager 範本檔案保持可重複使用，而不會含有某項部署特定的值。

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>將所有憑證都新增到虛擬機器擴展集 osProfile
安裝在叢集中的每個憑證都必須在擴展集資源 (Microsoft.Compute/virtualMachineScaleSets) 的 osProfile 區段中設定妥當。 此動作會指示資源提供者在 VM 上安裝憑證。 此安裝既包含叢集憑證，也包含任何您打算用於應用程式的應用程式安全性憑證︰

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-the-service-fabric-cluster-certificate"></a>設定 Service Fabric 叢集憑證
不論是在 Service Fabric 叢集資源 (Microsoft.ServiceFabric/clusters) 中，還是在虛擬機器擴展集資源中虛擬機器擴展集的 Service Fabric 延伸模組中，都必須設定叢集驗證憑證。 這個安排可讓 Service Fabric 資源提供者設定它，以用於管理端點的叢集驗證和伺服器驗證。

##### <a name="virtual-machine-scale-set-resource"></a>虛擬機器擴展集資源：
```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="service-fabric-resource"></a>Service Fabric 資源︰
```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="insert-azure-ad-configuration"></a>插入 Azure AD 組態
您稍早建立的 Azure AD 組態可以直接插入到您的 Resource Manager 範本中。 不過，建議您先將值擷取到參數檔案中，以便讓 Resource Manager 範本保持可重複使用，而不會含有某項部署特定的值。

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### <a "configure-arm" ></a>設定 Resource Manager 範本參數
<!--- Loc Comment: It seems that <a "configure-arm" > must be replaced with <a name="configure-arm"></a> since the link seems not to be redirecting correctly --->
最後，請使用 Key Vault 和 Azure AD PowerShell 命令的輸出值來填入參數檔案︰

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```
此時，您應該已經備妥下列元素：

* Key Vault 資源群組
  * 金鑰保存庫
  * 叢集伺服器驗證憑證
  * 資料編密憑證
* Azure Active Directory 租用戶
  * 適用於 Web 型管理和 Service Fabric Explorer 的 Azure AD 應用程式
  * 適用於原生用戶端管理的 Azure AD 應用程式
  * 使用者及其獲指派的角色
* Service Fabric 叢集 Resource Manager 範本
  * 透過 Key Vault 設定的憑證
  * 已設定 Azure Active Directory

下圖說明 Key Vault 和 Azure AD 組態在 Resource Manager 範本中發生作用的位置。

![Resource Manager 相依性對應][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>建立叢集
您現在已準備好使用 [Azure 資源範本部署][resource-group-template-deploy]來建立叢集。

#### <a name="test-it"></a>進行測試
使用下列 PowerShell 命令，以參數檔案來測試 Resource Manager 範本︰

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>進行部署
如果 Resource Manager 範本測試通過，使用下列 PowerShell 命令，以參數檔案來部署 Resource Manager 範本︰

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>將使用者指派給角色
建立應用程式來代表您的叢集之後，請將使用者指派給 Service Fabric 所支援的角色︰唯讀和系統管理員。您可以使用 [Azure 傳統入口網站][azure-classic-portal]來指派角色。

1. 在 Azure 入口網站中，移至您的租用戶，然後選取 [應用程式]。
2. 選取 Web 應用程式 (其名稱類似 `myTestCluster_Cluster`)。
3. 按一下 [使用者]  索引標籤。
4. 選取要指派的使用者，然後按一下畫面底部的 [指派] 按鈕。

    ![將使用者指派給角色按鈕][assign-users-to-roles-button]
5. 選取要指派給使用者的角色。

    ![[指派使用者] 對話方塊][assign-users-to-roles-dialog]

> [!NOTE]
> 如需 Service Fabric 中角色的詳細資訊，請參閱 [角色型存取控制 (適用於 Service Fabric 用戶端)](service-fabric-cluster-security-roles.md)。
>
>

 <a name="secure-linux-clusters"></a>
 <!--- Loc Comment: It seems that letter S in cluster was missing, which caused the wrong redirection of the link --->

## <a name="create-secure-clusters-on-linux"></a>在 Linux 上建立安全叢集
為了簡化此程序，我們提供了一個[協助程式指令碼](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux)。 在使用此協助程式指令碼之前，請確定您已安裝 Azure 命令列介面 (CLI)，而且它位於您的路徑中。 下載指令碼後，請執行 `chmod +x cert_helper.py` ，以確定指令碼有執行權限。 第一個步驟是使用 CLI 搭配 `azure login` 命令來登入您的 Azure 帳戶。 登入 Azure 帳戶之後，請使用協助程式搭配您 CA 簽署的憑證，如下列命令所示︰

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]
```

-ifile 參數可以搭配憑證類型 (pfx 或 pem，如果是自我簽署的憑證則是 ss) 接受 .pfx 檔案或 .pem 檔案作為輸入。
參數 -h 會列印出說明文字。


此命令會傳回下列三個字串做為輸出︰

* SourceVaultID：這是它為您建立的新 KeyVault ResourceGroup 的識別碼。
* CertificateUrl：用於存取憑證。
* CertificateThumbprint：用於驗證。

下列範例示範如何使用此命令︰

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
執行上述命令會提供下列三個字串給您︰

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

憑證的主體名稱必須與您用來存取 Service Fabric 叢集的網域相符。 必須如此相符，才能為叢集的 HTTPS 管理端點和 Service Fabric Explorer 提供 SSL。 您無法從 CA 取得 `.cloudapp.azure.com` 網域的 SSL 憑證。 您必須為您的叢集取得自訂網域名稱。 當您向 CA 要求憑證時，憑證的主體名稱必須與用於您叢集的自訂網域名稱相符。

這些主體名稱是您建立安全 Service Fabric 叢集 (不含 Azure AD) 所需的項目，如[設定 Resource Manager 範本參數](#configure-arm)所述。 您可以依照[驗證用戶端對叢集的存取權](service-fabric-connect-to-secure-cluster.md)的指示來連接到安全叢集。 Linux 叢集不支援 Azure AD 驗證。 您可以指派系統管理員和用戶端角色，如[將使用者指派給角色](#assign-roles)一節所述。 為 Linux 叢集指定系統管理員和用戶端角色時，您必須提供用於驗證的憑證指紋。 您不須提供主體名稱，因為不會執行任何鏈結驗證或撤銷。

如果您想要使用自我簽署的憑證來進行測試，可以使用相同的指令碼來產生該憑證。 您可以接著提供旗標 `ss` 而不是提供憑證名稱和憑證路徑，來將該憑證上傳到 Key Vault。 例如，請參閱下列命令來建立及上傳自我簽署的憑證︰

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net"
```
此命令會傳回相同的三個字串：SourceVault、CertificateUrl 及 CertificateThumbprint。 您可以接著使用這些字串來建立安全的 Linux 叢集，以及放置自我簽署憑證的位置。 您需要有自我簽署的憑證才能連接到叢集。 您可以依照[驗證用戶端對叢集的存取權](service-fabric-connect-to-secure-cluster.md)的指示來連接到安全叢集。

憑證的主體名稱必須與您用來存取 Service Fabric 叢集的網域相符。 必須如此相符，才能為叢集的 HTTPS 管理端點和 Service Fabric Explorer 提供 SSL。 您無法從 CA 取得 `.cloudapp.azure.com` 網域的 SSL 憑證。 您必須為您的叢集取得自訂網域名稱。 當您向 CA 要求憑證時，憑證的主體名稱必須與用於您叢集的自訂網域名稱相符。

您可以在 Azure 入口網站中從協助程式指令碼填入參數，如[在 Azure 入口網站中建立叢集](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal)一節所述。

## <a name="next-steps"></a>後續步驟
此時，您已具有以 Azure Active Directory 提供管理驗證的安全叢集。 接下來，請[連線到您的叢集](service-fabric-connect-to-secure-cluster.md)並了解如何[管理應用程式密碼](service-fabric-application-secret-management.md)。

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>針對設定用戶端驗用的 Azure Active Directory 進行疑難排解
如果您在設定用於用戶端驗證的 Azure AD 時遇到問題，請檢閱本節中可能的解決方案。

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer 會提示您選取憑證
#### <a name="problem"></a>問題
在 Service Fabric Explorer 中順利登入 Azure AD 之後，瀏覽器會返回首頁，但是會出現提示您選取憑證的訊息。

![SFX 選取憑證對話方塊][sfx-select-certificate-dialog]

#### <a name="reason"></a>原因
使用者未獲指派 Azure AD 叢集應用程式中的角色。 因此，Azure AD 驗證在 Service Fabric 叢集上發生失敗。 Service Fabric Explorer 會回復到憑證驗證。

#### <a name="solution"></a>方案
請依照設定 Azure AD 的指示進行操作，然後指派使用者角色。 另外，建議您如 `SetupApplications.ps1` 所做的一樣，開啟 [存取應用程式需要使用者指派]。

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>使用 PowerShell 進行連線時失敗，發生錯誤：「指定的認證無效」
#### <a name="problem"></a>問題
在您順利登入 Azure AD 之後，於使用 PowerShell 以 “AzureActiveDirectory” 安全性模式連接到叢集時連線失敗，發生錯誤：「指定的認證無效」。

#### <a name="solution"></a>方案
此解決方案與前一個相同。

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer 在您登入時傳回失敗："AADSTS50011"
#### <a name="problem"></a>問題
當您嘗試在 Service Fabric Explorer 中登入 Azure AD 時，頁面傳回失敗：「AADSTS50011：回覆地址 &lt;url&gt; 與針對應用程式設定的回覆地址不符：&lt;guid&gt;」。

![SFX 回覆地址不相符][sfx-reply-address-not-match]

#### <a name="reason"></a>原因
代表 Service Fabric Explorer 的叢集 (Web) 應用程式嘗試對照 Azure AD 來進行驗證，而它在要求中提供重新導向傳回 URL。 但該 URL 並未列在 Azure AD 應用程式 [回覆 URL] 清單中。

#### <a name="solution"></a>方案
在叢集 (Web) 應用程式的 [設定] 索引標籤上，將 Service Fabric Explorer 的 URL 新增到 [回覆 URL] 清單中，或取代清單內的其中一個項目。 完成時，請儲存變更。

![Web 應用程式回覆 url][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>透過 PowerShell 使用 Azure AD 驗證來連接叢集
若要連接 Service Fabric 叢集，請使用下列 PowerShell 命令範例︰

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

若要了解 Connect-ServiceFabricCluster Cmdlet，請參閱 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx)。

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>我是否可以在多個叢集中重複使用相同的 Azure AD 租用戶？
是。 但是請務必將 Service Fabric Explorer 的 URL 新增到叢集 (Web) 應用程式。 否則 Service Fabric Explorer 無法運作。

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>為什麼在已啟用 Azure AD 的情況下仍然需要伺服器憑證？
FabricClient 和 FabricGateway 會執行相互驗證。 在 Azure AD 驗證期間，Azure AD 整合會將用戶端身分識別提供給伺服器，而伺服器憑證則用來驗證伺服器身分識別。 如需有關 Service Fabric 憑證的詳細資訊，請參閱 [X.509 憑證和 Service Fabric][x509-certificates-and-service-fabric]。

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png

