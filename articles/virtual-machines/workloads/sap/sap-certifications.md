---
title: "適用於 SAP 的 Microsoft Azure 認證 | Microsoft Docs"
description: "目前 Azure 平台上 SAP 設定和認證的更新清單。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/12/2017
ms.author: rclaus
ms.custom: 
ms.openlocfilehash: 865fa54c908481b3f4c211f12293538c617b6129
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2017
---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>於 Microsoft Azure 上執行的 SAP 認證和設定

SAP 和 Microsoft 是已合作多年的穩固合作夥伴，能互相為彼此的客戶帶來許多好處。 Microsoft 一直持續更新其平台，並提交新的認證詳細資料給 SAP，以確保 Microsoft Azure 是執行 SAP 工作負載的最佳平台。 下表概述我們所支援的設定，以及持續增加的認證清單。 

## <a name="sap-hana-certifications"></a>SAP HANA 認證
參考：

- [SAP 附註 2316233：Microsoft Azure 上的 SAP HANA (大型執行個體)](https://launchpad.support.sap.com/#/notes/2316233) 涵蓋和 SAP HANA 支援相關的 HANA 大型執行個體。
- [SAP HANA 認證的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Amazon%20Web%20Services%2CMicrosoft%20Azure)可用於 SAP HANA 支援原生 Azure VM。

| SAP 產品 | 支援的 OS | Azure 供應項目 |
| --- | --- | --- |
| SAP HANA Developer Edition (包括由 SQLODBC、Windows 專用版的 ODBO、ODBC、JDBC 驅動程式、HANA Studio 及 HANA 資料庫組成的 HANA 用戶端軟體) | Red Hat Enterprise Linux、SUSE Linux Enterprise | D 系列 VM 系列 |
| Business One on HANA | SUSE Linux Enterprise | DS14_v2 |
| SAP S/4 HANA |Red Hat Enterprise Linux、SUSE Linux Enterprise | GS5 管制性運作、SAP HANA on Azure (大型執行個體) |
| Suite on HANA (OLTP) | Red Hat Enterprise Linux、SUSE Linux Enterprise | 適用於單一節點部署的 GS5 (針對非實際執行案例)、SAP HANA on Azure (大型執行個體) |
| HANA Enterprise for BW (OLAP) | Red Hat Enterprise Linux、SUSE Linux Enterprise | 適用於單一節點部署的 GS5、SAP HANA on Azure (大型執行個體) |
| SAP BW/4 HANA | Red Hat Enterprise Linux、SUSE Linux Enterprise | 適用於單一節點部署的 GS5、SAP HANA on Azure (大型執行個體) |

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver 認證
Microsoft Azure 已獲認證可用於下列 SAP 產品，因此具備 Microsoft 和 SAP 的完整支援。
參考：

- [1928533 - Azure 上的 SAP 應用程式：支援的產品和 Azure VM 類型](https://launchpad.support.sap.com/#/notes/1928533)用於所有 SAP NetWeaver 型應用程式，包括 SAP TREX、SAP LiveCache 及 SAP 內容伺服器。 還有所有資料庫，但不包括 SAP HANA。


| SAP 產品 | 客體作業系統 | RDBMS | 虛擬機器類型 |
| --- | --- | --- | --- |
| SAP Business Suite 軟體 |Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux、Oracle Linux |SQL Server；Oracle (僅限 Windows 及 Oracle Linux)、DB2、SAP ASE |A5 至 A11、D11 至 D14、DS11 至 DS14、DS11_v2 至 DS15_v2、GS1 至 GS5、M 系列 |
| SAP Business All-in-One |Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux |SQL Server；Oracle (僅限 Windows 及 Oracle Linux)、DB2、SAP ASE |A5 至 A11、D11 至 D14、DS11 至 DS14、DS11_v2 至 DS15_v2、GS1 至 GS5、M 系列 |
| SAP BusinessObjects BI |Windows |N/A |A5 至 A11、D11 至 D14、DS11 至 DS14、DS11_v2 至 DS15_v2、GS1 至 GS5、M 系列 |
| SAP NetWeaver |Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux |SQL Server；Oracle (僅限 Windows 及 Oracle Linux)、DB2、SAP ASE |A5 至 A11、D11 至 D14、DS11 至 DS14、DS11_v2 至 DS15_v2、GS1 至 GS5、M 系列 |

## <a name="other-sap-workload-supported-on-azure"></a>Azure 上支援的其他 SAP 工作負載

| SAP 產品 | 客體作業系統 | RDBMS | 虛擬機器類型 |
| --- | --- | --- | --- |
| SAP Business One on SQL Server | Windows  | SQL Server | 所有 NetWeaver 認證的 VM 類型 |
| SAP BPC 10.01 MS SP08 | Windows | | 所有 NetWeaver 認證的 VM 類型<br /> SAP 附註編號 2451795 |
| SAP Business Objects BI 平台 | Windows | | SAP 附註編號 2145537 |
| SAP Data Services 4.2 | | | SAP 附註編號 2288344 |
| SAP Hybris Commerce Platform 5.x 和 6.x | Windows | SQL Server，Oracle | 所有 NetWeaver 認證的 VM 類型<br /> [Hybris Wiki](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud) |
