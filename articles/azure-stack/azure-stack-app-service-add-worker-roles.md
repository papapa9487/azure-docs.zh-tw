---
title: "在應用程式服務中相應放大背景工作角色 - Azure Stack | Microsoft Docs"
description: "調整 Azure Stack 應用程式服務的詳細指導方針"
services: azure-stack
documentationcenter: 
author: kathm
manager: slinehan
editor: anwestg
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/6/2017
ms.author: kathm
ms.translationtype: HT
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: f844658c6ad2529fd385476be63095bdae7c88e5
ms.contentlocale: zh-tw
ms.lasthandoff: 09/15/2017

---
# <a name="app-service-on-azure-stack-adding-more-worker-roles"></a>Azure Stack 上的 App Service：新增更多背景工作角色 

此文件提供如何調整 Azure Stack 上之 App Service 背景工作角色的相關指示。 其中包含建立額外的背景工作角色以支援任何規模之應用程式的步驟。

> [!NOTE]
> 如果您 Azure Stack POC 環境的 RAM 未超過 96 GB，您可能很難增加額外的容量。

根據預設值，Azure Stack 上的 App Service 支援免費和共用背景工作角色層。 若要新增其他背景工作角色層，您需要新增更多背景工作角色。

如果您不確定已透過 Azure Stack 安裝上的預設 App Service 部署了哪個背景工作層，您可以在 [Azure Stack 上的 App Service 概觀](azure-stack-app-service-overview.md)中檢閱其他資訊。

有兩種方式可將額外的容量新增至 Azure Stack 上的 App Service：
1.  [直接從 App Service 資源提供者系統管理內新增其他背景工作角色](#Add-additional-workers-directly-from-within-the-App-Service-Resource-Provider-Admin)。
2.  [手動建立其他的 VM，並將它們新增到 App Service 資源提供者](#Create-additional-VMs-manually-and-add-them-to-the-App-Service-Resource-Provider)。

## <a name="add-additional-workers-directly-within-the-app-service-resource-provider-admin"></a>直接在 App Service 資源提供者系統管理內新增其他背景工作角色。

1.  以服務管理員身分登入 Azure Stack 入口網站。
2.  瀏覽至 [資源提供者]，然後選取 [App Service 資源提供者管理]。![Azure Stack 資源提供者][1]
3.  按一下 [角色]。  您可以在這裡看到所有已部署之 App Service 角色的明細。
4.  按一下 [新的角色執行個體] 選項 ![新增角色執行個體][2]
5.  在 [新的角色執行個體] 刀鋒視窗中：
    1. 選擇您想要新增多少個額外的**角色執行個體**。  在此預覽中，上限是 10 個。
    2. 選取**角色類型**。  在此預覽中，已將此選項限制為 [Web 背景工作]。
    3. 選取您想要部署這個背景工作的**背景工作層**，預設選項為 [小型]、[中型]、[大型] 或 [共用]。  如果您已建立自己的背景工作層，也可選取您的背景工作層。
    4. 按一下 [確定] 以部署額外的背景工作
6.  Azure Stack 上的 App Service 現在將會新增額外的 VM、設定它們、安裝所有必要的軟體，並在完成此程序時將它們標示為就緒。  此程序大約需要 80 分鐘。
7.  您可以檢視 [角色] 刀鋒視窗中的背景工作，來監視整備新背景工作的進度。

>[!NOTE]
>  在此預覽中，已將整合的 [新的角色執行個體] 流程限制為 [背景工作角色]，而且只會部署 A1 大小的 VM。  我們將在未來版本中擴充此功能。

## <a name="manually-adding-additional-capacity-to-app-service-on-azure-stack"></a>手動將額外的容量新增到 Azure Stack 上的 App Service。

以下為新增額外角色所需的步驟：

1. [建立新的虛擬機器](#step-1-create-a-new-vm-to-support-the-new-instance-size)
2. [設定虛擬機器](#step-2-configure-the-virtual-machine)
3. [在 Azure Stack 入口網站中設定 Web 背景工作角色](#step-3-configure-the-web-worker-role-in-the-azure-stack-portal)
4. [設定 App Service 方案](#step-4-configure-app-service-plans)

## <a name="step-1-create-a-new-vm-to-support-the-new-instance-size"></a>步驟 1：建立新的 VM 以支援新的執行個體大小
如[此文章](azure-stack-provision-vm.md)中所述的方式建立虛擬機器，確保會進行下列選擇：

* 使用者名稱和密碼：提供您在安裝 Azure Stack 上的 App Service 時所提供的相同使用者名稱和密碼。
* 訂用帳戶：使用預設的提供者訂用帳戶。
* 資源群組：選擇 [AppService-LOCAL]。

> [!NOTE]
> 將適用於背景工作角色的虛擬機器儲存在與要部署 Azure Stack 上之 App Service 相同的資源群組中 (這是適用於此版本的建議作法)。
> 
> 

## <a name="step-2-configure-the-virtual-machine"></a>步驟 2：設定虛擬機器
一旦完成部署，必須進行下列設定，才能支援 Web 背景工作角色：

1. 瀏覽至入口網站中的 [AppService-LOCAL] 資源群組，並選取您在步驟 1 中建立新的機器。
2. 按一下 VM 刀鋒視窗中的 [連線]，以下載遠端桌面設定檔。  開啟設定檔，以便開啟您 VM 的遠端桌面工作階段。
3. 使用您在步驟 1 中指定的使用者名稱和密碼來登入 VM。
4. 依序按一下 [開始] 按鈕並輸入 PowerShell 來開啟 PowerShell。 以滑鼠右鍵按一下 [PowerShell.exe]，然後選取 [以系統管理員身分執行]，在系統管理員模式中開啟 PowerShell。
5. 複製下列每個命令 (一次一個) 並貼至 PowerShell 視窗，然後按 Enter：
   
   ```netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes```
   ```netsh advfirewall firewall set rule group="Windows Management Instrumentation (WMI)" new enable=yes```
   ```reg add HKLM\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\system /v LocalAccountTokenFilterPolicy /t REG\_DWORD /d 1 /f```
   
6. 關閉您的遠端桌面工作階段。
7. 從入口網站重新啟動 VM。

> [!NOTE]
> 這些是針對 Azure Stack 上之 App Service 的最低需求。 它們是 Azure Stack 所含之 Windows 2012 R2 映像的預設設定。 這些指示已提供來供日後參考，也可供使用不同映像的那些情況使用。
> 
> 

## <a name="step-3-configure-the-worker-role-in-the-azure-stack-portal"></a>步驟 3：在 Azure Stack 入口網站中設定背景工作角色
1. 在 **ClientVM** 中，以服務管理員身分開啟入口網站。
2. 瀏覽至 [資源提供者] &gt; [App Service 資源提供者系統管理]。![App Service 資源提供者系統管理][3]
3. 在 [設定] 刀鋒視窗中，按一下 [角色]。![App Service 資源提供者角色][4]
4. 按一下 [新增角色執行個體]。
5. 在 [伺服器名稱] 文字方塊中，輸入您稍早建立 (在區段 1 中) 之伺服器的 **IP 位址**。
6. 選取您想要新增的**角色類型**：控制器、管理伺服器、前端、Web 背景工作、發行者或檔案伺服器。  在此範例中，請選取 [Web 背景工作]。
7. 按一下您想要部署新執行個體的 [階層] (小型、中型、大型或共用)。  如果您已建立自己的背景工作層，也可選取這些背景工作層。
8. 按一下 [確定]。
9. 返回 [角色] 檢視
10. 按一下對應至您已指派 VM 之 [角色類型] 與 [背景工作層] 組合的資料列。
11. 尋找您剛新增的伺服器名稱。 檢閱 [狀態] 欄，並在狀態成為「就緒」之後移至下一個步驟。 這大約需要 80 分鐘。 ![App Service 資源提供者角色就緒][5]

## <a name="step-4-configure-app-service-plans"></a>步驟 4：設定 App Service 方案

1. 登入 ClientVM 上的入口網站。
2. 瀏覽至 [新增] &gt; [Web 與行動裝置]。
3. 選取您想要部署的應用程式類型。
4. 提供應用程式的資訊，然後選取 [AppService 方案 / 位置]。
    1. 按一下 [新建] 。
    2. 建立您的方案，選取方案的對應定價層。

> [!NOTE]
> 您可以在此刀鋒視窗上建立多個方案。 不過，在部署之前，請確定您已選取適當的方案。
> 
> 

以下顯示的是預設可用的多個定價層範例。  您會注意到，如果特定的背景工作層沒有可用的背景工作，就無法使用選擇對應定價層的選項。![Azure Stack 上的 App Service 預設定價層][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-add-worker-roles/azure-stack-resource-providers.png
[2]: ./media/azure-stack-app-service-add-worker-roles/app-service-new-role-instance.png
[3]: ./media/azure-stack-app-service-add-worker-roles/app-service-resource-provider-admin.png
[4]: ./media/azure-stack-app-service-add-worker-roles/app-service-resource-provider-roles.png
[5]: ./media/azure-stack-app-service-add-worker-roles/app-service-resource-provider-role-ready.png
[6]: ./media/azure-stack-app-service-add-worker-roles/app-service-resource-provider-pricing-tier.png

