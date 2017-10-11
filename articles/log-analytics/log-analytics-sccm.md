---
title: "將 Configuration Manager 連線至 Log Analytics | Microsoft Docs"
description: "本文說明將 Configuration Manager 連線至 Log Analytics 並開始分析資料的步驟。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: banders
ms.openlocfilehash: 62d31ed486458245156f7fc832294d662c62991e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="connect-configuration-manager-to-log-analytics"></a>將 Configuration Manager 連線至 Log Analytics
您可以在 OMS 中將 System Center Configuration Manager 連線至 Log Analytics 來同步處理裝置集合資料。 這可讓 Configuration Manager 階層中的資料可在 OMS 中使用。

## <a name="prerequisites"></a>必要條件

Log Analytics 支援 System Center Configuration Manager 目前分支，1606 版和更高版本。  

## <a name="configuration-overview"></a>組態概觀
下列步驟摘要說明將 Configuration Manager 連線至 Log Analytics 的程序。  

1. 在 Azure 管理入口網站中，將 Configuration Manager 註冊為 Web 應用程式和/或 Web API 應用程式，並確保您擁有 Azure Active Directory 註冊中的用戶端識別碼和用戶端秘密金鑰。 如需如何完成此步驟的詳細資訊，請參閱[使用入口網站來建立可存取資源的 Active Directory 應用程式和服務主體](../azure-resource-manager/resource-group-create-service-principal-portal.md)。
2. 在 Azure 管理入口網站中，[為 Configuration Manager (已註冊的 Web 應用程式) 提供存取 OMS 的權限](#provide-configuration-manager-with-permissions-to-oms)。
3. 在 Configuration Manager 中，[使用新增 OMS 連線精靈新增連線](#add-an-oms-connection-to-configuration-manager)。
4. 如果密碼或用戶端祕密金鑰過期或遺失，在 Configuration Manager 中[更新連線屬性](#update-oms-connection-properties)。
5. 使用 OMS 入口網站的資訊，在執行 Configuration Manager 服務連線點網站系統角色的電腦上[下載並安裝 Microsoft Monitoring Agent](#download-and-install-the-agent)。 代理程式會將 Configuration Manager 資料傳送至 OMS。
6. 在 Log Analytics 中，以電腦群組的形式[從 Configuration Manager 匯入集合](#import-collections)。
7. 在 Log Analytics 中，以電腦群組的形式[從 Configuration Manager 檢視資料](log-analytics-computer-groups.md)。

若要深入了解如何將 Configuration Manager 連線至 OMS，請參閱[將資料從 Configuration Manager 同步處理至 Microsoft Operations Management Suite](https://technet.microsoft.com/library/mt757374.aspx)。

## <a name="provide-configuration-manager-with-permissions-to-oms"></a>為 Configuration Manager 提供 OMS 的權限
下列程序可為 Azure 管理入口網站提供存取 OMS 的權限。 具體來說，您必須授與參與者角色給資源群組中的使用者，以允許 Azure 管理入口網站將 Configuration Manager 連線至 OMS。

> [!NOTE]
> 您必須為 Configuration Manager 指定 OMS 的權限。 否則，當您在 Configuration Manager 中使用組態精靈時，將會收到錯誤訊息。
>
>

1. 開啟 [Azure 入口網站](https://portal.azure.com/)，然後按一下 [瀏覽] > [Log Analytics (OMS)] 以開啟 [Log Analytics (OMS)] 刀鋒視窗。  
2. 在 [Log Analytics (OMS)] 刀鋒視窗上按一下 [新增]，開啟 [OMS 工作區] 刀鋒視窗。  
   ![OMS 刀鋒視窗](./media/log-analytics-sccm/sccm-azure01.png)
3. 在 [OMS 工作區] 刀鋒視窗上提供下列資訊，然後按一下 [確定]。

   * **OMS 工作區**
   * **訂用帳戶**
   * **資源群組**
   * **位置**
   * **定價層**  
     ![OMS 刀鋒視窗](./media/log-analytics-sccm/sccm-azure02.png)  

     > [!NOTE]
     > 上述範例會建立新的資源群組。 在此範例中，資源群組只會用來為 Configuration Manager 提供 OMS 工作區的權限。
     >
     >
4. 按一下 [瀏覽] > [資源群組]，開啟 [資源群組] 刀鋒視窗。
5. 在 [資源群組] 刀鋒視窗中按一下您先前建立的資源群組，開啟 [&lt;資源群組名稱&gt; 設定] 刀鋒視窗。  
   ![資源群組設定刀鋒視窗](./media/log-analytics-sccm/sccm-azure03.png)
6. 在 [&lt;資源群組名稱&gt; 設定] 刀鋒視窗中按一下 [存取控制 (IAM)]，開啟 [&lt;資源群組名稱&gt; 使用者] 刀鋒視窗。  
   ![資源群組使用者刀鋒視窗](./media/log-analytics-sccm/sccm-azure04.png)  
7. 在 [&lt;資源群組名稱&gt; 使用者] 刀鋒視窗中按一下 [新增]，開啟 [新增存取] 刀鋒視窗。
8. 在 [新增存取] 刀鋒視窗中按一下 [選取角色]，然後選取 [參與者] 角色。  
   ![選取角色](./media/log-analytics-sccm/sccm-azure05.png)  
9. 按一下 [新增使用者]，選取 Configuration Manager 使用者，然後依序按一下 [選取] 和 [確定]。  
   ![新增使用者](./media/log-analytics-sccm/sccm-azure06.png)  

## <a name="add-an-oms-connection-to-configuration-manager"></a>對 Configuration Manager 新增 OMS 連線
若要新增 OMS 連線，Configuration Manager 環境的[服務連線點](https://technet.microsoft.com/library/mt627781.aspx)必須設定成線上模式。

1. 在 Configuration Manager 的 [管理] 工作區中選取 [OMS 連接器]。 這會開啟**新增 OMS 連線精靈**。 選取 [下一步] 。
2. 在 [一般] 畫面上，確認您已完成下列動作而且您有每個項目的詳細資料，然後選取 [下一步]。

   1. 在 Azure 管理入口網站中，您已將 Configuration Manager 註冊為 Web 應用程式和/或 Web API 應用程式，而且您擁有[註冊中的用戶端識別碼](../active-directory/active-directory-integrating-applications.md)。
   2. 在 Azure 管理入口網站中，您已經為 Azure Active Directory 中已註冊的應用程式建立應用程式祕密金鑰。  
   3. 在 Azure 管理入口網站中，您已經為已註冊的 Web 應用程式提供存取 OMS 的權限。  
      ![OMS 連線精靈的一般頁面](./media/log-analytics-sccm/sccm-console-general01.png)
3. 在 [Azure Active Directory] 畫面上對 OMS 設定連線設定 (方法是提供 [租用戶]、[用戶端識別碼] 和 [用戶端秘密金鑰])，然後選取 [下一步]。  
   ![OMS 連線精靈的 Azure Active Directory 頁面](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)
4. 如果您成功完成其他所有程序，則 [OMS 連線組態] 畫面上的資訊會自動出現在此頁面。 [Azure 訂用帳戶]、[Azure 資源群組] 和 [Operations Management Suite 工作區] 應該會出現連線設定資訊。  
   ![OMS 連線精靈的 OMS 連線頁面](./media/log-analytics-sccm/sccm-wizard-configure04.png)
5. 精靈會使用您輸入的資訊連線至 OMS 服務。 選取想要與 OMS 同步處理的裝置集合，然後按一下 [新增]。  
   ![選取集合](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)
6. 確認 [摘要] 畫面上的連線設定，然後選取 [下一步]。 [進度] 畫面會顯示連線狀態，然後應該 [完成]。

> [!NOTE]
> 您必須將 OMS 連線至階層中的最上層網站。 如果您將 OMS 連線至獨立主要網站，然後在環境中新增管理中心網站，則必須在新階層內先刪除再重新建立 OMS 連線。
>
>

在將 Configuration Manager 連結至 OMS 之後，您可以新增或移除集合，並檢視 OMS 連線的屬性。

## <a name="update-oms-connection-properties"></a>更新 OMS 連線屬性
如果密碼或用戶端秘密金鑰過期或遺失，您必須手動更新 OMS 連線屬性。

1. 在 Configuration Manager 中瀏覽至 [雲端服務]，然後選取 [OMS 連接器] 以開啟 [OMS 連線屬性] 頁面。
2. 在此頁面上按一下 [Azure Active Directory] 索引標籤，檢視 [租用戶]、[用戶端識別碼] 和 [用戶端秘密金鑰到期]。 **確認****用戶端秘密金鑰**是否已過期。

## <a name="download-and-install-the-agent"></a>下載並安裝代理程式
1. 在 OMS 入口網站中，[從 OMS 下載代理程式安裝檔案](log-analytics-windows-agents.md#download-the-agent-setup-file-from-oms)。
2. 使用下列其中一個方法，在執行 Configuration Manager 服務連線點網站系統角色的電腦上安裝並設定代理程式：
   * [使用安裝程式安裝代理程式](log-analytics-windows-agents.md#install-the-agent-using-setup)
   * [使用命令列安裝代理程式](log-analytics-windows-agents.md#install-the-agent-using-the-command-line)
   * [使用 Azure 自動化中的 DSC 安裝代理程式](log-analytics-windows-agents.md#install-the-agent-using-dsc-in-azure-automation)

## <a name="import-collections"></a>匯入集合
在 Configuration Manager 新增了 OMS 連線，並在執行 Configuration Manager 服務連線點網站系統角色的電腦上安裝了代理程式之後，下一個步驟是從 Configuration Manager 將集合匯入 OMS 中做為電腦群組。

啟用匯入之後，每 3 個小時就會擷取一次集合成員資格資訊，以便集合成員資格會隨時保持最新狀態。 您可以隨時選擇停用匯入。

1. 在 OMS 入口網站中，按一下 [設定]。
2. 按一下 [電腦群組] 索引標籤，然後按一下 [SCCM] 索引標籤。
3. 選取 [匯入 Configuration Manager 集合成員資格]，然後按一下 [儲存]。  
   ![電腦群組 - SCCM 索引標籤](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>從 Configuration Manager 檢視資料
在 Configuration Manager 新增了 OMS 連線，並在執行 Configuration Manager 服務連線點網站系統角色的電腦上安裝了代理程式之後，代理程式的資料會傳送至 OMS。 在 OMS 中，Configuration Manager 集合會顯示為[電腦群組](log-analytics-computer-groups.md)。 您可以從 [設定] 中 [電腦群組]下的 [Configuration Manager] 頁面檢視群組。

匯入集合之後，您可以看到已偵測到多少部具有集合成員資格的電腦。 您也可以看到已匯入的集合數目。

![電腦群組 - SCCM 索引標籤](./media/log-analytics-sccm/sccm-computer-groups02.png)

當您按一下其中一項時，[搜尋] 會開啟，顯示所有匯入的群組或是屬於每個群組的所有電腦。 使用 [記錄搜尋](log-analytics-log-searches.md)，您就可以開始深入分析 Configuration Manager 資料。

## <a name="next-steps"></a>後續步驟
* 請使用 [記錄檔搜尋](log-analytics-log-searches.md)，檢視有關 Configuration Manager 資料的詳細資訊。
