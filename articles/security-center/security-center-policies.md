---
title: "在 Azure 資訊安全中心設定安全性原則 | Microsoft Docs"
description: "本文件可協助您在「Azure 資訊安全中心」設定安全性原則。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: yurid
ms.openlocfilehash: 67564e930310433bf4d51f7642bdd7ebf7e8e600
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="set-security-policies-in-azure-security-center"></a>在 Azure 資訊安全中心設定安全性原則
這份文件透過帶領您完成執行這項工作的必要步驟，協助您設定資訊安全中心的安全性原則。


## <a name="how-security-policies-work"></a>安全性原則的運作方式為何？
資訊安全中心會為每個 Azure 訂用帳戶自動建立預設安全性原則。 您可以在資訊安全中心編輯原則，或使用 [Azure 原則](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction)來建立新的定義、定義額外的原則，以及在整個管理群組 (可以代表整個組織、其中的營業單位等等) 指派原則，並且監視這些領域中這些原則的合規性。

> [!NOTE]
> Azure 原則是以有限預覽形式提供。 按一下[這裡](https://aka.ms/getpolicy)即可加入。 如需 Azure 原則的詳細資訊，請閱讀[建立和管理原則來強制執行合規性](http://docs.microsoft.com/en-us/azure/azure-policy/create-manage-policy)。

## <a name="how-to-change-security-policies-in-security-center"></a>如何在資訊安全中心內變更安全性原則？
您可以在資訊安全中心內，編輯每個 Azure 訂用帳戶的預設安全性原則。 若要修改安全性原則，您必須是該訂用帳戶或所在管理群組的擁有者、參與者或安全性系統管理員。 登入 Azure 入口網站，並依照後續步驟在資訊安全中心內檢視安全性原則：

1. 在 [資訊安全中心] 儀表板的 [一般] 底下，按一下 [安全性原則]。
2. 選取您要啟用安全性原則的訂用帳戶。

    ![原則管理](./media/security-center-policies/security-center-policies-fig10.png)

3. 在 [原則元件] 區段中，按一下 [安全性原則]。

    ![原則元件](./media/security-center-policies/security-center-policies-fig12.png)

4. 這是透過 Azure 原則指派給資訊安全中心的預設原則。 您可以刪除 [原則和參數] 底下的項目，也可以新增 [可用的選項] 底下的其他原則定義。 若要這樣做，只須按一下定義名稱旁的加號。

    ![原則定義](./media/security-center-policies/security-center-policies-fig11.png)

5. 如果您想要更詳細的原則說明，請按一下該原則，隨即會開啟另一個含有詳細資料的頁面，以及具有 [原則定義 (https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy#policy-definition-structure) 結構的 JSON 程式碼：

    ![Json](./media/security-center-policies/security-center-policies-fig13.png)

6. 編輯完成時，請按一下 [儲存]。

## <a name="see-also"></a>另請參閱
在本文件中，您已了解如何在「Azure 資訊安全中心」設定安全性原則。 若要深入了解「Azure 資訊安全中心」，請參閱下列主題：

* [Azure 資訊安全中心規劃和操作指南](security-center-planning-and-operations-guide.md)。 了解如何規劃及了解採用 Azure 資訊安全中心的設計考量。
* [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md)。 了解如何監視 Azure 資源的健全狀況。
* [管理及回應 Azure 資訊安全中心的安全性警示](security-center-managing-and-responding-alerts.md)。 了解如何管理和回應安全性警示。
* [使用 Azure 資訊安全中心監視合作夥伴解決方案](security-center-partner-solutions.md)。 了解如何監視合作夥伴解決方案的健全狀態。
* [Azure 資訊安全中心常見問題集](security-center-faq.md)。 尋找有關使用服務的常見問題。
* [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/)。 尋找有關 Azure 安全性與相容性的部落格文章。
