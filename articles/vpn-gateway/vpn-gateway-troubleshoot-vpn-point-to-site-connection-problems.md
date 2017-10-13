---
title: "針對 Azure 點對站連線問題進行疑難排解| Microsoft Docs"
description: "了解如何針對點對站連線問題進行疑難排解。"
services: vpn-gateway
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/23/2017
ms.author: genli
ms.openlocfilehash: 76ab1600903705aad7f18f48f41cb7119c3c09bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-azure-point-to-site-connection-problems"></a>疑難排解：Azure 點對站連線問題

本文列出您可能遇到的常見點對站連線問題。 文中也會探討這些問題的可能原因和解決方案。

## <a name="vpn-client-error-a-certificate-could-not-be-found"></a>VPN 用戶端錯誤：找不到憑證

### <a name="symptom"></a>徵狀

當您嘗試使用 VPN 用戶端來連線到 Azure 虛擬網路時，會收到下列錯誤訊息：

**找不到可以使用這個可延伸的驗證通訊協定的憑證。(錯誤 798)**

### <a name="cause"></a>原因

如果 [憑證 - 目前的使用者\個人\憑證] 中遺失了用戶端憑證，就會發生此問題。

### <a name="solution"></a>方案

若要解決此問題，請遵循下列步驟：

1. 請確定下列憑證位於正確的位置：

    | 憑證 | 位置 |
    | ------------- | ------------- |
    | AzureClient.pfx  | 目前的使用者\個人\憑證 |
    | Azuregateway-*GUID*.cloudapp.net  | 目前的使用者\受信任的根憑證授權單位|
    | AzureGateway-*GUID*.cloudapp.net、AzureRoot.cer    | 本機電腦\受信任的根憑證授權單位|

2. 移至 Users\<使用者名稱>\AppData\Roaming\Microsoft\Network\Connections\Cm\<GUID>，在使用者和電腦的存放區中手動安裝憑證 (*.cer 檔案)。

如需如何安裝用戶端憑證的詳細資訊，請參閱[產生並匯出點對站連線的憑證](vpn-gateway-certificates-point-to-site.md)。

> [!NOTE]
> 匯入用戶端憑證時，請勿選取 [啟用加強私密金鑰保護] 選項。

## <a name="vpn-client-error-the-message-received-was-unexpected-or-badly-formatted"></a>VPN 用戶端錯誤：接收到的訊息超出預期或格式不正確

### <a name="symptom"></a>徵狀

當您嘗試使用 VPN 用戶端來連線到 Azure 虛擬網路時，會收到下列錯誤訊息：

**接收到的訊息超出預期或格式不正確。(錯誤 0x80090326)**

### <a name="cause"></a>原因

如果未將根憑證公開金鑰上傳到 Azure VPN 閘道，就會發生此問題。 如果金鑰已損毀或過期，也會發生此問題。

### <a name="solution"></a>方案

若要解決此問題，請在 Azure 入口網站中檢查根憑證的狀態，以查看它是否已遭撤銷。 如果它未遭撤銷，請嘗試將根憑證刪除，然後重新上傳。 如需詳細資訊，請參閱 [建立憑證](vpn-gateway-howto-point-to-site-classic-azure-portal.md#generatecerts)。

## <a name="vpn-client-error-a-certificate-chain-processed-but-terminated"></a>VPN 用戶端錯誤：憑證鏈結已處理但被終止 

### <a name="symptom"></a>徵狀 

當您嘗試使用 VPN 用戶端來連線到 Azure 虛擬網路時，會收到下列錯誤訊息：

**憑證鏈結已處理，但它終止於信任提供者所未信任的根憑證。**

### <a name="solution"></a>方案

1. 請確定下列憑證位於正確的位置：

    | 憑證 | 位置 |
    | ------------- | ------------- |
    | AzureClient.pfx  | 目前的使用者\個人\憑證 |
    | Azuregateway-*GUID*.cloudapp.net  | 目前的使用者\受信任的根憑證授權單位|
    | AzureGateway-*GUID*.cloudapp.net、AzureRoot.cer    | 本機電腦\受信任的根憑證授權單位|

2. 如果憑證已經在該位置中，請嘗試將憑證刪除，然後重新安裝。 **azuregateway-*GUID*.cloudapp.net** 憑證位於您從 Azure 入口網站下載的 VPN 用戶端組態套件中。 您可以使用檔案封存工具從套件擷取檔案。

## <a name="file-download-error-target-uri-is-not-specified"></a>檔案下載錯誤：未指定目標 URI

### <a name="symptom"></a>徵狀

您收到下列錯誤訊息：

**檔案下載錯誤。未指定目標 URI。**

### <a name="cause"></a>原因 

此問題的發生原因是閘道類型不正確。 

### <a name="solution"></a>方案

VPN 閘道類型必須是 **VPN**，且 VPN 類型必須是 **RouteBased**。

## <a name="vpn-client-error-azure-vpn-custom-script-failed"></a>VPN 用戶端錯誤：Azure VPN 自訂指令碼失敗 

### <a name="symptom"></a>徵狀

當您嘗試使用 VPN 用戶端來連線到 Azure 虛擬網路時，會收到下列錯誤訊息：

**自訂指令碼 (更新您的路由表) 失敗。(錯誤 8007026f)**

### <a name="cause"></a>原因

如果您嘗試使用捷徑來開啟站對點 VPN 連線，就可能發生此問題。

### <a name="solution"></a>方案 

直接開啟 VPN 套件，而不要從捷徑開啟開啟它。

## <a name="cannot-install-the-vpn-client"></a>無法安裝 VPN 用戶端

### <a name="cause"></a>原因 

必須有額外的憑證，才能信任您虛擬網路的 VPN 閘道。 此憑證包含在從 Azure 入口網站產生的 VPN 用戶端組態套件中。

### <a name="solution"></a>方案

擷取 VPN 用戶端組態套件，並尋找 .cer 檔案。 若要安裝憑證，請遵循下列步驟：

1. 開啟 mmc.exe。
2. 新增 [憑證] 嵌入式管理單元。
3. 選取本機電腦的 [電腦] 帳戶。
4. 以滑鼠右鍵按一下 [受信任的根憑證授權單位] 節點。 按一下 [所有工作] > [匯入]，然後瀏覽至您從 VPN 用戶端組態套件擷取的 .cer 檔案。
5. 重新啟動電腦。 
6. 嘗試安裝 VPN 用戶端。

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-data-is-invalid"></a>Azure 入口網站錯誤：無法儲存 VPN 閘道且資料無效

### <a name="symptom"></a>徵狀

當您嘗試在 Azure 入口網站中儲存 VPN 閘道變更時，收到下列錯誤訊息：

**無法儲存虛擬網路閘道 &lt;*閘道名稱*&gt;。 憑證 &lt;*憑證識別碼*&gt; 的資料無效。**

### <a name="cause"></a>原因 

如果您上傳的根憑證公開金鑰包含無效的字元 (例如空格)，就可能發生此問題。

### <a name="solution"></a>方案

請確定憑證中的資料不包含分行符號 (歸位字元) 之類的無效字元。 整個值應該是一個長行。 以下文字是憑證的範例：

    -----BEGIN CERTIFICATE-----
    MIIC5zCCAc+gAwIBAgIQFSwsLuUrCIdHwI3hzJbdBjANBgkqhkiG9w0BAQsFADAW
    MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0xNzA2MTUwMjU4NDZaFw0xODA2MTUw
    MzE4NDZaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
    AAOCAQ8AMIIBCgKCAQEAz8QUCWCxxxTrxF5yc5uUpL/bzwC5zZ804ltB1NpPa/PI
    sa5uwLw/YFb8XG/JCWxUJpUzS/kHUKFluqkY80U+fAmRmTEMq5wcaMhp3wRfeq+1
    G9OPBNTyqpnHe+i54QAnj1DjsHXXNL4AL1N8/TSzYTm7dkiq+EAIyRRMrZlYwije
    407ChxIp0stB84MtMShhyoSm2hgl+3zfwuaGXoJQwWiXh715kMHVTSj9zFechYd7
    5OLltoRRDyyxsf0qweTFKIgFj13Hn/bq/UJG3AcyQNvlCv1HwQnXO+hckVBB29wE
    sF8QSYk2MMGimPDYYt4ZM5tmYLxxxvGmrGhc+HWXzMeQIDAQABozEwLzAOBgNVHQ8B
    Af8EBAMCAgQwHQYDVR0OBBYEFBE9zZWhQftVLBQNATC/LHLvMb0OMA0GCSqGSIb3
    DQEBCwUAA4IBAQB7k0ySFUQu72sfj3BdNxrXSyOT4L2rADLhxxxiK0U6gHUF6eWz
    /0h6y4mNkg3NgLT3j/WclqzHXZruhWAXSF+VbAGkwcKA99xGWOcUJ+vKVYL/kDja
    gaZrxHlhTYVVmwn4F7DWhteFqhzZ89/W9Mv6p180AimF96qDU8Ez8t860HQaFkU6
    2Nw9ZMsGkvLePZZi78yVBDCWMogBMhrRVXG/xQkBajgvL5syLwFBo2kWGdC+wyWY
    U/Z+EK9UuHnn3Hkq/vXEzRVsYuaxchta0X2UNRzRq+o706l+iyLTpe6fnvW6ilOi
    e8Jcej7mzunzyjz4chN0/WVF94MtxbUkLkqP
    -----END CERTIFICATE-----

## <a name="azure-portal-error-failed-to-save-the-vpn-gateway-and-the-resource-name-is-invalid"></a>Azure 入口網站錯誤：無法儲存 VPN 閘道且資源名稱無效

### <a name="symptom"></a>徵狀

當您嘗試在 Azure 入口網站中儲存 VPN 閘道變更時，收到下列錯誤訊息： 

**無法儲存虛擬網路閘道 &lt;*閘道名稱*&gt;。 資源名稱 &lt;*您嘗試上傳的憑證名稱*&gt; 無效**。

### <a name="cause"></a>原因

此問題的發生原因是憑證的名稱包含無效的字元 (例如空格)。 

## <a name="azure-portal-error-vpn-package-file-download-error-503"></a>Azure 入口網站錯誤：VPN 套件檔案下載錯誤 503

### <a name="symptom"></a>徵狀

當您嘗試下載 VPN 用戶端組態套件時，收到下列錯誤訊息：

**無法下載檔案。錯誤詳細資料：錯誤 503。伺服器忙碌中。**
 
### <a name="solution"></a>方案

此錯誤可能是由暫時性的網路問題所造成。 請在幾分鐘後再重新嘗試下載 VPN 套件。

## <a name="azure-vpn-gateway-upgrade-all-p2s-clients-are-unable-to-connect"></a>Azure VPN 閘道升級：所有 P2S 用戶端都無法連線

### <a name="cause"></a>原因

如果憑證的存留期已過了 50%，就會變換該憑證。

### <a name="solution"></a>方案

若要解決此問題，請建立新憑證並重新發佈到 VPN 用戶端。 

## <a name="too-many-vpn-clients-connected-at-once"></a>一次有太多 VPN 用戶端連線

每個 VPN 閘道可允許的連線數目上限是 128。 您可以在 Azure 入口網站中查看已連線的用戶端總數。

## <a name="point-to-site-vpn-incorrectly-adds-a-route-for-100008-to-the-route-table"></a>點對站 VPN 錯誤地將 10.0.0.0/8 的路由新增到路由表

### <a name="symptom"></a>徵狀

當您撥接點對站用戶端上的 VPN 連線時，VPN 用戶端應該新增朝向 Azure 虛擬網路的路由。 IP 協助程式服務應該針對 VPN 用戶端的子網路新增路由。 

VPN 用戶端範圍屬於 10.0.0.0/8 的較小子網路，例如 10.0.12.0/24。 系統會新增較高優先順序的 10.0.0.0/8 路由，而不是 10.0.12.0/24 的路由。 

這個錯誤的路由會破壞與其他可能屬於 10.0.0.0/8 範圍內另一個子網路 (例如，未定義特定路由的 10.50.0.0/24) 之內部部署網路的連線。 

### <a name="cause"></a>原因

這是針對 Windows 用戶端設計的行為。 當用戶端使用 PPP IPCP 通訊協定時，其會從伺服器 (在此例中為 VPN 閘道) 取得通道介面的 IP 位址。 不過，由於通訊協定中的限制緣故，因此用戶端並沒有子網路遮罩。 因為沒有任何其他方式可取得此遮罩，所以用戶端會嘗試根據通道介面 IP 位址的類別來猜測子網路遮罩。 

因此，會根據下列靜態對應新增路由： 

如果位址屬於類別 A --> 套用 /8

如果位址屬於類別 B --> 套用 /16

如果位址屬於類別 C --> 套用 /24

## <a name="vpn-client-cannot-access-network-file-shares"></a>VPN 用戶端無法存取網路檔案共用

### <a name="symptom"></a>徵狀

VPN 用戶端已連線到 Azure 虛擬網路。 不過，用戶端無法存取網路共用。

### <a name="cause"></a>原因

使用 SMB 通訊協定來進行檔案共用存取。 在起始連線時，VPN 用戶端會新增工作階段認證，接著就發生此失敗。 建立連線之後，用戶端被迫使用快取認證來進行 Kerberos 驗證。 此程序會對金鑰發佈中心 (網域控制站) 起始查詢，以取得權杖。 因為用戶端會從網際網路連線，所以它可能無法連線到網域控制站。 因此，用戶端無法從 Kerberos 容錯移轉至 NTLM。 

系統只有在用戶端具有由其所加入之網域核發的有效憑證 (使用 SAN=UPN) 時，才會提示用戶端提供認證。 用戶端也必須實際連線到網域網路。 在此情況下，用戶端會嘗試使用該憑證並向外連線到網域控制站。 金鑰發佈中心接著會傳回 "KDC_ERR_C_PRINCIPAL_UNKNOWN" 錯誤。 系統會強制用戶端容錯移轉至 NTLM。 

### <a name="solution"></a>方案

若要解決此問題，請從下列登錄子機碼停用網域認證快取功能： 

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\DisableDomainCreds - Set the value to 1 


## <a name="cannot-find-the-point-to-site-vpn-connection-in-windows-after-reinstalling-the-vpn-client"></a>重新安裝 VPN 用戶端之後，在 Windows 中找不到點對站 VPN 連線

### <a name="symptom"></a>徵狀

您移除點對站 VPN 連線，然後重新安裝 VPN 用戶端。 在此情況下，並未成功設定 VPN 連線。 您在 Windows 的 [網路連線] 設定中看不到 VPN 連線。

### <a name="solution"></a>方案

若要解決此問題，請從 **C:\Users\TheUserName\AppData\Roaming\Microsoft\Network\Connections** 中刪除舊的 VPN 用戶端組態檔，然後重新執行 VPN 用戶端安裝程式。
