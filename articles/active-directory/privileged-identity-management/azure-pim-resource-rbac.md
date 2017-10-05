---
title: "Azure PIM 資源 RBAC 概觀 |Microsoft Docs"
description: "取得 PIM 中 RBAC 功能的概觀，包括術語和通知"
services: active-directory
documentationcenter: 
author: barclayn
manager: mbaldwin
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: barclayn
ms.translationtype: HT
ms.sourcegitcommit: a29f1e7b39b7f35073aa5aa6c6bd964ffaa6ffd0
ms.openlocfilehash: e80fe891be661acef5bb30dbf9af023a79ace191
ms.contentlocale: zh-tw
ms.lasthandoff: 09/21/2017

---
# <a name="pim-for-azure-resources-preview"></a>適用於 Azure 資源 的 PIM (預覽)

您現在可以利用 Azure Active Directory Privileged Identity Management (PIM) 來管理、控制和監視組織內，對 Azure 資源 (預覽) 的存取行為。 適用範圍包括訂用帳戶、資源群組甚至是虛擬機器。 任何在 Azure 入口網站內利用 Azure 角色型存取控制 (RBAC) 功能的資源，可善用 Azure AD PIM 所提供的絕佳安全性和生命週期管理功能。更多 Azure AD 角色的功能將於稍後推出。 

## <a name="pim-for-azure-resources-preview-helps-resource-administrators"></a>適用於 Azure 資源的 PIM (預覽) 可協助系統資源管理員

- 查看您所管理 Azure 資源的角色指派到哪些使用者和群組
- 視需要啟用「及時」存取以管理資源，例如「訂用帳戶」、「資源群組」以及其他
- 使用新的時間界限指派設定，讓已指派使用者/群組資源的存取自動到期
- 在快速工作和待命排程上指派暫時資源存取
- 在任何內建或自訂角色上強制執行 Multi-Factor Authentication 以存取資源 
- 在使用者作用中工作階段期間，取得資源存取相互關聯資源活動的報告
- 在新使用者或群組獲指派資源存取，以及啟用符合資格的指派時取得警示

Azure AD PIM 可以管理內建 Azure 資源角色，以及自訂 (RBAC) 角色，包括 (但不限於)：

- 擁有者
- 使用者存取系統管理員
- 參與者
- 安全性系統管理員
- 安全性管理員以及其他

>[!NOTE]
指派至擁有者或使用者存取管理員角色的使用者或群組成員，以及在 Azure AD 中啟用訂閱管理的全域管理員，為資源管理員。 這些系統管理員可指派角色、設定角色設定，並使用適用於 Azure 資源的 PIM 進行檢閱存取。 檢視[Azure 資源內建角色](../role-based-access-built-in-roles.md)清單

## <a name="tasks"></a>工作

在左側導覽功能表的 [工作] 區段中，PIM 提供多種快速存取行為，包括啟用角色、檢視擱置中的啟用/要求、擱置中核准 (適用於 [Azure AD directory 角色](azure-ad-pim-approval-workflow.md)) 和檢閱您的擱置中回應。
當存從 [概覽] 進入點取任何工作功能表項目時，最終檢視會包含 Azure AD 目錄角色和 Azure Resource 角色 (預覽)。 

![](media/azure-pim-resource-rbac/role-settings-details.png)

[我的角色] 包含一份您使用中和合格的角色指派清單，適用於 Azure AD 目錄角色和 Azure 資源角色 (預覽)。

## <a name="activate-roles"></a>啟用角色

啟用角色的 Azure 資源 (預覽) 導入了新的體驗，讓合資格的角色成員可排程在未來日期/時間啟用，並可在上限範圍中選取特定的啟用期間 (由系統管理員設定)。 深入了解[在這裡啟用 Azure AD 角色](../active-directory-privileged-identity-management-how-to-activate-role.md)

![](media/azure-pim-resource-rbac/contributor.png)

從 [啟用] 功能表中，輸入所需的開始日期和時間以啟用角色。 選擇性地減少啟用持續時間 (角色的啟用時間長度)，並輸入理由 (如有必要)；然後按一下 [啟用]。

如果未修改開始日期和時間，將會在數秒內啟用角色。 在 [我的角色] 頁面上，您會看到角色已排入啟用橫幅訊息的佇列。 按一下 [重新整理] 按鈕以清除此訊息。

![](media/azure-pim-resource-rbac/my-roles.png)

如果排定角色在未來日期時間啟用，暫止的要求會出現在左側導覽功能表的 [擱置要求] 索引標籤。 在事件中，已不再需要再啟用角色，使用者可透過按一下頁面右側的 [取消] 按鈕取消要求。

![](media/azure-pim-resource-rbac/pending-requests.png)

## <a name="discover-and-manage-azure-resources"></a>探索和管理 Azure 資源

若要尋找並管理 Azure 資源的角色，選取左側導覽功能表中的 [管理] 索引標籤底下的 Azure 資源 (預覽)。 使用頁面頂端的篩選或搜尋列找出資源。

![](media/azure-pim-resource-rbac/azure-resources.png)

## <a name="resource-dashboards"></a>資源儀表板

「系統管理員檢視」儀表板有四個主要元件。 展示過去七天資源角色啟用的圖表。 此資料範圍限制至所選資源，並會顯示最常見角色的啟用 (擁有者、參與者和使用者存取系統管理員)，也會將所有角色的啟用結合顯示。

啟用圖形的右邊為兩個圖表，顯示依指派類型分類的角色指派分佈，類型為使用者和群組。 選取圖表的一部分可將值更改為特定百分比 (反之亦然)。

![](media/azure-pim-resource-rbac/admin-view.png)

圖表下方會顯示過去 30 天中獲指派新角色的使用者和群組數 (左)，以及以總計指派數排序的角色清單 (遞減)。

![](media/azure-pim-resource-rbac/role-settings.png)

## <a name="manage-role-assignments"></a>管理角色指派

系統管理員管理角色指派，方法是從左側導覽選取 [角色] 或 [成員]。 選取 [角色] 可讓系統管理員將管理工作範圍限制至特定角色，而選取 [成員] 則會顯示該資源的所有使用者和群組角色指派。

![](media/azure-pim-resource-rbac/roles.png)

![](media/azure-pim-resource-rbac/members.png)

>[!NOTE]
如果您有擱置中的啟用角色，檢視成員資格時，系統會在頁面頂端顯示通知橫幅。

## <a name="assign-roles"></a>指派角色

若要將使用者或群組指派至角色，請選取角色 (若要檢視角色)，或在動作列中按一下 [新增] \(如在 [成員] 檢視中)。

![](media/azure-pim-resource-rbac/members2.png)

>[!NOTE]
若要從 [成員] 索引標籤新增使用者或群組，您必須先從 [新增] 功能表選取角色，才能選取使用者或群組。

![](media/azure-pim-resource-rbac/select-role.png)

從目錄選擇使用者或群組。

![](media/azure-pim-resource-rbac/choose.png)

從下拉式功能表中選擇適用的作業類型。 

**及時指派：**為使用者或群組成員提供合資格但不持續的的角色存取，時間可在指定期間或無限期 (如在角色設定中設定)。 

**直接指派：**無須使用者或群組成員即可啟用角色指派 (稱為持續存取)。 Microsoft 建議在短期使用上使用直接指派 (例如待命移位或以時間區分的活動)，這些工作完成時皆不需要存取。

![](media/azure-pim-resource-rbac/membership-settings.png)

指派類型下拉式清單下方的核取方塊可讓您指定指派是否應為永久性 (永久合資格啟用及時指派/永久啟用直接指派)。 若要指定特定指派的持續時間，取消選取核取方塊並修改開始和/或結束日期和時間欄位。

>[!NOTE]
如果其他系統管理員角色已在角色設定中，指定每個指派類型的最長指派時間，則核取方塊可能無法修改。

![](media/azure-pim-resource-rbac/calendar.png)

## <a name="view-activation-and-azure-resource-activity"></a>檢視啟用與 Azure 資源活動

如果您需要查看各種資源的特定使用者所採取了什麼動作，您可以檢閱與特定啟用期間相關聯的 Azure 資源活動 (適用於符合資格的使用者)。 若要開始，請從 [成員] 檢視或從特定角色的成員成員中選取使用者。 結果以圖表形式顯示，包含使用者在 Azure 資源上的動作 (依日期排序)，以及相同時間內最近的角色啟用。

![](media/azure-pim-resource-rbac/user-details.png)

取選特定角色啟用會顯示角色啟用的詳細資料，以及使用者作用中時發生的相應的 Azure Resource 活動。

![](media/azure-pim-resource-rbac/audits.png)

## <a name="modify-existing-assignments"></a>修改現有的指派

若要從使用者/群組詳細資料檢視修改現有的指派，請從頁面頂端的 [動作] 列選取 [變更設定]。 將指派類型變更「為時指派」或「直接指派」。

![](media/azure-pim-resource-rbac/change-settings.png)

## <a name="review-who-has-access-in-a-subscription"></a>檢閱訂用帳戶中具有存取權的使用者

若要檢閱訂用帳戶中的角色指派，請從左側導覽中，選取 [成員] 索引標籤或選取角色，並選擇特定角色以檢視成員。 

從動作列選取 [檢閱] 以檢閱現有的存取權檢閱，然後選取 [新增] 以建立新檢閱。

![](media/azure-pim-resource-rbac/owner.png)

[深入了解存取權檢閱](../active-directory-privileged-identity-management-how-to-perform-security-review.md)

>[!NOTE]
訂用帳戶資源類型僅在此時支援 [檢閱]。

## <a name="configure-role-settings"></a>配置角色設定

配置角色設定會定義套用至 PIM 環境中指派的預設值。 若要為資源定義預設值，請從左側導覽中選取 [角色設定] 索引標籤，或從任何角色中的動作列選取 [角色設定] 按鈕，以檢視目前選項。

從頁面頂端的動作列按一下 [編輯]，可修改每個設定。

![](media/azure-pim-resource-rbac/owner.png)

![](media/azure-pim-resource-rbac/owner02.png)

設定的變更會記錄在 [角色設定] 頁面，包括上次更新的日期時間和變更設定的系統管理員。

![](media/azure-pim-resource-rbac/role-settings-02.png)

## <a name="resource-audit"></a>資源稽核

資源稽核可讓您檢視資源的所有角色活動。 您可以使用預先定義的日期或自訂範圍來篩選資訊。
![](media/azure-pim-resource-rbac/last-day.png)資源稽核也可讓使用者快速檢視使用者的活動詳細資料。 在檢視中，所有的「啟用角色」動作是連結特定要求者的資源活動。
![](media/azure-pim-resource-rbac/resource-audit.png)

## <a name="just-enough-administration"></a>恰到好處的系統管理

配合 PIM 的 Azure 資源，您可在指派資源角色時輕易使用剛恰到好處的系統管理 (JEA) 最佳作法。 使用者和群組與 Azure 訂用帳戶或資源群組中指派的成員，可以現有角色指派在較小的範圍內啟用。 

從 [搜尋] 頁面中，尋找您需要管理的次及資源。

![](media/azure-pim-resource-rbac/azure-resources-02.png)

從左側導覽功能表中選取 [我的角色]，然後選擇適當的角色以啟用。 請註意，系統會繼承指派類型，因為角色已在訂用帳戶中獲指派，而非資源群組中 (如下所示)。

![](media/azure-pim-resource-rbac/my-roles-02.png)

## <a name="next-steps"></a>後續步驟

- [適用於 Azure 資源的內建角色](../role-based-access-built-in-roles.md)
- 深入了解[在這裡啟用 Azure AD 角色](../active-directory-privileged-identity-management-how-to-activate-role.md)
- [PIM 核准工作流程](azure-ad-pim-approval-workflow.md)
