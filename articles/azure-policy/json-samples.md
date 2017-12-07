---
title: "原則範本範例 | Microsoft Docs"
description: "Azure 原則的 JSON 範例"
services: azure-policy
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
tags: 
ms.assetid: 
ms.service: azure-policy
ms.devlang: na
ms.topic: samples
ms.tgt_pltfrm: 
ms.workload: 
ms.date: 11/13/2017
ms.author: banders
ms.custom: mvc
ms.openlocfilehash: 4ac9696028b9f24341a630d630b583ac5041cee0
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2017
---
# <a name="templates-for-azure-policy"></a>Azure 原則的範本

下表包含「Azure 原則」的 JSON 範本連結。 這些範例位於 [Azure 原則範例存放庫](https://github.com/Azure/azure-policy) \(英文\)。

| | |
|---|---|
|**計算**||
| [已核准的 VM 映像](scripts/allowed-custom-images.md) | 要求只能在環境中部署已核准的自訂映像。 您需指定已核准的映像識別碼陣列。 |
| [在 VM 不使用受管理磁碟時稽核](scripts/create-vm-managed-disk.md) | 稽核所建立的虛擬機器是否未使用受控磁碟。|
| [稽核擴充功能是否不存在](scripts/audit-ext-not-exist.md) | 稽核是否未搭配虛擬機器部署擴充功能。 您需指定擴充功能發行者和類型，以檢查是否已部署該擴充功能。 |
| [允許來自某個資源群組的自訂 VM 映像](scripts/allow-custom-vm-image.md) |  要求自訂映像必須來自某個已核准的資源群組。 您需指定已核准之資源群組的名稱。 |
| [拒絕 Hybrid Use Benefit](scripts/deny-hybrid-use.md) | 禁止使用 Azure Hybrid Use Benefit (AHUB)。 當您不想要允許使用內部部署授權時，請使用此範本。 |
| [不允許的 VM 擴充功能](scripts/not-allowed-vm-ext.md) | 禁止使用指定的擴充功能。 您需指定一個包含所禁止擴充功能類型的陣列。 |
| [只允許特定的 VM 平台映像](scripts/allow-certain-vm-image.md) | 要求虛擬機器使用特定版本的 UbuntuServer。 |
| [使用受控磁碟來建立 VM](scripts/use-managed-disk-vm.md) | 要求虛擬機器使用受控磁碟。|
|**監視**||
| [稽核診斷設定](scripts/audit-diag-setting.md) | 稽核是否未針對指定的資源類型啟用診斷設定。 您需指定資源類型陣列，以檢查是否已啟用診斷設定。 |
|**名稱和文字慣例**||
| [允許多個名稱模式](scripts/allow-multiple-name-patterns.md) | 允許多個名稱模式的其中一個用於資源。 |
| [需要類似模式](scripts/enforce-like-pattern.md) | 請確定資源名稱符合某個模式的類似條件。 |
| [需要比對模式](scripts/enforce-match-pattern.md) | 請確定資源名稱符合命名模式。 |
| [需要標籤比對模式](scripts/enforce-tag-match-pattern.md) | 請確認標籤值符合文字模式。 |
|**網路**||
| [允許的應用程式閘道 SKU](scripts/allowed-app-gate-sku.md) | 要求應用程式閘道使用已核准的 SKU。 您需指定已核准的 SKU 陣列。 |
| [稽核是否未針對區域啟用網路監看員](scripts/net-watch-not-enabled.md) | 稽核是否未針對指定的區域啟用網路監看員。 您需指定區域名稱，以檢查是否已啟用網路監看員。 |
| [對每個 NIC 使用 NSG X](scripts/nsg-on-nic.md) | 要求搭配每個虛擬網路介面使用特定的網路安全性群組。 您需指定要使用之網路安全性群組的識別碼。 |
| [對每個子網路使用 NSG X](scripts/nsg-on-subnet.md) | 要求搭配每個虛擬子網路使用特定的網路安全性群組。 您需指定要使用之網路安全性群組的識別碼。 |
| [允許的 Express Route 頻寬](scripts/allowed-er-band.md) | 要求 Express Route 使用一組指定的頻寬。 您需指定可為 Express Route 指定的 SKU 陣列。 |
| [允許的 Express Route 對等互連位置](scripts/allowed-peering-er.md) | 要求 Express Route 使用指定的對等互連位置。 您需指定允許的對等互連位置陣列。 |
| [允許的 Express Route SKU](scripts/allowed-er-skus.md) | 要求 Express Route 使用已核准的 SKU。 您需指定允許的 SKU 陣列。 |
| [允許的負載平衡器 SKU](scripts/allowed-lb-skus.md) | 要求負載平衡器使用已核准的 SKU。 您需指定允許的 SKU 陣列。 |
| [禁止對 ER 網路進行網路對等互連](scripts/no-peering-er-net.md) | 禁止將網路對等互連與指定資源群組中的網路建立關聯。 用來防止與中央受管理網路基礎結構建立連線。 您需指定要防止建立關聯的資源群組名稱。 |
| [禁止使用者定義的路由表](scripts/no-user-def-route-table.md)  |禁止使用使用者定義的路由表來部署虛擬網路。 |
| [允許的虛擬網路閘道 SKU](scripts/no-user-def-route-table.md) | 要求虛擬網路閘道使用已核准的 SKU 和閘道類型。 您需指定已核准的 SKU 陣列和已核准的閘道類型陣列。 |
| [針對 VM 網路介面使用已核准的子網路](scripts/use-approved-subnet-vm-nics.md) | 要求網路介面使用已核准的子網路。 您需指定已核准之子網路的識別碼。 |
| [針對 VM 網路介面使用已核准的 vNet](scripts/use-approved-vnet-vm-nics.md) | 要求網路介面使用已核准的虛擬網路。 您需指定已核准之虛擬網路的識別碼。 |
|**標記**||
| [計費標籤原則計畫](scripts/billing-tags-policy-init.md) | 要求針對成本中心和產品名稱使用指定的標籤值。 使用內建的原則來套用及強制執行必要的標籤。 您需為標籤指定必要的值。  |
| [對資源群組強制執行標籤及其值](scripts/enforce-tag-rg.md) | 要求對資源群組使用標籤和值。 您需指定必要的標籤名稱和值。  |
|**SQL**||
| [稽核 SQL DB 層級稽核設定](scripts/audit-sql-db-audit-setting.md) | 稽核 SQL 資料庫稽核設定是否與指定的設定不符。 您需指定一個指出應啟用或停用稽核設定的值。  |
| [稽核透明資料加密狀態](scripts/audit-trans-data-enc-status.md) | 稽核是否未啟用 SQL 資料庫透明資料加密。  |
| [稽核 DB 層級威脅偵測設定](scripts/audit-db-threat-det-setting.md) | 稽核 SQL 資料庫安全性警示原則是否未設定成指定的狀態。 您需指定一個指出已啟用或停用威脅偵測的值。  |
| [稽核 SQL Server 層級稽核設定](scripts/audit-sql-ser-leve-audit-setting.md) | 稽核 SQL Server 稽核設定是否與指定的設定不符。 您需指定一個指出應啟用或停用稽核設定的值。 |
| [稽核伺服器層級威脅偵測設定](scripts/audit-sql-ser-threat-det-setting.md) | 稽核 SQL 資料庫安全性警示原則是否未設定成指定的狀態。 您需指定一個指出已啟用或停用威脅偵測的值。  |
| [在沒有 Azure Active Directory 系統管理員時稽核](scripts/audit-no-aad-admin.md) | 在 SQL Server 沒有指派的 Azure Active Directory 系統管理員時稽核。 |
| [允許的 SQL DB SKU](scripts/allowed-sql-db-skus.md) | 要求 SQL 資料庫使用已核准的 SKU。 您需指定允許的 SKU 識別碼陣列或允許的 SKU 名稱陣列。 |
|**儲存體**||
| [允許的儲存體帳戶和虛擬機器 SKU](scripts/allowed-skus-storage.md) | 要求儲存體帳戶和虛擬機器使用已核准的 SKU。 使用內建的原則來確保使用已核准的 SKU。 您需指定已核准的虛擬機器 SKU 陣列和已核准的儲存體帳戶 SKU 陣列。 |
| [針對儲存體帳戶確保只允許 HTTPS 流量](scripts/ensure-https-stor-acct.md) | 要求儲存體帳戶使用 HTTPS 流量。  |
| [針對儲存體帳戶拒絕使用非經常性存取層處理](scripts/deny-cool-access-tiering.md) | 針對 Blob 儲存體帳戶禁止使用非經常性存取層處理。  |
| [確保儲存體檔案加密](scripts/ensure-store-file-enc.md) | 要求針對儲存體帳戶啟用檔案加密。  |
|**內建原則**||
| [允許的位置](scripts/allowed-locs.md) | 要求將所有資源都部署到已核准的位置。 您需指定已核准的位置陣列。  |
| [允許的資源類型](scripts/allowed-res-types.md) | 確保只部署已核准的資源類型。 您需指定允許的資源類型陣列。  |
| [允許的儲存體帳戶 SKU](scripts/allowed-stor-acct-skus.md) | 要求儲存體帳戶使用已核准的 SKU。 您需指定已核准的 SKU 陣列。 |
| [套用標籤及其預設值](scripts/apply-tag-def-val.md) | 附加指定的標籤名稱和值 (如果未提供該標籤)。 您需指定要套用的標籤名稱和值。  |
| [強制執行標籤及其值](scripts/enforce-tag-val.md) | 要求使用指定的標籤名稱和值。 您需指定要強制執行的標籤名稱和值。  |
| [不允許的資源類型](scripts/not-allowed-res-type.md) | 禁止部署指定的資源類型。 您需指定要封鎖的資源類型陣列。  |
| [要求 SQL Server 12.0 版](scripts/req-sql-12.md) | 要求 SQL Server 使用 12.0 版。  |
| [要求儲存體帳戶加密](scripts/req-store-acct-enc.md) | 要求儲存體帳戶使用 Blob 加密。  |
