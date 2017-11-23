---
title: "Azure Kubernetes 叢集的服務主體 | Microsoft Docs"
description: "在 AKS 中建立和管理 Kubernetes 叢集的 Azure Active Directory 服務主體"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: af27d01108cbfb3bd71023ffbce85f348abb0cfe
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2017
---
# <a name="service-principals-with-azure-container-service-aks"></a>服務主體與 Azure Container Service (AKS)

AKS 叢集需要 [Azure Active Directory 服務主體](../active-directory/develop/active-directory-application-objects.md)，才能與 Azure API 進行互動。 需要服務主體，才能以動態方式管理資源，例如[使用者定義的路由](../virtual-network/virtual-networks-udr-overview.md)及[第 4 層 Azure Load Balancer](../load-balancer/load-balancer-overview.md)。

本文說明為 AKS 中 Kubernetes 叢集設定服務主體的各種選項。

## <a name="before-you-begin"></a>開始之前

本文件中詳述的步驟假設您已建立 AKS 叢集，並建立與叢集的 kubectl 連線。 如果您需要這些項目，請參閱 [AKS 快速入門](./kubernetes-walkthrough.md)。

若要建立 Azure AD 服務主體，您必須有足夠權限向 Azure AD 租用戶註冊應用程式，並將應用程式指派給您訂用帳戶中的角色。 如果您沒有必要的權限，您可能需要要求您的 Azure AD 或訂用帳戶管理員指派必要權限，或預先建立 Kubernetes 叢集的服務主體。

您也必須安裝和設定 Azure CLI 版本 2.0.21 或更新版本。 執行 az --version 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="create-sp-with-aks-cluster"></a>建立 SP 與 AKS 叢集

當使用 `az aks create` 命令部署 AKS 叢集時，您可以選擇自動產生服務主體。

在下列範例中，會建立 AKS 叢集，因為未指定現有服務主體，所以會為叢集建立服務主體。 為了完成這項作業，您的帳戶必須具有建立服務主體的適當權限。

```azurecli
az aks create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>使用現有的 SP

可以使用現有的 Azure AD 服務主體，或預先建立以與 AKS 叢集搭配使用。 從您需要提供服務主體資訊之 Azure 入口網站部署叢集時，很有幫助。

當使用現有的服務主體時，它必須符合下列需求：

- 範圍：用來部署叢集的訂用帳戶
- 角色：參與者
- 用戶端密碼：必須是秘密

## <a name="pre-create-a-new-sp"></a>預先建立新 SP

若要使用 Azure CLI 建立服務主體，請使用 [az ad sp create-for-rbac]() 命令。

```azurecli
id=$(az account show --query id --output tsv)
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/$id"
```

輸出大致如下。 記下 `appId` 和 `password`。 建立 AKS 叢集時，會使用這些值。

```
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>使用現有的 SP

當使用預先建立的服務主體時，提供 `appId` 和 `password` 作為 `az aks create` 命令的引數值。

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myK8SCluster --service-principal <appId> ----client-secret <password>
```

如果從 Azure 入口網站部署 AKS 叢集，請在 AKS 叢集設定表單中輸入這些值。

![瀏覽至 Azure 投票的影像](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>其他考量

當使用 AKS 和 Azure AD 服務主體時，請記住下列項目。

* Kubernetes 的服務主體是叢集組態的一部分。 不過，請勿使用身分識別來部署叢集。
* 每個服務主體都會與 Azure AD 應用程式相關聯。 Kubernetes 叢集的服務主體可與任何有效的 Azure AD 應用程式名稱相關聯 (例如：`https://www.contoso.org/example`)。 應用程式的 URL 不一定是實際端點。
* 指定服務主體的 [用戶端識別碼] 時，您可以使用 `appId` 的值 (如本文所示) 或對應的服務主體`name` (例如，`https://www.contoso.org/example`)。
* 在 Kubernetes 叢集中的主要和節點 VM 上，服務主體認證會儲存在 /etc/kubernetes/azure.json 檔案中。
* 當您使用 `az aks create` 命令自動產生服務主體時，服務主體認證會寫入用來執行命令之電腦上的 ~/.azure/acsServicePrincipal.json 檔案。
* 當您使用 `az aks create` 命令自動產生服務主體時，服務主體也可以向在訂用帳戶中建立的 [Azure Container Registry](../container-registry/container-registry-intro.md) 進行驗證。

## <a name="next-steps"></a>後續步驟

如需有關 Azure Active Directory 服務主體的詳細資訊，請參閱 Azure AD 應用程式文件。

> [!div class="nextstepaction"]
> [應用程式和服務主體物件](../active-directory/develop/active-directory-application-objects.md)
