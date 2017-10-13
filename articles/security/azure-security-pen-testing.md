---
title: "滲透測試 | Microsoft Docs"
description: "本文提供滲透測試 (pentest) 程序和如何對 Azure 基礎結構中執行的應用程式執行滲透測試的概觀。"
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: yurid
ms.openlocfilehash: 070e848f753452953b9e5dfe94799e7c0a314530
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="pen-testing"></a>滲透測試
使用 Microsoft Azure 進行應用程式測試和部署最大的好處之一是，您不需要將內部部署基礎結構放在一起，即可開發、測試及部署應用程式。 所有基礎結構都是由 Microsoft Azure 平台服務處理。 您無需購置、取得和組裝自己的內部部署硬體。

這很好 - 但您仍然需要確定您審慎執行一般的安全性作業。 您所要做的事情之一是對您在 Azure 中部署的應用程式進行滲透測試。

您可能已經知道 Microsoft 會執行 [我們的 Azure 環境的滲透測試](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)。 這有助於改善我們的平台，並在改善安全性控制、推出新的安全性控制及改善我們的安全性程序方面引導我們的行動。

我們不會為您對應用程式進行滲透測試，但我們了解，您會想要並需要對自己的應用程式執行滲透測試。 那是件好事，因為當您增強應用程式的安全性時，便有助於讓整個 Azure 生態系統更加安全。

當您對您的應用程式進行滲透測試時，它對我們來說可能像是攻擊。 我們會 [持續監視](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx) 攻擊模式，並且在需要時會起始事件回應程序。 如果我們由於您自己的審慎滲透測試而觸發事件回應，對您和我們都沒有幫助。

怎麼辦？

準備要對您的 Azure 代管應用程式進行滲透測試時，您可以選擇[讓我們知道](https://portal.msrc.microsoft.com/en-us/engage/pentest)。 一旦我們知道您要執行特定測試，我們將不會不小心將您關閉 (例如封鎖您進行測試的來源 IP 位址)，只要測試符合如 [Microsoft 雲端整合滲透測試的參與規則](https://technet.microsoft.com/en-us/mt784683)中所述的 Azure 滲透測試條款和條件即可。
您可以執行的標準測試包括：

* 對端點進行測試，以發掘 [開放式 Web 應用程式安全性專案 (OWASP) 的前 10 大弱點](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [模糊測試](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) 
* [連接埠掃描](https://en.wikipedia.org/wiki/Port_scanner) 

您不能執行的一種測試是任何種類的 [拒絕服務 (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) 攻擊。 這包括起始 DoS 攻擊本身，或是執行可能會決定、示範或模擬任何類型的 DoS 攻擊的相關測試。

您是否已準備好要開始對主控於 Microsoft Azure 的您的應用程式進行滲透測試？ 如果是，請前往 [滲透測試概觀](https://technet.microsoft.com/library/mt784683.aspx) 頁面，然後按一下頁面底部的 [建立測試要求] 按鈕。 您也可以在條款與條件中找到有關滲透測試的詳細資訊，以及如何報告與 Azure 或任何其他 Microsoft 服務的安全性問題的相關實用連結。
