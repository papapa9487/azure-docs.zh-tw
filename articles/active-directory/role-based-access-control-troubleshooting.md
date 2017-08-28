---
title: "針對 Azure RBAC 進行疑難排解 | Microsoft Docs"
description: "取得有關角色型存取控制資源問題或疑問的協助。"
services: azure-portal
documentationcenter: na
author: andredm7
manager: femila
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: 9ad194308d30ca652b32ec3b76750b0e838472f4
ms.contentlocale: zh-tw
ms.lasthandoff: 07/20/2017

---
# <a name="role-based-access-control-troubleshooting"></a>角色型存取控制疑難排解

本文將回答關於授與角色之特定存取權限的常見問題，讓您知道在 Azure 入口網站中使用角色時預期能夠使用哪些項目，以及如何針對存取問題進行疑難排解。 這三種角色涵蓋了所有資源類型︰

* 擁有者  
* 參與者  
* 讀取者  

擁有者與參與者都可以完整存取管理經驗，但參與者無法將存取權限授予其他使用者或群組。 讀取者角色則是比較有趣，因此我們會在本文中多花點時間介紹。 請參閱 [角色型存取控制入門文章](role-based-access-control-configure.md) ，以詳細了解如何授與存取權。

## <a name="app-service-workloads"></a>應用程式服務工作負載
### <a name="write-access-capabilities"></a>寫入存取功能
如果您授與對單一 Web 應用程式授與使用者唯讀存取權限，部分功能可能會在未預期的情況下停用。 以下管理功能需要 Web 應用程式的**寫入**存取權限 (參與者或擁有者)，而且無法在任何唯讀情況中使用。

* 命令 (像是開始、停止等)
* 變更像是一般組態的設定、調整設定、備份設定與監視設定
* 存取發佈認證與其他機密，像是應用程式設定與連接字串
* 串流記錄
* 診斷記錄組態
* 主控台 (命令提示字元)
* 有效的近期部署項目 (以便本機 git 持續部署)
* 預估的花費
* Web 測試
* 虛擬網路 (只有當虛擬網路先前是由具備寫入存取權限的使用者所設定，才會顯示出來)。

如果您無法存取上述任何一個磚，請洽詢您的系統管理員，取得 Web 應用程式的參與者存取權限。

### <a name="dealing-with-related-resources"></a>處理相關資源
Web 應用程式因為幾個互有關聯的資源而顯得複雜。 以下是具有多個網站的典型資源群組：

![Web 應用程式資源群組](./media/role-based-access-control-troubleshooting/website-resource-model.png)

如此一來，如果您只授予某人 Web 應用程式存取權限，Azure 入口網站的網站刀鋒視窗上的諸多功能即會停用。

這些項目需要對應至您網站的「應用程式服務方案」的**寫入**權：  

* 檢視 Web 應用程式的定價層 (免費或標準)  
* 調整組態 (執行個體數量、虛擬機器大小、自動調整設定)  
* 配額 (儲存容量、頻寬、CPU)  

這些項目都需要包含您網站的整個「資源群組」的**寫入**權：  

* SSL 憑證與繫結 (相同資源群組與地理位置中的各個網站之間，可共用 SSL 憑證)  
* 警示規則  
* 自動調整設定  
* 應用程式見解元件  
* Web 測試  

## <a name="virtual-machine-workloads"></a>虛擬機器工作負載
差不多與 Web 應用程式相同的是，虛擬機器分頁上的某些功能需要具備虛擬機器 (或是資源群組中的其他資源) 的寫入存取權限。

虛擬機器與網域名稱、虛擬網路、儲存體帳戶及警示規則相關。

這些項目都需要具備「虛擬機器」的**寫入**權：

* 端點  
* IP 位址  
* 磁碟  
* 擴充功能  

這些項目都需要同時具備「虛擬機器」與所屬之「資源群組」(連同網域名稱) 的**寫入**權：  

* 可用性設定組  
* 負載平衡集合  
* 警示規則  

如果您無法存取上述任何一個磚，請洽詢您的系統管理員，以取得資源群組的參與者存取權限。

## <a name="see-more"></a>更多資訊
* [角色型存取控制](role-based-access-control-configure.md)：開始在 Azure 入口網站中使用 RBAC。
* [內建角色](role-based-access-built-in-roles.md)︰取得有關 RBAC 中標準角色的詳細資訊。
* [Azure RBAC 中的自訂角色](role-based-access-control-custom-roles.md)︰了解如何建立自訂角色，以符合您的存取需求。
* [建立存取權變更歷程記錄報告](role-based-access-control-access-change-history-report.md)︰追蹤 RBAC 中的角色指派變更。


