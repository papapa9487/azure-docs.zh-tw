---
title: "在 Azure Stack 中提供虛擬機器擴展集"
description: "了解雲端系統管理員如何可以將虛擬機器擴展新增至 Azure Stack Marketplace"
services: azure-stack
author: anjayajodha
ms.service: azure-stack
ms.topic: article
ms.date: 9/25/2017
ms.author: anajod
keywords: 
ms.openlocfilehash: 31aeb963bdf4fd32712bc6f29f64060ec1c77cb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>在 Azure Stack 中提供虛擬機器擴展集

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

虛擬機器擴展集是 Azure Stack 計算資源。 您可以使用它們來部署和管理一組相同的虛擬機器。 由於所有虛擬機器具有相同的設定，擴展集不需要預先佈建虛擬機器。 您可以更輕鬆地針對大量計算、巨量資料和容器化工作負載，建置大規模服務。

本主題會引導您完成讓擴展集可在 Azure Stack Marketplace 中使用的程序。 完成此程序之後，您的使用者可以將虛擬機器擴展集新增至其訂用帳戶。

Azure Stack 上的虛擬機器擴展集就像是 Azure 上的虛擬機器擴展集。 如需詳細資訊，請參閱下列視訊：
* [Mark Russinovich 講述 Azure 擴展集](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Guy Bowerman 與虛擬機器擴展集](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

在 Azure Stack 上，虛擬機器擴展集不支援自動擴展。 您可以使用 Azure Stack 入口網站、Resource Manager 範本或 PowerShell 將更多執行個體新增至擴展集。

## <a name="prerequisites"></a>必要條件
* **Powershell 和工具**

   安裝和設定適用於 Azure Stack 的 PowerShell 和 Azure Stack 工具。 請參閱[在 Azure Stack 使用 PowerShell 啟動和執行](azure-stack-powershell-configure-quickstart.md)。

   安裝 Azure Stack 工具之後，請確定您匯入下列 PowerShell 模組 (AzureStack-Tools-master 資料夾中 \ComputeAdmin 資料夾的相對路徑)：

        Import-Module .\AzureStack.ComputeAdmin.psm1

* **作業系統映像**

   如果您尚未新增至您的 Azure Stack Marketplace 作業系統映像，請參閱[Windows Server 2016 VM 映像新增至 Azure Stack Marketplace](azure-stack-add-default-image.md)。

   如需 Linux 支援，請下載 Ubuntu Server 16.04，並將使用 ```Add-AzsVMImage``` 搭配下列參數來新增它：```-publisher "Canonical" -offer "UbuntuServer" -sku "16.04-LTS"```。

## <a name="add-the-virtual-machine-scale-set"></a>新增虛擬機器擴展集

為您的環境編輯下列 PowerShell 指令碼，然後執行它以將虛擬機器擴展集新增至您的 Azure Stack Marketplace。 

``$User`` 是您用來連線系統管理員入口網站的帳戶。 例如： serviceadmin@contoso.onmicrosoft.com。

```
$Arm = "https://adminmanagement.local.azurestack.external"
$Location = "local"

Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint $Arm

$Password = ConvertTo-SecureString -AsPlainText -Force "<your Azure Stack administrator password>"

$User = "<your Azure Stack service administrator user name>"

$Creds =  New-Object System.Management.Automation.PSCredential $User, $Password

$AzsEnv = Get-AzureRmEnvironment AzureStackAdmin
$AzsEnvContext = Add-AzureRmAccount -Environment $AzsEnv -Credential $Creds
Select-AzureRmProfile -Profile $AzsEnvContext

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
```

## <a name="remove-a-virtual-machine-scale-set"></a>移除虛擬機器擴展集

若要移除虛擬機器擴充集資源庫項目，請執行下列 PowerShell 命令：

    Remove-AzsVMSSGalleryItem

> [!NOTE]
> 資源庫項目可能不會立即移除。 您可能需要重新整理入口網站數次，它才會從 Marketplace 中移除。


## <a name="next-steps"></a>後續步驟
[Azure Stack 的常見問題集](azure-stack-faq.md)

