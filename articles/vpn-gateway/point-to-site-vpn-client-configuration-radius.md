---
title: "建立和安裝適用於 P2S RADIUS 連線的 VPN 用戶端組態檔：PowerShell：Azure | Microsoft Docs"
description: "本文可協助您對使用 RADIUS 驗證的點對站連線建立 VPN 用戶端組態檔。"
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
ms.date: 10/16/2017
ms.author: cherylmc
ms.openlocfilehash: d7d2dbff4bcd0d76b56c6f142afae4ce8359bb37
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2017
---
# <a name="create-and-install-vpn-client-configuration-files-for-p2s-radius-authentication-preview"></a>建立和安裝 P2S RADIUS 驗證的 VPN 用戶端組態檔 (預覽)

VPN 用戶端組態檔包含在 ZIP 檔案內。 這些組態檔會提供必要的設定，以供原生 Windows 或 Mac IKEv2 VPN 用戶端透過點對站來連線至 VNet。 RADIUS 伺服器會提供多個驗證選項，因此 VPN 用戶端組態因選項而異。

### <a name="workflow"></a>工作流程

  1. 設定 P2S 連線的 Azure VPN 閘道。
  2. 設定 RADIUS 伺服器以供驗證。 
  3. 取得所選驗證選項適用的 VPN 用戶端組態，並使用它在 Windows 裝置上設定 VPN 用戶端。 這可讓您透過 P2S 連線來連線至 Azure VNet。

>[!IMPORTANT]
>如果您在產生 VPN 用戶端組態檔後，對點對站 VPN 組態進行任何變更 (例如 VPN 通訊協定類型或驗證類型)，您必須在使用者裝置上產生並安裝新的 VPN 用戶端組態檔。
>
>

## <a name="adeap"></a>使用者名稱/密碼驗證

* **AD 驗證：**常見案例是 AD 網域驗證。 在此案例中，使用者會使用其網域認證來連線到 Azure VNet。 您可以建立 VPN 用戶端組態檔以便進行 RADIUS AD 驗證。

* **不含 AD 的驗證：**您也可以設定不含 AD 的使用者名稱/密碼 RADIUS 驗證案例。

確定所有連線使用者都具有可透過 RADIUS 驗證的使用者名稱/密碼認證。 您只能建立 EAP-MSCHAPv2 使用者名稱/密碼驗證通訊協定的組態。 '-AuthenticationMethod' 會指定為 'EapMSChapv2'。

### <a name="usernamefiles"></a>產生 VPN 用戶端組態檔

使用下列命令來建立 VPN 用戶端組態：

```powershell 
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapMSChapv2"
```
 
執行命令後，它會傳回一個連結。 將該連結複製並貼到網頁瀏覽器，以下載 'VpnClientConfiguration.zip' 壓縮檔。 將該檔案解壓縮，以查看下列資料夾： 
 
* 名為 'WindowsAmd64' 和 'WindowsX86' 的資料夾。 這兩個資料夾分別包含 Windows 64 位元和 32 位元的安裝程式套件。 
* 名為 'GenericDevice' 的資料夾。 這個資料夾包含用來建立自有 VPN 用戶端組態的一般資訊。 請略過此資料夾。
* 如果您在建立虛擬網路閘道時設定 IKEv2，您會看到名為 'Mac' 的資料夾，其中包含名為 'mobileconfig' 的檔案。 這個檔案用來設定 Mac 用戶端。

您可以擷取您已經建立的用戶端組態檔。 'Get-AzureRmVpnClientConfiguration' Cmdlet 會傳回一個 URL (連結) 以供您下載 VpnClientConfiguration.zip 檔案。 如果您對 P2S VPN 組態進行任何變更 (例如，VPN 通訊協定類型或驗證類型)，該組態不會自動更新。 您必須執行 'New-AzureRmVpnClientConfiguration' Cmdlet 來重新建立組態。

若要擷取先前產生的用戶端組態檔，請使用下列命令：

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```
 
### <a name="adwincli"></a>設定 Windows VPN 用戶端

您可以在每個 Windows 用戶端電腦上使用相同的 VPN 用戶端組態套件，但前提是版本必須符合用戶端的架構。 如需用戶端支援的作業系統清單，請參閱[常見問題集](vpn-gateway-vpn-faq.md#P2S)的＜點對站＞一節。

請使用下列步驟來設定原生 Windows VPN 用戶端的憑證驗證：

1. 選取 Windows 電腦架構所對應的 VPN 用戶端組態檔。 若是 64 位元的處理器架構，請選擇 'VpnClientSetupAmd64' 安裝程式套件。 若是 32 位元的處理器架構，請選擇 'VpnClientSetupX86' 安裝程式套件。 
2. 對套件按兩下來加以安裝。 如果您看到 SmartScreen 快顯視窗，請按一下 [更多資訊]，然後按一下 [仍要執行]。
3. 在用戶端電腦上，瀏覽至 [網路設定]，然後按一下 [VPN]。 VPN 連線會顯示其連線的虛擬網路名稱。 

### <a name="admaccli"></a>設定 Mac (OSX) VPN 用戶端

1. 選取 **VpnClientSetup mobileconfig** 檔案，並將它傳送給每個使用者。 您可以使用電子郵件或其他方法來執行此作業。

2. 在 Mac 上找出 **mobileconfig** 檔案。

  ![找出 mobilconfig 檔案](./media/point-to-site-vpn-client-configuration-radius/admobileconfigfile.png)
3. 按兩下設定檔加以安裝，請按一下 [繼續]。 設定檔名稱與您的 VNet 名稱相同。

  ![安裝](./media/point-to-site-vpn-client-configuration-radius/adinstall.png)
4. 按一下 [繼續]，以信任設定檔的寄件者並繼續進行安裝。

  ![繼續](./media/point-to-site-vpn-client-configuration-radius/adcontinue.png)
5. 在設定檔安裝期間，您有選項可指定用於 VPN 驗證的使用者名稱和密碼。 不一定要輸入此資訊。 若已指定，系統會儲存此資訊並在您初始連線時自動使用。 按一下 [安裝] 繼續進行。

  ![settings](./media/point-to-site-vpn-client-configuration-radius/adsettings.png)
6. 輸入在您的電腦上安裝設定檔時所需之必要權限的使用者名稱和密碼。 按一下 [確定] 。

  ![使用者名稱和密碼](./media/point-to-site-vpn-client-configuration-radius/adusername.png)
7. 安裝之後，設定檔便會顯示在 [設定檔] 對話方塊中。 稍後也可以從 [系統喜好設定] 開啟此對話方塊。

  ![系統喜好設定]](./media/point-to-site-vpn-client-configuration-radius/adsystempref.png)
8. 若要存取 VPN 連線，請從 [系統喜好設定] 開啟 [網路] 對話方塊。

  ![網路](./media/point-to-site-vpn-client-configuration-radius/adnetwork.png)
9. VPN 連線會顯示為 [IkeV2 VPN]。 更新 **mobileconfig** 檔案，即可變更此名稱。

  ![connection](./media/point-to-site-vpn-client-configuration-radius/adconnection.png)
10. 按一下 [驗證設定] 。 選擇下拉式清單中的 [使用者名稱]，然後輸入您的認證。 如果您先前已輸入認證，則會自動在下拉式清單中選擇 [使用者名稱] 並預先填入使用者名稱和密碼。 按一下 [確定]  來儲存設定。 此會讓您回到 [網路] 對話方塊。

  ![authenticate](./media/point-to-site-vpn-client-configuration-radius/adauthentication.png)
11. 按一下 [套件] 以儲存變更。 若要起始連線，請按一下 [連線]。

## <a name="certeap"></a>憑證驗證
 
您可以建立 VPN 用戶端組態檔，以便進行採用 EAP-TLS 通訊協定的 RADIUS 憑證驗證。 通常，企業所發行的憑證可用來驗證 VPN 的使用者。 確定所有連線使用者都已在使用者的裝置上安裝憑證，且 RADIUS 伺服器可以驗證該憑證。
 
* '-AuthenticationMethod' 為 'EapTls'。
* 在憑證驗證期間，用戶端會藉由驗證憑證來驗證 RADIUS 伺服器。 -RadiusRootCert 是 .cer 檔案，其中包含用來驗證 RADIUS 伺服器的根憑證。  
* Windows 裝置有時會有多個用戶端憑證。 在驗證期間，這可能會導致快顯對話方塊列出所有的憑證。 接著，使用者必須選擇要使用的憑證。 指定用戶端憑證應鏈結至的根憑證，即可篩選出正確的憑證。 '-ClientRootCert' 是包含根憑證的 .cer 檔案。這是選擇性參數。 如果您要連線的裝置只有一個用戶端憑證，則不需要指定此參數。

### <a name="certfiles"></a>產生 VPN 用戶端組態檔

使用下列命令來建立 VPN 用戶端組態：
 
```powershell
New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls" -RadiusRootCert <full path name of .cer file containing the RADIUS root> -ClientRootCert <full path name of .cer file containing the client root>
```

此 Cmdlet 結果會傳回一個連結。 將該連結複製並貼到網頁瀏覽器，以下載 'VpnClientConfiguration.zip' 壓縮檔。 將該檔案解壓縮，以查看下列資料夾：

* 名為 'WindowsAmd64' 和 'WindowsX86' 的資料夾。 這兩個資料夾分別包含 Windows 64 位元和 32 位元的安裝程式套件。 
* 名為 'GenericDevice' 的資料夾。 這個資料夾包含用來建立自有 VPN 用戶端組態的一般資訊。

您可以擷取您已經建立的用戶端組態檔。 'Get-AzureRmVpnClientConfiguration' Cmdlet 會傳回一個 URL (連結) 以供您下載 VpnClientConfiguration.zip 檔案。 如果您對 P2S VPN 組態進行任何變更 (例如，VPN 通訊協定類型或驗證類型)，該組態不會自動更新。 您必須執行 'New-AzureRmVpnClientConfiguration' Cmdlet 來重新建立組態。

若要擷取先前產生的用戶端組態檔，請使用下列命令：

```powershell
Get-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW"
```
 
### <a name="certwincli"></a>設定 Windows VPN 用戶端

1. 選取組態套件並將它安裝在用戶端裝置上。 若是 64 位元的處理器架構，請選擇 'VpnClientSetupAmd64' 安裝程式套件。 若是 32 位元的處理器架構，請選擇 'VpnClientSetupX86' 安裝程式套件。 如果您看到 SmartScreen 快顯視窗，請按一下 [更多資訊]，然後按一下 [仍要執行]。 您也可以儲存要在其他用戶端電腦上安裝的套件。
2. 在用戶端電腦上，瀏覽至 [網路設定]，然後按一下 [VPN]。 VPN 連線會顯示其連線的虛擬網路名稱。

### <a name="certmaccli"></a>設定 MAC VPN 用戶端

您必須為每個連線到 Azure VNet 的 Mac 裝置建立個別的設定檔。 這是因為這些裝置需要在設定檔中指定可供驗證的使用者憑證。 **Generic** 資料夾含有建立設定檔所需的所有資訊。

  * **VpnSettings.xml** 包含重要的設定，例如伺服器位址和通道類型。
  * **VpnServerRoot.cer** 包含在 P2S 連線設定期間驗證 VPN 閘道所需的根憑證。
  * **RadiusServerRoot.cer** 包含在驗證期間驗證 RADIUS 伺服器所需的根憑證。

請使用下列步驟來設定 Mac 上原生 VPN 用戶端的憑證驗證：

1. 將 **VpnServerRoot** 和 **RadiusServerRoot** 根憑證匯入至您的 Mac。 若要這麼做，請將該檔案複製到 Mac 上並對該檔案按兩下。  
按一下 [新增] 進行匯入。

  **新增 VpnServerRoot**

  ![新增憑證](./media/point-to-site-vpn-client-configuration-radius/addcert.png)

  **新增 RadiusServerRoot**

  ![新增憑證](./media/point-to-site-vpn-client-configuration-radius/radiusrootcert.png)
2. 開啟 [網路喜好設定] 底下的 [網路] 對話方塊，然後按一下 [+] 建立適用於 Azure VNet P2S 連線的新 VPN 用戶端連線設定檔。

  [介面] 值是 [VPN]，[VPN 類型] 值則是 [IKEv2]。 在 [服務名稱] 欄位中指定設定檔名稱，然後按一下 [建立] 以建立 VPN 用戶端連線設定檔。

  ![網路](./media/point-to-site-vpn-client-configuration-radius/network.png)
3. 從 **Generic** 資料夾的 **VpnSettings.xml** 檔案複製 **VpnServer** 標記值。 將此值貼到設定檔的 [伺服器位址] 和 [遠端識別碼] 欄位。 將 [地區設定識別碼] 欄位保留空白。

  ![伺服器資訊](./media/point-to-site-vpn-client-configuration-radius/servertag.png)
4. 按一下 [驗證設定]，然後選取 [憑證]。 

  ![驗證設定](./media/point-to-site-vpn-client-configuration-radius/certoption.png)
5. 按一下 [選取...] 以選擇您要用來驗證的憑證。

  ![憑證](./media/point-to-site-vpn-client-configuration-radius/certificate.png)
6. [選擇身分識別] 會顯示一份憑證清單供您選擇。 選取適當憑證，然後按一下 [繼續]。

  ![身分識別](./media/point-to-site-vpn-client-configuration-radius/identity.png)
7. 在 [地區設定識別碼] 欄位中指定憑證名稱 (來自步驟 5)。 在此範例中，名稱是 "ikev2Client.com"。 然後，按一下 [套用] 按鈕以儲存變更。

  ![apply](./media/point-to-site-vpn-client-configuration-radius/applyconnect.png)
8. 在 [網路] 對話方塊中按一下 [套用] 以儲存所有變更。 然後，按一下 [連線] 以啟動 Azure VNet 的 P2S 連線。

## <a name="otherauth"></a>其他驗證類型或通訊協定

若要使用不同的驗證類型 (例如 OTP，而不是使用者名稱/密碼或憑證)，或使用不同的驗證通訊協定 (例如 PEAP-MSCHAPv2，而不是 EAP-MSCHAPv2)，您必須建立自己的 VPN 用戶端組態設定檔。 若要建立設定檔，您需要虛擬網路閘道 IP 位址、通道類型以及分割通道路由等資訊。 您可以使用下列步驟來取得此資訊：

1. 使用 'Get-AzureRmVpnClientConfiguration' Cmdlet 來產生 EapMSChapv2 的 VPN 用戶端組態。 如需相關指示，請參閱本文的[這一節](#ccradius)。

2. 解壓縮 VpnClientConfiguration.zip 檔案，並找出 GenenericDevice 資料夾。 忽略包含 64 位元和 32 位元架構之 Windows 安裝程式的資料夾。
 
3. GenenericDevice 資料夾包含名為 VpnSettings 的 XML 檔案。 這個檔案包含所有必要資訊。

  * VpnServer - Azure VPN 閘道的 FQDN。 這是用戶端所連線至的位址。
  * VpnType - 您用來連線的通道類型。
  * 路由 - 您必須在設定檔中設定的路由，所以只有 Azure VNet 繫結流量會透過 P2S 通道傳送。
  * GenenericDevice 資料夾也包含一個稱為 'VpnServerRoot' 的 .cer 檔案。 此檔案包含在 P2S 連線設定期間驗證 Azure VPN 閘道所需的根憑證。 在將連線到 Azure VNet 的所有裝置上安裝憑證。 
 
## <a name="next-steps"></a>後續步驟

回到本文以[完成 P2S 設定](point-to-site-how-to-radius-ps.md)。
