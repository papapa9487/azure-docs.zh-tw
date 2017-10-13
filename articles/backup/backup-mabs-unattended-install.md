---
title: "以無訊息方式安裝 Azure 備份伺服器 v2 | Microsoft Docs"
description: "使用 PowerShell 指令碼來以無訊息方式安裝 Azure 備份伺服器 v2。 這種安裝也稱為自動安裝。"
services: backup
documentationcenter: " "
author: markgalioto
manager: carmonm
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/30/2017
ms.author: markgal;masaran
ms.openlocfilehash: 91778a67f9ef523aa87b7918197e0d0ded0f5702
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="run-an-unattended-installation-of-azure-backup-server-v2"></a>執行 Azure 備份伺服器 v2 的自動安裝

了解如何執行 Azure 備份伺服器 v2 的自動安裝。 

如果您要安裝 Azure 備份伺服器 v1，這些步驟並不適用。

## <a name="install-backup-server-v2"></a>安裝備份伺服器 v2

1. 在裝載 Azure 備份伺服器 v2 的伺服器上建立文字檔  (您可以在記事本或其他文字編輯器中建立檔案)。將檔案儲存為 MABSSetup.ini。 

2. 在 MABSSetup.ini 檔案中貼上下列程式碼。 以您的環境值取代括號內的文字 (\< \>)。 範例如下列文字：

  ```
  [OPTIONS]
  UserName=administrator
  CompanyName=<Microsoft Corporation>
  SQLMachineName=localhost
  SQLInstanceName=<SQL instance name>
  SQLMachineUserName=administrator
  SQLMachinePassword=<admin password>
  SQLMachineDomainName=<machine domain>
  ReportingMachineName=localhost
  ReportingInstanceName=<reporting instance name>
  SqlAccountPassword=<admin password>
  ReportingMachineUserName=<username>
  ReportingMachinePassword=<reporting admin password>
  ReportingMachineDomainName=<domain>
  VaultCredentialFilePath=<vault credential full path and complete name>
  SecurityPassphrase=<passphrase>
  PassphraseSaveLocation=<passphrase save location>
  UseExistingSQL=<1/0 use or do not use existing SQL>
  ```

3. 儲存檔案。 然後，在安裝伺服器之提升權限的命令提示字元中輸入下列命令：

  ```
  start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
  ```

您可以使用下列旗標來進行安裝：</br>
**/f**：.ini 檔案路徑</br>
**/l**：記錄路徑</br>
**/i**：安裝路徑</br>
**/x**：解除安裝路徑</br>

## <a name="next-steps"></a>後續步驟
在安裝備份伺服器之後，請了解如何準備您的伺服器或開始保護工作負載。

- [準備備份伺服器工作負載](backup-azure-microsoft-azure-backup.md)
- [使用備份伺服器來備份 VMware 伺服器](backup-azure-backup-server-vmware.md)
- [使用備份伺服器來備份 SQL Server](backup-azure-sql-mabs.md)
- [在備份伺服器中新增新式備份儲存體](backup-mabs-add-storage.md)
