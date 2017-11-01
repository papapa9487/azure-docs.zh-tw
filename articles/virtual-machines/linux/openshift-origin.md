---
title: "在 Azure 中部署 OpenShift Origin | Microsoft Docs"
description: "在 Azure 中部署 OpenShift Origin。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 1a40c4cc064b32aced7e976f40f6ed6a57e62204
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-openshift-origin-in-azure"></a>在 Azure 中部署 OpenShift Origin

有多種方式可以在 Azure 中部署 OpenShift Origin。 您可以手動部署所需的所有 Azure 基礎結構元件，然後依照 OpenShift Origin [文件](https://docs.openshift.org/3.6/welcome/index.html) \(英文\) 進行。
您也可以使用能夠簡化 OpenShift Origin 叢集部署的現有 Resource Manager 範本。 一旦這類範本位於[這裡](https://github.com/Microsoft/openshift-origin) \(英文\)。

## <a name="deploy-using-the-openshift-origin-template"></a>使用 OpenShift Origin 範本部署

使用您稍早針對 `aadClientId` 參數所建立之服務主體中的 `appId` 值。

下列範例會建立名為 **azuredeploy.parameters.json**，且具有所有必要輸入的參數檔案。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

### <a name="deploy-using-azure-cli"></a>使用 Azure CLI 部署


> [!NOTE] 
> 下列命令需要 Azure CLI 2.0.8 或更新版本。 您可以使用 `az --version` 命令來確認 az CLI 版本。 若要更新 CLI 版本，請參閱[安裝 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

下列範例會使用 myOpenShiftCluster 的部署名稱，將 OpenShift 叢集和所有相關的資源部署到名稱為 myResourceGroup 的資源群組。 範本直接參考自 Github 儲存機制，而且會使用名為 **azuredeploy.parameters.json** 檔案的本機參數檔案。

```azurecli 
az group deployment create -g myResourceGroup --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

根據部署的節點總數，部署需要至少 25 分鐘才能完成。 當部署完成時，OpenShift 主控台的 URL 和 OpenShift 主機的 DNS 名稱會列印到終端機。

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ssh clusteradmin@myopenshiftmaster.cloudapp.azure.com -p 2200"
}
```

## <a name="connect-to-the-openshift-cluster"></a>連線到 OpenShift 叢集

部署完成時，使用 `OpenShift Console Uri` 可透過瀏覽器連線至 OpenShift 主控台。 或者，您可以使用下列命令，連線至 OpenShift 主機：

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [az group delete](/cli/azure/group#delete) 命令將資源群組、OpenShift 叢集和所有相關資源移除。

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

- [部署後工作](./openshift-post-deployment.md)
- [OpenShift 部署疑難排解](./openshift-troubleshooting.md)
- [開始使用 OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html) \(英文\)
