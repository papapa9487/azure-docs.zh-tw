---
title: "使用 Azure 角色型存取控制管理備份 | Microsoft Docs"
description: "使用角色型存取控制來管理復原服務保存庫中的備份管理作業存取權。"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 3bd46b97-4b29-47a5-b5ac-ac174dd36760
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/22/2017
ms.author: trinadhk;markgal
ms.translationtype: HT
ms.sourcegitcommit: cf381b43b174a104e5709ff7ce27d248a0dfdbea
ms.openlocfilehash: d0b6eb8eea8971eb8f80c6623f9a41a3692241b3
ms.contentlocale: zh-tw
ms.lasthandoff: 08/23/2017

---

# <a name="use-role-based-access-control-to-manage-azure-backup-recovery-points"></a>使用角色型存取控制來管理 Azure 備份復原點
Azure 角色型存取控制 (RBAC) 可以對 Azure 進行更細緻的存取權管理。 RBAC 可讓您區隔小組內的職責，而僅授與使用者執行作業所需的存取權。

> [!IMPORTANT]
> Azure 備份所提供的角色僅限執行可在 Azure 入口網站或復原服務保存庫 PowerShell Cmdlet 中執行的動作。 在 Azure 備份代理程式用戶端 UI、System Center Data Protection Manager UI 或 Azure 備份伺服器 UI 中執行的動作則非這些角色所能控制。

Azure 備份提供 3 種用來控制備份管理作業的內建角色。 深入了解 [Azure RBAC 內建角色](../active-directory/role-based-access-built-in-roles.md)

* [備份參與者](../active-directory/role-based-access-built-in-roles.md#backup-contributor) - 此角色具有所有用來建立和管理備份的權限，但用來建立復原服務保存庫和賦予他人存取權的權限除外。 您可以將此角色想做是管理備份的系統管理員，其可執行每一種備份管理作業。
* [備份操作員](../active-directory/role-based-access-built-in-roles.md#backup-operator) - 此角色擁有參與者的所有權限，但用來移除備份和管理備份原則的權限除外。 此角色相當於參與者，但無法執行破壞性作業，例如停止備份並刪除資料，或移除內部部署資源的註冊。
* [備份讀取者](../active-directory/role-based-access-built-in-roles.md#backup-reader) - 此角色擁有用來檢視所有備份管理作業的權限。 您可以將此角色想做是監視者。

如果您想要定義自己的角色，獲得更進一步控制，請參閱如何建立 [Azure RBAC 中的自訂角色](../active-directory/role-based-access-control-custom-roles.md)。



## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>將備份的內建角色對應至備份管理動作
下表會擷取備份管理動作與執行該作業所需的最小對應 RBAC 角色。

| 管理作業 | 所需的最小 RBAC 角色 |
| --- | --- |
| 建立復原服務保存庫 | 保存庫資源群組的參與者 |
| 啟用 Azure VM 的備份 | 在保存庫上為備份操作員，在 VM 上為虛擬機器參與者 |
| VM 的隨選備份 | 備份操作員 |
| 還原 VM | VM 和 Vnet 將部署之所在位置的備份操作員和資源群組參與者 |
| 從 VM 備份還原磁碟、個別檔案 | 備份操作員 |
| 建立 Azure VM 備份的備份原則 | 備份參與者 |
| 修改 Azure VM 備份的備份原則 | 備份參與者 |
| 刪除 Azure VM 備份的備份原則 | 備份參與者 |
| 在 VM 備份上停止備份 (保留資料或刪除資料) | 備份參與者 |
| 註冊內部部署 Windows Server/用戶端/SCDPM 或 Azure 備份伺服器 | 備份操作員 |
| 刪除已註冊的內部部署 Windows Server/用戶端/SCDPM 或 Azure 備份伺服器 | 備份參與者 |

## <a name="next-steps"></a>後續步驟
* [角色型存取控制](../active-directory/role-based-access-control-configure.md)：開始在 Azure 入口網站中使用 RBAC。
* 了解如何使用下列各項管理存取權：
  * [PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)
  * [Azure CLI](../active-directory/role-based-access-control-manage-access-azure-cli.md)
  * [REST API](../active-directory/role-based-access-control-manage-access-rest.md)
* [角色型存取控制疑難排解](../active-directory/role-based-access-control-troubleshooting.md)︰取得修正常見問題的建議。

