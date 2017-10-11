---
title: "Azure AD Connect︰同步服務執行個體 | Microsoft Docs"
description: "本頁記載 Azure AD 執行個體的特殊考量。"
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: e8321c3d16253226a5931cacbce6fa5d50b697bd
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect：執行個體的特殊考量
Azure AD Connect 最常搭配 Azure AD 和 Office 365 的全球執行個體。 但還有其他執行個體，而它們有不同的 URL 需求和其他特殊考量。

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Germany
[Microsoft Cloud Germany](http://www.microsoft.de/cloud-deutschland) 是由德國資料信託所運作的最高雲端。

| 要在 Proxy 伺服器中開啟的 URL |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| +憑證撤銷清單 |

當您登入 Azure AD 租用戶時，必須使用 onmicrosoft.de 網域中的帳戶。

Microsoft Cloud Germany 目前沒有的功能︰

* 無法使用 **Azure AD Connect Health**。
* 無法使用「自動更新」。
* **密碼回寫**適用於 Azure AD Connect 1.1.570.0 預覽版本和更新的版本。
* 無法使用其他 Azure AD Premium 服務。

## <a name="microsoft-azure-government-cloud"></a>Microsoft Azure Government 雲端
[Microsoft Azure Government 雲端](https://azure.microsoft.com/features/gov/) 是用於美國政府的雲端。

此雲端是以舊版的 DirSync 提供支援。 從 Azure AD Connect 組建 1.1.180 起，即支援新一代的雲端。 這一代使用僅限美國的端點，而且有不同的 URL 清單要在您的 Proxy 伺服器中開啟。

| 要在 Proxy 伺服器中開啟的 URL |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.gov.us.microsoftonline.com |
| +憑證撤銷清單 |

Azure AD Connect 無法自動偵測您的 Azure AD 租用戶是否位於 Government 雲端。 而您在安裝 Azure AD Connect 時需要採取下列動作。

1. 開始 Azure AD Connect 安裝。
2. 當您看見您應在其中接受 EULA 的第一頁時，請勿繼續進行，但讓安裝精靈保持執行。
3. 啟動 regedit 並將登錄機碼 `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` 變更為 `2` 值。
4. 回到 Azure AD Connect 安裝精靈，接受 EULA 並繼續進行。 在安裝期間，務必使用 **自訂組態** 安裝路徑 (而非快速安裝)。 然後如往常一樣繼續安裝。

Microsoft Azure Government 雲端目前沒有的功能︰

* 無法使用 **Azure AD Connect Health**。
* 無法使用「自動更新」。
* **密碼回寫**適用於 Azure AD Connect 1.1.570.0 預覽版本和更新的版本。
* 無法使用其他 Azure AD Premium 服務。

## <a name="next-steps"></a>後續步驟
深入了解 [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。
