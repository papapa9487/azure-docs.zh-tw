---
title: "Azure App Service Environment 管理位址"
description: "列出用於命令 App Service Environment 的管理位址"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: 949b445793768f593af205321bf3ab5a7aa150c0
ms.contentlocale: zh-tw
ms.lasthandoff: 07/14/2017

---
# <a name="app-service-environment-management-addresses"></a>App Service Environment 管理位址

App Service Environment (ASE) 是將 Azure App Service 部署到 Azure 虛擬網路 (VNet) 中子網路的部署作業。  ASE 必須可從 Azure App Service 存取，以便可以受到管理。  此 ASE 管理流量會周遊使用者控制的網路。  它從 Azure App Service 管理伺服器到與 ASE 建立關聯的公用 VIP。  如需 ASE 網路服務相依性的詳細資訊，請閱讀[網路考量和 App Service Environment][networking]。  如需 ASE 的一般資訊，您可以從 [App Service Environment 簡介][intro]開始。

本文件列出 ASE 管理流量的來源 IP。 您可以使用這些位址來建立網路安全性群組，以鎖定連入流量，或視需要在路由表中使用它們。  若要使用此資訊，您需要使用：

* 針對所有區域列出的 IP 位址
* 符合您部署 ASE 之目標區域的 IP 位址。

連入的管理流量會從這些 IP 位址進到連接埠 454 和 455。

| 區域 | 位址 |
|--------|-----------|
| 所有區域 | 70.37.57.58, 157.55.208.185 52.174.22.21,13.94.149.179,13.94.143.126,13.94.141.115, 52.178.195.197, 52.178.190.65, 52.178.184.149, 52.178.177.147, 13.75.127.117, 40.83.125.161, 40.83.121.56, 40.83.120.64, 52.187.56.50, 52.187.63.37, 52.187.59.251, 52.187.63.19, 52.165.158.140, 52.165.152.214, 52.165.154.193, 52.165.153.122, 104.44.129.255, 104.44.134.255, 104.44.129.243, 104.44.129.141 |
| 美國中南部和美國中北部 | 23.102.188.65, 191.236.154.88 |
| 澳大利亞東南部和澳大利亞東部 | 23.101.234.41, 104.210.90.65 |
| 美國西部和美國東部 | 104.45.227.37, 191.236.60.72 |
| 西歐和北歐 | 191.233.94.45, 191.237.222.191 |
| 美國中西部和美國西部 2 | 13.78.148.75, 13.66.225.188 |
| 美國中部和美國東部 2 | 104.43.165.73, 104.46.108.135 |
| 東亞和東南亞 | 23.99.115.5, 104.215.158.33 |
| 日本東部和日本西部 | 104.41.185.116, 191.239.104.48 |
| 加拿大中部和加拿大東部 | 40.85.230.101, 40.86.229.100 |
| 英國西部和英國南部 | 51.141.8.34, 51.140.185.75 |
| 韓國中部和韓國南部 | 52.231.200.177, 52.231.32.117 |
| 巴西南部和美國中南部| 104.41.46.178, 23.102.188.65 |
| 印度中部和印度南部 | 104.211.98.24, 104.211.225.66 |
| 印度西部和印度南部 | 104.211.160.229, 104.211.225.66 |


<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md

