---
title: "在 Azure 中部署 Docker 容器叢集 | Microsoft Docs"
description: "在 Azure Container Service 中使用 Azure 入口網站或 Resource Manager 範本部署 Kubernetes、DC/OS，或 Docker Swarm 解決方案。"
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, Containers, Micro-services, Mesos, Azure, dcos, swarm, kubernetes, azure container service, acs
ms.service: container-service
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: rogardle
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 0ef256537bf095e2a5d582bd345a9c8dcede2095
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2017
---
# <a name="deploy-a-docker-container-hosting-solution-using-the-azure-portal"></a>使用 Azure 入口網站部署 Docker 容器主控解決方案



Azure Container Service 支援快速部署常用的開放原始碼容器叢集和協調流程解決方案。 本文件會逐步引導您使用 Azure 入口網站或Azure Resource Manager 快速入門範本來部署 Azure Container Service 叢集。 

您也可以使用 [Azure CLI 2.0](container-service-create-acs-cluster-cli.md) 或 Azure Container Service API 來部署 Azure Container Service 叢集。

若為背景，請參閱 [Azure Container Service 簡介](../container-service-intro.md)。


## <a name="prerequisites"></a>必要條件

* **Azure 訂用帳戶**：如果您沒有帳戶，請註冊[免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)。 針對較大的叢集，請考慮隨用隨付訂用帳戶或其他購買選項。

    > [!NOTE]
    > 您的 Azure 訂用帳戶使用量和[資源配額](../../azure-subscription-service-limits.md)(例如核心配額) 可以限制您部署的叢集大小。 若要要求增加配額，可免費[開啟線上客戶支援要求](../../azure-supportability/how-to-create-azure-support-request.md)。
    >

* **SSH RSA 公開金鑰**︰透過入口網站或其中一個 Azure 快速入門範本部署時，您必須提供公開金鑰，以便對 Azure Container Service 虛擬機器進行驗證。 若要建立安全殼層 (SSH) RSA 金鑰，請參閱 [OS X 和 Linux](../../virtual-machines/linux/mac-create-ssh-keys.md) 和 [Windows](../../virtual-machines/linux/ssh-from-windows.md)指引。 

* **服務主體用戶端識別碼和密碼** (僅限 Kubernetes)：如需建立 Azure Active Directory 服務主體的詳細資訊和指引，請參閱[關於 Kubernetes 叢集的服務主體](../kubernetes/container-service-kubernetes-service-principal.md)。



## <a name="create-a-cluster-by-using-the-azure-portal"></a>使用 Azure 入口網站建立叢集
1. 登入 Azure 入口網站、選取[新增]，並在 Azure Marketplace 中搜尋 [Azure Container Service]。

    ![Marketplace 中的 Azure Container Service](./media/container-service-deployment/acs-portal1.png)  <br />

2. 按一下 [Azure Container Service]，然後按一下 [建立]。

3. 在 [基本概念] 刀鋒視窗中，輸入下列資訊：

    * **Orchestrator**︰ 選取其中一個容器 Orchestrators 以在叢集上部署。
        * **DC/OS**：部署 DC/OS 叢集。
        * **Swarm**：部署 Docker Swarm 叢集。
        * **Kubernetes**：部署 Kubernetes 叢集。
    * **訂用帳戶**：選取 Azure 訂用帳戶。
    * **資源群組**：輸入部署的新資源群組名稱。
    * **位置**：選取 Azure Container Service 部署的 Azure 區域。 如需可用性，請查看[依區域提供的產品](https://azure.microsoft.com/regions/services/)。
    
    ![基本設定](./media/container-service-deployment/acs-portal3.png)  <br />
    
    準備好繼續時請按一下 [確定]  。

4. 在 [主機組態] 刀鋒視窗中，針對叢集中的 Linux 主要節點輸入下列設定 (有些是每個 Orchestrator 的特定設定)︰

    * **主要 DNS 名稱**︰用來為主要節點建立唯一完整網域名稱 (FQDN) 的前置詞。 主要 FQDN 的格式是 *prefix*mgmt.*location*.cloudapp.azure.com。
    * **使用者名稱**︰在叢集中每個 Linux 虛擬機器上帳戶的使用者名稱。
    * **SSH RSA 公開金鑰**：新增要用於對 Linux 虛擬機器進行驗證的公開金鑰。 請務必不要讓此金鑰包含分行符號，而讓它包含 `ssh-rsa` 前置詞。 `username@domain` 後置詞是選擇性的。 金鑰應如下所示：**ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**。 
    * **服務主體**︰如果您選取 Kubernetes Orchestrator，請輸入 Azure Active Directory 「服務主體用戶端識別碼」(也稱為 appId) 和「服務主體用戶端密碼」。 如需詳細資訊，請參閱[關於 Kubernetes 叢集的服務主體](../kubernetes/container-service-kubernetes-service-principal.md)。
    * **主要主機計數**：叢集中主要主機的數目。
    * **VM 診斷**︰對於某些 Orchestrator，您可以啟用主要節點上的 VM 診斷。

    ![主要組態](./media/container-service-deployment/acs-portal4.png)  <br />

    準備好繼續時請按一下 [確定]  。

5. 在 [代理程式組態] 刀鋒視窗中，輸入下列資訊：

    * **代理程式計數**：若為 Docker Swarm 和 Kubernetes，此值是代理程式擴展集內的初始代理程式數目。 若為 DC/OS，此值是私人擴展集內的初始代理程式數目。 此外，也會建立 DC/OS 的公用擴展集，其中包含預先決定的代理程式數目。 此公用擴展集內的代理程式數目是由叢集中的主要節點數目來決定：一個主要節點需要一個公用代理程式，而三或五個主要節點則需要兩個公用代理程式。
    * **代理程式虛擬機器大小**：代理程式虛擬機器的大小。
    * **作業系統**︰這個設定只有在您選取 Kubernetes Orchestrator 時才可以使用。 選擇要在代理程式上執行 Linux 散發套件或 Windows Server 作業系統。 此設定會決定您的叢集可以執行 Linux 或 Windows 容器應用程式。 

        > [!NOTE]
        > 對於 Kubernetes 叢集，Windows 容器支援處於預覽階段。 在 DC/OS 和 Swarm 叢集上，Azure Container Service 中目前只支援 Linux 代理程式。

    * **代理程式認證**︰如果您選取 Windows 作業系統，請輸入代理程式 VM 的系統管理員「使用者名稱」和「密碼」。 

    ![代理程式組態](./media/container-service-deployment/acs-portal5.png)  <br />

    準備好繼續時請按一下 [確定]  。

6. 服務驗證完成後請按一下 [確定]。

    ![驗證](./media/container-service-deployment/acs-portal6.png)  <br />

7. 檢閱條款。 若要啟動部署程序，請按一下 [建立]。

    如果您已選擇將部署釘選到 Azure 入口網站，您就可以看到部署狀態。

    ![部署狀態](./media/container-service-deployment/acs-portal8.png)  <br />

部署需要數分鐘才能完成。 然後，Azure Container Service 叢集便可供使用。


## <a name="create-a-cluster-by-using-a-quickstart-template"></a>使用快速入門範本建立叢集
Azure 快速入門範本可供在 Azure Container Service 中部署叢集。 提供的快速入門範本可以修改為包含其他或進階 Azure 組態。 若要使用 Azure 快速入門範本建立 Azure Container Service 叢集，您需要 Azure 訂用帳戶。 如果您沒有訂用帳戶，請註冊[免費試用](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)。 

請使用範本和 Azure CLI 2.0 並遵循下列步驟來部署叢集 (請參閱[安裝和設定指示](/cli/azure/install-az-cli2))。

> [!NOTE] 
> 如果您在 Windows 系統上，可以使用 Azure PowerShell 並以類似的步驟來部署範本。 請參閱本節稍後的步驟。 您也可以透過[入口網站](../../azure-resource-manager/resource-group-template-deploy-portal.md)或其他方法來部署範本。

1. 若要部署 DC/OS、Docker Swarm 或 Kubernetes 叢集，請從 GitHub 選取其中一個可用的快速入門範本。 部分清單如下。 除了預設的 Orchestrator 選項有所不同外，DC/OS 和 Swarm 範本完全相同。

    * [DC/OS 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Swarm 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Kubernetes 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. 登入您的 Azure 帳戶 (`az login`)，並確定 Azure CLI 已連接至 Azure 訂用帳戶。 您可以使用下列命令來查看預設訂用帳戶：

    ```azurecli
    az account show
    ```
    
    如果您有多個訂用帳戶，而且需要設定不同的預設訂用帳戶，請執行 `az account set --subscription` 並指定訂用帳戶識別碼或名稱。

3. 最佳作法是使用新的資源群組進行部署。 若要建立資源群組，請使用 `az group create` 命令指定資源群組名稱和位置： 

    ```azurecli
    az group create --name "RESOURCE_GROUP" --location "LOCATION"
    ```

4. 建立包含必要範本參數的 JSON 檔案。 下載名為 `azuredeploy.parameters.json` 的參數檔，其隨附於 GitHub 中的 Azure Container Service 範本 `azuredeploy.json`。 輸入您叢集所需的參數值。 

    例如，若要使用 [DC/OS 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)，請提供 `dnsNamePrefix` 和 `sshRSAPublicKey` 的參數值。 請參閱 `azuredeploy.json` 中的說明和其他參數的選項。  
 

5. 藉由下列命令傳遞部署參數檔，以建立 Container Service 叢集，其中︰

    * **RESOURCE_GROUP** 是您在上一個步驟中建立的資源群組名稱。
    * **DEPLOYMENT_NAME** (選用) 是您提供的部署名稱。
    * **TEMPLATE_URI** 是部署檔案 `azuredeploy.json` 的位置。 此 URI 必須是 Raw 檔案，而不是 GitHub UI 的指標。 若要尋找這個 URI，請在 GitHub 中選取 `azuredeploy.json` 檔案，然後按一下 [Raw] 按鈕。  

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters @azuredeploy.parameters.json
    ```

    您也可以在命令列上以 JSON 格式字串提供參數。 使用類似下列的命令：

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters "{ \"param1\": {\"value1\"} … }"
    ```

    > [!NOTE]
    > 部署需要數分鐘才能完成。
    > 

### <a name="equivalent-powershell-commands"></a>對等的 PowerShell 命令
您也可以使用 PowerShell 部署 Azure Container Service 叢集範本。 這份文件以 1.0 版的 [Azure PowerShell 模組](https://azure.microsoft.com/blog/azps-1-0/)為基礎。

1. 若要部署 DC/OS、Docker Swarm 或 Kubernetes 叢集，請從 GitHub 選取其中一個可用的快速入門範本。 部分清單如下。 請注意，除了預設的 Orchestrator 選項有所不同外，DC/OS 和 Swarm 範本完全相同。

    * [DC/OS 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Swarm 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Kubernetes 範本](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. 在 Azure 訂用帳戶中建立叢集之前，請確認您的 PowerShell 工作階段已登入 Azure。 您若要這麼做，可透過 `Get-AzureRMSubscription` 命令：

    ```powershell
    Get-AzureRmSubscription
    ```

3. 如果您需要登入 Azure，請使用 `Login-AzureRMAccount` 命令：

    ```powershell
    Login-AzureRmAccount
    ```

4. 最佳作法是使用新的資源群組進行部署。 若要建立資源群組，請使用 `New-AzureRmResourceGroup` 命令，並指定資源群組名稱和目的地區域：

    ```powershell
    New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
    ```

5. 建立資源群組後，您就可以使用下列命令來建立叢集。 使用 `-TemplateUri` 參數指定所需範本的 URI。 當您執行此命令時，PowerShell 會提示您輸入部署參數值。

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
    ```

#### <a name="provide-template-parameters"></a>提供範本參數
如果您熟悉 PowerShell，您知道您可以輸入減號 (-) 然後按下 TAB 鍵，循環 Cmdlet 的可用參數。 相同的功能也適用於您在範本中定義的參數。 在您輸入範本名稱之後，Cmdlet 便會立即擷取範本、剖析參數，並將範本參數動態新增至命令。 這可讓您輕鬆指定範本參數值。 另外，如果您忘記必要參數值，則 PowerShell 會出現此值的提示。

以下是包含參數的完整命令。 請對資源名稱提供您自己的值。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>後續步驟
既然您有一個可運作的叢集，請參閱這些文件來了解連接和管理的詳細資料：

* [連接到 Azure Container Service 叢集](../container-service-connect.md)
* [使用 Azure Container Service 和 DC/OS](container-service-mesos-marathon-rest.md)
* [使用 Azure Container Service 和 Docker Swarm](container-service-docker-swarm.md)
* [使用 Azure Container Service 和 Kubernetes](../kubernetes/container-service-kubernetes-walkthrough.md)
