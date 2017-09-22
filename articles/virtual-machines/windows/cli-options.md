---
title: "在 Windows 上使用 Azure CLI | Microsoft Docs"
description: "在 Windows 上使用 Azure CLI"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/14/2017
ms.author: nepeters
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 0ac4aa10db43fb84119ab97cf78b2d5592cfd277
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---

# <a name="using-the-azure-cli-on-windows"></a>在 Windows 上使用 Azure CLI

Azure 命令列介面 (CLI) 提供可用來建立和管理 Azure 資源的命令列和指令碼環境。 Azure CLI 適用於 Mac OS X、Linux 和 Windows 作業系統。 在這些作業系統中，CLI 命令是完全相同的，但作業系統特定的指令碼語法可能會不同。

此文件詳述該如何在 Windows 上安裝和執行 Azure CLI，以及其各自的語法考量。 如需深入的 Azure CLI 文件，請參閱 [Azure CLI 文件]( https://docs.microsoft.com/en-us/cli/azure/overview) (英文)。

## <a name="windows-subsystem-for-linux"></a>適用於 Linux 的 Windows 子系統

適用於 Linux 的 Windows 子系統 (WSL) 可在 Windows 10 Anniversary Edition 與更新版本提供 Ubuntu Linux 環境。 啟用時，WSL 會提供原生 Bash 體驗，以供用來建立和執行 Azure CLI 指令碼。 因為 WSL 會提供原生 Bash 體驗，Azure CLI 指令碼可以在 Mac OS、Linux 和 Windows 之間共用而不需要修改。

若要在 WSL 使用 Azure CLI，請完成下列工作。

|工作 | 範例的指示 |
|---|---|
| 啟用 WSL | [安裝 WSL 文件](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide) |
| 安裝 Azure CLI |[在 WSL/Ubuntu 14.04 上安裝 CLI](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2#ubuntu)|

## <a name="powershell"></a>PowerShell

Azure CLI 可以原生方式在 Windows 中執行。 在此組態中，Azure CLI 套件會安裝在 Windows 作業系統上，並可從 PowerShell 執行命令。 在此組態中，Azure CLI 命令和指令碼可以執行於任何受支援的 Windows 版本，但必須使用平台特定的指令碼語法。 因此，指令碼不一定能在 Mac OS、Linux 和 Windows 之間共用而不需要修改。

若要在 Windows 上使用 Azure CLI，請使用這些指示安裝套件：[在 Windows 上安裝 CLI](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2#windows)。

## <a name="docker-image"></a>Docker 映像

在使用 Docker for Windows 時，您可以啟動包含 Azure CLI 的 Docker 映像。 此映像是以 Linux 為基礎，並包含原生 Bash 體驗。  在使用 Docker for Windows 和 Azure CLI 映像時，要在 Mac OS、Linux 和 Windows 之間共用的指令碼。 

若要在 Docker for Windows 上使用 Azure CLI，請確定 Docker for Windows 正在執行，並執行下列命令。

```bash
docker run -it azuresdk/azure-cli-python:latest bash
```

完成後，便會啟動已預先載入 Azure CLI 工具的 Bash 工作階段。

## <a name="next-steps"></a>後續步驟

[Azure 虛擬機器的 CLI 範例](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Azure Web Apps 的 CLI 範例](../../app-service/app-service-cli-samples.md)

[Azure SQL 的 CLI 範例](../../sql-database/sql-database-cli-samples.md)

