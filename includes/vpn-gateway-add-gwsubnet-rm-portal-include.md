1. 在[入口網站](http://portal.azure.com)中，瀏覽至要建立虛擬網路閘道的 Resource Manager 虛擬網路。
2. 在 VNet 頁面的 [設定] 中，按一下 [子網路] 以展開 [子網路] 頁面。
3. 在 [子網路] 頁面中，按一下 [+閘道子網路] 以開啟 [新增子網路] 頁面。

  ![新增閘道子網路](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addgwsub.png "新增閘道子網路")
4. 子網路的 [名稱] 會自動填入 'GatewaySubnet' 這個值。 為了讓 Azure 將此子網路視為閘道子網路，需要有這個值。 調整自動填入的 [位址範圍] 值，以符合您的組態需求，然後按一下頁面底部的 [確定] 以建立子網路。

  ![新增子網路](./media/vpn-gateway-add-gwsubnet-rm-portal-include/addsubnetgw.png "新增子網路")