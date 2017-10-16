---
title: "在傳統入口網站中啟用 Azure AD 應用程式 Proxy | Microsoft Docs"
description: "在 Azure 傳統入口網站中開啟應用程式 Proxy，並安裝反向 Proxy 的連接器。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/02/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.openlocfilehash: ea97fdc8d146ed524a932018b572ceda0982738b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="enable-application-proxy-in-the-classic-portal-and-download-connectors"></a>在傳統入口網站中啟用應用程式 Proxy 並下載連接器
本文將逐步引導您完成為 Azure AD 中的雲端目錄啟用 Microsoft Azure AD 應用程式 Proxy 的步驟。

如果您不熟悉應用程式 Proxy 可協助您執行的工作，請深入了解 [如何為內部部署應用程式提供安全的遠端存取](active-directory-application-proxy-get-started.md)。

## <a name="application-proxy-prerequisites"></a>應用程式 Proxy 先決條件
您可以啟用並使用應用程式 Proxy 服務之前，必須具備：

* Microsoft Azure AD [基本或進階訂用帳戶](active-directory-editions.md) 以及您是全域管理員的 Azure AD 目錄。
* 您可以在執行 Windows Server 2012 R2 或 2016 的伺服器上，安裝應用程式 Proxy 連接器。 伺服器會將要求傳送至雲端的應用程式 Proxy 服務，而且需要您所發佈之應用程式的 HTTP 或 HTTPS 連線。
  * 如需單一登入已發佈的應用程式，這部電腦應該會加入與您要發佈的應用程式相同的 AD 網域中。 如需詳細資訊，請參閱[使用應用程式 Proxy 進行單一登入](active-directory-application-proxy-sso-using-kcd.md)
* 如果您的組織使用 Proxy 伺服器來連線至網際網路，請參閱[使用現有的內部部署 Proxy 伺服器](application-proxy-working-with-proxy-servers.md)，以取得如何設定伺服器的詳細資訊。

## <a name="open-your-ports"></a>開啟您的連接埠

若要準備適合 Azure AD 應用程式 Proxy 的環境，您需要先啟用 Azure 資料中心的通訊。 如果路徑中有防火牆，請確定防火牆已開啟，以便連接器可以對應用程式 Proxy 提出 HTTPS (TCP) 要求。

1. 開啟下列連接埠以**輸出**流量：

   | 連接埠號碼 | 使用方式 |
   | --- | --- |
   | 80 | 下載憑證撤銷清單 (CRL) 時驗證 SSL 憑證 |
   | 443 | 應用程式 Proxy 服務的所有傳出通訊 |

   如果您的防火牆根據原始使用者強制執行流量，請針對來自當做網路服務執行的 Windows 服務的流量，開放這些連接埠。

   > [!IMPORTANT]
   > 下表反映連接器 1.5.132.0 版及更新版本的連接埠需求。 如果仍有舊版的連接器，您也需要啟用下列連接埠：5671、8080、9090、9091、9350、9352 和 10100–10120。
   >
   >如需將連接器更新至最新版本的詳細資訊，請參閱[了解 Azure AD 應用程式 Proxy 連接器](application-proxy-understand-connectors.md#automatic-updates)。

2. 如果您的防火牆或 Proxy 允許建立 DNS 白名單，您可以建立 msappProxy.net 和 servicebus.windows.net 的白名單連線。 如果不是，您需要允許對每週更新的 [Azure 資料中心 IP 範圍](https://www.microsoft.com/download/details.aspx?id=41653)的存取。

3. 請使用 [Azure AD 應用程式 Proxy 連接器連接埠測試工具](https://aadap-portcheck.connectorporttest.msappproxy.net/)，來確認您的連接器是否能夠連線到「應用程式 Proxy」服務。 至少，請確定「美國中部」區域及離您最近的區域都具有綠色勾選記號。 除此之外，綠色勾選記號越多代表恢復能力越佳。

## <a name="enable-application-proxy-in-azure-ad"></a>Azure AD 中啟用應用程式 Proxy
1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/)中，以系統管理員身分登入。
2. 移至 Active Directory，並選取您要啟用應用程式 Proxy 所在的目錄。

    ![Active Directory - 圖示](./media/active-directory-application-proxy-enable/ad_icon.png)
3. 在目錄頁面上選取 [設定]，然後向下捲動至 [應用程式 Proxy]。
4. 將 [啟用此目錄的應用程式 Proxy 服務] 切換為 [已啟用]。

    ![啟用應用程式 Proxy](./media/active-directory-application-proxy-enable/app_proxy_enable.png)
5. 選取 [立即下載] 。 [Azure AD 應用程式 Proxy 連接器下載] 隨即開啟。 閱讀並接受授權條款，然後按一下 [下載]  以儲存連接器的 Windows Installer 檔案 (.exe)。

## <a name="install-and-register-the-connector"></a>安裝並註冊連接器
1. 在您根據必要條件所準備好的伺服器上執行 **AADApplicationProxyConnectorInstaller.exe** 。
2. 依照精靈中的指示進行安裝。
3. 在安裝期間，系統將提示您向 Azure AD 租用戶的應用程式 Proxy 註冊連接器。

   * 提供您的 Azure AD 全域管理員認證。 您的全域管理員租用戶可能與您的 Microsoft Azure 認證不同。
   * 請確定註冊連接器的系統管理員與您啟用應用程式 Proxy 服務的位置在相同的目錄中。 例如，如果租用戶網域為 contoso.com，則系統管理員應該是 admin@contoso.com ，或該網域上的其他別名。
   * 如果 [IE 增強式安全性設定] 在伺服器上設定為 [開啟]，可能會封鎖註冊畫面。 若要允許存取，請依照錯誤訊息中的指示。 請確定已停用 [Internet Explorer 增強式安全性]。
   * 如果連接器註冊不成功，請參閱 [針對應用程式 Proxy 進行疑難排解](active-directory-application-proxy-troubleshoot.md)。  
4. 安裝完成後，會在您的伺服器中新增兩個新的服務：

   * **Microsoft AAD 應用程式 Proxy 連接器** 可啟用連線

     * **Microsoft AAD 應用程式 Proxy 連接器更新程式**是自動更新服務。 更新程式會檢查連接器的新版本，並且視需要更新連接器。

     ![應用程式 Proxy 連接器服務 - 螢幕擷取畫面](./media/active-directory-application-proxy-enable/app_proxy_services.png)
5. 按一下安裝視窗中的 [完成]  。

如需連接器的相關資訊，請參閱[了解 Azure AD 應用程式 Proxy 連接器](application-proxy-understand-connectors.md)。

為了實現高可用性，您應該至少部署兩個連接器。 若要部署更多連接器，請重複步驟 2 和 3。 每個連接器都必須分別進行註冊。

如果您想要解除安裝連接器，請解除安裝連接器服務和更新程式服務。 重新啟動電腦，才能完全移除此服務。

## <a name="next-steps"></a>後續步驟
您現在已經準備好 [使用應用程式 Proxy 發佈應用程式](active-directory-application-proxy-publish.md)。

如果您有位於不同網路或不同位置上的應用程式，您可以使用連接器群組將不同的連接器組織成邏輯單元。 深入了解 [使用應用程式 Proxy 連接器](active-directory-application-proxy-connectors.md)。
