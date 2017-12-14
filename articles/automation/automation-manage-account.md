---
title: "管理 Azure 自動化帳戶 | Microsoft Docs"
description: "本文說明如何管理自動化帳戶的組態，例如憑證更新、刪除和設定錯誤。"
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/13/2017
ms.author: magoedte
ms.openlocfilehash: c7ad5f539afc25c7c36712f8af25ce4e4f6d31a7
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2017
---
# <a name="manage-azure-automation-account"></a>管理 Azure 自動化帳戶
有時您必須在自動化帳戶到期前更新憑證。 如果您認為執行身分帳戶遭到盜用，您可加以刪除並重新建立。 本節會討論如何執行這些作業。

## <a name="self-signed-certificate-renewal"></a>自我簽署憑證更新
您為執行身分帳戶建立的自我簽署憑證，會在建立日起算一年後到期。 您可以在該憑證到期前隨時更新憑證。 當您更新憑證時，目前的有效憑證會予以保留，以確保已排入佇列或正在執行以及使用該執行身分帳戶進行驗證的任何 Runbook 不會受到負面影響。 憑證在到期日之前會保持有效。

> [!NOTE]
> 如果您已設定您的自動化執行身分帳戶以使用您的企業憑證授權單位所核發的憑證，而且您使用此選項，該企業憑證會由自我簽署憑證所取代。

若要更新憑證，請執行下列步驟︰

1. 在 Azure 入口網站中，開啟自動化帳戶。

2. 在**自動化帳戶**上 
3. ，於 [帳戶屬性] 窗格中，於 [帳戶設定] 底下選取 [執行身分帳戶]。

    ![自動化帳戶的屬性窗格](media/automation-manage-account/automation-account-properties-pane.png)
3. 在 [執行身分帳戶] 屬性頁面中，選取您想要更新憑證的執行身分帳戶或傳統執行身分帳戶。

4. 在所選帳戶的 [屬性] 窗格上，按一下 [更新憑證]。

    ![更新執行身分帳戶的憑證](media/automation-manage-account/automation-account-renew-runas-certificate.png)

5. 更新憑證時，您可以在功能表的 [通知] 底下追蹤進度。

## <a name="delete-a-run-as-or-classic-run-as-account"></a>刪除執行身分或傳統執行身分帳戶
本節說明如何刪除並重新建立執行身分或傳統執行身分帳戶。 當您執行此動作時，系統不會保留自動化帳戶。 刪除執行身分或傳統執行身分帳戶之後，您可以在 Azure 入口網站中重新建立它。

1. 在 Azure 入口網站中，開啟自動化帳戶。

2. 在 [自動化帳戶] 頁面上，選取 [執行身分帳戶]。

3. 在 [執行身分帳戶] 屬性頁面中，選取您想要刪除的執行身分帳戶或傳統執行身分帳戶。 然後，在所選帳戶的 [屬性] 窗格上，按一下 [刪除]。

 ![刪除執行身分帳戶](media/automation-manage-account/automation-account-delete-runas.png)

4. 刪除帳戶時，您可以在功能表的 [通知] 底下追蹤進度。

5. 帳戶刪除之後，您可以在 [執行身分帳戶] 屬性頁面中，選取建立選項 [Azure 執行身分帳戶]來重新建立它。

 ![重新建立自動化執行身分帳戶](media/automation-manage-account/automation-account-create-runas.png)

## <a name="misconfiguration"></a>設定錯誤
在初始設定期間，您可能會以不正確的方式刪除或建立要讓執行身分或傳統執行身分帳戶正常運作所需的某些設定項目。 這些項目包括︰

* 憑證資產
* 連線資產
* 已從參與者角色移除了執行身分帳戶
* Azure AD 中的服務主體或應用程式

在上述設定錯誤和其他這類情況中，自動化帳戶會偵測到變更，並在帳戶的 [執行身分帳戶] 屬性頁面上顯示 [不完整] 狀態。

![不完整的執行身分帳戶設定狀態](media/automation-manage-account/automation-account-runas-incomplete-config.png)

當您選取執行身分帳戶時，帳戶的 [屬性] 窗格會顯示下列錯誤訊息：

![不完整的執行身分設定警告訊息](media/automation-manage-account/automation-account-runas-incomplete-config-msg.png)。

您可以藉由刪除並重新建立這些執行身分帳戶，來快速解決帳戶的問題。

## <a name="next-steps"></a>後續步驟
* 如需服務主體的詳細資訊，請參閱 [應用程式物件和服務主體物件](../active-directory/active-directory-application-objects.md)。
* 如需 Azure 自動化中角色型存取控制的詳細資訊，請參閱 [Azure 自動化中的角色型存取控制](automation-role-based-access-control.md)。
* 如需有關憑證和 Azure 服務的詳細資訊，請參考 [Azure 雲端服務的憑證概觀](../cloud-services/cloud-services-certs-create.md)。