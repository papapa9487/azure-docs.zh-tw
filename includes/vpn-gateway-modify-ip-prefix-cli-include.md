### <a name="noconnection"></a>修改區域網路閘道 IP 位址首碼 - 沒有閘道連線

如果您沒有閘道連線，而且想要新增或移除 IP 位址首碼，您會使用與您用於建立區域網路閘道的相同命令 [az network local-gateway create](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_create)。 您也可以使用此命令來更新 VPN 裝置的閘道 IP 位址。 若要覆寫目前的設定，請使用現有的區域網路閘道名稱。 如果您使用不同的名稱，您會建立新的區域網路閘道，而不是覆寫現有閘道。

每次進行變更時，您都必須指定完整的首碼清單，而不只是指定您想要變更的首碼。 僅指定我們想要保留的首碼。 在此案例中為 10.0.0.0/24 和 20.0.0.0/24

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

### <a name="withconnection"></a>修改區域網路閘道 IP 位址首碼 - 現有閘道連線

如果您有閘道連線，而且想要新增或移除 IP 位址首碼，您可以使用 [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_update) 更新首碼。 這會導致您 VPN 連線的停機時間。 修改 IP 位址首碼時，您不需要刪除 VPN 閘道。

每次進行變更時，您都必須指定完整的首碼清單，而不只是指定您想要變更的首碼。 在此範例中，10.0.0.0/24 和 20.0.0.0/24 已存在。 我們會新增首碼 30.0.0.0/24 和 40.0.0.0/24，並且在更新時指定全部 4 個首碼。

```azurecli
az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 --name VNet1toSite2 -g TestRG1
```
