Azure 會提供下列 VPN 閘道 SKU：

|**SKU**   | **S2S/VNet-to-VNet<br>通道** | **P2S<br>連線** | **彙總<br>輸送量基準測試** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| 最大 30                         | 最大 128               | 650 Mbps                    |
|**VpnGw2**| 最大 30                         | 最大 128               | 1 Gbps                      |
|**VpnGw3**| 最大 30                         | 最大 128               | 1.25 Gbps                   |
|**基本** | 最大 10                         | 最大 128               | 100 Mbps                    | 
|          |                                 |                        |                             | 

- 「彙總輸送量基準測試」是以透過單一閘道彙總之多個通道的量值為基礎。 由於網際網路流量條件和您的應用程式行為，這不是保證的輸送量。

- 在 [價格](https://azure.microsoft.com/pricing/details/vpn-gateway) 頁面上可以找到價格資訊。

- 可以在 [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) 頁面上找到 SLA (服務等級協定) 資訊。