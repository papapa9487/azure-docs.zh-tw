---
title: "設定 Azure App Service 的 PremiumV2 層 | Microsoft Docs"
description: "了解如何藉由調整為新的 PremiumV2 定價層，讓 Azure App Service 中的 Web 應用程式、行動應用程式和 API 應用程式獲得更好的效能。"
keywords: "App Service, Azure App Service, 級別, 可調整, App Service方案, App Service 成本"
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: cephalin
ms.openlocfilehash: 92cc8d8b0f67dde95ea2e3fc2f0f083bd8ac8aab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>設定 Azure App Service 的 PremiumV2 層

新的 **PremiumV2** 層提供搭載更快速處理器的 [Dv2 系列 VM](../virtual-machines/windows/sizes-general.md#dv2-series)、SSD 儲存體，以及**標準**層雙倍的記憶體/核心比率。 在本文中，您將了解如何在 **PremiumV2** 層建立應用程式，或將應用程式相應增加為 **PremiumV2** 層。

## <a name="prerequisites"></a>必要條件

若要將 Web 應用程式相應增加為 **PremiumV2**，您在 Azure App Service 中所擁有的 Web 應用程式必須是在低於 **PremiumV2** 的定價層中執行。

<a name="availability"></a>

## <a name="premiumv2-availability"></a>PremiumV2 可用性

PremiumV2 層目前僅提供給 Windows 上的 App Service 使用。 Linux 容器尚未獲得支援。

PremiumV2 目前已在大部分 Azure 區域中推出，且適用範圍仍在擴大。 若要查看您的區域是否可以使用，請在 [Azure Cloud Shell](../cloud-shell/overview.md) 中執行下列 Azure CLI 命令：

```azurecli-interactive
az appservice list-locations --sku P1V2
```

如果您在建立應用程式或 App Service 方案時收到錯誤，很可能表示您所選擇的區域無法使用 **PremiumV2**。

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>在 PremiumV2 層中建立應用程式

App Service 應用程式的定價層會定義在其執行所在的 [App Service 方案](azure-web-sites-web-hosting-plans-in-depth-overview.md)中。 您可以獨自建立 App Service 方案，或在建立 Web 應用程式時順便建立。

在 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>中設定 App Service 方案時，請選取 [定價層]。 

選擇其中一個 **PremiumV2** 選項，然後按一下 [選取]。

![](media/app-service-configure-premium-tier/pick-premium-tier.png)

> [!IMPORTANT] 
> 如果您沒有看到 **P1V2**、**P2V2** 和 **P3V2** 選項，原因可能是您所選擇的區域不提供 **PremiumV2**，也可能是您所設定的是 Linux 的 App Service 方案，因此不支援 **PremiumV2**。

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>將現有應用程式相應增加為 PremiumV2 層

在將現有應用程式相應增加為 **PremiumV2** 層之前，請確定您的區域有提供 **PremiumV2**。 如需資訊，請參閱 [PremiumV2 可用性](#availability)。 如果您的區域未提供，請參閱[從不受支援的區域相應增加](#unsupported)。

視裝載環境而定，相應增加可能需要執行額外的步驟。 

在 <a href="https://portal.azure.com" target="_blank">Azure 入口網站</a>中，開啟您的 App Service 應用程式頁面。

在 App Service 應用程式頁面的左側導覽中，選取 [相應增加 (App Service 方案)]。

![](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

選取其中一個 **PremiumV2** 大小，然後按一下 [選取]。

![](media/app-service-configure-premium-tier/scale-up-tier-select.png)

如果作業成功完成，應用程式的概觀頁面會顯示其已進入 **PremiumV2** 層。

![](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>如果您收到錯誤

某些 App Service 方案無法相應增加為 PremiumV2 層。 如果相應增加作業對您發送錯誤訊息，就表示您的應用程式需要新的 App Service 方案。

在與現有 App Service 應用程式相同的區域和資源群組中，建立 Windows App Service 方案。 遵循[在 PremiumV2 層中建立應用程式](#create)中的步驟，將它設定為 **PremiumV2** 層。 如有需要，請使用與現有 App Service 方案相同的相應放大組態 (執行個體數目、自動調整等等)。

再次開啟 App Service 應用程式頁面。 在 App Service 的左側導覽中，選取 [變更 App Service 方案]。

![](media/app-service-configure-premium-tier/change-plan.png)

選取您剛才建立的 App Service 方案。

![](media/app-service-configure-premium-tier/select-plan.png)

變更作業完成後，應用程式就會在 **PremiumV2** 層執行。

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-region"></a>從不受支援的區域相應增加

如果應用程式是在尚未提供 **PremiumV2** 的區域內執行，您可以將應用程式移到不同區域以利用 **PremiumV2**。 您有兩個選擇：

- 在新的 **PremiumV2** 方案中建立應用程式，然後重新部署應用程式程式碼。 遵循[在 PremiumV2 層中建立應用程式](#create)中的步驟，將它設定為 **PremiumV2** 層。 如有需要，請使用與現有 App Service 方案相同的相應放大組態 (執行個體數目、自動調整等等)。
- 如果應用程式已在現有的**進階**層執行，則可以複製應用程式與所有應用程式設定、連接字串和部署組態。

    ![](media/app-service-configure-premium-tier/clone-app.png)

    在 [複製應用程式] 頁面上，您可以在您想要的區域建立新的 App Service 方案，並指定您要複製的設定。

## <a name="automate-with-scripts"></a>使用指令碼進行自動化

您可以使用 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/overview)，透過指令碼在 **PremiumV2** 層自動建立應用程式。

### <a name="azure-cli"></a>Azure CLI

下列命令會在 P1V2 建立 App Service 方案。 您可以在 Cloud Shell 中執行該命令。 `--sku` 選項包括 P1V2、P2V2 和 P3V2。

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

下列命令會在 P1V2 建立 App Service 方案。 `-WorkerSize` 選項包括 [小型]、[中型] 和 [大型]。

```PowerShell
New-AzureRmAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>其他資源

[在 Azure 中相應增加應用程式的規模](web-sites-scale.md)  
[手動或自動調整執行個體計數](../monitoring-and-diagnostics/insights-how-to-scale.md)