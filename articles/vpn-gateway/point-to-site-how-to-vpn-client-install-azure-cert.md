---
title: "安裝 P2S 用戶端憑證 | Azure"
description: "本文可協助您安裝用於 P2S 憑證驗證的用戶端憑證。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2017
ms.author: cherylmc
ms.openlocfilehash: fc0cc37794ef291c9d27b094211b38cec90da55c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-client-certificate-for-point-to-site-azure-certificate-authentication-connections"></a>安裝用於 Azure 點對站憑證驗證連線的用戶端憑證

使用 Azure 點對站憑證驗證來連線到虛擬網路的所有用戶端都必須有用戶端憑證。 本文可協助您安裝使用 P2S 連線至 VNet 時要供驗證使用的用戶端憑證。

## <a name="generate"></a>產生並匯出用戶端憑證

您可以透過使用企業 CA 解決方案所產生的根憑證來產生用戶端憑證，也可以透過自我簽署的根憑證來產生。 如需相關步驟，請參閱 [PowerShell](vpn-gateway-certificates-point-to-site.md) 或 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) 指示。 在產生用戶端憑證後，請將它們匯出為 .pfx 檔案。 匯出時請務必包含整個憑證鏈結。

## <a name="installwin"></a>在 Windows 用戶端上安裝憑證

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>在 Mac 用戶端上安裝憑證

只有 Resource Manager 部署模型能支援 Mac VPN 用戶端。 傳統部署模型則不支援。

> [!NOTE]
>  IKEv2 目前為預覽版。
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="next-steps"></a>後續步驟

繼續進行點對站組態步驟。

* [Azure 入口網站](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Azure 入口網站 (傳統)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)