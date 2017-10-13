---
title: "使用 Site Recovery 針對 VMware 到 Azure 的複寫規劃網路服務 | Microsoft Docs"
description: "本文討論使用 Azure Site Recovery 將 Azure VM 複寫至 Azure 時所需的網路規劃"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/01/2017
ms.author: sujayt
ms.openlocfilehash: 7b37e853f6b97ba313111f9201f877846a28fae9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="step-3-plan-networking-for-azure-vm-replication"></a>步驟 3：規劃 Azure VM 複寫的網路服務

在驗證[部署必要條件](azure-to-azure-walkthrough-prerequisites.md)之後，請閱讀本文以了解使用 Azure Site Recovery 服務，從一個 Azure 區域將 Azure 虛擬機器 (VM) 複寫及復原到另一個 Azure 區域時的網路服務考量。 

- 當您完成本文時，應該清楚了解如何設定您要複寫的 Azure VM 之輸出存取，以及如何從您的內部部署網站連線到這些 VM。
- 請在本文下方張貼意見，或在 [Azure 復原服務論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中提出問題。

>[!NOTE]
> Site Recovery 的 Azure VM 複寫目前為預覽狀態。



## <a name="network-overview"></a>網路概觀

通常您的 Azure VM 是位於 Azure 虛擬網路/子網路，且會使用 Azure ExpressRoute 或 VPN 連線，從您的內部部署網站連線至 Azure。

通常會使用防火牆和/或網路安全性群組 (NSG) 來保護網路。 防火牆可使用以 IP 為基礎的清單作為基礎的 URL，來控制網路連線能力。 NSG 會使用以 IP 範圍為基礎的規則。 


若要 Site Recovery 如預期運作，必須從您需要複寫的 VM 對輸出網路連線能力進行一些變更。 Site Recovery 不支援使用驗證 Proxy 來控制網路連線能力。 如果您有驗證 Proxy，就無法啟用複寫。 


下圖說明適用於 Azure VM 上執行之應用程式的一般環境。

![客戶環境](./media/azure-to-azure-walkthrough-network/source-environment.png)

**Azure VM 環境**

您可能也需要使用 Azure ExpressRoute 或 VPN 連線，才能設定從您內部部署網站到 Azure 的連線。 

![客戶環境](./media/azure-to-azure-walkthrough-network/source-environment-expressroute.png)

**內部部署連線至 Azure**



## <a name="outbound-connectivity-for-urls"></a>URL 的輸出連線能力

如果您要使用以 URL 為基礎的防火牆 Proxy 來控制輸出連線能力，請確定您允許這些 Site Recovery 所使用的 URL

**URL** | **詳細資料**  
--- | ---
*.blob.core.windows.net | 允許將資料從 VM 寫入來源地區的快取儲存體帳戶中。
login.microsoftonline.com | 提供 Site Recovery 服務 URL 的授權和驗證。
*.hypervrecoverymanager.windowsazure.com | 允許從 VM 與 Site Recovery 服務進行通訊。
*.servicebus.windows.net | 需要此項目方可從 VM 寫入 Site Recovery 監視和診斷資料。

## <a name="outbound-connectivity-for-ip-address-ranges"></a>IP 位址範圍的輸出連線能力

- 您可以自動建立 NSG 的必要規則，方法是下載並執行[此指令碼](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702)。
- 建議您建立測試 NSG 的必要 NSG 規則，並確認沒有問題後，再建立生產環境 NSG 的規則。
- 若要建立所需的 NSG 規則數目，請確定您的訂用帳戶位於允許清單中。 請連絡支援人員，以提高您訂用帳戶中的 NSG 規則限制。
如果您要使用任何 IP 型防火牆 Proxy 或 NSG 規則控制輸出連線能力，根據虛擬機器的來源和目標位置，下列的 IP 範圍需要在允許清單中：

    - 對應於來源位置的所有 IP 位址範圍。 下載[範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)。)需要位於允許清單中，才能從 VM 將資料寫入快取儲存體帳戶。
    - 對應於 Office 365 [驗證與身分識別 IP V4 端點](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)的所有 IP 範圍。 如果將新的 IP 新增至 Office 365 IP 範圍，您就必須建立新的 NSG 規則。
-     Site Recovery 服務端點 IP 位址 ([XML 檔案中提供](https://aka.ms/site-recovery-public-ips))，取決於您的目標位置： 

   **目標位置** | **Site Recovery 服務 IP** |  **Site Recovery 監視 IP**
   --- | --- | ---
   東亞 | 52.175.17.132</br>40.83.121.61 | 13.94.47.61
   東南亞 | 52.187.58.193</br>52.187.169.104 | 13.76.179.223
   印度中部 | 52.172.187.37</br>52.172.157.193 | 104.211.98.185
   印度南部 | 52.172.46.220</br>52.172.13.124 | 104.211.224.190
   美國中北部 | 23.96.195.247</br>23.96.217.22 | 168.62.249.226
   北歐 | 40.69.212.238</br>13.74.36.46 | 52.169.18.8
   西歐 | 52.166.13.64</br>52.166.6.245 | 40.68.93.145
   美國東部 | 13.82.88.226</br>40.71.38.173 | 104.45.147.24
   美國西部 | 40.83.179.48</br>13.91.45.163 | 104.40.26.199
   美國中南部 | 13.84.148.14</br>13.84.172.239 | 104.210.146.250
   美國中部 | 40.69.144.231</br>40.69.167.116 | 52.165.34.144
   美國東部 2 | 52.184.158.163</br>52.225.216.31 | 40.79.44.59
   日本東部 | 52.185.150.140</br>13.78.87.185 | 138.91.1.105
   日本西部 | 52.175.146.69</br>52.175.145.200 | 138.91.17.38
   巴西南部 | 191.234.185.172</br>104.41.62.15 | 23.97.97.36
   澳洲東部 | 104.210.113.114</br>40.126.226.199 | 191.239.64.144
   澳大利亞東南部 | 13.70.159.158</br>13.73.114.68 | 191.239.160.45
   加拿大中部 | 52.228.36.192</br>52.228.39.52 | 40.85.226.62
   加拿大東部 | 52.229.125.98</br>52.229.126.170 | 40.86.225.142
   美國中西部 | 52.161.20.168</br>13.78.230.131 | 13.78.149.209
   美國西部 2 | 52.183.45.166</br>52.175.207.234 | 13.66.228.204
   英國西部 | 51.141.3.203</br>51.140.226.176 | 51.141.14.113
   英國南部 | 51.140.43.158</br>51.140.29.146 | 51.140.189.52

## <a name="example-nsg-configuration"></a>範例 NSG 設定

本節會說明如何設定 NSG 規則，以便複寫可適用於 VM。 如果您要使用 NSG 規則控制輸出連線能力，請對於全部所需 IP 範圍使用「允許 HTTPS 輸出」規則。

在此範例中，VM 來源位置為「美國東部」。 複寫目標位置是美國中部。


### <a name="example"></a>範例

#### <a name="east-us"></a>美國東部

1. 建立對應於[美國東部 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)的規則。 需要此項目方可從 VM 將資料寫入快取儲存體帳戶中。
2. 針對對應於 Office 365 [驗證和身分識別 IP V4 端點](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)的所有 IP 範圍建立規則。
3. 建立對應於目標位置的規則：

   **位置** | **Site Recovery 服務 IP** |  **Site Recovery 監視 IP**
    --- | --- | ---
   美國中部 | 40.69.144.231</br>40.69.167.116 | 52.165.34.144

#### <a name="central-us"></a>美國中部

需要這些規則，才能在容錯移轉後，啟用從目標區域到來源地區的複寫。

1. 建立對應於[美國中部 IP 範圍](https://www.microsoft.com/download/confirmation.aspx?id=41653)的規則。
2. 針對對應於 Office 365 [驗證和身分識別 IP V4 端點](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)的所有 IP 範圍建立規則。
3. 建立對應於來源位置的規則：

   **位置** | **Site Recovery 服務 IP** |  **Site Recovery 監視 IP**
    --- | --- | ---
   美國東部 | 13.82.88.226</br>40.71.38.173 | 104.45.147.24


## <a name="existing-on-premises-connection"></a>現有的內部部署連線

如果您在 Azure 中的內部部署網站與來源位置之間有 ExpressRoute 或 VPN 連線，請遵循下列指導方針：

**組態** | **詳細資料**
--- | ---
**強制通道** | 通常預設路由 (0.0.0.0/0) 會強制輸出網際網路流量流經內部部署位置。 不建議這樣做。 複寫流量及 Site Recovery 通訊應該留在 Azure 中。<br/><br/> 作為解決方案，請新增[這些 IP 範圍](#outbound-connectivity-for-azure-site-recovery-ip-ranges)的使用者定義路由 (UDR)，以讓流量不會在內部部署傳送。
**連線能力** | 如果應用程式需要連線到內部部署機器，或是內部部署用戶端透過 VPN/ExpressRoute 內部部署連線到應用程式，請確定目標 Azure 區域與內部部署網站之間至少有[站對站連線](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)。<br/><br/> 如果目標 Azure 區域與內部部署網站之間的流量過高，請在目標區域與內部部署之間建立另一個 [ExpressRoute 連線](../expressroute/expressroute-introduction.md)。<br/><br/> 如果您需要對於容錯移轉後的 VM 保留 IP，請將目標區域的站對站/ExpressRoute 連線維持在中斷連線的狀態。 這樣可確保來源和目標 IP 位址範圍之間沒有範圍衝突。
**ExpressRoute** | 在來源和目標區域中建立 ExpressRoute 線路。<br/><br/> 建立來源網路與 ExpressRoute 線路之間的連線，以及目標網路與線路之間的連線。<br/><br/> 建議您在來源和目標區域中使用不同的 IP 範圍。 線路無法同時連線到一個以上具有相同 IP 範圍的 Azure 網路。<br/><br/> 您可以在兩個區域建立相同 IP 範圍的虛擬網路，並在兩個區域中建立 ExpressRoute 線路。 針對容錯移轉，請中斷來源虛擬網路的線路，然後連線目標的虛擬網路中的線路。<br/><br/> 如果主要區域已完全關閉，中斷連線作業可能會失敗。 在此情況下，目標虛擬網路不會取得 ExpressRoute 連線能力。
**ExpressRoute Premium** | 您可以在同一個地理政治區域中建立線路。<br/><br/> 若要在不同的地理政治區域中建立線路，您需要 Azure ExpressRoute Premium。<br/><br/> 使用 Premium 時，成本就是累加的。 如果您已經在使用它，就不需要額外成本。<br/><br/> 深入了解[位置](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region)和[定價](https://azure.microsoft.com/pricing/details/expressroute/)。



## <a name="next-steps"></a>後續步驟

移至[步驟 4：建立保存庫](azure-to-azure-walkthrough-vault.md)

