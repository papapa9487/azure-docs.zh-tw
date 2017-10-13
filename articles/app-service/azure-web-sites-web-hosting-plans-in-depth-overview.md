---
title: "Azure App Service 方案深入概觀 | Microsoft Docs"
description: "了解 Azure App Service 之應用程式服務方案的運作方式，以及在管理經驗上帶來的效益。"
keywords: "App Service, Azure App Service, 級別, 可調整, App Service方案, App Service 成本"
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: byvinyal
ms.openlocfilehash: 688f57de662fec6a04227c35d6578097c795c6da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-app-service-plans-in-depth-overview"></a>Azure App Service 方案深入概觀

App Service 方案代表用來裝載應用程式的實體資源集合。

App Service 方案可定義：

- 區域 (美國西部、美國東部等)
- 級別計數 (一、二、三個執行個體等)
- 執行個體大小 (小型、中型、大型)
- SKU (免費、共用、基本、標準、進階、PremiumV2、隔離)

[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 中的 Web Apps、Mobile Apps、Function Apps (或 Functions) 全部都在 App Service 方案中執行。  相同訂用帳戶及區域中的應用程式可以共用 App Service 方案。 

所有指派給 **App Service 方案**的應用程式都會共用其所定義的資源。 這可讓您在單一 App Service 方案中託管多個應用程式時，能夠節省成本。

您的 **App Service 方案**涵蓋 [免費] 和 [共用] 層，以及 [基本]、[標準]、[進階] 和 [隔離] 層。 每個較高層級都可讓您存取更多的資源與功能。

如果 App Service 方案設為 [基本] 或更高的層級，則您可以控制 VM 的**大小**和級別計數。

比方說，如果您的方案設定為使用**標準**層中的兩個「小型」執行個體，則該方案中的所有應用程式會在兩個執行個體上執行。 應用程式也可以存取**標準**層功能。 應用程式在其上執行的方案執行個體完全受管理且高度可用。

> [!IMPORTANT]
> App Service 方案的定價層 (SKU) 可決定成本，而不是其中裝載的應用程式數目。

本文探討 App Service 方案的重要特性 (例如定價層和級別)，以及這些特性在您管理應用程式時的運作方式。

## <a name="new-pricing-tier-premiumv2"></a>新的定價層：PremiumV2

新的 **PremiumV2** 層提供搭載更快速處理器的 [Dv2 系列 VM](../virtual-machines/windows/sizes-general.md#dv2-series)、SSD 儲存體，以及**標準**層雙倍的記憶體/核心比率。 **PremiumV2** 也支援透過增加執行個體計數來提高規模，同時仍提供標準方案中的所有進階功能。 現有**進階**層中可用的所有功能都包含在 **PremiumV2** 中。

類似於其他專用層，此層有三個可用的 VM 大小：

- 小型 (1 個 CPU 核心，3.5 GiB 記憶體) 
- 中型 (2 個 CPU 核心，7 GiB 記憶體) 
- 大型 (4 個 CPU 核心，14 GiB 記憶體)  

如需 **PremiumV2** 價格資訊，請參閱 [App Service 價格](/pricing/details/app-service/)。

若要開始使用新的 **PremiumV2** 定價層，請參閱[設定 App Service 的 PremiumV2 層](app-service-configure-premium-tier.md)。

## <a name="apps-and-app-service-plans"></a>應用程式和 App Service 方案

在任何指定時間，應用程式服務方案中的一個應用程式只能與一個應用程式服務方案產生關聯。

應用程式和方案都包含在**資源群組**中。 資源群組可做為其內各項資源的生命週期界限。 您可以使用資源群組集中管理應用程式的一切層面。

單一資源群組可擁有多個 App Service 方案，因此您可以將不同的應用程式配置到不同的實際資源。

例如，您可以將資源分隔為開發、測試和生產環境。 將生產和開發/測試的環境分開可讓您隔離資源。 如此一來，對您應用程式的新版本進行負載測試就不會與您的生產應用程式競爭相同的資源，生產應用程式是為真實的客戶提供服務。

當您在單一資源群組中有多個方案時，您也可以定義一個跨越地理區域的應用程式。

例如，在兩個區域中執行的高度可用應用程式包括至少兩個方案，每個區域一個方案，而一個應用程式則與每個方案相關聯。 在這種情況下，應用程式的所有複本都在單一資源群組內。 將多個方案和多個應用程式放在一個資源群組中，可輕鬆管理、控制和檢視應用程式的健康情況。

## <a name="create-an-app-service-plan-or-use-existing-one"></a>建立 App Service 方案或使用現有的方案

在 App Service 中建立新的 Web 應用程式時，將應用程式放入現有的 App Service 方案中，即可共用裝載的資源。 若要判斷新的應用程式是否會有所需的資源，您必須了解現有 App Service 方案的容量，以及新應用程式預期的負載。 過度配置的資源可能會導致新的和現有應用程式停機。

如果有下列情況，建議將您的應用程式隔離至新的 App Service 方案中：

- 應用程式會耗用大量資源。
- 應用程式的縮放係數不同於現有方案中裝載的其他應用程式。
- 應用程式需要不同地理區域中的資源。

這樣可讓您為應用程式配置一組新的資源，更充分掌控您的應用程式。

## <a name="create-an-app-service-plan"></a>建立應用程式服務方案

> [!TIP]
> 如果您有 App Service 環境，請參閱[在 App Service 環境中建立 App Service 方案](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)。

您可以在應用程式建立期間，建立空白的 App Service 方案。

在 [Azure 入口網站](https://portal.azure.com)中，按一下 **[新增]** > **[Web + 行動]**，然後選取 **[Web 應用程式]** 或其他 App Service 應用程式種類。

![在 Azure 入口網站中建立應用程式。][createWebApp]

接著，您可以選取或建立新應用程式的應用程式服務方案。

 ![建立 App Service 方案。][createASP]

若要建立 App Service 方案，請按一下 [[+] 建立新的]，輸入 [App Service 方案] 名稱，然後選取適當 [位置]。 按一下 [定價層] ，然後為服務選取適當的定價層。 選取 [檢視全部] 以檢視其他價格選項，例如 [免費] 和 [共用]。 選取定價層後，請按一下 [選取]  按鈕。

## <a name="move-an-app-to-a-different-app-service-plan"></a>將應用程式移到不同的應用程式服務方案

您可以在 [Azure 入口網站](https://portal.azure.com)中將應用程式移至不同的 App Service 方案。 只要方案都位於_相同的資源群組與地理區域_中，您就可以在這些方案之間移動應用程式。

若要將應用程式移到另一個方案︰

- 瀏覽至您想要移動的應用程式。
- 在 [功能表] 中，尋找 [App Service 方案]區段。
- 選取 [變更 App Service 方案]，以啟動程序。

[變更 App Service 方案] 會開啟 [App Service 方案] 選取器。 此時，您可以挑選要將此應用程式移往的現有方案。 只會顯示相同資源群組和區域中的方案。

![App Service 方案選取器。][change]

每個方案都有其專屬定價層。 比方說，如果將網站從免費層移至標準層，則所有指派給它的應用程式可以使用標準層的所有功能和資源。

## <a name="clone-an-app-to-a-different-app-service-plan"></a>將應用程式移到不同的 App Service 方案

如果您想要將應用程式移至不同的區域，有一個替代方法是複製應用程式。 複製會在任何區域中的新或現有 App Service 方案中產生您應用程式的複本。

您可以在功能表的 [開發工具] 區段中找到 [複製應用程式]。

> [!IMPORTANT]
> 複製具有某些限制，若要深入了解，請閱讀 [Azure App Service 應用程式複製](app-service-web-app-cloning.md)。

## <a name="scale-an-app-service-plan"></a>調整 App Service 方案

有三種方式可以調整方案：

- **變更方案的定價層**。 基本層的方案可以轉換成「標準」，所有指派給它的應用程式將使用標準層的功能。
- **變更方案的執行個體大小**。 例如，基本層中使用小型執行個體的方案可以變更為使用大型執行個體。 與該方案相關聯的所有應用程式現在都可以使用較大執行個體大小所提供的額外記憶體和 CPU 資源。
- **變更方案的執行個體計數**。 例如，已相應放大為 3 個執行個體的標準方案可調整為 10 個執行個體。 進階方案可以相應放大為 20 個執行個體 (視實際情況而定)。 與該方案相關聯的所有應用程式現在都可以使用較大執行個體計數所提供的額外記憶體和 CPU 資源。

您可以按一下應用程式或 App Service 方案設定下方的 [相應增加]  項目，來變更定價層和執行個體大小。 變更會套用到 App Service 方案，並影響它裝載的所有應用程式。

 ![設定值以相應增加應用程式。][pricingtier]

## <a name="app-service-plan-cleanup"></a>App Service 方案清除

> [!IMPORTANT]
> 沒有相關聯應用程式的 **App Service 方案**仍會產生費用，因為它們會繼續保留計算容量。

為了避免非預期的費用，刪除 App Service 方案中裝載的最後一個應用程式時，也會根據預設刪除所產生的空白 App Service 方案。

## <a name="summary"></a>摘要

應用程式服務方案代表可跨應用程式共用的一組特性和功能。 App Service 方案可讓您將特定應用程式彈性地配置給一組資源，並進一步最佳化 Azure 資源使用率。 如此一來，如果您想要節省測試環境的成本，則可以跨多個應用程式共用方案。 透過延展到多個區域和方案，也可以最大化生產環境的輸送量。

## <a name="whats-changed"></a>變更的項目

- 如需從網站變更為 App Service 的指南，請參閱： [Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)

[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
