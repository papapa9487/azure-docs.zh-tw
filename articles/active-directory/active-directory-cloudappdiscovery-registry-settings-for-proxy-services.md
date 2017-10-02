---
title: "Proxy 服務的雲端應用程式探索登錄設定 | Microsoft Docs"
description: "本主題的目標是為您提供在執行 Cloud App Discovery 代理程式的電腦上設定必要連接埠所需的步驟。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 8d78e925-e331-40ba-904a-e4ef14260cac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: nigu
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: b8584809b76d6be12a6f489f0bb819081d1803d2
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>Proxy 服務的 Cloud App Discovery 登錄設定
本主題的目的是要告訴您如何在執行 Cloud App Discovery 代理程式的電腦上設定所需連接埠。 根據預設，Cloud App Discovery 代理程式設定為僅使用連接埠 80 或 443。 如果您計劃要在具有使用自訂連接埠 (既不是 80 也不是 443) 的 Proxy 伺服器的環境中安裝 Cloud App Discovery，您必須設定代理程式使用此連接埠。 設定是以登錄機碼為基礎。

> [!TIP] 
> 請查看 Azure Active Directory (Azure AD) 中目前是無代理程式 Cloud App Discovery 的增強功能，這些增強功能是藉由[與 Microsoft Cloud App Security 整合](https://portal.cloudappsecurity.com)而獲得增強。

## <a name="modify-the-port-used-by-the-computer-running-the-cloud-app-discovery-agent"></a>修改執行 Cloud App Discovery 代理程式的電腦所使用的連接埠

1. 啟動 [登錄編輯器]。
  ![Run](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)
2. 導覽至或建立下列登錄機碼：**HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud App Discovery\Endpoint**
3. 建立新的**多字串**值，稱為**連接埠**。 
  ![](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)
4. 若要開啟 [編輯多字串] 對話方塊中，按兩下 [連接埠] 值。
5. 在 [數值資料] 中輸入以下的值，然後新增您組織所使用的所有自訂連接埠： <br><br>
   **80** <br>
   **8080** <br>
   **8118** <br>
   **8888** <br>
   **81** <br>
   **12080** <br>
   **6999** <br>
   **30606** <br>
   **31595** <br>
   **4080** <br>
   **443** <br>
   **1110** <br><br>
   ![編輯多字串](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)
6. 按一下 [確定]，關閉 [編輯多字串] 對話方塊。

## <a name="next-steps"></a>後續步驟

* [如何探索組織內使用未經批准的雲端應用程式](active-directory-cloudappdiscovery-whatis.md) 


