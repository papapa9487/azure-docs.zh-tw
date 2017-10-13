---
title: "Azure 虛擬機器的多個 IP 位址 - 範本 | Microsoft Docs"
description: "了解如何使用 Azure Resource Manager 範本將多個 IP 位址指派給虛擬機器。"
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2016
ms.author: jdial
ms.openlocfilehash: d4b189fb23dda1167c4f6b17b618c718d32dd98f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本將多個 IP 位址指派給虛擬機器

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

本文說明如何使用 Resource Manager 範本透過 Azure Resource Manager 部署模型建立虛擬機器 (VM)。 透過傳統部署模型部署 VM 時，無法將多個公用和私人 IP 位址指派給相同的 NIC。 若要深入了解 Azure 部署模型，請參閱[了解部署模型](../resource-manager-deployment-model.md)文章。

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="template-description"></a>範本描述

部署範本，可讓您使用不同的組態值，快速且一致地建立 Azure 資源。 如果您不熟悉 Azure Resource Manager 範本，請參閱 [Resource Manager 範本逐步解說](../azure-resource-manager/resource-manager-template-walkthrough.md?toc=%2fazure%2fvirtual-network%2ftoc.json)文章。 這篇文章會利用[使用多個 IP 位址部署 VM](https://azure.microsoft.com/resources/templates/101-vm-multiple-ipconfig) 範本。

<a name="resources"></a>部署範本會建立下列資源︰

|資源|名稱|說明|
|---|---|---|
|網路介面|myNic1|這篇文章的案例一節所述的三個 IP 組態會建立並指派給此 NIC。|
|公用 IP 位址資源|會建立 2 個：myPublicIP 和 myPublicIP2|這些資源會獲得指派靜態公用 IP 位址，並且指派給案例中所述的 IPConfig-1 和 IPConfig-2 IP 組態。|
|VM|myVM1|標準 DS3 VM。|
|虛擬網路|myVNet1|具有一個名為 mySubnet 子網路的虛擬網路。|
|儲存體帳戶|對於部署是唯一的|儲存體帳戶。|

<a name="parameters"></a>在部署範本時，您必須指定下列參數的值︰

|名稱|說明|
|---|---|
|adminUsername|系統管理員使用者名稱。 使用者名稱必須符合 [Azure 使用者名稱需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。|
|adminPassword|系統管理員密碼的密碼必須符合 [Azure 密碼需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。|
|dnsLabelPrefix|PublicIPAddressName1 的 DNS 名稱。 DNS 名稱會解析為指派給 VM 的其中一個公用 IP 位址。 名稱在您建立 VM 所在的 Azure 區域 (位置) 中必須是唯一的。|
|dnsLabelPrefix1|PublicIPAddressName2 的 DNS 名稱。 DNS 名稱會解析為指派給 VM 的其中一個公用 IP 位址。 名稱在您建立 VM 所在的 Azure 區域 (位置) 中必須是唯一的。|
|OSVersion|VM 的 Windows/Linux 版本。 作業系統是所選取之指定 Windows/Linux 版本完全修補的映像。|
|imagePublisher|所選取的 VM 的 Windows/Linux 映像發佈者。|
|imageOffer|所選取的 VM 的 Windows/Linux 映像。|

範本部署的每個資源是使用數個預設設定進行設定。 您可以透過下列方法檢視這些設定︰

- **在 GitHub 上檢視範本︰**如果您熟悉範本，您可以在[範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json)內檢視設定。
- **在部署之後檢視設定︰**如果您不熟悉範本，可以使用下列其中一個章節中的步驟部署範本，然後在部署之後檢視設定。

您可以使用 Azure 入口網站、PowerShell 或 Azure 命令列介面 (CLI) 來部署範本。 所有方法都會產生相同的結果。 若要部署範本，請完成下列其中一個章節中的步驟︰

## <a name="deploy-using-the-azure-portal"></a>使用 Azure 入口網站進行部署

若要使用 Azure 入口網站部署範本，請完成下列步驟：

1. 視需要修改範本。 範本會部署這篇文章的[資源](#resources)一節所列出的資源和設定。 若要深入了解範本以及如何撰寫它們，請參閱[撰寫 Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json)文章。
2. 使用下列其中一個方法部署範本︰
    - **在入口網站中選取範本︰**完成[從自訂範本部署資源](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template)文章中的步驟。 選擇名為 *101-vm-multiple-ipconfig*的預先存在範本。
    - **直接︰**按一下下列的按鈕以直接在入口網站中開啟範本︰<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-multiple-ipconfig%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

不論您選擇的方法為何，您必須提供先前在這篇文章中所列出的[參數](#parameters)值。 部署 VM 之後，連接至 VM 並且將私人 IP 位址新增至您部署的作業系統，方法是完成本文的[將 IP 位址新增至 VM 作業系統](#os-config)一節中的步驟。 請勿將公用 IP 位址新增至作業系統。

## <a name="deploy-using-powershell"></a>使用 PowerShell 進行部署

若要使用 PowerShell 部署範本，請完成下列步驟：

1. 藉由完成[使用 PowerShell 部署範本](../azure-resource-manager/resource-group-template-deploy-cli.md)文章中的步驟來部署範本。 本文章說明部署範本的多個選項。 如果您選擇使用 `-TemplateUri parameter` 進行部署，此範本的 URI 是 *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*。 如果您選擇使用 `-TemplateFile` 參數進行部署，將[範本檔案](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json)的內容從 GitHub 複製到您的電腦上的新檔案。 視需要修改範本內容。 範本會部署這篇文章的[資源](#resources)一節所列出的資源和設定。 若要了解範本以及如何撰寫它們，請參閱[撰寫 Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)文章。

    不論您選擇用來部署範本的選項為何，您必須提供這篇文章的[參數](#parameters)一節中所列的參數值。 如果您選擇使用參數檔案提供參數，將[參數檔案](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json)的內容從 GitHub 複製到您的電腦上的新檔案。 修改檔案中的值。 使用您建立的檔案做為 `-TemplateParameterFile` 參數的值。

    若要判斷 OSVersion、ImagePublisher 和 imageOffer 參數的有效值，請完成[瀏覽和選取 Windows VM 映像](../virtual-machines/windows/cli-ps-findimage.md)文章中的步驟。

    >[!TIP]
    >如果您不確定 dnslabelprefix 是否可供使用，請輸入 `Test-AzureRmDnsAvailability -DomainNameLabel <name-you-want-to-use> -Location <location>` 命令以了解。如果可以使用，命令會傳回 `True`。

2. 部署 VM 之後，連接至 VM 並且將私人 IP 位址新增至您部署的作業系統，方法是完成本文的[將 IP 位址新增至 VM 作業系統](#os-config)一節中的步驟。 請勿將公用 IP 位址新增至作業系統。

## <a name="deploy-using-the-azure-cli"></a>使用 Azure CLI 進行部署

若要使用 Azure CLI 1.0 部署範本，請完成下列步驟：

1. 藉由完成[使用 Azure CLI 部署範本](../azure-resource-manager/resource-group-template-deploy-cli.md)文章中的步驟來部署範本。 本文章說明部署範本的多個選項。 如果您選擇使用 `--template-uri` (-f) 進行部署，此範本的 URI 是 *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*。 如果您選擇使用 `--template-file` (-f) 參數進行部署，將[範本檔案](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json)的內容從 GitHub 複製到您的電腦上的新檔案。 視需要修改範本內容。 範本會部署這篇文章的[資源](#resources)一節所列出的資源和設定。 若要了解範本以及如何撰寫它們，請參閱[撰寫 Azure Resource Manager 範本](../azure-resource-manager/resource-group-authoring-templates.md)文章。

    不論您選擇用來部署範本的選項為何，您必須提供這篇文章的[參數](#parameters)一節中所列的參數值。 如果您選擇使用參數檔案提供參數，將[參數檔案](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json)的內容從 GitHub 複製到您的電腦上的新檔案。 修改檔案中的值。 使用您建立的檔案做為 `--parameters-file` (-e) 參數的值。

    若要判斷 OSVersion、ImagePublisher 和 imageOffer 參數的有效值，請完成[瀏覽和選取 Windows VM 映像](../virtual-machines/windows/cli-ps-findimage.md)文章中的步驟。

2. 部署 VM 之後，連接至 VM 並且將私人 IP 位址新增至您部署的作業系統，方法是完成本文的[將 IP 位址新增至 VM 作業系統](#os-config)一節中的步驟。 請勿將公用 IP 位址新增至作業系統。

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
