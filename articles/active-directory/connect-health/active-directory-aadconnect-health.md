---
title: "在雲端中監視內部部署身分識別基礎結構。"
description: "這是 Azure AD Connect Health 頁面，其中說明它的功能，以及您可能會使用它的原因。"
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 82798ea6-5cd3-4f30-93ae-d56536f8d8e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 881ce13b6e4b10064294e590431434b29da3fb33
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="monitor-your-on-premises-identity-infrastructure-and-synchronization-services-in-the-cloud"></a>在雲端中監視內部部署身分識別基礎結構和同步處理服務。
Azure Active Directory (Azure AD) Connect Health 可協助您監視和了解內部部署身分識別基礎結構和同步處理服務。 它提供重要身分識別元件 (例如 Active Directory Federation Services (AD FS) 伺服器、Azure AD Connect 伺服器 (也稱為同步處理引擎)、Active Directory 網域控制站等) 的監視功能，讓您維護可靠的 Office 365 或 Microsoft Online Services 連線。它也使這些元件的相關重要資料點變得容易存取，讓您取得使用情況和其他重要情資，以做出明智的決策。

這些資訊會呈現在 [Azure AD Connect Health 入口網站](https://aka.ms/aadconnecthealth)中。 在 Azure AD Connect Health 入口網站中，您可以檢視警示、效能監視、使用情況分析等資訊。 Azure AD Connect Health 就像功能濾鏡，可讓您集中監控重要身分識別元件的健康狀態。

![Azure AD Connect Health 是什麼](./media/active-directory-aadconnect-health/aadconnecthealth2.png)

隨著 Azure AD Connect Health 的功能越來越多，入口網站透過身分識別的功能濾鏡，提供單一儀表板。 您會有更為穩固健全的整合式環境，讓使用者的辦事能力更強。

## <a name="why-use-azure-ad-connect-health"></a>為何使用 Azure AD Connect Health？
將內部部署目錄與 Azure AD 整合時，使用者會更具生產力，因為只要一個通用身分識別，即可存取雲端和內部部署資源。 然而，此整合也會帶來挑戰，必須確保此環境良好健全，才能讓使用者從任何裝置可靠地存取內部部署和雲端中的資源。 Azure AD Connect Health 協助您監視及深入了解用來存取 Office 365 或其他 Azure AD 應用程式的內部部署身分識別基本結構。 使用方式相當簡單，您只需將代理程式安裝在各個內部部署身分識別伺服器中即可。

## <a name="azure-ad-connect-health-for-ad-fsactive-directory-aadconnect-health-adfsmd"></a>[適用於 AD FS 的 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md)
在 Windows Server 2008 R2、Windows Server 2012 和 Windows Server 2012 R2 上，適用於 AD FS 的 Azure AD Connect Health 支援 AD FS 2.0。 它也支援監視可驗證外部網路存取的 AD FS Proxy 或 Web 應用程式 Proxy 伺服器。 透過容易安裝且成本低廉的健康情況代理程式，適用於 AD FS 的 Azure AD Connect Health 提供下列一組主要功能：

* 利用警示進行監視，以了解 AD FS 與 AD FS Proxy 伺服器何時健康狀態不良
* 重大警示的電子郵件通知
* 效能資料的趨勢，適合用於 AD FS 的容量規劃
* 透過樞紐 (應用程式、使用者、網路位置等) 提供 AD FS 登入的使用情況分析，有助於了解 AD FS 的使用情況
* AD FS 報告，例如嘗試使用錯誤使用者名稱/密碼的前 50 名使用者及其最後一個 IP 位址

下列影片提供適用於 AD FS 的 Azure AD Connect Health 概觀。

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health--Monitor-you-identity-bridge/player]
>
>

## <a name="azure-ad-connect-health-for-syncactive-directory-aadconnect-health-syncmd"></a>[適用於同步處理的 Azure AD Connect Health](active-directory-aadconnect-health-sync.md)
適用於同步處理的 Azure AD Connect Health 可監視及提供內部部署 Active Directory 與 Azure AD 之間發生的同步處理相關資訊。 適用於同步處理的 Azure AD Connect Health 提供下列一組主要功能：

* 利用警示進行監視，以了解 Azure AD Connect 伺服器 (也稱為同步處理引擎) 何時健康狀態不良
* 重大警示的電子郵件通知
* 同步處理操作情資，包括同步處理作業的延遲圖表以及不同作業 (如新增、更新、刪除) 的趨勢
* 有關同步處理屬性和上次成功匯出至 Azure AD 的快速概覽資訊
* 物件層級同步處理錯誤的相關報告\(不需要 Azure AD Premium\)

下列影片提供適用於同步處理的 Azure AD Connect Health 概觀。

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Health-Monitoring-the-sync-engine/player]
>
>

## <a name="azure-ad-connect-health-for-ad-dsactive-directory-aadconnect-health-addsmd"></a>[適用於 AD DS 的 Azure AD Connect Health](active-directory-aadconnect-health-adds.md)
適用於 Active Directory Domain Services (AD DS) 的 Azure AD Connect Health 可以監視安裝在 Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2 及 Windows Server 2016 上的網域控制站。 健康狀態代理程式可讓您從雲端監視內部部署 AD DS 環境。 適用於 AD DS 的 Azure AD Connect Health 提供下列一組主要功能：

* 監視警示可偵測網域控制站健全狀況不良，並發送重大警示的電子郵件通知
* 網域控制站儀表板可讓您快速檢視網域控制站的健全狀況和操作狀態
* 複寫狀態儀表板擁有最新的複寫資訊，以及偵測到錯誤時可用的疑難排解指南連結
* 可在任一處快速存取常用效能計數器的效能資料圖形，其為疑難排解和監視用途所必需

下列影片提供適用於 AD DS 的 Azure AD Connect Health 概觀。

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health-monitors-on-premises-AD-Domain-Services/player]
>
>

## <a name="get-started-with-azure-ad-connect-health"></a>開始使用 Azure AD Connect Health
若要開始使用 Azure AD Connect Health，請使用下列步驟：

1. [取得 Azure AD Premium](../active-directory-get-started-premium.md) 或[開始試用](https://azure.microsoft.com/trial/get-started-active-directory/)。
2. 在身分識別伺服器上[下載和安裝 Azure AD Connect Health 代理程式](#download-and-install-azure-ad-connect-health-agent)。
3. 在 [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth) 檢視 Azure AD Connect Health 儀表板。

> [!NOTE]
> 請記住，您必須先在目標伺服器上安裝 Azure AD Connect Health 代理程式，Azure AD Connect Health 儀表板中才會出現資料。
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>下載和安裝 Azure AD Connect Health 代理程式
* 確定您符合 Azure AD Connect Health 的[需求](active-directory-aadconnect-health-agent-install.md#requirements)。
* 開始使用適用於 AD FS 的 Azure AD Connect Health
    * [下載適用於 AD FS 的 Azure AD Connect Health 代理程式](http://go.microsoft.com/fwlink/?LinkID=518973)
    * [請參閱安裝指示](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)。
* 開始使用適用於同步處理的 Azure AD Connect Health
    * [下載並安裝最新版的 Azure AD Connect](http://go.microsoft.com/fwlink/?linkid=615771)。 適用於同步處理的健康狀態代理程式將會隨著 Azure AD Connect 安裝 (1.0.9125.0 或更高版本) 一起安裝。
* 開始使用適用於 AD DS 的 Azure AD Connect Health
    * [下載適用於 AD DS 的 Azure AD Connect Health 代理程式](http://go.microsoft.com/fwlink/?LinkID=820540)。
    * [請參閱安裝指示](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds)。

## <a name="azure-ad-connect-health-portal"></a>Azure AD Connect Health 入口網站
Azure AD Connect Health 入口網站會顯示警示、效能監視和使用情況分析。 https://aka.ms/aadconnecthealth URL 會帶您前往 Azure AD Connect Health 的主要刀鋒視窗。 您可以將刀鋒視窗視為視窗。 在主要刀鋒視窗上，您會看到 [快速啟動]、Azure AD Connect Health 中的服務以及其他設定選項。 請參閱下列螢幕擷取畫面，以及螢幕擷取畫面後面的簡短說明。 部署代理程式之後，健康狀態服務會自動識別 Azure AD Connect Health 所監視的服務。

> [!NOTE]
> 如需授權資訊，請參閱 [Azure AD Connect 常見問題集](active-directory-aadconnect-health-faq.md)或 [Azure AD 定價頁面](https://aka.ms/aadpricing)。
    
![Azure AD Connect Health 入口網站](./media/active-directory-aadconnect-health/portal4.png)

* **快速入門**︰當您選取此選項時，[快速入門] 刀鋒視窗隨即開啟。 您可以選取 [取得工具] 下載 Azure AD Connect Health 代理程式。 您也可以存取文件，並提供意見反應。
* **Active Directory Federation Services**：此選項會顯示 Azure AD Connect Health 目前正在監視的所有 AD FS 服務。 當您選取執行個體時，開啟的刀鋒視窗會顯示該服務執行個體的相關資訊。 這項資訊包括概觀、屬性、警示、監視和使用情況分析。 如需深入了解這些功能，請參閱[搭配 AD FS 使用 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md)。
* **Azure Active Directory Connect (同步處理)**：此選項會顯示 Azure AD Connect Health 目前正在監視的 Azure AD Connect 伺服器。 當您選取項目時，開啟的刀鋒視窗會顯示 Azure AD Connect 伺服器的相關資訊。 如需深入了解這些功能，請參閱[使用 Azure AD Connect Health 進行同步處理](active-directory-aadconnect-health-sync.md)。
* **Active Directory Domain Services**：此選項會顯示 Azure AD Connect Health 目前正在監視的所有 AD DS 樹系。 當您選取樹系時，開啟的刀鋒視窗會顯示該樹系的相關資訊。 這項資訊包括基本資訊概觀、網域控制站儀表板、複寫狀態儀表板、警示和監視。 如需深入了解這些功能，請參閱[在 AD DS 使用 Azure AD Connect Health](active-directory-aadconnect-health-adds.md)。
* **設定**︰本節包含開啟或關閉下列功能的選項︰

  - 自動更新可將 Azure AD Connect Health 代理程式自動更新為最新版本：您將會在發佈最新版的 Azure AD Connect Health 代理程式時，自動更新為最新版本。 此選項預設為啟用狀態。
  - 僅允許 Microsoft 存取您的 Azure AD 目錄的健康狀況資料供疑難排解之用：如果啟用此選項，Microsoft 可以看到您所看到的相同資料。 這項資訊可能有助於進行問題的疑難排解和協助。 此選項預設為停用狀態。

## <a name="related-links"></a>相關連結
* [Azure AD Connect Health 代理程式安裝](active-directory-aadconnect-health-agent-install.md)
* [Azure AD Connect Health 操作](active-directory-aadconnect-health-operations.md)
* [在 AD FS 使用 Azure AD Connect Health](active-directory-aadconnect-health-adfs.md)
* [使用 Azure AD Connect Health 進行同步處理](active-directory-aadconnect-health-sync.md)
* [在 AD DS 使用 Azure AD Connect Health](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health 常見問題集](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health 版本歷程記錄](active-directory-aadconnect-health-version-history.md)
