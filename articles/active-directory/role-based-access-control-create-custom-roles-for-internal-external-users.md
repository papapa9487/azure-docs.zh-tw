---
title: "建立自訂的角色型存取控制角色，並指派給 Azure 中的內部和外部使用者 | Microsoft Docs"
description: "將針對內部和外部使用者使用 PowerShell 和 CLI 所建立的自訂 RBAC 角色進行指派"
services: active-directory
documentationcenter: 
author: andreicradu
manager: catadinu
editor: kgremban
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/10/2017
ms.author: a-crradu
ms.translationtype: HT
ms.sourcegitcommit: 890acae2aebf7684e567b9b49377ca7b6da95245
ms.openlocfilehash: cccd0af0c991efe330567c2459717798d116e68f
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---
## <a name="intro-on-role-based-access-control"></a>角色型存取控制簡介

角色型存取控制是僅限 Azure 入口網站使用的功能，可讓訂用帳戶擁有者將細微角色指派給其他能夠管理其環境中特定資源範圍的使用者。

RBAC 可提供更好的安全性管理，對象包括大型組織，以及與需要存取您環境中的特定資源，但不一定需要存取整個基礎結構或任何計費相關範圍的外部共同作業者、廠商或自由工作者共同合作的 SMB。 RBAC 能提供彈性，可擁有一個系統管理員帳戶 (訂用帳戶等級中的服務系統管理員角色) 所管理的 Azure 訂用帳戶，並邀請多個使用者在相同的訂用帳戶下運作，而無需任何系統管理權限。 從管理和計費的觀點而言，RBAC 功能證實是各種情況中使用 Azure 的有效時間和管理選項。

## <a name="prerequisites"></a>必要條件
在 Azure 環境中使用 RBAC 需要︰

* 將獨立的 Azure 訂用帳戶指派至使用者作為擁有者 (訂用帳戶角色)
* 具有 Azure 訂用帳戶的擁有者角色
* 具有 [Azure 入口網站](https://portal.azure.com)的存取權
* 請確定使用者訂用帳戶已登錄下列資源提供者︰**Microsoft.Authorization**。 如需有關如何登錄資源提供者的詳細資訊，請參閱 [Resource Manager 提供者、區域、API 版本及結構描述](/azure-resource-manager/resource-manager-supported-services.md)。
<!---Loc Comment: Link [Resource Manager providers, regions, API versions and schemas] is broken with an error message "404 - Content Not Found--->

> [!NOTE]
> 從 O365 入口網站佈建的 Office 365 訂用帳戶或 Azure Active Directory 授權 (例如︰存取 Azure Active Directory) 沒有資格使用 RBAC。

## <a name="how-can-rbac-be-used"></a>如何使用 RBAC
RBAC 在 Azure 中可套用於三個不同範圍。 從最低到最高的範圍，如下所示︰

* 訂用帳戶 (最高)
* 資源群組
* 資源範圍 (向個別 Azure 資源範圍提供目標權限的最低存取等級)

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>指派訂用帳戶範圍內的 RBAC 角色
使用 RBAC 時，有兩個常見的範例 (但是不限於)︰

* 邀請來自組織 (並非管理使用者 Azure Active Directory 租用戶的一部分) 外部的使用者來管理特定的資源或是整個訂用帳戶
* 適用於組織內部 (它們屬於使用者 Azure Active Directory 租用戶的一部分) 的使用者，但屬於不同小組或群組的使用者除外，它們需要細微存取整個訂用帳戶，或特定資源群組或環境中之資源範圍

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>為 Azure Active Directory 外部的使用者授與訂用帳戶等級的存取權
只有訂用帳戶的**擁有者**可授與 RBAC 角色，因此必須使用此角色已預先指派或已建立 Azure 訂用帳戶的使用者名稱來記錄管理使用者。

從 Azure 入口網站中，在您以管理員身分登入之後，請選取「訂用帳戶」，並選擇所需的訂用帳戶。
![Azure 入口網站中的訂用帳戶刀鋒視窗](./media/role-based-access-control-create-custom-roles-for-internal-external-users/0.png) 根據預設，如果管理使用者已購買 Azure 訂用帳戶，使用者就會顯示為**帳戶管理員**，這是訂用帳戶角色。 如需 Azure 訂用帳戶角色的詳細資訊，請參閱[新增或變更管理訂用帳戶或服務的 Azure 系統管理員角色](/billing/billing-add-change-azure-subscription-administrator.md)。

在此範例中，使用者 "alflanigan@outlook.com" 是「預設租用戶 Azure」AAD 租用戶中的「免費試用版」訂用帳戶之**擁有者**。 這個使用者是利用初始 Microsoft 帳戶 “Outlook” (Microsoft Account = Outlook, Live etc.) 的 Azure 訂用帳戶建立者，因此這個租用戶中新增之所有其他使用者的預設網域名稱為 **"@alflaniganuoutlook.onmicrosoft.com"**。 根據設計，新網域的語法構成方式是，將建立租用戶的使用者之使用者名稱和網域名稱加以組合，並新增 **".onmicrosoft.com"** 延伸。
此外，使用者在新增及驗證新租用戶的自訂網域名稱之後，可以使用租用戶中的自訂網域名稱進行登入。 如需如何驗證 Azure Active Directory 租用戶中自訂網域名稱的詳細資訊，請參閱[將自訂網域名稱新增至您的目錄](/active-directory/active-directory-add-domain)。

在此範例中，「預設租用戶 Azure」目錄僅包含具有 "@alflanigan.onmicrosoft.com" 網域名稱的使用者。

選取訂用帳戶之後，管理使用者必須依序按一下 [存取控制 (IAM)] 以及 [新增角色]。





![Azure 入口網站中的存取控制 IAM 功能](./media/role-based-access-control-create-custom-roles-for-internal-external-users/1.png)





![在 Azure 入口網站的存取控制 IAM 功能中新增使用者](./media/role-based-access-control-create-custom-roles-for-internal-external-users/2.png)

下一個步驟是選取要指派的角色，以及要指派 RBAC 角色的使用者。 在 [角色] 下拉式功能表中，管理使用者只會看到 Azure 中提供的內建 RBAC 角色。 如需每個角色及其可指派範圍的詳細說明，請參閱 [Azure 角色型存取控制的內建角色](/active-directory/role-based-access-built-in-roles.md)。
<!---Loc Comment: Link [Built-in roles for Azure Role-Based Access Control] is broken with an error message "404 - Content Not Found--->

然後，管理使用者必須新增外部使用者的電子郵件地址。 預期的行為是要使外部使用者不顯示在現有的租用戶中。 外部使用者受邀請之後，他會與目前已指派訂用帳戶範圍內之 RBAC 角色的所有目前使用者一起顯示在 [訂用帳戶] > [存取控制 (IAM)] 下。





![將權限新增至新的 RBAC 角色](./media/role-based-access-control-create-custom-roles-for-internal-external-users/3.png)





![訂用帳戶等級的 RBAC 角色清單](./media/role-based-access-control-create-custom-roles-for-internal-external-users/4.png)

已邀請使用者 "chessercarlton@gmail.com" 成為「免費試用」訂用帳戶的**擁有者**。 傳送邀請之後，外部使用者會收到包含啟用連結的電子郵件確認。
![RBAC 角色的電子郵件邀請](./media/role-based-access-control-create-custom-roles-for-internal-external-users/5.png)

在組織外部的新使用者，沒有任何「預設租用戶 Azure」目錄中的現有屬性。 外部使用者取得同意在與訂用帳戶 (已受指派角色) 相關聯的目錄中加以記錄之後，便會建立它們。





![RBAC 角色的電子郵件邀請訊息](./media/role-based-access-control-create-custom-roles-for-internal-external-users/6.png)

從現在起，外部使用者在 Azure Active Directory 租用戶中會顯示為外部使用者，在 Azure 入口網站和傳統入口網站中都可加以檢視。





![使用者刀鋒視窗 Azure Active Directory Azure 入口網站](./media/role-based-access-control-create-custom-roles-for-internal-external-users/7.png)





![使用者刀鋒視窗 Azure Active Directory Azure 傳統入口網站](./media/role-based-access-control-create-custom-roles-for-internal-external-users/8.png)

在這兩個入口網站的**使用者**檢視中，外部使用者的辨識方式為︰

* Azure 入口網站中不同的圖示類型
* 傳統入口網站中不同的來源點

不過，除非**全域管理員**加以允許，否則將**擁有者**或**參與者**存取權授與給**訂用帳戶**範圍內的外部使用者，並不允許存取管理使用者的目錄。 在使用者內容中，可以識別具有 **Member** 和 **Guest** 這兩個常見的參數的**使用者類型**。 當來賓為外部來源受邀至目錄的使用者時，在目錄中登錄的使用者就是成員。 如需詳細資訊，請參閱 [Azure Active Directory 系統管理員如何新增 B2B 共同作業使用者](/active-directory/active-directory-b2b-admin-add-users)。
<!---Loc Comment: Link [How do Azure Active Directory admins add B2B collaboration users] is broken with an error message "404 - Content Not Found--->

> [!NOTE]
> 在入口網站中輸入認證後，請確定外部使用者選取正確的目錄進行登入。 相同使用者可以存取多個目錄，並且可以選擇其中一個目錄，方法是按一下 Azure 入口網站右上角的使用者名稱，然後從下拉式清單中選取適當的目錄。

在目錄中使用來賓身分時，外部使用者可以管理 Azure 訂用帳戶的所有資源，但無法存取目錄。





![存取限於 Azure Active Directory Azure 入口網站](./media/role-based-access-control-create-custom-roles-for-internal-external-users/9.png)

Azure Active Directory 和 Azure 訂用帳戶沒有父子式關聯性，如同其他 Azure 資源 (例如︰虛擬機器、虛擬網路、Web Apps、儲存體等) 與 Azure 訂用帳戶。 將 Azure 訂用帳戶用來管理 Azure 目錄的存取權時，所有後者會建立在 Azure 訂用帳戶下，並加以管理和計費。 如需詳細資訊，請參閱 [Azure 訂用帳戶與 Azure AD 有何相關](/active-directory/active-directory-how-subscriptions-associated-directory)。

所有內建 RBAC 角色的**擁有者**和**參與者**會提供對環境中所有資源的完整管理存取權，差異在於參與者無法建立新的 RBAC 角色並加以刪除。 其他內建角色 (例如**虛擬機器參與者**) 僅對該名稱所表示的資源提供完整管理存取權，不論它們要建立在哪一個**資源群組**。

在訂用帳戶等級指派**虛擬機器參與者**的內建 RBAC 角色，表示使用者指派角色︰

* 可以檢視所有虛擬機器，不論其部署日期和所屬的資源群組
* 具有訂用帳戶中虛擬機器的完整管理存取權
* 無法檢視訂用帳戶中任何其他資源類型
* 無法從計費觀點來操作任何變更

> [!NOTE]
> RBAC 是僅限 Azure 入口網站使用的功能，它不會授與傳統入口網站的存取權。

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>將內建的 RBAC 角色指派給外部使用者
這項測試的不同案例中，會將外部使用者 "alflanigan@gmail.com" 新增為**虛擬機器參與者**。




![虛擬機器參與者內建角色](./media/role-based-access-control-create-custom-roles-for-internal-external-users/11.png)

這個具有此內建角色之外部使用者的正常行為，就是僅查看和管理虛擬機器，以及其相鄰 Resource Manager 僅在部署時所需的資源。 根據設計，這些限制的角色只會向他們在 Azure 入口網站中建立的對應資源提供存取權，不論部分角色仍可部署在傳統入口網站中 (例如︰虛擬機器)。





![Azure 入口網站中的虛擬機器參與者角色概觀](./media/role-based-access-control-create-custom-roles-for-internal-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>為相同目錄中的使用者授與訂用帳戶等級的存取權
程序流程與新增外部使用者的相同，從授與 RBAC 角色的系統管理員觀點以及取得角色存取權的使用者皆然。 此處的差異在於，受邀的使用者無法接收任何電子郵件邀請，因為訂用帳戶內的所有資源範圍在登入之後都會在儀表板中提供使用。

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>指派資源群組範圍內的 RBAC 角色
對於兩種類型的使用者 - 外部或內部 (相同目錄的一部分)，指派**資源群組**範圍內的 RBAC 角色與指派訂用帳戶等級的角色程序相同。 獲指派 RBAC 角色的使用者只會在其環境中看到已從 Azure 入口網站中的**資源群組**圖示指派存取權的資源群組。

## <a name="assign-rbac-roles-at-the-resource-scope"></a>指派資源範圍內的 RBAC 角色
在 Azure 中指派資源範圍內的角色與指派訂用帳戶等級或資源群組等級的角色程序相同，這兩個案例皆遵循相同的工作流程。 同樣地，獲指派 RBAC 角色的使用者只能在 [所有資源] 索引標籤或直接在儀表板中，看到他們已獲指派可存取的項目。

在資源群組範圍或資源範圍內之 RBAC 的一個重點是，讓使用者確定登入正確的目錄。





![Azure 入口網站中的目錄登入](./media/role-based-access-control-create-custom-roles-for-internal-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>為 Azure Active Directory 群組指派 RBAC 角色
在 Azure 中三個不同的範圍使用 RBAC 的所有案例，都提供以指派的使用者身分管理、部署和系統管理各種資源，而不需要管理個人訂用帳戶。 無論 RBAC 角色是指派給訂用帳戶、資源群組還是資源範圍，所有已指派的使用者所進一步建立的資源，都會以使用者擁有存取權的其中一個 Azure 訂用帳戶加以計費。 如此一來，擁有整個 Azure 訂用帳戶計費系統管理員權限的使用者，可以完整概觀耗用量，不論資源管理者是誰。

對於較大型組織，考慮到管理使用者想要將細微存取授與小組或整個部門的方面，而非個別授與每位使用者，可透過相同的方式來套用 Azure Active Directory 群組的 RBAC 角色，因此考慮將它作為非常有效率的時間和管理選項。 為了說明此範例中，**參與者**角色已新增至訂用帳戶等級之租用戶的其中一個群組。





![新增 AAD 群組的 RBAC 角色](./media/role-based-access-control-create-custom-roles-for-internal-external-users/14.png)

這些群組都是安全性群組，只在 Azure Active Directory 內進行佈建和管理。

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-powershell"></a>建立自訂的 RBAC 角色，才能使用 PowerShell 開啟支援要求
Azure 中提供的內建 RBAC 角色會以環境中可用的資源作為基礎來確保特定權限等級。 不過，如果這些角色都不符合管理使用者的需求，可透過建立自訂的 RBAC 角色，選擇限制更多存取權。

建立自訂的 RBAC 角色需要採用一個內建角色、加以編輯，然後將它匯入回環境中。 會使用 PowerShell 或 CLI 來管理角色的上傳與下載。

請務必了解建立自訂角色的必要條件，其可授與訂用帳戶等級的細微存取權，並且提供受邀使用者開啟支援要求的彈性。

此範例中，讓使用者可存取並檢視所有資源範圍，但不能加以編輯或新建的內建角色**讀取器**已進行自訂，可供使用者選擇開啟支援要求。

將**讀取器**角色匯出的第一個動作，必須在以系統管理員身分透過提高權限執行的 PowerShell 中完成。

```
Login-AzureRMAccount

Get-AzureRMRoleDefinition -Name "Reader"

Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json

```





![讀取器 RBAC 角色的 PowerShell 螢幕擷取畫面](./media/role-based-access-control-create-custom-roles-for-internal-external-users/15.png)

然後，您必須擷取角色的 JSON 範本。





![自訂讀取器 RBAC 角色的 JSON 範本](./media/role-based-access-control-create-custom-roles-for-internal-external-users/16.png)

典型的 RBAC 角色是由三個主要區段所組成︰**Actions**、**NotActions** 和 **AssignableScopes**。

在**動作**區段中，會列出此角色所有允許的作業。 請務必了解，每個動作都會從資源提供者加以指派。 在此情況下，必須列出 **Microsoft.Support** 資源提供者才能建立支援票證。

若要查看所有可用且已在您訂用帳戶中登錄的資源提供者，您可以使用 PowerShell。
```
Get-AzureRMResourceProvider

```
此外，您可以檢查所有可用的 PowerShell Cmdlet 來管理資源提供者。
    ![資源提供者管理的 PowerShell 螢幕擷取畫面](./media/role-based-access-control-create-custom-roles-for-internal-external-users/17.png)

為限制特定 RBAC 角色的所有動作，會在 **NotActions** 一節中列出資源提供者。
最後，在使用 RBAC 角色的位置必須包含明確的訂用帳戶識別碼。 訂用帳戶識別碼會列在 **AssignableScopes** 下，否則不允許您將角色匯入您的訂用帳戶。

建立並自訂 RBAC 角色之後，必須將它匯入回環境。

```
New-AzureRMRoleDefinition -InputFile "C:\rbacrole2.json"

```

在此範例中，此 RBAC 角色的自訂名稱為「讀取器支援票證存取等級」，讓使用者可檢視訂用帳戶中的所有項目，並且開啟支援要求。

> [!NOTE]
> 允許開啟支援要求的兩個內建 RBAC 角色動作是**擁有者**和**參與者**。 使用者若要開啟支援要求，必須僅獲指派訂用帳戶範圍內的 RBAC 角色，因為所有支援要求都會以 Azure 訂用帳戶作為基礎加以建立。

已從相同的目錄將這個新的自訂角色指派給使用者。





![匯入 Azure 入口網站之自訂 RBAC 角色的螢幕擷取畫面](./media/role-based-access-control-create-custom-roles-for-internal-external-users/18.png)





![將自訂匯入的 RBAC 角色指派給相同目錄中之使用者的螢幕擷取畫面](./media/role-based-access-control-create-custom-roles-for-internal-external-users/19.png)





![自訂匯入的 RBAC 角色之權限的螢幕擷取畫面](./media/role-based-access-control-create-custom-roles-for-internal-external-users/20.png)

此範例已進一步詳細說明，強調此自訂 RBAC 角色的限制，如下所示︰
* 可以建立新的支援要求
* 無法建立新的資源範圍 (例如︰虛擬機器)
* 無法建立新的資源群組





![建立支援要求之自訂 RBAC 角色的螢幕擷取畫面](./media/role-based-access-control-create-custom-roles-for-internal-external-users/21.png)





![無法建立 VM 之自訂 RBAC 角色的螢幕擷取畫面](./media/role-based-access-control-create-custom-roles-for-internal-external-users/22.png)





![無法建立新 RG 之自訂 RBAC 角色的螢幕擷取畫面](./media/role-based-access-control-create-custom-roles-for-internal-external-users/23.png)

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-azure-cli"></a>建立自訂的 RBAC 角色，才能使用 Azure CLI 開啟支援要求
在 Mac 上執行而不需要存取 PowerShell，Azure CLI 就是最好的選擇。

建立自訂角色的步驟都相同，唯一的例外狀況是，使用 CLI，就無法在 JSON 範本中下載角色，但可以在 CLI 中加以檢視。

此範例中，我選擇的內建角色是**備份讀取器**。

```

azure role show "backup reader" --json

```





![備份讀取器角色顯示的 CLI 螢幕擷取畫面](./media/role-based-access-control-create-custom-roles-for-internal-external-users/24.png)

在 JSON 範本中複製內容後編輯 Visual Studio 中的角色，已在**動作**區段中新增 **Microsoft.Support** 資源提供者，讓這個使用者可以開啟支援要求，同時可繼續當作備份保存庫的讀取器。 同樣地，在 **AssignableScopes** 區段中要使用此角色的位置必須新增訂用帳戶識別碼。

```

azure role create --inputfile <path>

```





![匯入自訂 RBAC 角色的 CLI 螢幕擷取畫面](./media/role-based-access-control-create-custom-roles-for-internal-external-users/25.png)

現在可於 Azure 入口網站中使用新角色，指派程序與先前範例中的相同。





![使用 CLI 1.0 所建立之自訂 RBAC 角色的 Azure 入口網站螢幕擷取畫面](./media/role-based-access-control-create-custom-roles-for-internal-external-users/26.png)

從最新的組建 2017 起，Azure Cloud Shell 已正式推出。 Azure Cloud Shell 是 IDE 和 Azure 入口網站的補充。 您可以利用此服務，取得經過驗證且裝載於 Azure 內以瀏覽器為基礎的殼層，您可以使用它而不使用安裝在您電腦上的 CLI。





![Azure Cloud Shell](./media/role-based-access-control-create-custom-roles-for-internal-external-users/27.png)

