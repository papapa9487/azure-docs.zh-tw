---
title: "Microsoft Azure Stack 開發套件版本資訊 | Microsoft Docs"
description: 
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: helaw
ms.openlocfilehash: b3f8768b612f476485cb45cfc47b8b7ab0f00e96
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-development-kit-release-notes"></a>Azure Stack 開發套件版本資訊

適用於：Azure Stack 開發套件

這些版本資訊提供 Azure Stack 開發套件中的新功能和已知問題的相關資訊。  如果您不確定所執行的版本，您可以使用[入口網站來進行檢查](azure-stack-updates.md#determine-the-current-version)。

## <a name="release-build-201709283"></a>版本組建 20170928.3

### <a name="known-issues"></a>已知問題

#### <a name="powershell"></a>PowerShell

Azure PowerShell 1.2.11 尚未推出，但本週稍後將追蹤某個版本。 在這之前，請考慮下列問題：
* App Service、SQL 配接器以及 MYSQL 配接器資源提供者均依存於 PowerShell 1.2.11。 因此，會將它們延遲，直到新版 Azure PowerShell 推出為止。
* 即使 Azure PowerShell 1.2.11 尚未推出，文件中仍會提及它。
* AzureStack-Tools 仍會參考 Azure PowerShell 1.2.10，而且保持如此，直到 1.2.11 推出為止。
* Azure PS 1.2.10 仍可用於搭配開發套件進行許多作業。 當 Azure PS 1.2.11 推出時，需要將它取代。



#### <a name="deployment"></a>部署
* 在部署期間，您必須使用 IP 位址指定時間伺服器。  

#### <a name="portal"></a>入口網站
* 您可能會在入口網站中看到空白的儀表板。  您可以選取位於入口網站右上角的齒輪，然後選取 [還原預設設定]，以還原儀表板。
* 租用戶不需要訂用帳戶就能瀏覽完整的市集，而且將會看到如方案和供應項目的管理項目。  對租用戶而言，這些項目都是非功能性項目。
* 資源群組下的 [移動] 按鈕已停用。  這個已停用的按鈕是預期的行為，因為目前不支援在訂用帳戶之間移動資源群組。
* 您無法使用 Azure Stack 入口網站檢視訂用帳戶的權限。  解決方法是，可以使用 Powershell 確認權限。
* 您會看到警示，通知您註冊您的 Azure Stack 開發套件。  此警示是預期的行為。  
  

#### <a name="services"></a>服務
* 雖然可以透過範本建立虛擬機器擴展集，但是沒有可用於建立它們的市集體驗。
* 您無法使用入口網站的公用 IP 位址建立負載平衡器。  作為因應措施，您可以使用 PowerShell 來建立負載平衡器。
* VM 可用性設定組只能設定一個容錯網域和一個更新網域。  
* 在訂用帳戶中建立第一個 Azure 函式之前，租用戶必須先註冊儲存體資源提供者。
* 刪除租用戶訂用帳戶會導致產生孤立的資源。  因應措施是，先刪除租用戶資源或整個資源群組，然後再刪除租用戶訂用帳戶。 
* 建立網路負載平衡器時，必須建立 NAT 規則。 如果沒有，在建立負載平衡器之後嘗試新增 NAT 規則時會收到錯誤。
* 系統會提供選項，供租用戶建立具備異地備援儲存體的虛擬機器。  此設定會導致虛擬機器建立失敗。
* 這最多可能需要一個小時，然後租用戶才能在新的 SQL 或 MySQL SKU 中建立資料庫。 
* 不支援在不是由資源提供者執行的 SQL 和 MySQL 主控伺服器中直接建立項目，且可能會導致不相符的狀態。
* 不得使用基礎結構備份刀鋒視窗。


#### <a name="fabric"></a>網狀架構
* 計算資源提供者會顯示未知的狀態。
* 縮放單位節點的基本資訊中不會顯示 BMC IP 位址和模型。  這是 Azure Stack 開發套件中的預期行為。

