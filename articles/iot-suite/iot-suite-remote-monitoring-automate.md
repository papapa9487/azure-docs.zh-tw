---
title: "偵測遠端監視解決方案中的裝置問題 - Azure | Microsoft Docs"
description: "本教學課程將說明如何使用規則和動作，來自動偵測遠端監視解決方案中的臨界值型裝置問題。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 11/10/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 173ffbdd70313ef5a0d2af2cf1c8996d2395274a
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2017
---
# <a name="detect-issues-using-threshold-based-rules"></a>使用臨界值型規則來偵測問題

本教學課程將說明遠端監視解決方案中的規則引擎功能。 為了介紹這些功能，本教學課程會使用 Contoso IoT 應用程式中的情節。

Contoso 有一個會在 **Chiller** 裝置回報的壓力超出 250 PSI 時產生重大警示的規則。 身為操作員，您會想要尋找初始壓力尖峰，以識別可能有感應器發生故障的 **Chiller** 裝置。 為了識別這些裝置，您會建立一個在壓力超出 150 PSI 時產生警告的規則。

在本教學課程中，您將了解如何：

>[!div class="checklist"]
> * 檢視您解決方案中的規則
> * 建立新的規則
> * 編輯現有的規則
> * 刪除規則

## <a name="prerequisites"></a>必要條件

若要依循本教學課程進行操作，您需要在 Azure 訂用帳戶中有一個已部署的遠端監視解決方案執行個體。

如果您尚未部署遠端監視解決方案，應該先完成[部署遠端監視預先設定的解決方案](iot-suite-remote-monitoring-deploy.md)教學課程。

## <a name="view-the-rules-in-your-solution"></a>檢視您解決方案中的規則

解決方案中的 [規則和動作] 頁面會顯示所有目前規則的清單：

![[規則和動作] 頁面](media/iot-suite-remote-monitoring-automate/rulesactions.png)

若只要檢視適用於 **Chiller** 裝置的規則，請套用篩選條件：

![篩選規則清單](media/iot-suite-remote-monitoring-automate/rulesactionsfilter.png)

當您從清單中選取某個規則時，即可檢視該規則的詳細資訊並進行編輯：

![檢視規則詳細資料](media/iot-suite-remote-monitoring-automate/rulesactionsdetail.png)

若要停用、啟用或刪除一或多個規則，請選從清單中選取多個規則：

![選取多個規則](media/iot-suite-remote-monitoring-automate/rulesactionsmultiselect.png)

## <a name="create-a-new-rule"></a>建立新的規則

若要新增一個在 **Chiller** 裝置中壓力超出 150 PSI 時產生重大警告的規則，請選擇 [新增規則]：

![建立規則](media/iot-suite-remote-monitoring-automate/rulesactionsnewrule.png)

使用下列值來建立規則：

| 設定          | 值                                 |
| ---------------- | ------------------------------------- |
| 名稱             | Chiller 警告                       |
| 來源           | **Chiller** 裝置群組              |
| 觸發欄位    | 壓力                              |
| 觸發運算子 | 大於                          |
| 觸發值    | 150                                   |
| 嚴重性層級   | 警告                               |
| 警示事件文字 | Chiller 壓力超出 150 PSI |

若要儲存新規則，請選擇 [套用]。

您可以在 [規則和動作] 頁面或在 [儀表板] 頁面上，檢視規則的觸發時間。

## <a name="edit-an-existing-rule"></a>編輯現有的規則

若要對現有的規則進行變更，請從規則清單中選取該規則。 然後，在 [規則詳細資料]**Rule Detail** 面板中，選擇 [編輯模式]。

![編輯規則](media/iot-suite-remote-monitoring-automate/rulesactionsedit.png)

## <a name="disable-a-rule"></a>停用規則

若要暫時關閉某個規則，您可以在規則清單中將它停用。 選擇要停用的規則，然後選擇 [停用]。 清單中該規則的 [狀態] 會變更以指出規則現在已停用。 您可以使用相同的程序來重新啟用先前停用的規則。

![停用規則](media/iot-suite-remote-monitoring-automate/rulesactionsdisable.png)

如果您從清單中選取多個規則，便可以同時啟用和停用多個規則。

## <a name="delete-a-rule"></a>刪除規則

若要永久刪除某個規則，請從規則清單中選取該規則，然後選擇 [刪除]。

如果您從清單中選取多個規則，便可以同時刪除多個規則。

## <a name="next-steps"></a>後續步驟

本教學課程已示範如何：

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 檢視您解決方案中的規則
> * 建立新的規則
> * 編輯現有的規則
> * 刪除規則

既然您已了解如何使用臨界值型規則來偵測問題，建議接下來的步驟是了解如何：

* [管理及設定您的裝置](./iot-suite-remote-monitoring-manage.md)。
* [針對裝置問題進行疑難排解和補救](./iot-suite-remote-monitoring-maintain.md)。
* [使用模擬裝置來測試您的解決方案](iot-suite-remote-monitoring-test.md)。

<!-- Next tutorials in the sequence -->