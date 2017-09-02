---
title: "向 Azure 容器登錄庫驗證 | Microsoft Docs"
description: "如何使用 Azure Active Directory 服務主體或管理員帳戶登入 Azure 容器登錄庫"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 128a937a-766a-415b-b9fc-35a6c2f27417
ms.service: container-registry
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: aa2a6bf3d7d9ec22020036851fc0f2bca37e31bf
ms.contentlocale: zh-tw
ms.lasthandoff: 07/15/2017

---
# <a name="authenticate-with-a-private-docker-container-registry"></a>向私用 Docker 容器登錄進行驗證
若要使用 Azure 容器登錄庫中的容器映像，請使用 `docker login` 命令登入。 您可以使用 **[Azure Active Directory 服務主體](../active-directory/active-directory-application-objects.md)**或登錄庫特定的**管理帳戶**登入。 本文提供關於這些身分識別的詳細資訊。



## <a name="service-principal"></a>服務主體

您可以[指派服務主體](container-registry-get-started-azure-cli.md#assign-a-service-principal)到登錄庫，並使用於基本 Docker 驗證。 在大部分情況下，建議使用服務主體。 將服務主體的應用程式識別碼和密碼提供給 `docker login` 命令，如下列範例所示︰

```
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

登入後，Docker 會快取認證，因此您不需要記憶應用程式識別碼。

> [!TIP]
> 如果您想，您可以執行 `az ad sp reset-credentials` 命令以重新產生服務主體的密碼。
>


服務主體允許登錄庫的[角色型存取](../active-directory/role-based-access-control-configure.md)。 可用的角色如下：
  * 讀取者 (僅擁有提取存取權限)。
  * 投稿人 (提取和推送)。
  * 擁有者 (提取、推送及並指派角色給其他使用者)。

Azure Container Registry 無法進行匿名存取。 您可以使用[Docker 中樞](https://docs.docker.com/docker-hub/)存取公用映像。

您可以指派多個服務主體到登錄庫，就能為不同的使用者或應用程式定義存取權。 在如下的開發人員或 DevOps 案例中，服務主體也會啟用與登錄庫的「無周邊」連線：

  * 從登錄庫到協調流程系統的容器部署，包括 DC/OS、Docker Swarm 和 Kubernetes。 您也可以將容器登錄庫提取到相關的 Azure 服務 (例如 [Container Service](../container-service/index.yml)、[App Service](../app-service/index.md)、[Batch](../batch/index.md) 及 [Service Fabric](/azure/service-fabric/) 等)。

  * 建立容器映像，並將其推送到登錄庫的連續整合和部署解決方案 (例如 Visual Studio Team Services 或 Jenkins)。





## <a name="admin-account"></a>管理帳戶
您建立的每個登錄，都會自動建立一個管理帳戶。 此帳戶預設為停用，但您可以啟用它以管理認證，例如透過[入口網站](container-registry-get-started-portal.md#manage-registry-settings)或使用 [Azure CLI 2.0 命令](container-registry-get-started-azure-cli.md#manage-admin-credentials)。 每個管理帳戶會提供兩個可以重新產生的密碼。 這兩個密碼讓您在重新產生其他密碼時，可以使用其中一個密碼來維持對登錄的連線。 如果已啟用此帳戶，您可以傳送使用者名稱和密碼到 `docker login` 命令，向登錄庫進行基本驗證。 例如：

```
docker login myregistry.azurecr.io -u myAdminName -p myPassword1
```

> [!IMPORTANT]
> 管理帳戶是專為單一使用者存取登錄庫而設計，主要用於測試。 不建議和其他使用者共用管理帳戶認證。 對登錄庫而言，所有使用者都會顯示為單一使用者。 變更或停用此帳戶，會停用使用該認證之所有使用者的登錄庫存取權。
>


### <a name="next-steps"></a>後續步驟
* [使用 Docker CLI 推送您的第一個映像](container-registry-get-started-docker-cli.md)。
* 如需容器登錄庫預覽中的驗證詳細資訊，請參閱[部落格文章](https://blogs.msdn.microsoft.com/stevelasker/2016/11/17/azure-container-registry-user-accounts/)。

