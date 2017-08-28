建立虛擬網路閘道時，您必須指定想要使用的閘道 SKU。 根據工作負載、輸送量、功能和 SLA 的類型，選取符合您需求的 SKU。

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

###  <a name="workloads"></a>生產與開發測試工作負載

由於 SLA 和功能集的差異，我們建議將下列 SKU 用於產生與開發測試：

| **工作負載**                       | **SKU**               |
| ---                                | ---                    |
| **生產、重要工作負載** | VpnGw1、VpnGw2、VpnGw3 |
| **開發測試或概念證明**   | 基本                  |
|                                    |                        |

如果您使用舊式 SKU，生產 SKU 建議為基本和高效能 SKU。 如需舊式 SKU 的相關資訊，請參閱[閘道 SKU (舊版 SKU)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md)。

###  <a name="feature"></a>閘道 SKU 功能集

新式閘道 SKU 可簡化閘道上提供的功能集：

| **SKU**| **特性**|
| ---    | ---         |
|**基本**   | **路由式 VPN**：10 個通道 (含 P2S)<br><br>**原則式 VPN** (IKEv1)：1 個通道；沒有 P2S|
| **VpnGw1、VpnGw2 和 VpnGw3** | **路由式 VPN**：最多 30 個通道 (*)，P2S、BGP、主動-主動、自訂 IPsec/IKE 原則、ExpressRoute/VPN 共存 |
|        |             |

(*) 您可以設定 "PolicyBasedTrafficSelectors"，將以路由為基礎的 VPN 閘道 (VpnGw1、VpnGw2、VpnGw3) 連線至多個內部部署以原則為基礎的防火牆裝置。 如需詳細資訊，請參閱[使用 PowerShell 將 VPN 閘道連線至多個內部部署原則式 VPN 裝置](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md)。

###  <a name="resize"></a>調整閘道 SKU 的大小

1. 您可以調整 VpnGw1、VpnGw2 和 VpnGw3 SKU 之間的大小。
2. 使用舊式閘道 SKU 時，您可以在基本、標準和高效能 SKU 之間調整大小。
2. 您**無法**將大小從基本/標準/高效能 SKU 調整為新的 VpnGw1/VpnGw2/VpnGw3 SKU。 您反而必須[移轉](#migrate)至新的 SKU。

###  <a name="migrate"></a>從舊式 SKU 移轉至新的 SKU

[!INCLUDE [Migrate SKU](./vpn-gateway-migrate-legacy-sku-include.md)]
