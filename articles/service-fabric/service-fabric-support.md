---
title: "了解 Azure Service Fabric 支援選項 | Microsoft Docs"
description: "支援的 Azure Service Fabric 叢集版本和檔案支援票證連結"
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/12/2017
ms.author: pkc
ms.openlocfilehash: 7bc0f70e99be04cd646c0b7c610f7c6e4a74dfc2
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2017
---
# <a name="azure-service-fabric-support-options"></a>Azure Service Fabric 支援選項

為了在您執行應用程式工作負載所在的 Service Fabric 叢集傳遞適當的支援，我們已經為您設定各種選項。 依據所需的支援層級和問題的嚴重性而定，您必須挑選正確的選項。 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>報告實際執行問題，或要求 Azure 的付費支援

對於報告 Azure 上部署之 Service Fabric 叢集的問題，在 [Azure 入口網站](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)或 [Microsoft 支援入口網站](http://support.microsoft.com/oas/default.aspx?prid=16146)上建立支援票證。

深入了解：
 
- [Microsoft 對於 Azure 的支援](https://azure.microsoft.com/en-us/support/plans/?b=16.44)。
- [Microsoft 頂級支援](https://support.microsoft.com/en-us/premier)。

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>報告實際執行問題，或要求獨立 Service Fabric 叢集的付費支援

對於報告內部部署或其他雲端上部署之 Service Fabric 叢集的問題，在 [Microsoft 支援入口網站](http://support.microsoft.com/oas/default.aspx?prid=16146)上建立專業支援票證。

深入了解：

- [Microsoft 對於內部部署的專業支援](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)。
- [Microsoft 頂級支援](https://support.microsoft.com/en-us/premier)。

## <a name="report-azure-service-fabric-issues"></a>報告 Azure Service Fabric 問題 
我們已設定 GitHub 存放庫以報告 Service Fabric 問題。  我們也會主動監視下列論壇。

### <a name="github-repo"></a>GitHub 存放庫 
在 [Service-Fabric-issues git 存放庫](https://github.com/Azure/service-fabric-issues)上報告 Azure Service Fabric 問題。 此存放庫適用於報告和追蹤 Azure Service Fabric 的問題，並進行小規模的功能要求。 **請勿使用此存放庫來報告即時網站問題**。

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow 和 MSDN 論壇
[StackOverflow 上的 Service Fabric 標籤][stackoverflow]和 [MSDN 上的 Service Fabric 論壇][msdn-forum]最適合用來詢問關於平台如何運作以及您如何使用它來完成特定工作的問題。

### <a name="azure-feedback-forum"></a>Azure 意見反應論壇
[Service Fabric 的 Azure 意見反應論壇][uservoice-forum]是您用來提交對於產品的大功能構想的最佳位置，因為檢閱最受歡迎的要求是我們中程至長程規劃的一部分。 我們希望您在社群中集結您的建議的支援。


## <a name="supported-service-fabric-versions"></a>支援的 Service Fabric 版本。

請確定您的叢集一律執行支援的 Service Fabric 版本。 當我們宣布發行新版本的 Service Fabric 時，從當日起至少 60 天後，舊版就會標示為結束支援。 新的版本會於 [Service Fabric 小組部落格上](https://blogs.msdn.microsoft.com/azureservicefabric/)發佈。

請詳閱下列文件，以了解如何保持您的叢集執行支援的 Service Fabric 版本。

- [在 Azure 叢集上升級 Service Fabric 版本](service-fabric-cluster-upgrade.md)
- [在獨立 Windows 伺服器叢集上升級 Service Fabric 版本](service-fabric-cluster-upgrade-windows-server.md)
 
以下是支援的 Service Fabric 版本清單以及其支援結束日期。

| **Service Fabric 執行階段叢集** | **相容的 SDK / NuGet 套件版本** | **結束支援日期** |
| --- | --- | --- |
| 5.3.121 之前的所有叢集版本 |小於或等於 2.3 版本 |2017 年 1 月 20 日 |
| 5.3.* |小於或等於 2.3 版本 |2017 年 2 月 24 日 |
| 5.4.* |小於或等於 2.4 版本 |2017 年 5 月 10 日       |
| 5.5.* |小於或等於 2.5 版本 |2017 年 8 月 10 日    |
| 5.6.* |小於或等於 2.6 版本 |2017 年 10 月 13 日   |
| 5.7.* |小於或等於 2.7 版 |2017 年 12 月 15 日  |
| 6.0.* |小於或等於 2.8 版 |目前版本，沒有結束日期

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric 預覽版本 - 不支援用於生產環境。
有時候，我們會以預覽形式發行包含我們想得到意見反應之重要功能的版本。 這些預覽版本只能用於測試目的。 您的生產叢集應一律執行支援的穩定 Service Fabric 版本。 預覽版本一律從 255 作為主要和次要版本號碼的開頭。 例如，如果您看到 Service Fabric 版本 255.255.5703.949，該發行版本只能使用於測試叢集並處於預覽狀態。 這些預覽版本也會在 [Service Fabric 小組部落格](https://blogs.msdn.microsoft.com/azureservicefabric)上宣告，並且有內含功能的詳細資料。

這些預覽版本沒有付費的支援選項。 使用[報告 Azure Service Fabric 問題](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues)底下所列的其中一個選項來提出問題或提供意見反應。

## <a name="next-steps"></a>後續步驟

- [在 Azure 叢集上升級 Service Fabric 版本](service-fabric-cluster-upgrade.md)
- [在獨立 Windows 伺服器叢集上升級 Service Fabric 版本](service-fabric-cluster-upgrade-windows-server.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples
