---
title: "在 Azure Stack 中使用 Azure Resource Manager 範本 | Microsoft Docs"
description: "了解如何在 Azure Stack 中使用 Azure Resource Manager 範本來佈建資源。"
services: azure-stack
documentationcenter: 
author: heathl17
manager: byronr
editor: 
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 7648855011e8f77c35713d2d2ae50f2e474a08a6
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>在 Azure Stack 中使用 Azure 資源管理員範本

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

Azure Resource Manager 範本可藉由單一協調作業，來部署和佈建應用程式的所有資源。 您也可以重新部署範本，對資源群組中的資源進行變更。

可藉由 Microsoft Azure Stack 入口網站、PowerShell、命令列和 Visual Studio 部署這些範本。

下列快速入門範本位於 [GitHub](http://aka.ms/azurestackgithub)：

## <a name="deploy-sharepoint-non-high-availability"></a>部署 SharePoint (非高可用性)
使用 PowerShell DSC 擴充功能來建立 SharePoint 2013 伺服器陣列，其中包含下列資源：

* 虛擬網路
* 三個儲存體帳戶
* 兩個外部負載平衡器
* 一部 VM，設定為具單一網域之新樹系的網域控制站
* 一部 VM，設定為 SQL Server 2014 獨立伺服器
* 一部 VM，設定為一部電腦的 SharePoint 2013 伺服器陣列

## <a name="deploy-ad-non-high-availability"></a>部署 AD (非高可用性)
使用 PowerShell DSC 擴充功能來建立 AD 網域控制站伺服器，其中包含下列資源：

* 虛擬網路
* 一個儲存體帳戶
* 一個外部負載平衡器
* 一部 VM，設定為具單一網域之新樹系的網域控制站

## <a name="deploy-adsql-non-high-availability"></a>部署 AD/SQL (非高可用性)
使用 PowerShell DSC 擴充功能來建立 SQL Server 2014 獨立伺服器，其中包含下列資源：

* 虛擬網路
* 兩個儲存體帳戶
* 一個外部負載平衡器
* 一部 VM，設定為具單一網域之新樹系的網域控制站
* 一部 VM，設定為 SQL Server 2014 獨立伺服器

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server
使用 PowerShell DSC 擴充功能，設定現有的虛擬機器本機設定管理員 (LCM)，將其註冊到 Azure 自動化帳戶 DSC 提取伺服器。

## <a name="create-a-virtual-machine-from-a-user-image"></a>從使用者映像建立虛擬機器
從自訂使用者映像建立虛擬機器。 這個範本也會部署虛擬網路 (含 DNS)、公用 IP 位址及網路介面。

## <a name="simple-vm"></a>簡單的 VM
部署一部 Windows VM，其中包含虛擬網路 (含 DNS)、公用 IP 位址及網路介面。

## <a name="cancel-a-running-template-deployment"></a>取消執行中的範本部署
若要取消執行中的範本部署，請使用 `Stop-AzureRmResourceGroupDeployment` PowerShell Cmdlet。

## <a name="next-steps"></a>後續步驟
[使用入口網站部署範本](azure-stack-deploy-template-portal.md)

[Azure Resource Manager 概觀](../../azure-resource-manager/resource-group-overview.md)


