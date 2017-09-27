# 概觀
## [關於 VPN 閘道](vpn-gateway-about-vpngateways.md)
## [VPN 閘道常見問題集](vpn-gateway-vpn-faq.md)
## [訂用帳戶與服務限制](../azure-subscription-service-limits.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)

# 開始使用
## [規劃與設計 VPN 閘道](vpn-gateway-plan-design.md)
## [關於 VPN 閘道設定](vpn-gateway-about-vpn-gateway-settings.md)
## [關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)
## [關於密碼編譯需求](vpn-gateway-about-compliance-crypto.md)
## [關於 BGP 和 VPN 閘道](vpn-gateway-bgp-overview.md)
## [關於高可用性連線](vpn-gateway-highlyavailable.md)
## [關於點對站連線](point-to-site-about.md)

# 作法
## 設定站對站連線
### [Azure 入口網站](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
### [Azure CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
### [Azure 入口網站 (傳統)](vpn-gateway-howto-site-to-site-classic-portal.md)
### [傳統入口網站 (傳統)](vpn-gateway-site-to-site-create.md)

## 設定點對站連線 - 原生 Azure 憑證驗證
### 設定 P2S VPN
#### [Azure 入口網站](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
#### [Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
#### [Azure 入口網站 (傳統)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
### 建立自我簽署憑證
#### [Azure PowerShell](vpn-gateway-certificates-point-to-site.md)
#### [Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
### [建立和安裝 VPN 用戶端組態檔](point-to-site-vpn-client-configuration-azure-cert.md)
### [裝用戶端憑證](point-to-site-how-to-vpn-client-install-azure-cert.md)

## 設定點對站連線 - RADIUS 驗證
### 設定 P2S VPN
#### [Azure PowerShell](point-to-site-how-to-radius-ps.md)
### [建立和安裝 VPN 用戶端組態檔](point-to-site-vpn-client-configuration-radius.md)

## 設定 VNet 對 VNet 連線
### [Azure 入口網站](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
### [Azure PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
### [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
### [Azure 入口網站 (傳統)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
## 在部署模型間設定 VNet 對 VNet 連線
### [Azure 入口網站](vpn-gateway-connect-different-deployment-models-portal.md)
### [Azure PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
## 設定站對站和 ExpressRoute 並存連線
### [Azure PowerShell](../expressroute/expressroute-howto-coexist-resource-manager.md?toc=%2fazure%2fvpn-gateway%2ftoc.json)
## 設定多個站對站連線
### [Azure 入口網站](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
### [Azure PowerShell (傳統)](vpn-gateway-multi-site.md)
## 連線多個原則型 VPN 裝置
### [Azure PowerShell](vpn-gateway-connect-multiple-policybased-rm-ps.md)
## 在連線上設定 IPsec/IKE 原則
### [Azure PowerShell](vpn-gateway-ipsecikepolicy-rm-powershell.md)
## 設定高可用性的主動-主動連線
### [Azure PowerShell](vpn-gateway-activeactive-rm-powershell.md)
## 設定適用於 VPN 閘道的 BGP
### [Azure PowerShell](vpn-gateway-bgp-resource-manager-ps.md)
## 設定強制通道
### [Azure PowerShell](vpn-gateway-forced-tunneling-rm.md)
### [Azure PowerShell (傳統)](vpn-gateway-about-forced-tunneling.md)
## 修改區域網路閘道設定
### [Azure 入口網站](vpn-gateway-modify-local-network-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-modify-local-network-gateway.md)
### [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
## [確認 VPN 閘道連線](vpn-gateway-verify-connection-resource-manager.md)
## [重設 VPN 閘道](vpn-gateway-resetgw-classic.md)
## 刪除 VPN 閘道
### [Azure 入口網站](vpn-gateway-delete-vnet-gateway-portal.md)
### [Azure PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
### [Azure PowerShell (傳統)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
## [設定 VPN 閘道 (傳統)](vpn-gateway-configure-vpn-gateway-mp.md)
## [閘道 SKU (舊版)](vpn-gateway-about-skus-legacy.md)
## 設定第三方 VPN 裝置
### [概觀和 Azure 組態](vpn-gateway-3rdparty-device-config-overview.md)
### [範例：Cisco ASA 裝置 (IKEv2/no BGP)](vpn-gateway-3rdparty-device-config-cisco-asa.md)
## 疑難排解
### [驗證 VNet 的 VPN 輸送量](vpn-gateway-validate-throughput-to-vnet.md)
### [社群建議的 VPN 或防火牆裝置設定](vpn-gateway-third-party-settings.md)
### [點對站連線問題](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
### [站對站連線會間歇性中斷](vpn-gateway-troubleshoot-site-to-site-disconnected-intermittently.md)
### [無法連線至站對站連線](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md) 

# 參考
## [Azure PowerShell](/powershell/module/azurerm.network/?view=azurermps-4.0.0#vpn)
## [Azure PowerShell (傳統)](/powershell/module/azure/?view=azuresmps-3.7.0#networking)
## [REST](/rest/api/network/virtualnetworkgateways)
## [REST (傳統)](https://msdn.microsoft.com/library/jj154113)
## [Azure CLI](/cli/azure/network/vnet-gateway)

# 相關參考
## [虛擬網路](/azure/virtual-network/)
## [應用程式閘道](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [流量管理員](/azure/traffic-manager/)
## [負載平衡器](/azure/load-balancer/)
## [ExpressRoute](/azure/expressroute/)

# 資源
## [Azure 藍圖](https://azure.microsoft.com/roadmap/?category=networking)
## [部落格](https://azure.microsoft.com/blog/topics/networking)
## [論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [價格](https://azure.microsoft.com/pricing/details/vpn-gateway)
## [定價計算機](https://azure.microsoft.com/pricing/calculator/)
## [SLA](https://azure.microsoft.com/support/legal/sla)
## [影片](https://azure.microsoft.com/documentation/videos/index/?services=vpn-gateway)
