---
title: "使用 Azure Stack 中的系統管理員入口網站 | Microsoft Docs"
description: "身為 Azure Stack 操作員，您應了解如何使用系統管理員入口網站。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 02c7ff03-874e-4951-b591-28166b7a7a79
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 3a1be7a08fab8ad0253f26e6a0683617bff4b7c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-administrator-portal-in-azure-stack"></a>使用 Azure Stack 中的系統管理員入口網站

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

Azure Stack 中有兩種入口網站：系統管理員入口網站和使用者入口網站 (有時也稱作「租用戶」入口網站)。 身為 Azure Stack 操作員，您可以使用系統管理員入口網站來進行每日的 Azure Stack 管理和作業。 

## <a name="access-the-administrator-portal"></a>存取系統管理員入口網站

如果是開發套件環境，您必須先確定可以透過「遠端桌面連線」或透過虛擬私人網路 (VPN) [連線到開發套件主機](azure-stack-connect-azure-stack.md)。

若要存取系統管理員入口網站，請瀏覽至入口網站 URL，然後使用 Azure Stack 操作員的認證進行登入。 如果是整合系統，則入口網站 URL 會依 Azure Stack 部署的區域名稱和外部完整網域名稱 (FQDN) 之不同而有所不同。

| Environment | 系統管理員入口網站 URL |   
| -- | -- | 
| 開發套件| https://adminportal.local.azurestack.external  |
| 整合系統 | https://adminportal.&lt;*區域*&gt;.&lt;*FQDN*&gt; | 
| | |

 ![系統管理員入口網站](media/azure-stack-manage-portals/image1.png)

在系統管理員入口網站中，您可以執行像是下列各項作業：

* 管理基礎結構 (包括系統健康情況、更新、容量等)
* 植入 Marketplace
* 建立方案和產品
* 為使用者建立訂用帳戶

在 [快速入門教學課程] 圖格中，有最常見工作的線上文件連結。
 
雖然操作員能夠在系統管理員入口網站中建立虛擬機器、虛擬網路及儲存體帳戶等資源，但您應該[登入使用者入口網站](user/azure-stack-use-portal.md)來建立和測試資源。 (快速入門教學課程圖格中的 [建立虛擬機器] 連結會讓您在系統管理員入口網站中建立虛擬機器，但此程序的目的只是要在初始部署之後驗證 Azure Stack)。

## <a name="subscription-behavior"></a>訂用帳戶行為
 
系統管理員入口網站中只有一個訂用帳戶可用。 此訂用帳戶是 [預設提供者訂用帳戶]。 您無法新增任何其他的訂用帳戶，在系統管理員入口網站中使用。

身為 Azure Stack 操作員，您可以從系統管理員入口網站為使用者 (包括您自己) 新增訂用帳戶。 使用者 (包括您自己) 可從系統管理員入口網站存取並使用這些訂用帳戶。 使用者入口網站不提供任何系統管理員入口網站的管理或操作功能存取權。

系統管理員和使用者入口網站是由 Azure Resource Manager 的個別執行個體所支援的。 由於 Resource Manager 的分隔，因此訂用帳戶並不會跨入口網站。 例如，如果您以 Azure Stack 操作員的身分登入使用者入口網站，便無法存取「預設提供者訂用帳戶」。 因此，您就沒有任何系統管理功能的存取權。 您可從公用產品為自己建立訂用帳戶，但系統仍會將您視為租用戶使用者。

  >[!NOTE]
  在開發套件環境中，如果使用者與 Azure Stack 操作員屬於相同的租用戶目錄，就不會封鎖他們登入系統管理員入口網站。 不過，就無法存取任何系統管理功能。 此外，他們也無法從系統管理員入口網站新增訂用帳戶，或是存取使用者入口網站中可供他們使用的供應項目。

## <a name="administrator-portal-tips"></a>系統管理員入口網站秘訣

### <a name="customize-the-dashboard"></a>自訂儀表板

儀表板包含一組預設圖格。 您可以按一下 [編輯儀表板] 來修改預設儀表板，或是按一下 [新增儀表板] 來新增自訂儀表板。 您可以輕鬆地將圖格新增到儀表板中。 例如，您可以按一下 新增，在 供應項目 + 方案 上按一下滑鼠右鍵，然後按一下釘選到儀表板。

### <a name="quick-access-to-online-documentation"></a>快速存取線上文件

若要存取 Azure Stack 操作員文件，請按一下系統管理員入口網站右上角的 說明及支援 圖示 (問號)，然後按一下說明 + 支援。

### <a name="quick-access-to-help-and-support"></a>快速存取說明及支援

如果您按一下系統管理員入口網站右上角的 說明及支援 圖示 (問號)，然後按一下新增支援要求，將會進行下列其中一項操作：

- 如果您使用的是整合系統，此動作會開啟一個網站，可讓您直接向「Microsoft 客戶支援服務」(CSS) 建立支援票證。 若要了解何時應該透過 Microsoft 支援或透過原始設備製造商 (OEM) 硬體廠商支援，請參考 [Azure Stack 系統管理員基本知識](azure-stack-manage-basics.md) 的＜從哪裡取得支援＞一節。
- 如果您使用的是開發套件，則此動作會直接開啟 Azure Stack 論壇網站。 我們會定期留意這些論壇。 由於開發套件是一個評估環境，因此並未透過 Microsoft CSS 提供官方支援。

## <a name="next-steps"></a>後續步驟

- [Azure Stack 中的區域管理](azure-stack-region-management.md)
