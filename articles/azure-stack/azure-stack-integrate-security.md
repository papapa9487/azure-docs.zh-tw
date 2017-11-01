---
title: "Azure Stack 資料中心整合 - 安全性"
description: "了解如何將 Azure Stack 安全性與您的資料中心安全性整合"
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 10/17/2017
ms.author: victorh
keywords: 
ms.openlocfilehash: eb7c651362838d44d6558e080e6130b4a8041d1e
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2017
---
# <a name="azure-stack-datacenter-integration---security"></a>Azure Stack 資料中心整合 - 安全性

適用於：Azure Stack 整合系統

Azure Stack 在設計和建置時，已將安全性考慮進去。 Azure Stack 是鎖定的系統，因此不支援安裝軟體安全性代理程式。

本文會協助您將 Azure Stack 的安全性功能以及您的資料中心內已部署的安全性解決方案整合在一起。

## <a name="security-logs"></a>安全性記錄

Azure Stack 會收集基礎結構角色和縮放單位節點的作業系統和安全性事件，頻率為每兩分鐘一次。 這些記錄會儲存在儲存體帳戶的 Blob 容器中。

每個基礎結構角色都會有一個儲存體帳戶，且所有典型的作業系統事件也會有一個通用儲存體帳戶。

健康情況資源提供者可透過 REST 通訊協定來加以呼叫，以將 URL 擷取至 Blob 容器。 第三方安全性解決方案可使用 API 和儲存體帳戶來擷取事件以供處理。

### <a name="use-azure-storage-explorer-to-view-events"></a>使用 Azure 儲存體總管來檢視事件

您可以使用稱為 Azure 儲存體總管的工具來擷取 Azure Stack 所收集的事件。 您可以從 [http://storageexplorer.com](http://storageexplorer.com) 下載 Azure 儲存體總管。

下列程序範例可供您用來針對 Azure Stack 設定 Azure 儲存體總管：

1. 以操作員身分登入 Azure Stack 系統管理員入口網站。
2. 瀏覽**儲存體帳戶**，然後尋找 **frphealthaccount**。 **frphealthaccount** 帳戶是用來儲存所有作業系統事件的通用儲存體帳戶。

   ![儲存體帳戶](media/azure-stack-integrate-security/storage-accounts.png)

3. 選取 [frphealthaccount]，然後按一下 [存取金鑰]。

   ![存取金鑰](media/azure-stack-integrate-security/access-keys.png)

4. 將存取金鑰複製到剪貼簿。
5. 開啟 [Azure 儲存體總管]。
6. 在 [編輯] 功能表上，選取 [目標 Azure Stack]。
7. 選取 [新增帳戶]，然後選取 [使用儲存體帳戶名稱和金鑰]。

   ![連接儲存體](media/azure-stack-integrate-security/connect-storage.png)

8. 按一下 [下一步] 。
9. 在 [連結外部儲存體] 頁面上：

   a. 輸入帳戶名稱 **frphealthaccount**。

   b. 貼上儲存體帳戶存取金鑰。

   c. 在 [儲存體端點網域] 下，選取 [其他]，然後指定儲存體端點 **[區域].[網域名稱]**。

   d. 選取 [使用 HTTP] 核取方塊。

   ![連結外部儲存體](media/azure-stack-integrate-security/attach-storage.png)

10. 按 [下一步]、檢閱摘要，然後 [完成] 精靈。
11. 您現在可以瀏覽個別的 Blob 容器，並下載事件。

   ![瀏覽 Blob](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>使用程式設計語言來存取事件

您可以使用各種程式設計語言來存取儲存體帳戶。 請使用下列文件挑選符合您語言的範例：

[https://azure.microsoft.com/resources/samples/?term=storage+account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>裝置存取稽核

Azure Stack 中的所有實體裝置都支援使用 TACACS 或 RADIUS。 這包括存取基礎板管理控制器 (BMC) 和網路交換器。

Azure Stack 解決方案在推出時並未內建 RADIUS 或 TACACS。 不過，這些解決方案已通過驗證，可支援使用市面上現有的 RADIUS 或 TACACS 解決方案。

至於 RADIUS，則僅有 MSCHAPv2 通過驗證。 這表示使用 RADIUS 的最安全實作。
請洽詢您的 OEM 硬體廠商，以在 Azure Stack 解決方案所包含的裝置中啟用 TACAS 或 RADIUS。

## <a name="syslog"></a>syslog

Azure Stack 中的所有實體裝置都可以傳送 Syslog 訊息。 Azure Stack 解決方案並未隨附 Syslog 伺服器。 不過，這些解決方案已通過驗證，可支援將訊息傳送到市面上現有的 Syslog 解決方案。

Syslog 目的地位址是針對部署所收集的選擇性參數，但您也可以在部署後才新增此參數。

## <a name="next-steps"></a>後續步驟

[Azure Stack 資料中心整合 - 發佈端點](azure-stack-integrate-endpoints.md)
