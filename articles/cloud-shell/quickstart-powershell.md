---
title: "Azure Cloud Shell (預覽) 中的 PowerShell 快速入門 | Microsoft Docs"
description: "Cloud Shell 中 PowerShell 的快速入門"
services: Azure
documentationcenter: 
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: damaerte
ms.openlocfilehash: fd1d340bc0408eaeb0b7b18235df109224eae5f5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Azure Cloud Shell 中 PowerShell 的快速入門

本文件會詳細說明如何在 [Azure 入口網站](https://aka.ms/PSCloudPreview)中使用 Azure Cloud Shell 中的 PowerShell。

> [!NOTE]
> 也提供 [Azure Cloud Shell 中的 Bash](quickstart.md) 快速入門。

## <a name="start-cloud-shell"></a>啟動 Cloud Shell

1. 從 Azure 入口網站的頂端導覽列中，按一下 **Cloud Shell**

  ![](media/quickstart-powershell/shell-icon.png)

2. 從下拉式清單中選取 PowerShell 環境，而且您會在 Azure 磁碟機 `(Azure:)` 中

  ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>執行 PowerShell 命令

在 Cloud Shell 中執行一般 PowerShell 命令，例如：

```Powershell
PS Azure:\> Get-Date
Monday, September 25, 2017 08:55:09 AM

PS Azure:\> Get-AzureRmVM -Status

ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>導覽 Azure 資源

 1. 列出您的訂用帳戶

    ``` Powershell
    PS Azure:\> dir
    ```

 2. `cd` 到您慣用的訂用帳戶

    ``` Powershell
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. 檢視目前訂用帳戶下的所有 Azure 資源
 
    鍵入 `dir`，以列出 Azure 資源的多個檢視。
 
    ``` PowerShell
    PS Azure:\MySubscriptionName> dir

        Directory: azure:\MySubscriptionName

    Mode Name
    ---- ----
    +    AllResources
    +    ResourceGroups
    +    StorageAccounts
    +    VirtualMachines
    +    WebApps
     ```

### <a name="allresources-view"></a>AllResources 檢視 
在 `AllResources` 目錄下鍵入 `dir`，以檢視 Azure 資源。
    
    PS Azure:\MySubscriptionName> dir AllResources

### <a name="explore-resource-groups"></a>探索資源群組

 您可以移至 `ResourceGroups` 目錄以及可找到虛擬機器的特定資源群組內。

``` PowerShell
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271

```
> [!NOTE]
> 您可能會注意到，第二次鍵入 `dir` 時，Cloud Shell 可以更快速地顯示項目。
> 原因是將子項目快取在記憶體中，以獲得較佳的使用者體驗。
不過，您一律可以使用 `dir -Force` 來取得全新資料。

### <a name="navigate-storage-resources"></a>導覽儲存體資源
    
進入 `StorageAccounts` 資料夾，即可輕鬆地導覽儲存體資源
    
``` PowerShell 
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files


Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>


```

您可以搭配使用連接字串與下列命令來掛接 Azure 檔案共用。
        
``` PowerShell
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>


```

如需詳細資料，請參閱[掛接 Azure 檔案共用並在 Windows 中存取共用][azmount]。

您也可以導覽 Azure 檔案共用下的目錄，如下所示：

            
``` PowerShell
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1

    
```

### <a name="interact-with-virtual-machines"></a>與虛擬機器互動

您可以透過 `VirtualMachines` 目錄，在目前訂用帳戶下找到所有虛擬機器。
    
``` PowerShell
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running


```

#### <a name="invoke-powershell-script-across-remote-vms"></a>在遠端 VM 之間叫用 PowerShell 指令碼

 > [!WARNING]
 > 請參閱[針對 Azure VM 遠端管理進行疑難排解](troubleshooting.md#powershell-resolutions)。

  假設您有名為 MyVM1 的 VM，讓我們使用 `Invoke-AzureRmVMCommand` 在遠端電腦上叫用 PowerShell ScriptBlock。

  ``` Powershell
  Invoke-AzureRmVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -EnableRemoting
  ```
  您也可以先導覽至 virtualMachines 目錄，並執行 `Invoke-AzureRmVMCommand`，如下所示。

  ``` Powershell
  PS Azure:\> cd MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzureRmVMCommand -Scriptblock{Get-ComputerInfo}
  ```
  您會看到類似以下的輸出：

  ``` Powershell
  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>以互動方式登入遠端 VM

您可以使用 `Enter-AzureRmVM`，以互動方式登入在 Azure 中執行的 VM。

  ``` Powershell
  Enter-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup -EnableRemoting
  ```

您也可以先導覽至 `virtualMachines` 目錄，並執行 `Enter-AzureRmVM`，如下所示。

  ``` Powershell
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzureRmVM
 ```

### <a name="discover-webapps"></a>探索 WebApp

進入 `WebApps` 資料夾，即可輕鬆地導覽儲存體資源

``` PowerShell
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps


Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US



# You can use Azure cmdlets to Start/Stop your web apps for example,
PS Azure:\MySubscriptionName\WebApps> Start-AzureRmWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -force
PS Azure:\MySubscriptionName\WebApps> dir -force

    Directory: Azure:\MySubscriptionName\WebApps


Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

```

## <a name="list-available-commands"></a>列出可用命令

在 `Azure` 磁碟機下，鍵入 `Get-AzureRmCommand` 以取得內容特定 Azure 命令。

或者，您可以一律使用 `Get-Command *azurerm* -Module AzureRM.*` 來找出可用的 Azure 命令。

## <a name="install-custom-modules"></a>安裝自訂模組

您可以執行 `Install-Module`，以從 [PowerShell 資源庫][gallery]安裝模組。

## <a name="get-help"></a>Get-Help

鍵入 `Get-Help` 以取得 Azure Cloud Shell 中 PowerShell 的相關資訊。

``` Powershell
PS Azure:\> Get-Help
```

例如，針對特定命令，您仍然可以執行後接 Cmdlet 的 Get-Help。

``` Powershell
PS Azure:\> Get-Help Get-AzureRmVM
```

## <a name="use-azure-file-storage-to-store-your-data"></a>使用 Azure 檔案儲存體來儲存資料

您可以建立指令碼 (例如 `helloworld.ps1`)，並將它儲存至 clouddrive，以跨殼層工作階段使用它。

``` Powershell
cd C:\users\ContainerAdministrator\CloudDrive
PS C:\users\ContainerAdministrator\CloudDrive> vim .\helloworld.ps1
# Add the content, such as 'Hello World!'
PS C:\users\ContainerAdministrator\CloudDrive> .\helloworld.ps1
Hello World!
```

下次當您在 Cloud Shell 中使用 PowerShell 時，`helloworld.ps1` 檔案將會存在於掛接 Azure 檔案共用的 `CloudDrive` 資料夾下。

## <a name="use-custom-profile"></a>使用自訂設定檔

您可以建立 PowerShell 設定檔 (`profile.ps1` 或 `Microsoft.PowerShell_profile.ps1`) 來自訂 PowerShell 環境。 將它儲存到 `CloudDrive` 下，以在您啟動 Cloud Shell 時將它載入至每個 PowerShell 工作階段。

如需了解如何建立設定檔，請參閱[關於設定檔][profile]。

## <a name="use-git"></a>使用 Git

若要在 CloudShell 中複製 git 存放庫，您需要建立[個人存取權杖][githubtoken]，並使用它作為使用者名稱。 有了權杖之後，請複製存放庫，如下所示：

 ``` PowerShell
  git clone https://<your-access-token>@github.com/username/repo.git

```
因為在您登出或工作階段逾時之後不會保存 CloudShell 中的工作階段，所以下次登入時不會有 Git 設定檔。 若要保存您的 Git 設定，您必須將 .gitconfig 儲存至 `CloudDrive`，並複製它，或在啟動 `CloudShell` 時建立符號連結。 在 profile.ps1 中使用下列程式碼片段，建立與 `CloudDrive` 的符號連結。

 ``` PowerShell
 
# .gitconfig path relative to this script
$script:gitconfigPath = Join-Path $PSScriptRoot .gitconfig

# Create a symlink to .gitconfig in user's $home
if(Test-Path $script:gitconfigPath){

    if(-not (Test-Path (Join-Path $Home .gitconfig ))){
         New-Item -ItemType SymbolicLink -Path $home -Name .gitconfig -Value $script:gitconfigPath
    }
}

```
## <a name="exit-the-shell"></a>結束 Shell

鍵入 `exit` 以終止工作階段。

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/