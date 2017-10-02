---
title: "建立和安裝適用於 Azure 憑證驗證的 P2S VPN 用戶端組態檔：PowerShell：Azure | Microsoft Docs"
description: "本文可協助您對使用憑證驗證的點對站連線建立和安裝 VPN 用戶端組態檔。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 95f14f2b77565b53c6e270f6afbf9873cdac606a
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>建立和安裝適用於原生 Azure 憑證驗證 P2S 組態的 VPN 用戶端組態檔

VPN 用戶端組態檔包含在 ZIP 檔案內。 這些組態檔會提供原生 Windows 或 Mac IKEv2 VPN 用戶端所需的設定，以供其透過使用原生 Azure 憑證驗證的點對站連線來連線至 VNet。

>[!NOTE]
>適用於 P2S 的 IKEv2 目前為預覽版。
>

### <a name="workflow"></a>P2S 工作流程

  1. 設定 P2S 連線的 Azure VPN 閘道。
  2. 產生根憑證和用戶端憑證。 將根憑證公開金鑰上傳至 Azure，並將用戶端憑證安裝在用戶端裝置上。 系統會使用憑證來驗證要連線的使用者。
  3. 取得所選驗證選項適用的 VPN 用戶端組態，並用它來設定 Windows 和 Mac 裝置上的 VPN 用戶端。 這可讓您透過點對站連線來連線至 Azure VNet。

>[!NOTE]
>用戶端組態檔是 VNet 所使用之 VPN 組態特有的檔案。 如果您在產生 VPN 用戶端組態檔後對「點對站 VPN 組態」進行任何變更 (例如 VPN 通訊協定類型或驗證類型)，請務必為使用者裝置產生新的 VPN 用戶端組態檔。
>
>

## <a name="generate"></a>產生 VPN 用戶端組態檔

在開始之前，請確定所有要連線的使用者都已在使用者裝置上安裝有效的憑證。 如需如何安裝用戶端憑證的詳細資訊，請參閱[安裝用戶端憑證](point-to-site-how-to-vpn-client-install-azure-cert.md)。

1. 在產生 VPN 用戶端組態檔時，「-AuthenticationMethod」的值是「EapTls」。 使用下列命令來產生 VPN 用戶端組態檔：

  ```powershell
  New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -VirtualNetworkGatewayName "VNet1GW" -AuthenticationMethod "EapTls"
  ```
2. 前面的命令會傳回連結以供您下載用戶端組態檔。 將該連結複製並貼到網頁瀏覽器，以下載「VpnClientConfiguration.zip」檔案。 將該檔案解壓縮以檢視下列資料夾：

  * **WindowsAmd64** 和 **WindowsX86**，這兩個資料夾分別包含 Windows 32 位元和 64 位元的安裝程式套件。 **WindowsAmd64** 安裝程式套件不只適用於 AMD，也適用於所有受支援的 64 位元 Windows 用戶端。
  * **Generic**，這個資料夾包含用來建立自有 VPN 用戶端組態的一般資訊。 請略過此資料夾。 當閘道上設定了 IKEv2 或 SSTP+IKEv2 時才會提供 Generic 資料夾。 如果只設定了 SSTP，則不會提供 Generic 資料夾。

### <a name="to-retrieve-client-configuration-files"></a>擷取用戶端組態檔

如果您已經產生用戶端組態檔，而且您只需要擷取這些組態檔，您可以使用 'Get-AzureRmVpnClientConfiguration' Cmdlet。 'Get-AzureRmVpnClientConfiguration' Cmdlet 會傳回一個 URL (連結) 以供您下載 VpnClientConfiguration.zip 檔案。 如果您對 P2S VPN 組態進行任何變更 (例如，VPN 通訊協定類型或驗證類型)，該組態不會自動更新。 您必須改為執行 'New-AzureRmVpnClientConfiguration' Cmdlet 來重新建立組態。

若要擷取先前產生的用戶端組態檔，請使用下列範例：

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -VirtualNetworkGatewayName "VNet1GW"
```
 
## <a name="installwin"></a>安裝 Windows VPN 用戶端組態套件

您可以在每個 Windows 用戶端電腦上使用相同的 VPN 用戶端組態套件，但前提是版本必須符合用戶端的架構。 如需用戶端支援的作業系統清單，請參閱 [VPN 閘道常見問題集](vpn-gateway-vpn-faq.md#P2S)的＜點對站＞一節。

請使用下列步驟來設定原生 Windows VPN 用戶端的憑證驗證：

1. 選取 Windows 電腦架構所對應的 VPN 用戶端組態檔。 若是 64 位元的處理器架構，請選擇 'VpnClientSetupAmd64' 安裝程式套件。 若是 32 位元的處理器架構，請選擇 'VpnClientSetupX86' 安裝程式套件。 
2. 對套件按兩下來加以安裝。 如果您看到 SmartScreen 快顯視窗，請按一下 [更多資訊]，然後按一下 [仍要執行]。
3. 在用戶端電腦上，瀏覽至 [網路設定]，然後按一下 [VPN]。 VPN 連線會顯示其連線的虛擬網路名稱。 

## <a name="installmac"></a>安裝 Mac (OSX) VPN 用戶端組態

您必須為每個連線到 Azure VNet 的 Mac 裝置建立各自的 VPN 用戶端組態。 請勿將相同組態檔重複用於多個 Mac 裝置。 這是因為您必須在 VPN 用戶端組態檔中為這些裝置指定使用者憑證。 **Generic** 資料夾含有建立 VPN 用戶端組態所需的所有資訊。 Generic 資料夾包含下列檔案：

* **VpnSettings.xml**，此檔案包含重要的設定，例如伺服器位址和通道類型。 
* **VpnServerRoot.cer**，此檔案包含所需的根憑證，以供您在 P2S 連線設定期間驗證 Azure VPN 閘道。

請使用下列步驟來設定 Mac 上原生 VPN 用戶端的憑證驗證：

1. 將 **VpnServerRoot** 根憑證匯入 Mac 中。 若要這麼做，請將該檔案複製到 Mac 上並對該檔案按兩下。  
按一下 [新增] 來進行匯入。

  ![新增憑證](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
2. 開啟 [網路喜好設定] 底下的 [網路] 對話方塊，然後按一下 [+] 建立適用於 Azure VNet P2S 連線的新 VPN 用戶端連線設定檔。

  [介面] 值是 [VPN]，[VPN 類型] 值則是 [IKEv2]。 在 [服務名稱] 欄位中指定設定檔名稱，然後按一下 [建立] 以建立 VPN 用戶端連線設定檔。

  ![網路](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
3. 從 **Generic** 資料夾的 **VpnSettings.xml** 檔案複製 **VpnServer** 標記值。 將此值貼到設定檔的 [伺服器位址] 和 [遠端識別碼] 欄位。

  ![伺服器資訊](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
4. 按一下 [驗證設定]，然後選取 [憑證]。 

  ![驗證設定](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
5. 按一下 [選取...] 以選擇您要用來驗證的憑證。

  ![憑證](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
6. [選擇身分識別] 會顯示一份憑證清單供您選擇。 選取適當憑證，然後按一下 [繼續]。

  ![身分識別](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
7. 在 [地區設定識別碼] 欄位中指定憑證名稱 (來自步驟 5)。 在此範例中，名稱是 "ikev2Client.com"。 然後，按一下 [套用] 按鈕以儲存變更。

  ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
8. 在 [網路] 對話方塊中按一下 [套用] 以儲存所有變更。 然後，按一下 [連線] 以啟動 Azure VNet 的 P2S 連線。

## <a name="next-steps"></a>後續步驟

回到本文以[完成 P2S 設定](vpn-gateway-howto-point-to-site-rm-ps.md)。
