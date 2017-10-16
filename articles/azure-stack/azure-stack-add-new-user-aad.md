---
title: "在 Azure Active Directory 中新增 Azure Stack 租用戶帳戶 | Microsoft Docs"
description: "在部署 Microsoft Azure Stack 開發套件之後，您將需要建立至少一個租用戶使用者帳戶，以便可以瀏覽租用戶入口網站。"
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 91d1c52c0abf14656e08b511e4f6c8041e319020
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
適用於：Azure Stack 開發套件

# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>在 Azure Active Directory 中新增 Azure Stack 租用戶帳戶
在[部署 Azure Stack 開發套件](azure-stack-run-powershell-script.md)之後，您將需要租用戶使用者帳戶，以便可以瀏覽租用戶入口網站並測試您的供應項目與方案。 您可以[使用 Azure 入口網站](#create-an-azure-stack-tenant-account-using-the-azure-portal)或[使用 PowerShell](#create-an-azure-stack-tenant-account-using-powershell) 來建立租用戶帳戶。

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>使用 Azure 入口網站建立 Azure Stack 租用戶帳戶
您必須擁有 Azure 訂用帳戶，才能使用 Azure 入口網站。

1. 登入 [Azure](http://manage.windowsazure.com)。
2. 在 Microsoft Azure 左側的導覽列中，按一下 [Active Directory] 。
3. 在目錄清單中，按一下您想要用於 Azure Stack 的目錄，或建立一個新的目錄。
4. 在此目錄頁面上，按一下 [使用者] 。
5. 按一下 [新增使用者] 。
6. 在 [新增使用者] 精靈的 [使用者類型] 清單中，選擇 [您組織中的新使用者]。
7. 在 [使用者名稱]  方塊中，輸入使用者名稱。
8. 在 [新增使用者精靈] **@** 方塊中，選擇適當的項目。
9. 按下一個箭頭。
10. 在精靈的 [使用者設定檔]頁面中，輸入**名字**、**姓氏**和**顯示名稱**。
11. 在 [角色] 清單中，選擇 [使用者]。
12. 按下一個箭頭。
13. 在 [取得暫時密碼] 頁面上，按一下 [建立]。
14. 複製 [新密碼] 。
15. 以新的帳戶登入 Microsoft Azure。 在系統提示時變更密碼。
16. 以新的帳戶登入 `https://portal.local.azurestack.external`，查看租用戶入口網站。

## <a name="create-an-azure-stack-tenant-account-using-powershell"></a>使用 PowerShell 建立 Azure Stack 租用戶帳戶
如果您沒有 Azure 訂用帳戶，就無法使用 Azure 入口網站來新增租用戶使用者帳戶。 在此情況下，您可以改為使用適用於 Windows PowerShell 的 Azure Active Directory 模組。

> [!NOTE]
> 如果您使用 Microsoft 帳戶 (Live ID) 來部署 Azure Stack 開發套件，就無法使用 AAD PowerShell 來建立租用戶帳戶。 
> 
> 

1. 安裝[適用於 IT 專業人員的 Microsoft Online Services 登入小幫手 RTW](https://www.microsoft.com/en-us/download/details.aspx?id=41950)。
2. 安裝[適用於 Windows PowerShell (64 位元版本) 的 Azure Active Directory 模組](http://go.microsoft.com/fwlink/p/?linkid=236297) \(英文\)，並將它開啟。
3. 執行下列 Cmdlet：

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. 以新的帳戶登入 Microsoft Azure。 在系統提示時變更密碼。
2. 以新的帳戶登入 `https://portal.local.azurestack.external`，查看租用戶入口網站。

