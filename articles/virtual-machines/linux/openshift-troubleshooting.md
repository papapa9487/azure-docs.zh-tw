---
title: "針對 Azure 中的 OpenShift 部署進行疑難排解 | Microsoft Docs"
description: "Azure 中的 OpenShift 部署疑難排解"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: ea3664870480f6ed170972a5f52940dc4a852219
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2017
---
# <a name="troubleshooting-openshift-deployment-in-azure"></a>Azure 中的 OpenShift 部署疑難排解

如果 OpenShift 叢集未成功部署，則可以進行一些疑難排解工作來縮小問題的範圍。 檢視部署狀態，並針對下列結束代碼清單進行比較。

- 結束代碼 3：您的 Red Hat 訂用帳戶使用者名稱 / 密碼或組織識別碼 / 啟用金鑰不正確
- 結束代碼 4：您的 Red Hat 集區識別碼不正確，或沒有可用的權利
- 結束代碼 5：無法佈建 Docker 精簡集區磁碟區
- 結束代碼 6：OpenShift 叢集安裝失敗
- 結束代碼 7：OpenShift 叢集安裝成功，但是 Azure 雲端提供者設定失敗：主要節點上的主要組態問題
- 結束代碼 8：OpenShift 叢集安裝成功，但是 Azure 雲端提供者設定失敗：主要節點上的節點組態問題
- 結束代碼 9：OpenShift 叢集安裝成功，但是 Azure 雲端提供者設定失敗：基礎結構或應用程式節點上的節點組態問題
- 結束代碼 10：OpenShift 叢集安裝成功，但是 Azure 雲端提供者設定失敗：更正主要節點或無法將主機設為無法排程
- 結束代碼 11：計量無法部署
- 結束代碼 12：記錄無法部署

針對結束代碼 7-10，OpenShift 叢集未安裝，但是 Azure 雲端提供者設定失敗。 您可以使用 SSH 連線到主要節點 (來源) 或防禦節點 (容器平台)，然後從該處使用 SSH 連線到叢集中的每一個節點並修正問題。

結束代碼 7-9 常見的失敗原因是，服務主體對於訂用帳戶或資源群組沒有適當的權限。 如果這確實是個問題，則指派正確的權限，並手動重新執行使所有後續指令碼失敗的指令碼。
請務必重新啟動失敗的服務 (例如，systemctl restart atomic-openshift-node.service)，然後再次執行指令碼。

如需進一步疑難排解，在連接埠 2200 (來源) 上使用 SSH 連線到您的主要節點，或在連接埠 22 (容器平台) 上使用 SSH 連線到防禦節點。 您必須是根 (sudo su-)，然後瀏覽至下列目錄：/var/lib/waagent/custom-script/download

您應該會看到名為 '0' 和 '1' 的資料夾。 在上述每個資料夾中，您會看到兩個檔案，分別是 stderr 和 stdout。 您可以瀏覽這些檔案，以判定失敗發生的位置。
