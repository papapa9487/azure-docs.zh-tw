---
title: "搭配 AKS 使用 Azure 檔案 | Microsoft Docs"
description: "搭配 AKS 使用 Azure 磁碟"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6e88c590e11aa8d2f4ae17e8b5e164483f0a6820
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/02/2017
---
# <a name="using-azure-files-with-kubernetes"></a>搭配 Kubernetes 使用 Azure 檔案

容器型應用程式常常需要存取和保存外部資料磁碟區中的資料。 Azure 檔案可用來作為這個外部資料存放區。 本文將詳細說明如何在 Azure Container Service 中使用 Azure 檔案作為 Kubernetes 磁碟區。

如需有關 Kubernetes 磁碟區的詳細資訊，請參閱 [Kubernetes 磁碟區][kubernetes-volumes]。

## <a name="creating-a-file-share"></a>建立檔案共用

您可以將現有的「Azure 檔案」共用與 Azure Container Service 搭配使用。 如果您需要建立一個共用，請使用以下一組命令。

使用 [az group create][az-group-create] 命令來建立「Azure 檔案」共用的資源群組。 儲存體帳戶的資源群組與 Kubernetes 叢集必須位於相同區域。

```azurecli-interactive
az group create --name myResourceGroup --location westus2
```

使用 [az storage account create][az-storage-create] 命令來建立「Azure 儲存體」帳戶。 儲存體帳戶名稱必須是唯一的。 將 `--name` 引數的值更新成唯一的值。

```azurecli-interactive
az storage account create --name mystorageaccount --resource-group myResourceGroup --sku Standard_LRS
```

使用 [az storage account keys list ][az-storage-key-list] 命令來傳回儲存體金鑰。 將 `--account-name` 引數的值更新成唯一的儲存體帳戶名稱。

記下其中一個金鑰值，在後續的步驟中將會用到此值。

```azurecli-interactive
az storage account keys list --account-name mystorageaccount --resource-group myResourceGroup --output table
```

使用 [az storage share create][az-storage-share-create] 命令來建立「Azure 檔案」共用。 將 `--account-key` 值更新成最後一個步驟中收集的值。

```azurecli-interactive
az storage share create --name myfileshare --account-name mystorageaccount --account-key <key>
```

## <a name="create-kubernetes-secret"></a>建立 Kubernetes 祕密

Kubernetes 必須要有認證才能存取檔案共用。 「Azure 儲存體」帳戶名稱和金鑰不會與每個 Pod 儲存在一起，而是一次性地儲存在 [Kubernetes 祕密][kubernetes-secret]中，然後供每個「Azure 檔案」磁碟區參考。 

Kubernetes 祕密資訊清單中的值必須是 Base64 編碼值。 使用下列命令來傳回已編碼的值。

首先，將儲存體帳戶的名稱編碼。 將 `storage-account` 取代成您的 Azure 儲存體帳戶名稱。

```azurecli-interactive
echo -n <storage-account> | base64
```

接著，需要儲存體帳戶存取金鑰。 執行下列命令來傳回已編碼的金鑰。 將 `storage-key` 取代成先前步驟中收集的金鑰

```azurecli-interactive
echo -n <storage-key> | base64
```

建立名為 `azure-secret.yml` 的檔案，然後將下列 YAML 複製進來。 將 `azurestorageaccountname` 和 `azurestorageaccountkey` 值更新成在上一個步驟中擷取的 Base64 編碼值。

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: azure-secret
type: Opaque
data:
  azurestorageaccountname: <base64_encoded_storage_account_name>
  azurestorageaccountkey: <base64_encoded_storage_account_key>
```

使用 [kubectl apply][kubectl-apply] 命令來建立祕密。

```azurecli-interactive
kubectl apply -f azure-secret.yml
```

## <a name="mount-file-share-as-volume"></a>將檔案共用掛接成磁碟區

您可以藉由在 Pod 的規格中設定磁碟區，將「Azure 檔案」共用掛接到 Pod 中。建立一個名為 `azure-files-pod.yml` 且含有下列內容的新檔案。 將 `share-name` 更新成提供給「Azure 檔案」共用的名稱。

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-files-pod
spec:
 containers:
  - image: kubernetes/pause
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
  - name: azure
    azureFile:
      secretName: azure-secret
      shareName: <share-name>
      readOnly: false
```

使用 kubectl 來建立 Pod。

```azurecli-interactive
kubectl apply -f azure-files-pod.yml
```

您現在有一個 Azure 檔案共用掛接在 `/mnt/azure` 目錄中的執行中容器。 您可以在透過 `kubectl describe pod azure-files-pod`檢查 Pod 時，查看磁碟區掛接。

## <a name="next-steps"></a>後續步驟

使用「Azure 檔案」來深入了解 Kubernetes 磁碟區。

> [!div class="nextstepaction"]
> [適用於 Azure 檔案的 Kubernetes 外掛程式](https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md) \(英文\)

<!-- LINKS -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[az-storage-create]: /cli/azure/storage/account#az_storage_account_create
[az-storage-key-list]: /cli/azure/storage/account/keys#az_storage_account_keys_list
[az-storage-share-create]: /cli/azure/storage/share#az_storage_share_create
[kubectl-apply]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#apply
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[az-group-create]: /cli/azure/group#az_group_create