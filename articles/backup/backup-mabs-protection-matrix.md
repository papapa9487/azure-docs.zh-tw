---
title: "Azure 備份伺服器可以備份哪些內容 | Microsoft Docs"
description: "本文提供支援矩陣，其中列出 Azure 備份伺服器 v2 保護的所有工作負載、資料類型和安裝。"
services: backup
documentation center: 
author: markgalioto
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
keywords: 
ms.date: 05/15/2017
ms.topic: article
ms.author: markgal,masaran
manager: carmonm
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: 3608c310f43ff9f97563d252437d50d962162f47
ms.contentlocale: zh-tw
ms.lasthandoff: 08/04/2017

---

# <a name="azure-backup-server-protection-matrix"></a>Azure 備份伺服器保護矩陣

本文列出您可以使用 Azure 備份伺服器保護的各種伺服器和工作負載。 下列矩陣列出可使用 Azure 備份伺服器 v1 和 v2 保護的項目。

## <a name="protection-support-matrix"></a>保護支援矩陣

|工作負載|版本|Azure 備份伺服器</br> installation|Azure 備份</br> 伺服器 v2|Azure 備份</br> 伺服器 v1 |保護和復原|
|------------|-----------|--------------------|--------------------------------------------|--------------------------------|---------------------------|
|System Center VMM|VMM 2016、<br/>VMM 2012、SP1、R2|實體伺服器<br /><br />Hyper-V 虛擬機器|Y|Y|所有部署案例：資料庫|
|用戶端電腦 (64 位元和 32 位元)|Windows 10|實體伺服器<br /><br />Hyper-V 虛擬機器<br /><br />VMware 虛擬機器|Y|Y|檔案<br /><br />受保護的磁碟區必須是 NTFS。 不支援 FAT 和 FAT32。<br /><br />磁碟區至少必須是 1 GB。 DPM 使用磁碟區陰影複製服務 (VSS) 建立資料快照集，而且此快照集僅適用於磁碟區至少為 1 GB 時。|
|用戶端電腦 (64 位元和 32 位元)|Windows 8.1|實體伺服器<br /><br />Hyper-V 虛擬機器|Y|Y|檔案<br /><br />受保護的磁碟區必須是 NTFS。 不支援 FAT 和 FAT32。<br /><br />磁碟區至少必須是 1 GB。 DPM 使用磁碟區陰影複製服務 (VSS) 建立資料快照集，而且此快照集僅適用於磁碟區至少為 1 GB 時。|
|用戶端電腦 (64 位元和 32 位元)|Windows 8.1|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y|Y|檔案<br /><br />受保護的磁碟區必須是 NTFS 且至少為 1 GB。|
|用戶端電腦 (64 位元和 32 位元)|Windows 8|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|檔案<br /><br />受保護的磁碟區必須是 NTFS 且至少為 1 GB。|
|用戶端電腦 (64 位元和 32 位元)|Windows 8|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y|Y|檔案<br /><br />受保護的磁碟區必須是 NTFS 且至少為 1 GB。|
|用戶端電腦 (64 位元和 32 位元)|Windows 7|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|檔案<br /><br />受保護的磁碟區必須是 NTFS 且至少為 1 GB。|
|用戶端電腦 (64 位元和 32 位元)|Windows 7|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y|Y |檔案<br /><br />受保護的磁碟區必須是 NTFS 且至少為 1 GB。|
|用戶端電腦 (64 位元和 32 位元)|Windows Vista SP2|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|檔案<br /><br />受保護的磁碟區必須是 NTFS 且至少為 1 GB。|
|用戶端電腦 (64 位元和 32 位元)|Windows Vista SP1|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|檔案<br /><br />受保護的磁碟區必須是 NTFS 且至少為 1 GB。|
|用戶端電腦 (64 位元和 32 位元)|Windows Vista|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|檔案<br /><br />受保護的磁碟區必須是 NTFS 且至少為 1 GB。|
|用戶端電腦 (64 位元和 32 位元)|Windows Vista|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|磁碟區、共用、資料夾、檔案、系統狀態/裸機、已啟用重複資料刪除功能的磁碟區|
|伺服器 (32 位元和 64 位元)|Windows Server 2016|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)<br /><br />VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)<br /><br />實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y<br /><br />不包括 Nano 伺服器|N|磁碟區、共用、資料夾、檔案、系統狀態/裸機、已啟用重複資料刪除功能的磁碟區|
|伺服器 (32 位元和 64 位元)|Windows Server 2012 R2 - Datacenter 和 Standard|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)|Y|Y |磁碟區、共用、資料夾、檔案<br /><br />DPM 必須至少在 Windows Server 2012 R2 上執行，才能保護已啟用重複資料刪除功能的 Windows Server 2012 磁碟區。|
|伺服器 (32 位元和 64 位元)|Windows Server 2012 R2 - Datacenter 和 Standard|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y|Y|磁碟區、共用、資料夾、檔案、系統狀態/裸機<br /><br />DPM 必須在 Windows Server 2012 或 2012 R2 上執行，才能保護已啟用重複資料刪除功能的 Windows Server 2012 磁碟區。|
|伺服器 (32 位元和 64 位元)|Windows Server 2012/2012 SP1 - Datacenter 和 Standard|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|磁碟區、共用、資料夾、檔案、系統狀態/裸機<br /><br />DPM 必須至少在 Windows Server 2012 R2 上執行，才能保護已啟用重複資料刪除功能的 Windows Server 2012 磁碟區。|
|伺服器 (32 位元和 64 位元)|Windows Server 2012/2012 SP1 - Datacenter 和 Standard|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)|Y|Y|磁碟區、共用、資料夾、檔案<br /><br />DPM 必須至少在 Windows Server 2012 R2 上執行，才能保護已啟用重複資料刪除功能的 Windows Server 2012 磁碟區。|
|伺服器 (32 位元和 64 位元)|Windows Server 2012/2012 SP1 - Datacenter 和 Standard|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y|Y|磁碟區、共用、資料夾、檔案、系統狀態/裸機<br /><br />DPM 必須至少在 Windows Server 2012 R2 上執行，才能保護已啟用重複資料刪除功能的 Windows Server 2012 磁碟區。|
|伺服器 (32 位元和 64 位元)|Windows Server 2008 R2 SP1 - Standard 和 Enterprise|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y<br /><br />您必須執行 SP1 並安裝 [Windows Management Frame 4.0](https://www.microsoft.com/en-us/download/details.aspx?id=40855)|Y|磁碟區、共用、資料夾、檔案、系統狀態/裸機|
|伺服器 (32 位元和 64 位元)|Windows Server 2008 R2 SP1 - Standard 和 Enterprise|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)|Y<br /><br />您必須執行 SP1 並安裝 [Windows Management Frame 4.0](https://www.microsoft.com/en-us/download/details.aspx?id=40855)|Y |磁碟區、共用、資料夾、檔案|
|伺服器 (32 位元和 64 位元)|Windows Server 2008 R2 SP1 - Standard 和 Enterprise|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y<br /><br />您必須執行 SP1 並安裝 [Windows Management Frame 4.0](https://www.microsoft.com/en-us/download/details.aspx?id=40855)|Y |磁碟區、共用、資料夾、檔案、系統狀態/裸機|
|伺服器 (32 位元和 64 位元)|Windows Server 2008 R2|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|磁碟區、共用、資料夾、檔案、系統狀態/裸機|
|伺服器 (32 位元和 64 位元)|Windows Server 2008 R2|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)|N|Y|磁碟區、共用、資料夾、檔案|
|伺服器 (32 位元和 64 位元)|Windows Server 2008 R2|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|N|Y|磁碟區、共用、資料夾、檔案、系統狀態/裸機|
|伺服器 (32 位元和 64 位元)|Windows Server 2008|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|N|Y|磁碟區、共用、資料夾、檔案、系統狀態/裸機|
|伺服器 (32 位元和 64 位元)|Windows Server 2008|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y|Y |磁碟區、共用、資料夾、檔案、系統狀態/裸機|
|伺服器 (32 位元和 64 位元)|Windows Storage Server 2008|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|磁碟區、共用、資料夾、檔案、系統狀態/裸機|
|SQL Server|SQL Server 2016|實體伺服器 <br /><br /> 內部部署 Hyper-V 虛擬機器 <br /> <br /> Azure 虛擬機器 <br /><br /> VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y |N|所有部署案例：資料庫|
|SQL Server|SQL Server 2014|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)|Y|Y |所有部署案例：資料庫|
|SQL Server|SQL Server 2014|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y|Y|所有部署案例：資料庫|
|SQL Server|SQL Server 2012 SP2|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y |所有部署案例：資料庫|
|SQL Server|SQL Server 2012 SP2|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)|Y|Y|所有部署案例：資料庫|
|SQL Server|SQL Server 2012 SP2|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y|Y|所有部署案例：資料庫|
|SQL Server|SQL Server 2012、SQL Server 2012 SP1|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|所有部署案例：資料庫|
|SQL Server|SQL Server 2012、SQL Server 2012 SP1|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)|Y|Y|所有部署案例：資料庫|
|SQL Server|SQL Server 2012、SQL Server 2012 SP1|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y|Y|所有部署案例：資料庫|
|SQL Server|SQL Server 2008 R2|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|所有部署案例：資料庫|
|SQL Server|SQL Server 2008 R2|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)|Y|Y|所有部署案例：資料庫|
|SQL Server|SQL Server 2008 R2|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y|Y |所有部署案例：資料庫|
|SQL Server|SQL Server 2008|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|所有部署案例：資料庫|
|SQL Server|SQL Server 2008|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)|Y|Y|所有部署案例：資料庫|
|SQL Server|SQL Server 2008|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y|Y|所有部署案例：資料庫|
|Exchange|Exchange 2016|實體伺服器<br/><br/> 內部部署 Hyper-V 虛擬機器|Y|Y|保護 (所有部署案例)：獨立 Exchange 伺服器、資料庫可用性群組 (DAG) 下的資料庫<br /><br />復原 (所有部署案例)：信箱、DAG 下的信箱資料庫|
|Exchange|Exchange 2016|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y|Y|保護 (所有部署案例)：獨立 Exchange 伺服器、資料庫可用性群組 (DAG) 下的資料庫<br /><br />復原 (所有部署案例)：信箱、DAG 下的信箱資料庫|
|Exchange|Exchange 2013|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|保護 (所有部署案例)：獨立 Exchange 伺服器、資料庫可用性群組 (DAG) 下的資料庫<br /><br />復原 (所有部署案例)：信箱、DAG 下的信箱資料庫|
|Exchange|Exchange 2013|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y|Y |保護 (所有部署案例)：獨立 Exchange 伺服器、資料庫可用性群組 (DAG) 下的資料庫<br /><br />復原 (所有部署案例)：信箱、DAG 下的信箱資料庫|
|Exchange|Exchange 2010|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|保護 (所有部署案例)：獨立 Exchange 伺服器、資料庫可用性群組 (DAG) 下的資料庫<br /><br />復原 (所有部署案例)：信箱、DAG 下的信箱資料庫|
|Exchange|Exchange 2010|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y|Y |保護 (所有部署案例)：獨立 Exchange 伺服器、資料庫可用性群組 (DAG) 下的資料庫<br /><br />復原 (所有部署案例)：信箱、DAG 下的信箱資料庫|
|Exchange|Exchange 2007|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|保護 (所有部署案例)：儲存群組<br /><br />復原 (所有部署案例)：儲存群組、資料庫、信箱|
|Exchange|Exchange 2007|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y|Y |保護 (所有部署案例)：儲存群組<br /><br />復原 (所有部署案例)：儲存群組、資料庫、信箱|
|SharePoint|SharePoint 2016|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器<br /><br />Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)<br /><br />VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y |N|保護 (所有部署案例)：伺服器陣列、前端網頁伺服器內容<br /><br />復原 (所有部署案例)：伺服器陣列、資料庫、Web 應用程式、檔案或清單項目、SharePoint 搜尋、前端網頁伺服器<br /><br />請注意，不支援保護針對內容資料庫使用 SQL Server 2012 AlwaysOn 功能的 SharePoint 伺服器陣列。|
|SharePoint|SharePoint 2013|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|保護 (所有部署案例)：伺服器陣列、前端網頁伺服器內容<br /><br />復原 (所有部署案例)：伺服器陣列、資料庫、Web 應用程式、檔案或清單項目、SharePoint 搜尋、前端網頁伺服器<br /><br />請注意，不支援保護針對內容資料庫使用 SQL Server 2012 AlwaysOn 功能的 SharePoint 伺服器陣列。|
|SharePoint|SharePoint 2013|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時) - DPM 2012 R2 更新彙總套件 3 及更新版本|Y|Y|保護 (所有部署案例)：伺服器陣列、SharePoint 搜尋、前端網頁伺服器內容<br /><br />復原 (所有部署案例)：伺服器陣列、資料庫、Web 應用程式、檔案或清單項目、SharePoint 搜尋、前端網頁伺服器<br /><br />請注意，不支援保護針對內容資料庫使用 SQL Server 2012 AlwaysOn 功能的 SharePoint 伺服器陣列。|
|SharePoint|SharePoint 2013|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y|Y |保護 (所有部署案例)：伺服器陣列、SharePoint 搜尋、前端網頁伺服器內容<br /><br />復原 (所有部署案例)：伺服器陣列、資料庫、Web 應用程式、檔案或清單項目、SharePoint 搜尋、前端網頁伺服器<br /><br />請注意，不支援保護針對內容資料庫使用 SQL Server 2012 AlwaysOn 功能的 SharePoint 伺服器陣列。|
|SharePoint|SharePoint 2010|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|保護 (所有部署案例)：伺服器陣列、SharePoint 搜尋、前端網頁伺服器內容<br /><br />復原 (所有部署案例)：伺服器陣列、資料庫、Web 應用程式、檔案或清單項目、SharePoint 搜尋、前端網頁伺服器|
|SharePoint|SharePoint 2010|Azure 虛擬機器 (當工作負載當做 Azure 虛擬機器執行時)|Y|Y |保護 (所有部署案例)：伺服器陣列、SharePoint 搜尋、前端網頁伺服器內容<br /><br />復原 (所有部署案例)：伺服器陣列、資料庫、Web 應用程式、檔案或清單項目、SharePoint 搜尋、前端網頁伺服器|
|SharePoint|SharePoint 2010|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y|Y|保護 (所有部署案例)：伺服器陣列、SharePoint 搜尋、前端網頁伺服器內容<br /><br />復原 (所有部署案例)：伺服器陣列、資料庫、Web 應用程式、檔案或清單項目、SharePoint 搜尋、前端網頁伺服器|
|SharePoint|SharePoint 2007|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|保護 (所有部署案例)：伺服器陣列、SharePoint 搜尋、前端網頁伺服器內容<br /><br />復原 (所有部署案例)：伺服器陣列、資料庫、Web 應用程式、檔案或清單項目、SharePoint 搜尋、前端網頁伺服器|
|SharePoint|SharePoint 2007|VMWare 中的 Windows 虛擬機器 (保護在 VMWare 的 Windows 虛擬機器中執行的工作負載)|Y|Y|保護 (所有部署案例)：伺服器陣列、SharePoint 搜尋、前端網頁伺服器內容<br /><br />復原 (所有部署案例)：伺服器陣列、資料庫、Web 應用程式、檔案或清單項目、SharePoint 搜尋、前端網頁伺服器|
|Hyper-V 主機 - Hyper-V 主機伺服器、叢集或 VM 上的 DPM 保護代理程式|Windows Server 2016|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|N|保護：Hyper-V 電腦、叢集共用磁碟區 (CSV)<br /><br />復原：虛擬機器、檔案和資料夾的項目層級復原、磁碟區、虛擬硬碟|
|Hyper-V 主機 - Hyper-V 主機伺服器、叢集或 VM 上的 DPM 保護代理程式|Windows Server 2012 R2 - Datacenter 和 Standard|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|保護：Hyper-V 電腦、叢集共用磁碟區 (CSV)<br /><br />復原：虛擬機器、檔案和資料夾的項目層級復原、磁碟區、虛擬硬碟|
|Hyper-V 主機 - Hyper-V 主機伺服器、叢集或 VM 上的 DPM 保護代理程式|Windows Server 2012 - Datacenter 和 Standard|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|保護：Hyper-V 電腦、叢集共用磁碟區 (CSV)<br /><br />復原：虛擬機器、檔案和資料夾的項目層級復原、磁碟區、虛擬硬碟|
|Hyper-V 主機 - Hyper-V 主機伺服器、叢集或 VM 上的 DPM 保護代理程式|Windows Server 2008 R2 SP1 - Enterprise 和 Standard|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|Y|Y|保護：Hyper-V 電腦、叢集共用磁碟區 (CSV)<br /><br />復原：虛擬機器、檔案和資料夾的項目層級復原、磁碟區、虛擬硬碟|
|Hyper-V 主機 - Hyper-V 主機伺服器、叢集或 VM 上的 DPM 保護代理程式|Windows Server 2008|實體伺服器<br /><br />內部部署 Hyper-V 虛擬機器|N|N|保護：Hyper-V 電腦、叢集共用磁碟區 (CSV)<br /><br />復原：虛擬機器、檔案和資料夾的項目層級復原、磁碟區、虛擬硬碟|
|VMware VM|VMware Server 5.5 或 6.0 or 6.5 |內部部署 Hyper-V 虛擬機器|Y|Y (具有 UR1)|叢集共用磁碟區 (CSV)、NFS 和 SAN 儲存體上的 VMware VM<br /> 檔案和資料夾的項目層級復原僅適用於 Windows<br /> 不支援 VMware vApps|
|Linux|當做 Hyper-V 或 VMware 客體執行的 Linux|內部部署 Hyper-V 虛擬機器|Y|Y|Hyper-V 必須在 Windows Server 2012 R2 或 Windows Server 2016 上執行。 保護：整部虛擬機器<br /><br />復原：整部虛擬機器|

## <a name="cluster-support"></a>叢集支援
Azure 備份伺服器可以保護下列叢集應用程式中的資料：

-   檔案伺服器

-   SQL Server

-   Hyper-V - 如果您使用擴充的 DPM 保護來保護 Hyper-V 叢集，則無法為受保護的 Hyper-V 工作負載新增次要保護。

    如果您在 Windows Server 2008 R2 上執行 Hyper-V，請務必安裝 KB [975354](https://support.microsoft.com/en-us/kb/975354) 中所述的更新。
    如果您在叢集設定的 Windows Server 2008 R2 上執行 Hyper-V，請務必安裝 SP2 和 KB [971394](https://support.microsoft.com/en-us/kb/971394)。

-   Exchange Server - Azure 備份伺服器可以保護支援之 Exchange Server 版本的非共用磁碟叢集 (叢集連續複寫)，也可以保護設定用於本機連續複寫的 Exchange Server。

-   SQL Server - Azure 備份伺服器不支援備份裝載於叢集共用磁碟區 (CSV) 上的 SQL Server 資料庫。

Azure 備份伺服器可以保護位於與 DPM 伺服器相同的網域中，以及位於子網域或受信任網域中的叢集工作負載。 如果您想要保護未信任網域或工作群組中的資料來源，請針對單一伺服器使用 NTLM 或憑證驗證，或是針對叢集只使用憑證驗證。

