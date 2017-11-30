---
title: "在 Azure Active Directory 中設定 Cloud App Discovery 服務 | Microsoft Docs"
description: "使用 Cloud App Discovery 來尋找並管理應用程式，以便提供雲端使用和影子 IT 的可操作資訊。"
services: active-directory
keywords: "雲端應用程式探索, 管理應用程式"
documentationcenter: 
author: curtand
manager: femila
tags: ignite
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: curtand
ms.reviewer: nigu
ms.openlocfilehash: 92cafe24fc3a038f2acd68ec21ec845316ef46de
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2017
---
# <a name="set-up-cloud-app-discovery-in-azure-ad"></a>在 Azure AD 中設定 Cloud App Discovery

Azure AD 中的 Cloud App Discovery 現在會以 Microsoft Cloud App Security 中可用資料的整合為基礎。 為持續提供雲端使用和陰影 IT 的資訊，Cloud App Discovery 會比較您的流量記錄與超過 15,000 個雲端應用程式的 Cloud App Security 目錄。 本文會描述安裝程序，並包含每個步驟的詳細資訊連結。 也會說明防火牆和 Proxy 資訊，以及記錄檔支援。

## <a name="prerequisites"></a>必要條件

* 您的組織必須有 Azure AD Premium P1 授權才能使用本產品。 如需詳細資訊，請參閱 [Azure Active Directory 價格](https://azure.microsoft.com/pricing/details/active-directory/)。
* 若要設定 Cloud App Discovery，您必須是 Azure Active Directory 中的全域管理員或安全性讀取者。

## <a name="setup-steps"></a>設定步驟

1. [設定快照集報表](cloudappdiscovery-set-up-snapshots.md)來檢查記錄格式，確定記錄向 Cloud App Discovery 提供有用的資訊。 它們也可對您從防火牆和 Proxy 伺服器手動上傳的流量記錄，提供特定可見性。

2. [設定連續報告](https://docs.microsoft.com/cloud-app-security/discovery-docker)會分析使用 Cloud App Security 記錄收集器從您的網路轉送的所有記錄。 您可以使用這類報告來識別新的應用程式和使用趨勢。

3. 如果目前不支援記錄，請[設定自訂記錄剖析器](https://docs.microsoft.com/cloud-app-security/custom-log-parser)，讓 Cloud App Discovery 可以分析它們。
  
## <a name="log-processing-flow"></a>記錄處理流程

視資料量而定，產生報告可能需要數分鐘到數小時的時間不等。 以下是分析的內容：

* **上傳** 您網路的 Web 流量記錄會上傳至入口網站。
* **剖析** Cloud App Security 會使用每個資料來源的專用剖析器，剖析並擷取來自流量記錄的資料。
* **分析** 針對 Cloud App Security 目錄分析流量資料，以識別超過 15,000 個雲端應用程式。 在分析過程中也會識別作用中的使用者和 IP 位址。
* **產生報告** Cloud App Security 會針對從記錄檔擷取的資料產生報告，並將報告提供給 Cloud App Discovery 使用。

> [!NOTE]
> * 系統會一天分析兩次連續報告資料。
> * 記錄收集器會在上傳資料前壓縮資料。 記錄收集器的輸出流量大約為已接收流量記錄大小的 10%。

## <a name="using-traffic-logs-for-cloud-app-discovery"></a>使用 Cloud App discovery 流量記錄

Cloud App Discovery 會使用您流量記錄中的資料。 您可以在記錄中新增的詳細資料愈多，就可取得更好的可見性。 Cloud App Discovery 需要具有下列屬性的 Web 流量資料：

* 交易日期
* 來源 IP 位址
* **建議**的來源使用者
* 目的地 IP 位址
* **建議**的目的地 URL (URL 會提供比 IP 位址更精確的雲端應用程式偵測)
* 資料量總計
* 已上傳或下載的資料數量，可供深入了解雲端應用程式使用量模式
* 採取的動作 (允許/封鎖)

Cloud App Discovery 無法顯示，或分析記錄中未包含的屬性。 例如，**Cisco ASA 防火牆**標準記錄格式不包含 [每項交易已上傳的位元組數量]、[使用者名稱] 或 [目標 URL]，只包含目的地 IP 位址。 因此，您可能比較無法經由此資料來源了解雲端應用程式。 針對 Cisco ASA 防火牆，將資訊層級設定為 6.1。

為了成功產生 Cloud App Discovery 報告，您的流量記錄必須符合下列條件:

1.  資料來源是[支援的防火牆或 Proxy 伺服器](#supported-firewalls-and-proxies)。
2.  記錄格式符合預期的標準格式。 這會在上傳階段檢查。 若要將記錄格式最佳化，請參閱[建立 Cloud App Discovery 快照集報告](cloudappdiscovery-set-up-snapshots.md)。
3.  事件不超過 90 天。
4.  記錄檔有效，且包含輸出流量資訊。

## <a name="supported-firewalls-and-proxy-servers"></a>支援的防火牆和 Proxy 伺服器

* Barracuda - Web 應用程式防火牆 (W3C)
* Blue Coat Proxy SG - 存取記錄 (W3C)
* Check Point
* Cisco ASA 防火牆 (針對 Cisco ASA 防火牆，將資訊層級設定為 6)
* Cisco IronPort WSA
* Cisco ScanSafe
* Cisco Meraki – URL 記錄
* Clavister NGFW (Syslog)
* Dell Sonicwall
* Fortinet Fortigate
* Juniper SRX
* Juniper SSG
* McAfee Secure Web Gateway
* Microsoft Forefront Threat Management Gateway (W3C)
* Palo Alto 系列防火牆
* Sophos SG
* Sophos Cyberoam
* Squid (一般)
* Squid (原生)
* Websense - Web Security Solutions - 調查詳細報告 (CSV)
* Websense - Web Security Solutions - 網際網路活動記錄 (CEF)
* Zscaler

> [!NOTE]
> Cloud App Discovery 同時支援 IPv4 和 IPv6 位址。

如果不支援您的記錄，請選取 [其他] 作為 [資料來源]，並指定您嘗試上傳的裝置和記錄。 Cloud App Security 雲端分析師小組會檢閱您的記錄。 新增您的記錄類型支援時，我們會通知您，但是您可以定義符合您的記錄格式的自訂剖析器。 如需詳細資訊，請參閱[使用自訂記錄剖析器](https://docs.microsoft.com/cloud-app-security/custom-log-parser)。

## <a name="data-attributes-according-to-vendor-documentation"></a>資料屬性 (根據廠商說明文件)

| 資料來源         | 目標應用程式 URL | 目標應用程式 IP 位址 | 使用者名稱 | 來源 IP 位址 | 總流量 | 上傳的位元組 |
|-----------------------------------------|----------------|---------------|----------|-----------|---------------|----------------|
| Barracuda                               | **是**        | **是**       | **是**  | **是**   | 否            | 否             |
| Blue Coat                               | **是**        | 否            | **是**  | **是**   | **是**       | **是**        |
| Checkpoint                              | 否             | **是**       | 否       | **是**   | 否            | 否             |
| Cisco ASA                               | 否             | **是**       | 否       | **是**   | **是**       | 否             |
| Cisco FWSM                              | 否             | **是**       | 否       | **是**   | **是**       | 否             |
| Cisco Ironport WSA                      | **是**        | **是**       | **是**  | **是**   | **是**       | **是**        |
| Cisco Meraki                            | **是**        | **是**       | 否       | **是**   | 否            | 否             |
| Clavister NGFW (Syslog)                 | **是**        | **是**       | **是**  | **是**   | **是**       | **是**        |
| Dell SonicWall                          | **是**        | **是**       | 否       | **是**   | **是**       | **是**        |
| Fortigate                               | 否             | **是**       | 否       | **是**   | **是**       | **是**        |
| Juniper SRX                             | 否             | **是**       | 否       | **是**   | **是**       | **是**        |
| Juniper SSG                             | 否             | **是**       | 否       | **是**   | **是**       | **是**        |
| McAfee SWG                              | **是**        | 否            | 否       | **是**   | **是**       | **是**        |
| MS TMG                                  | **是**        | 否            | **是**  | **是**   | **是**       | **是**        |
| Palo Alto Networks                      | **是**        | **是**       | **是**  | **是**   | **是**       | **是**        |
| Sophos                                  | **是**        | **是**       | **是**  | **是**   | **是**       | 否             |
| Squid (一般)                          | **是**        | 否            | **是**  | **是**   | 否            | **是**        |
| Squid (原生)                          | **是**        | 否            | **是**  | **是**   | 否            | **是**        |
| Websense - 調查報 (CSV)   | **是**        | **是**       | **是**  | **是**   | **是**       | **是**        |
| Websense - 網際網路活動記錄 (CEF)  | **是**        | **是**       | **是**  | **是**   | **是**       | **是**        |
| Zscaler                                 | **是**        | **是**       | **是**  | **是**   | **是**       | **是**        |


## <a name="next-steps"></a>後續步驟
使用下列連結繼續在 Azure AD 中設定 Cloud App Discovery。

* [建立快照集報告](cloudappdiscovery-set-up-snapshots.md)
* [設定連續報告](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [使用自訂記錄剖析器](https://docs.microsoft.comcommit/cloud-app-security/custom-log-parser)
