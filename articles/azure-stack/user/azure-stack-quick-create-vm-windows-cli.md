---
title: "使用 Azure CLI 在 Azure Stack 上建立 Windows 虛擬機器 | Microsoft Docs"
description: "了解如何使用 Azure CLI 在 Azure Stack 上建立 Windows VM"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 196bf4351ebd2bf977102571de385edae6f9612b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-windows-virtual-machine-on-azure-stack-using-azure-cli"></a>使用 Azure CLI 在 Azure Stack 上建立 Windows 虛擬機器

Azure CLI 可用來從命令列建立和管理 Azure Stack 資源。 本指南詳細說明如何使用 Azure CLI 在 Azure Stack 中建立 Windows Server 2016 虛擬機器。 建立虛擬機器之後，您就會與遠端桌面連線、安裝 IIS，然後檢視預設的網站。 

## <a name="prerequisites"></a>必要條件 

* 請確定您的 Azure Stack 操作員已將 “Windows Server 2016” 映像新增到 Azure Stack 市集。  

* Azure Stack 需要特定版本的 Azure CLI，才能建立和管理資源。 如果您尚未針對 Azure Stack 設定 Azure CLI，請依照步驟來[安裝和設定 Azure CLI](azure-stack-connect-cli.md)。

## <a name="create-a-resource-group"></a>建立資源群組

資源群組是在其中部署與管理 Azure Stack 資源的邏輯容器。 從您的開發套件或 Azure Stack 整合系統，執行 [az group create](/cli/azure/group#create) 命令來建立資源群組。 我們已為此文件中的所有變數指派值，您可以使用它們或指派不同的值。 下列範例會在本機位置建立名為 myResourceGroup 的資源群組。

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>建立虛擬機器

使用 [az vm create](/cli/azure/vm#create) 命令來建立 VM。 下列範例會建立名為 myVM 的 VM。 此範例會使用 Demouser 作為系統管理使用者名稱，並使用 Demouser@123 作為密碼。 將這些值更新為適合您環境的值。 連線到虛擬機器連線時需要使用這些值。

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

建立 VM 時，請記下將用於存取 VM 的輸出 PublicIPAddress 參數。
 
## <a name="open-port-80-for-web-traffic"></a>針對 Web 流量開啟連接埠 80

依預設，只能透過 RDP 連線至 Azure Stack 中部署的 Windows 虛擬機器。 如果此 VM 即將成為 Web 伺服器，您需要從網際網路開啟連接埠 80。 使用 [az vm open-port](/cli/azure/vm#open-port) 命令來開啟所需的連接埠。

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>連接至虛擬機器

使用下列命令，建立與虛擬機器的遠端桌面工作階段。 以虛擬機器的公用 IP 位址取代 IP 位址。 出現提示時，請輸入您在建立虛擬機器時所使用的認證。

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>使用 PowerShell 安裝 IIS

您現已登入 Azure VM，可使用一行 PowerShell 來安裝 IIS，並啟用本機防火牆規則以允許 Web 流量通過。 開啟 PowerShell 提示字元並執行下列命令：

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>檢視 IIS 歡迎使用頁面

安裝 IIS 後，現在經由網際網路在您的 VM 上開啟連接埠 80 - 您可以使用所選的網頁瀏覽器來檢視預設 IIS 歡迎使用畫面。 請務必使用您上面記載的公用 IP 位址來瀏覽預設網頁。 

![IIS 預設網站](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png) 

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 [az group delete](/cli/azure/group#delete) 命令來移除資源群組、VM 和所有相關資源。

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>後續步驟

在這個快速入門中，您已部署簡單的 Windows 虛擬機器。 若要深入了解 Azure Stack 虛擬機器，請繼續移至 [Azure Stack 中虛擬機器的考量](azure-stack-vm-considerations.md)。
