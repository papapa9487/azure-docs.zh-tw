---
title: "將 Azure-SSIS 整合執行階段加入 VNET | Microsoft Docs"
description: "了解如何將 Azure-SSIS 整合執行階段加入 Azure 虛擬網路。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: spelluru
ms.openlocfilehash: 02d9102bfc9b0d8084988609282f2c78b5789e7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>將 Azure-SSIS 整合執行階段加入虛擬網路
如果符合下列其中一種條件，您必須將 Azure-SSIS 整合執行階段 (IR) 加入 Azure 虛擬網路 (VNet)： 

- 您要在屬於 VNet 一部分的 SQL Server 受管理執行個體 (私人預覽) 上裝載 SSIS 目錄資料庫。
- 您想要從 Azure-SSIS 整合執行階段上執行的 SSIS 套件連線至內部部署資料存放區。

 Azure Data Factory 第 2 版 (預覽) 可讓您將 Azure-SSIS 整合執行階段加入傳統 VNet 中。 目前，尚未支援 Azure Resource Manager VNet。 不過，您可以如下節所示暫時解決它。 

如果 SSIS 套件只會存取公用雲端資料存放區，則您不需要將 Azure-SSIS IR 加入 VNet 中。 如果 SSIS 套件存取內部部署資料存放區，則您必須將 Azure-SSIS IR 加入連線至內部部署網路的 VNet 中。 如果在不在 VNet 的 Azure SQL Database 中裝載 SSIS 目錄，您需要開啟適當的連接埠。 如果在傳統 VNet 的 Azure SQL 受管理執行個體中裝載 SSIS 目錄，您可以將 Azure-SSIS IR 加入相同的傳統 VNet (或) 不同的傳統 VNet，而此 VNet 具有與含 Azure SQL 受管理執行個體之 VNet 的傳統對傳統 VNet 連線。 下列各節提供更多詳細資料。  

## <a name="access-on-premises-data-stores"></a>存取內部部署資料存放區
如果 SSIS 套件存取內部部署資料存放區，請將 Azure-SSIS 整合執行階段加入連線至內部部署網路的 VNet 中。 以下是一些需要注意的重要事項： 

- 如果沒有現有 VNet 連線到您的內部部署網路，請先建立 Azure-SSIS 整合執行階段要加入的[傳統 VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 然後，設定從該 VNet 到您內部部署網路的站對站 [VPN 閘道連線](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)/[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) 連線。
- 如果在與 Azure-SSIS 整合執行階段相同的位置中有現有傳統 VNet 連線到您的內部部署網路，則可以在其中加入 Azure-SSIS 整合執行階段。
- 如果在與 Azure-SSIS 整合執行階段不同的位置中有現有傳統 VNet 連線到您的內部部署網路，則可以先建立 Azure-SSIS 整合執行階段要加入的[傳統 VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 然後，設定[傳統對傳統 VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) 連線。
- 如果有現有 Azure Resource Manager VNet 連線到您的內部部署網路，請先建立 Azure-SSIS 整合執行階段要加入的[傳統 VNet](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。 然後，設定[傳統對 Azure Resource Manager VNet](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 連線。

## <a name="domain-name-services-server"></a>網域名稱服務伺服器 
如果您需要在 Azure-SSIS 整合執行階段所加入的 VNet 中使用自己的網域名稱系統 (DNS) 伺服器，請遵循指南以[確定 VNet 中 Azure-SSIS 整合執行階段的節點可以解析 Azure 端點](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server)。

## <a name="network-security-group"></a>網路安全性群組
如果您需要在 Azure-SSIS 整合執行階段所加入的 VNet 中實作網路安全性群組 (NSG)，請允許透過下列連接埠的輸入/輸出流量：

| 連接埠 | 方向 | 傳輸通訊協定 | 目的 | 輸入來源/輸出目的地 |
| ---- | --------- | ------------------ | ------- | ----------------------------------- |
| 10100<br/>20100<br/>30100  | 輸入 | TCP | Azure 服務會使用這些連接埠與 VNet 中的 Azure SSIS 整合執行階段節點進行通訊。 | Internet | 
| 443 | 輸出 | TCP | VNet 中的 Azure-SSIS 整合執行階段節點會使用此連接埠來存取 Azure 服務 (例如，Azure 儲存體、事件中樞等等)。 | 網際網路 | 
| 1433<br/>11000-11999<br/>14000-14999  | 輸出 | TCP | VNet 中的 Azure-SSIS 整合執行階段節點會使用這些連接埠來存取 Azure SQL Database 伺服器所裝載的 SSISDB (不適用於 Azure SQL 受管理執行個體所裝載的 SSISDB)。 | Internet | 

## <a name="script-to-configure-vnet"></a>設定 VNet 的指令碼 
您可以使用[本文](create-azure-ssis-integration-runtime.md)中的引導式 PowerShell 指令碼，以在 VNet 中佈建 Azure-SSIS 整合執行階段。 指令碼會自動設定 VNet 權限和設定，讓您可以將 Azure-SSIS 整合執行階段加入 VNet 中。  


## <a name="use-portal-to-configure-vnet"></a>使用入口網站設定 VNet
執行指令碼是設定 VNet 的最簡單方式。 如果您沒有存取權可以設定 VNet/自動設定失敗，則該 VNet 的擁有者/您可以使用下列步驟嘗試手動設定它們：

### <a name="find-the-resource-id-for-your-azure-vnet"></a>尋找您 Azure VNet 的資源識別碼。
 
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 按一下 [更多服務]。 篩選並選取 [虛擬網路 (傳統)]。
3. 篩選並選取清單中的 [虛擬網路]。 
4. 在 [虛擬網路 (傳統)] 頁面中，選取 [屬性]。 

    ![傳統 VNet 資源識別碼](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. 按一下 [資源識別碼] 的 [複製] 按鈕，將傳統網路的資源識別碼複製至剪貼簿。 將剪貼簿中的識別碼儲存至 OneNote 或檔案中。
6. 按一下左功能表上的 [子網路]，並確定 [可用的位址] 數目大於 Azure-SSIS 整合執行階段中的節點。

    ![VNet 中的可用位址數目](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. 將 **MicrosoftAzureBatch** 加入 VNet 的**傳統虛擬機器參與者**角色。 
    1. 按一下左功能表上的 [存取控制 (IAM)]，然後按一下工具列中的 [新增]。
    
        ![存取控制 -> 新增](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png) 
    2. 在 [新增權限] 頁面中，針對 [角色] 選取 [傳統虛擬機器參與者]。 在 [選取] 文字方塊中鍵入 **MicrosoftAzureBatch**，然後從搜尋結果清單中選取 [MicrosoftAzureBatch]。 
    
        ![新增權限 - 搜尋](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)
    3. 按一下 [儲存] 以儲存設定並關閉頁面。
    
        ![儲存存取設定](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)
    4. 確認您在參與者清單中看到 **MicrosoftAzureBatch**。
    
        ![確認 AzureBatch 存取](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)
5. 確認在具有 VNet 的 Azure 訂用帳戶中註冊 Azure Batch 提供者，或註冊 Azure Batch 提供者。 如果您的訂用帳戶中已經有 Azure Batch 帳戶，則會註冊 Azure Batch 的訂用帳戶。
    1. 在 Azure 入口網站中，按一下入口網站中，按一下左功能表上的 [訂用帳戶]。 
    2. 選取您的 **訂用帳戶**。 
    3. 按一下左邊的 [資源提供者]，並確認 `Microsoft.Batch` 是已註冊的提供者。 
    
        ![confirmation-batch-registered](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

    如果您在清單中看不到 `Microsoft.Batch`，若要進行註冊，請在訂用帳戶中[建立空白 Azure Batch 帳戶](../batch/batch-account-create-portal.md)。 您可以稍後刪除它。 
         


## <a name="next-steps"></a>後續步驟
如需 Azure-SSIS 執行階段的詳細資訊，請參閱下列主題： 

- [Azure-SSIS 整合執行階段](concepts-integration-runtime.md#azure-ssis-integration-runtime)。 本文提供整合執行階段的一般概念性資訊，包括 Azure-SSIS IR。 
- [教學課程：將 SSIS 套件部署至 Azure](tutorial-deploy-ssis-packages-azure.md)。 本文逐步說明如何建立 Azure-SSIS IR，並使用 Azure SQL Database 裝載 SSIS 目錄。 
- [如何：建立 Azure-SSIS 整合執行階段](create-azure-ssis-integration-runtime.md)。 本文展開教學課程，並提供使用 Azure SQL 受管理執行個體 (私人預覽) 以及將 IR 加入 VNet 的指示。 
- [監視 Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). 本文示範如何在傳回的資訊中擷取 Azure-SSIS IR 的相關資訊和狀態描述。 
- [管理 Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). 本文示範如何停止、啟動或移除 Azure-SSIS IR。 它也會示範如何將更多節點新增至 IR，藉此相應放大 Azure-SSIS IR。 
