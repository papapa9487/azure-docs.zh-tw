---
title: "了解 Azure 微服務安全性原則 | Microsoft Docs"
description: "如何以系統和本機安全性帳戶執行 Service Fabric 應用程式的概觀，包括應用程式在啟動前需要執行某些特殊權限動作的 SetupEntry 點"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: mfussell
ms.openlocfilehash: aae828489b708a5b538df1d63c12be23d0423da7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="configure-security-policies-for-your-application"></a>設定應用程式的安全性原則
藉由使用 Azure Service Fabric，您便可以保護在叢集中以不同使用者帳戶執行的應用程式。 在以該使用者帳戶部署時，Service Fabric 也會協助保護應用程式所使用的資源，例如檔案、目錄和憑證。 如此一來，即使在共用主控環境中，執行中的應用程式就不會彼此干擾。

根據預設，Service Fabric 應用程式會在用以執行 Fabric.exe 程序的帳戶之下執行。 Service Fabric 也能夠以本機使用者帳戶或本機系統帳戶 (在應用程式的資訊清單內指定) 執行應用程式。 支援的本機系統帳戶類型為 **LocalUser**、**NetworkService**、**LocalService** 和 **LocalSystem**。

 當您使用獨立安裝程式在資料中心的 Windows Server 上執行 Service Fabric 時，您可以使用 Active Directory 網域帳戶，包括群組受管理服務帳戶。

您可以定義和建立使用者群組，以便將一或多個使用者新增至每個群組一起管理。 當不同的服務進入點有多個使用者，而且他們需要具備可在群組層級取得的某些常見權限時，這會很有用。

## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>設定服務安裝程式進入點的原則
如[應用程式模型](service-fabric-application-model.md)所述，安裝程式進入點 **SetupEntryPoint** 是以與 Service Fabric 相同的認證執行的特殊權限進入點 (通常為 *NetworkService* 帳戶)，優先於其他任何進入點。 **EntryPoint** 指定的可執行檔通常是長時間執行的服務主機。 因此有個別安裝程式的進入點，就不需要使用較高權限來長時間執行服務主機可執行檔。 **EntryPoint** 指定的可執行檔是在 **SetupEntryPoint** 成功結束之後執行。 產生的程序會受到監視，萬一終止或當機，則會同樣從 **SetupEntryPoint** 開始來重新啟動。

以下簡單的服務資訊清單範例會顯示服務的 SetupEntryPoint 和主要的 EntryPoint。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
```

### <a name="configure-the-policy-by-using-a-local-account"></a>使用本機帳戶設定原則
在您設定服務以擁有安裝進入點之後，就能在應用程式資訊清單中變更用以執行的安全性權限。 下列範例示範如何將服務設定成以使用者系統管理員帳戶權限執行。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
```

首先，以使用者名稱 (例如 SetupAdminUser) 建立 **Principals** 區段。 這表示使用者是 Administrators 系統群組的成員。

接著在 **ServiceManifestImport** 區段下方設定原則，以將此主體套用至 **SetupEntryPoint**。 這會告知 Service Fabric，當 **MySetup.bat** 檔案執行時，執行身分 `RunAs` 應該具備系統管理員權限。 假設您「尚未」將原則套用至主要進入點，則 **MyServiceHost.exe** 中的程式碼將會以系統 **NetworkService** 帳戶執行。 這是用來執行所有服務進入點的預設帳戶。

現在讓我們將 MySetup.bat 檔案加入 Visual Studio 專案，以測試系統管理員權限。 在 Visual Studio 中，以滑鼠右鍵按一下服務專案，並新增名為 MySetup.bat 的新檔案。

接下來，確定 MySetup.bat 檔案已包含於服務封裝中。 預設不會包含該檔案。 選取該檔案、以滑鼠右鍵按一下操作功能表，然後選擇 [屬性] 。 在 [屬性] 對話方塊中，確定已將 [複製到輸出目錄] 設為 [有更新時才複製]。 請參閱下列螢幕擷取畫面。

![Visual Studio CopyToOutput for SetupEntryPoint 批次檔][image1]

現在開啟 MySetup.bat 檔案並加入下列命令：

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

接下來，建置解決方案並部署至本機開發叢集。 啟動服務之後 (如 Service Fabric 總管中所示)，您就可以看到 MySetup.bat 檔案成功的方式有兩種。 開啟 PowerShell 命令提示字元並輸入：

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

接著，記下已在 Service Fabric 總管中部署並啟動服務的節點名稱，例如節點 2。 接下來，瀏覽至應用程式執行個體工作資料夾，尋出 out.txt 檔案，其中顯示 **TestVariable**的值。 例如，如果此服務已部署至節點 2，則您可以移至 **MyApplicationType** 的這個路徑：

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

### <a name="configure-the-policy-by-using-local-system-accounts"></a>使用本機系統帳戶設定原則
通常最好是使用本機系統帳戶 (而不是系統管理員帳戶) 執行啟動指令碼。 使用系統管理員群組成員的身分執行 RunAs 原則通常沒有作用，因為電腦預設啟用使用者存取控制 (UAC)。 在此情況下，**建議是以 LocalSystem 身分 (而不是以新增到系統管理員群組的本機使用者身分) 執行 SetupEntryPoint**。 下列範例示範如何設定 SetupEntryPoint 以 LocalSystem 身分執行：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

對於 Linux 叢集，若要以 **root** 身分執行服務或安裝程式進入點，您可以將 **AccountType** 指定為 **LocalSystem**。

## <a name="start-powershell-commands-from-a-setup-entry-point"></a>從安裝程式進入點啟動 PowerShell 命令
若要從 **SetupEntryPoint** 點執行 PowerShell，您可以在指向 PowerShell 檔案的批次檔中執行 **PowerShell.exe**。 首先，將 PowerShell 檔案新增至服務專案，例如 **MySetup.ps1**。 請記得設定 [有更新時才複製]  屬性，讓這個檔案也會包含於服務封裝內。 下列範例顯示的範例批次檔可啟動名為 MySetup.ps1 的 PowerShell 檔案，以設定名為 **TestVariable** 的系統環境變數。

MySetup.bat 可啟動 PowerShell 檔案：

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

在 PowerShell 檔案中，加入以下項目來設定系統環境變數：

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> 當批次檔執行時，預設會在稱為 **work** 的應用程式資料夾中查看檔案。 在此情況下，當 MySetup.bat 執行時，我們希望它會在相同資料夾 (也就是應用程式的 **code package** 資料夾) 中尋找 MySetup.ps1 檔。 若要變更此資料夾，請設定工作資料夾：
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="use-console-redirection-for-local-debugging"></a>使用主控台重新導向進行本機偵錯
基於偵錯的目的，查看執行指令碼後的主控台輸出偶爾會有幫助。 若要這樣做，您可以設定主控台重新導向原則，將輸出寫入至檔案。 檔案輸出會寫入至部署並執行應用程式所在的節點上，稱為 **log** 的應用程式資料夾中。 (請參閱先前範例中在何處可以找到此資料夾。)

> [!WARNING]
> 切勿在實際部署的應用程式中使用主控台重新導向原則，因為這可能會影響應用程式容錯移轉。 僅將此原則用於本機開發及偵錯。  
> 
> 

下列範例示範如何使用 FileRetentionCount 值設定主控台重新導向：

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

如果您現在變更 MySetup.ps1 檔案來撰寫 **Echo** 命令，這將會寫入至輸出檔案以進行偵錯：

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

**您為您的指令碼偵錯之後，請立即移除這個主控台重新導向原則**。

## <a name="configure-a-policy-for-service-code-packages"></a>設定服務程式碼封裝的原則
在前述步驟中，您看到了如何將 RunAs 原則套用到 SetupEntryPoint。 讓我們深入了解如何建立可當作服務原則套用的不同主體。

### <a name="create-local-user-groups"></a>建立本機使用者群組
您可以定義和建立使用者群組，然後將一或多個使用者新增至群組。 當不同的服務進入點有多個使用者，而他們需要具備可在群組層級取得的某些通用權限時，這會很有用。 下列範例顯示名為 **LocalAdminGroup** 且具有系統管理員權限的本機群組。 Customer1 和 Customer2 這兩個使用者會成為此本機群組的成員。

```xml
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
```

### <a name="create-local-users"></a>建立本機使用者
您可以建立一個本機使用者，以便用來協助保護應用程式內的服務。 在應用程式資訊清單的主體區段中指定 **LocalUser** 帳戶類型時，Service Fabric 會在部署應用程式所在的電腦上建立本機使用者帳戶。 根據預設，這些帳戶的名稱不會與應用程式資訊清單中所指定的名稱一樣 (例如，下列範例中的 Customer3)。 相反地，它們會動態產生並具有隨機密碼。

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

如果應用程式要求使用者帳戶和密碼在所有機器上必須都相同 (例如，為了啟用 NTLM 驗證)，叢集資訊清單就必須將 NTLMAuthenticationEnabled 設定為 true。 叢集資訊清單也必須指定用來在所有機器產生相同密碼的 NTLMAuthenticationPasswordSecret。

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

### <a name="assign-policies-to-the-service-code-packages"></a>將原則指派給服務程式碼封裝
**ServiceManifestImport** 的 **RunAsPolicy** 區段會指定主體區段中應用來執行程式碼封裝的帳戶。 它也會將服務資訊清單中的程式碼封裝關聯至主體區段中的使用者帳戶。 您可以為安裝或主要進入點指定此原則，或指定 `All` 以將其套用到兩者。 下列範例顯示套用不同的原則：

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

如果未指定 **EntryPointType** ，則預設值會設定為 EntryPointType ="Main"。 當您想要以系統帳戶執行某些高權限設定作業時，指定 **SetupEntryPoint** 特別有用。 實際的服務程式碼可利用較低權限的帳戶來執行。

### <a name="apply-a-default-policy-to-all-service-code-packages"></a>將預設原則套用到所有的服務程式碼封裝
您使用 **DefaultRunAsPolicy** 區段來針對未定義特定 **RunAsPolicy** 的所有程式碼套件，指定預設的使用者帳戶。 如果在應用程式所使用的服務資訊清單中指定的大部分程式碼封裝必須以相同的使用者身分執行，則應用程式可以只使用該使用者帳戶定義預設的 RunAs 原則。 下列範例指定某個程式碼封裝未指定 **RunAsPolicy** 時，該程式碼封裝應該以主體區段中指定的 **MyDefaultAccount** 執行。

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```
### <a name="use-an-active-directory-domain-group-or-user"></a>使用 Active Directory 網域群組或使用者
對於安裝在 Windows Server 上的 Service Fabric 執行個體，獨立安裝程式可讓您以 Active Directory 使用者或群組帳戶的認證來執行服務。 這是在您網域內的內部部署 Active Directory，與 Azure Active Directory (Azure AD) 無關。 然後，您可以使用網域使用者或群組，存取網域中已經被授與權限的其他資源 (例如檔案共用)。

下列範例顯示的 Active Directory 使用者稱為 *TestUser*，其網域密碼以稱為 *MyCert* 的憑證加密。 您可以使用 `Invoke-ServiceFabricEncryptText` Powershell 命令來建立密碼加密文字。 如需詳細資訊，請參閱[管理 Service Fabric 應用程式中的密碼](service-fabric-application-secret-management.md)。

您必須將密碼解密的憑證私密金鑰以頻外方法部署到本機電腦 (在 Azure 中，這是透過 Azure Resource Manager)。 然後，當 Service Fabric 將服務封裝部署到電腦時，它就能夠將密碼解密，並連同使用者名稱向 Active Directory 進行驗證，而在這些認證下執行。

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```
### <a name="use-a-group-managed-service-account"></a>使用群組受管理服務帳戶。
對於使用獨立安裝程式安裝於 Windows Server 上的 Service Fabric 執行個體，您可以群組受管理服務帳戶 (gMSA) 來執行服務。 注意︰這是您的網域內部部署的 Active Directory，與 Azure Active Directory (Azure AD) 無關。 使用 gMSA，就不需將密碼或加密的密碼儲存於 `Application Manifest`。

下列範例示範如何建立名為 *svc-Test$* 的 gMSA 帳戶；如何將受管理的服務帳戶部署至叢集節點；以及如何設定使用者主體。

##### <a name="prerequisites"></a>必要條件。
- 網域需要一個 KDS 根金鑰。
- 網域必須位於 Windows Server 2012 或更新版本的功能層級上。

##### <a name="example"></a>範例
1. 讓 Active Directory 網域系統管理員能夠使用 `New-ADServiceAccount` 指令程式來建立群組受管理服務帳戶，並確定 `PrincipalsAllowedToRetrieveManagedPassword` 包括所有 Service Fabric 叢集節點。 請注意，`AccountName`、`DnsHostName` 和 `ServicePrincipalName` 必須是唯一的。
```
New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
```
2. 在每個 Service Fabric 叢集節點 (例如 `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`) 上，安裝並測試 gMSA。
```
Add-WindowsFeature RSAT-AD-PowerShell
Install-AdServiceAccount svc-Test$
Test-AdServiceAccount svc-Test$
```
3. 設定使用者主體，並設定 RunAsPolicy 來參考使用者。
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
      </Policies>
   </ServiceManifestImport>
  <Principals>
    <Users>
      <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
    </Users>
  </Principals>
</ApplicationManifest>
```

## <a name="assign-a-security-access-policy-for-http-and-https-endpoints"></a>為 HTTP 和 HTTPS 端點指派安全性存取原則
如果您將 RunAs 原則套用到服務，而且服務資訊清單宣告具有 HTTP 通訊協定的端點資源，則您必須指定 **SecurityAccessPolicy**，以確保配置給這些端點的連接埠都已針對用以執行服務的 RunAs 使用者帳戶，正確列入存取控制清單中。 否則， **http.sys** 無法存取服務，而且您從用戶端呼叫時將會失敗。 下列範例會將 Customer1 帳戶套用到名為 **EndpointName** 的端點，這會給予它完整的存取權限。

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
```

針對 HTTPS 端點，您也必須指出要傳回給用戶端的憑證名稱。 在作法上，您可以使用 **EndpointBindingPolicy**，並搭配應用程式資訊清單之憑證區段中定義的憑證。

```xml
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
```
## <a name="upgrading-multiple-applications-with-https-endpoints"></a>使用 https 端點來升級多個應用程式
使用 http**s** 時，請務必小心，不要將「相同的連接埠」用於相同應用程式的不同執行個體。 原因是 Service Fabric 將無法升級其中一個應用程式執行個體的憑證。 舉例來說，如果應用程式 1 或應用程式 2 都想要將其憑證 1 升級至憑證 2。 當進行升級時，Service Fabric 可能已清除憑證 1 在 http.sys 的註冊，儘管另一個應用程式仍然在使用它。 為了防止這種情況，Service Fabric 會偵測到該連接埠上已經有另一個應用程式以該憑證註冊 (因為 http.sys)，而讓作業失敗。

因此，Service Fabric 不支援在不同的應用程式執行個體中，使用「相同的連接埠」來升級兩個不同的服務。 換句話說，您無法在相同連接埠的不同服務上使用相同的憑證。 如果您需要在相同的連接埠上使用共用憑證，就必須使用放置條件約束，以確保將這些服務放在不同的機器上。 或者，可能的話，考慮針對每個應用程式執行個體中的每個服務，使用 Service Fabric 動態連接埠。 

如果您看到使用 https 進行升級失敗，系統將會顯示錯誤警告「Windows HTTP 伺服器 API 針對共用連接埠的應用程式不支援多個憑證」。

## <a name="a-complete-application-manifest-example"></a>完整的應用程式資訊清單範例
下列應用程式資訊清單顯示許多不同的設定：

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
     <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟
* [了解應用程式模型](service-fabric-application-model.md)
* [在服務資訊清單中指定資源](service-fabric-service-manifest-resources.md)
* [部署應用程式](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
