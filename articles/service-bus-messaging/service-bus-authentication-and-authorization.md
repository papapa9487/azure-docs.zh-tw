---
title: "Azure 服務匯流排驗證和授權 | Microsoft Docs"
description: "使用共用存取簽章 (SAS) 驗證向服務匯流排驗證應用程式。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 28fb41499c919e5006f1be7daa97610c2a0583af
ms.contentlocale: zh-tw
ms.lasthandoff: 08/10/2017

---
# <a name="service-bus-authentication-and-authorization"></a>服務匯流排驗證和授權

應用程式可以使用共用存取簽章 (SAS) 驗證向 Azure 服務匯流排進行驗證。 共用存取簽章 (SAS) 驗證可讓應用程式使用在命名空間或在與特定權限相關聯的實體上設定的存取金鑰，向服務匯流排進行驗證。 您可以接著使用此金鑰來產生共用存取簽章權杖，以便用戶端用來向服務匯流排進行驗證。

> [!IMPORTANT]
> 您應該使用 SAS 而不是 Azure Active Directory 存取控制 (也稱為存取控制服務或 ACS)，因為 ACS 已被取代。 SAS 可為服務匯流排提供簡單、有彈性且容易使用的驗證配置。 在應用程式不需要管理已授權「使用者」概念的情況下，可以使用 SAS。 如需詳細資訊，請參閱 [此部落格文章](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/)。

## <a name="shared-access-signature-authentication"></a>共用存取簽章驗證

[SAS 驗證](service-bus-sas.md) 可讓您授與使用者具有特定權限之服務匯流排資源的存取權。 服務匯流排中的 SAS 驗證牽涉到在服務匯流排資源上設定具有相關權限的密碼編譯金鑰。 接著用戶端可以藉由提出 SAS 權杖 (其中包含正在存取的資源 URI 及利用設定金鑰簽署的到期日) 存取該資源。

您可以在服務匯流排命名空間上設定 SAS 的金鑰。 金鑰套用至該命名空間中的所有訊息實體。 您也可以在服務匯流排佇列和主題上設定金鑰。 [Azure 轉送](../service-bus-relay/relay-authentication-and-authorization.md)上也支援 SAS。

若要使用 SAS，您可以在命名空間、佇列或主題上設定 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 物件。 此規則由下列元素組成：

* *KeyName* 。
* *PrimaryKey* 是用來簽署/驗證 SAS 權杖的密碼編譯金鑰。
* *SecondaryKey* 是用來簽署/驗證 SAS 權杖的密碼編譯金鑰。
* *權限* 表示接聽、傳送或管理授與權限的集合。

在命名空間層級設定的授權規則可以授與命名空間中所有實體的存取權給具備使用對應金鑰簽署之權杖的用戶端。 在服務匯流排命名空間、佇列或主題上最多可以設定 12 條這類授權規則。 根據預設，具備所有權限的 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 會在第一次佈建時為每個命名空間設定。

若要存取實體，用戶端需要使用特定 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)產生的 SAS 權杖。 SAS 權杖乃是使用資源字串的 HMAC-SHA256 所產生，該字串由宣告存取的資源 URI 以及具備與授權規則相關聯之密碼編譯金鑰的過期所組成。

服務匯流排的 SAS 驗證支援包含在 Azure .NET SDK 2.0 版或更新版本中。 SAS 包括 [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)的支援。 所有接受連接字串做為參數的 API 包括 SAS 連接字串的支援。

## <a name="next-steps"></a>後續步驟

- 如需 SAS 的詳細資料，請繼續閱讀[使用共用存取簽章的服務匯流排驗證](service-bus-sas.md)。
- [變更為已啟用 ACS 的命名空間。](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/)
- 如需 Azure 轉送驗證和授權的相關對應資訊，請參閱 [Azure 轉送驗證和授權](../service-bus-relay/relay-authentication-and-authorization.md)。 


