---
title: "Azure AD Connect 同步：了解及自訂同步處理 | Microsoft Docs"
description: "說明 Azure AD Connect 同步處理如何運作及如何自訂。"
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: markvi
ms.openlocfilehash: 4edac05325ad12596d982d113df0db7461124b12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Azure AD Connect 同步處理：了解及自訂同步處理
Azure Active Directory Connect 同步處理服務 (Azure AD Connect 同步處理) 是 Azure AD Connect 的主要元件。 它會負責執行同步處理內部部署環境與 Azure AD 之間識別資料的所有相關作業。 Azure AD Connect 同步處理是 DirSync、Azure AD 同步，以及已設定 Azure Active Directory 連接器之 Forefront Identity Manager 的後續版本。

本主題是「Azure AD Connect 同步」(也稱為「同步引擎」) 的主網頁，並列出所有其他相關主題的連結。 如需 Azure AD Connect 的連結，請參閱 [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

同步服務包含兩個元件：內部部署的 **Azure AD Connect 同步**元件，以及 Azure AD 中的服務端，稱為 **Azure AD Connect 同步服務**。 這是 DirSync、Azure AD Sync 與 Azure AD Connect 中很常見的服務。

## <a name="azure-ad-connect-sync-topics"></a>Azure AD Connect 同步處理主題
| 主題 | 涵蓋內容和讀取時機 |
| --- | --- |
| **Azure AD Connect 同步處理基本概念** | |
| [了解架構](active-directory-aadconnectsync-understanding-architecture.md) |適合於不熟悉同步處理引擎並想要深入了解所用架構和詞彙的人員。 |
| [技術概念](active-directory-aadconnectsync-technical-concepts.md) |精簡版的架構主題，簡要說明所用的詞彙。 |
| [Azure AD Connect 的拓撲](active-directory-aadconnect-topologies.md) |說明同步處理引擎支援的各種拓撲和案例。 |
| **自訂組態** | |
| [再次執行安裝精靈](active-directory-aadconnectsync-installation-wizard.md) |說明當您再次執行 Azure AD Connect 安裝精靈時可以使用哪些選項。 |
| [了解宣告式佈建](active-directory-aadconnectsync-understanding-declarative-provisioning.md) |說明稱為宣告式佈建的組態模型。 |
| [了解宣告式佈建運算式](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |說明宣告式佈建中所用運算式語言的語法。 |
| [了解預設組態](active-directory-aadconnectsync-understanding-default-configuration.md) |說明現成可用的規則和預設組態 還說明規則如何一起運作，以供現成可用的案例使用。 |
| [了解使用者和連絡人](active-directory-aadconnectsync-understanding-users-and-contacts.md) |延續前一個主題，並說明使用者和連絡人的組態如何一起運作 (特別是在多樹系的環境中)。 |
| [如何變更預設組態](active-directory-aadconnectsync-change-the-configuration.md) |逐步說明如何對屬性流程進行一般組態變更。 |
| [變更預設組態的最佳作法](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) |支援限制以及對現成可用的組態進行變更。 |
| [設定篩選](active-directory-aadconnectsync-configure-filtering.md) |說明如何限制哪些物件正同步處理至 Azure AD 的各種選項，以及逐步說明如何設定這些選項。 |
| **功能和案例** | |
| [防止意外刪除](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) |說明 [防止意外刪除]  功能以及如何加以設定。 |
| [排程器](active-directory-aadconnectsync-feature-scheduler.md) |描述匯入、同步處理及匯出資料的內建排程器。 |
| [實作密碼同步處理](active-directory-aadconnectsync-implement-password-synchronization.md) |說明密碼同步處理的運作方式、實作方式，以及其操作方式及疑難排解方式。 |
| [裝置回寫](active-directory-aadconnect-feature-device-writeback.md) |說明在 Azure AD Connect 中，裝置回寫的運作方式。 |
| [目錄擴充](active-directory-aadconnectsync-feature-directory-extensions.md) |說明如何使用您自己的自訂屬性擴充 Azure AD 結構描述。 |
| **同步處理服務** | |
| [Azure AD Connect 同步處理服務功能](active-directory-aadconnectsyncservice-features.md) |描述同步處理服務端，以及如何在 Azure AD 中變更同步設定。 |
| [重複屬性恢復功能](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) |描述如何啟用及使用 **userPrincipalName** 和 **proxyAddresses** 重複屬性值恢復功能。 |
| **作業和 UI** | |
| [同步處理服務管理員](active-directory-aadconnectsync-service-manager-ui.md) |說明 Synchronization Service Manager UI，包括 [[作業](active-directory-aadconnectsync-service-manager-ui-operations.md)]、[[連接器](active-directory-aadconnectsync-service-manager-ui-connectors.md)]、[[Metaverse 設計工具](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)] 及 [[Metaverse 搜尋](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)] 索引標籤。 |
| [作業工作和考量](active-directory-aadconnectsync-operations.md) |說明作業考量，例如災害復原。 |
| **作法...** | |
| [重設 Azure AD 帳戶](active-directory-aadconnectsync-howto-azureadaccount.md) |如何將 Azure AD Connect 同步處理連線至 Azure AD 時所使用的服務帳戶重設認證。 |
| **詳細資訊和參考** | |
| [連接埠](active-directory-aadconnect-ports.md) |列出您需要在同步處理引擎以及內部部署目錄與 Azure AD 之間開啟的連接埠。 |
| [將屬性同步處理至 Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) |列出在內部部署 AD 與 Azure AD 之間進行同步處理的所有屬性。 |
| [函式參考](active-directory-aadconnectsync-functions-reference.md) |列出宣告式佈建中可用的所有函式。 |

## <a name="additional-resources"></a>其他資源
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)

