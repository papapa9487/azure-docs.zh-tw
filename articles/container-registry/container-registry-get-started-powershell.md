---
title: "Azure 容器登錄存放庫 | Microsoft Docs"
description: "如何使用 Docker 映像的 Azure 容器登錄存放庫"
services: container-registry
documentationcenter: 
author: cristy
manager: balans
editor: dlepow
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2017
ms.author: cristyg
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 1e5d5ea5b1ec121fe008abc48178b1d58f540ce1
ms.contentlocale: zh-tw
ms.lasthandoff: 08/21/2017

---

# <a name="create-a-private-docker-container-registry-using-the-azure-powershell"></a>使用 Azure PowerShell 建立私人 Docker 容器登錄
在 Windows 電腦上，使用 [Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview) 中的命令建立容器登錄並管理其設定。 您也可以使用 [Azure 入口網站](container-registry-get-started-portal.md)、[Azure CLI](container-registry-get-started-azure-cli.md)，或以程式設計方式用容器登錄 [REST API](https://go.microsoft.com/fwlink/p/?linkid=834376) 來建立並管理容器登錄。


* 如需相關背景和概念，請參閱[概觀](container-registry-intro.md)
* 如需支援的 Cmdlet 完整清單，請參閱 [Azure 容器登錄管理 Cmdlet](https://docs.microsoft.com/en-us/powershell/module/azurerm.containerregistry/)。


## <a name="prerequisites"></a>必要條件
* **Azure PowerShell**：若要安裝並開始使用 Azure PowerShell，請參閱[安裝指示](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps)。 執行 `Login-AzureRMAccount`登入您的 Azure 訂用帳戶。 如需詳細資訊，請參閱[開始使用 Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/get-started-azurep)。
* **資源群組**：先建立[資源群組](../azure-resource-manager/resource-group-overview.md#resource-groups)再建立容器登錄，或使用現有的資源群組。 請確定資源群組是位於[可使用](https://azure.microsoft.com/regions/services/)容器登錄庫服務的位置。 如需使用 Azure PowerShell 來建立資源群組，請參閱 [PowerShell 參考](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps#create-a-resource-group)。
* **儲存體帳戶** (選用)：建立標準 Azure [儲存體帳戶](../storage/common/storage-introduction.md)以支援相同位置中的容器登錄。 如果您以 `New-AzureRMContainerRegistry` 建立登錄庫時沒有指定儲存體帳戶，此命令會為您建立一個儲存體帳戶。 如需使用 PowerShell 來建立儲存體帳戶，請參閱 [PowerShell 參考](https://docs.microsoft.com/en-us/powershell/module/azure/new-azurestorageaccount)。 目前不支援進階儲存體。
* **服務主體** (選用)：當您使用 PowerShell 建立登錄時，依預設不會針對存取進行設定。 您可以將現有的 Azure Active Directory 服務主體指派至登錄庫，或建立並指派一個新的，視您的需求而定。 或者，您也可以啟用登錄的管理使用者帳戶。 請參閱本文稍後的章節。 如需登錄庫存取權的詳細資訊，請參閱[驗證容器登錄庫](container-registry-authentication.md)。

## <a name="create-a-container-registry"></a>建立容器登錄庫
執行 `New-AzureRMContainerRegistry` 命令建立容器登錄庫。

> [!TIP]
> 當您建立登錄庫時，請指定含有字母和數字的全域唯一最上層網域名稱。 範例中的登錄庫名稱是 `MyRegistry`，請換成您自己的唯一名稱。
>
>

下列命令使用最少的參數在「美國中南部」位置的 `MyResourceGroup` 資源群組中建立容器登錄庫 `MyRegistry`︰

```PowerShell
$Registry = New-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry"
```

* `-StorageAccountName` 為選擇性。 如果未指定，則會在指定的資源群組中使用由登錄名稱和時間戳記所組成的名稱建立儲存體帳戶。

## <a name="assign-a-service-principal"></a>指派服務主體
使用 PowerShell 的命令，將 Azure Active Directory [服務主體](../azure-resource-manager/resource-group-authenticate-service-principal.md)指派到登錄庫。 這些範例中的服務主體是指派至擁有者角色，但如果您想要，可以指派至[其他角色](../active-directory/role-based-access-control-configure.md)。

### <a name="create-a-service-principal"></a>建立服務主體
在下列命令中，會建立新的服務主體。 以 `-Password` 參數指定強式密碼。

```PowerShell
$ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName ApplicationDisplayName -Password "MyPassword"
```

### <a name="assign-a-new-or-existing-service-principal"></a>指派新的或現有的服務主體
您可以將新的或現有的服務主體指派到登錄庫。 若要將擁有者角色存取權指派到登錄庫，請執行類似下列範例的命令：

```PowerShell
New-AzureRMRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Registry.Id
```

##<a name="sign-in-to-the-registry-with-the-service-principal"></a>使用服務主體登入登錄庫
將服務主體指派到登錄庫之後，您就可以使用下列命令來登入：

```PowerShell
docker login -u $ServicePrincipal.ApplicationId -p myPassword
```

## <a name="manage-admin-credentials"></a>管理管理員認證
系統會自動建立每個容器登錄庫的管理員帳戶，並預設停用此帳戶。 下列範例示範用 PowerShell 命令來管理容器登錄庫的管理員認證。

### <a name="obtain-admin-user-credentials"></a>取得管理員使用者認證
```PowerShell
Get-AzureRMContainerRegistryCredential -ResourceGroupName "MyResourceGroup" -Name "MyRegistry"
```

### <a name="enable-admin-user-for-an-existing-registry"></a>啟用現有登錄庫的管理員使用者
```PowerShell
Update-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry" -EnableAdminUser
```

### <a name="disable-admin-user-for-an-existing-registry"></a>停用現有登錄庫的管理員使用者
```PowerShell
Update-AzureRMContainerRegistry -ResourceGroupName "MyResourceGroup" -Name "MyRegistry" -DisableAdminUser
```

## <a name="next-steps"></a>後續步驟
* [使用 Docker CLI 推送您的第一個映像](container-registry-get-started-docker-cli.md)

