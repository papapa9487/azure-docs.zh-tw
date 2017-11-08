---
title: "部署遠端監視解決方案架構 - Azure | Microsoft Docs"
description: "本教學課程示範如何從 azureiotsuite.com 佈建遠端監視預先設定的解決方案。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 08/09/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: babcf20b58af1415e0e658e0a622cb056e34642b
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-the-remote-monitoring-preconfigured-solution"></a>部署遠端監視預先設定解決方案

本教學課程示範如何佈建遠端監視預先設定的解決方案。 您要從 azureiotsuite.com 部署解決方案。您也可以使用 CLI 部署解決方案來了解此選項，請參閱[從命令列部署預先設定的解決方案](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploy-a-pcs-from-the-command-line)。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 設定預先設定的解決方案
> * 部署預先設定的解決方案
> * 登入預先設定的解決方案

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要一個有效的 Azure 訂用帳戶。

如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。

## <a name="deploy-the-preconfigured-solution"></a>部署預先設定的解決方案

您必須選擇一些設定選項，才能將預先設定的解決方案部署到 Azure 訂用帳戶：

1. 使用 Azure 帳戶認證登入 [azureiotsuite.com](https://www.azureiotsuite.com)，然後按一下 [+] 以建立解決方案。

1. 按一下 [遠端監視] 圖格上的 [選取]。

1. 在 [建立遠端監視解決方案] 頁面上，輸入遠端監視預先設定的解決方案的 [解決方案名稱]。

1. 選取**基本**或**企業**部署。 如果您要部署解決方案來了解其運作方式或是執行示範，請選擇 [基本] 選項來將成本降至最低。

1. 選擇 **Java** 或 **.NET** 作為語言。 所有的微服務都可供作為 Java 或 .NET 的實作。

1. 如需有關設定選擇的詳細資訊，請檢閱 [解決方案詳細資料] 面板。

1. 選取您要用來佈建解決方案的 [訂用帳戶] 和 [區域]。

1. 按一下 [建立解決方案]  開始佈建程序。 此程序通常需要數分鐘的執行時間。

如需疑難排解資訊，請參閱 GitHub 存放庫中的[部署失敗時該怎麼辦](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide#what-to-do-when-a-deployment-fails)。

## <a name="sign-in-to-the-preconfigured-solution"></a>登入預先設定的解決方案

佈建程序完成時，您可以登入遠端監視預先設定解決方案。

1. 在 [佈建解決方案] 頁面上，選擇您新的遠端監視解決方案。

1. 您可以檢視有關所出現之面板中的遠端監視解決方案的資訊。 選擇 [解決方案儀表板] 以連線到遠端監視解決方案。

    > [!NOTE]
    > 當您完成時，可以從此面板刪除遠端監視解決方案。

1. 遠端監視解決方案儀表板會顯示在您的瀏覽器中。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 設定預先設定的解決方案
> * 部署預先設定的解決方案
> * 登入預先設定的解決方案

既然您已部署遠端監視解決方案，下一步便是[探索解決方案儀表板的功能](./iot-suite-remote-monitoring-explore.md)。

<!-- Next tutorials in the sequence -->