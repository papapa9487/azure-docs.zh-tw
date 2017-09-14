---
title: "保護 Azure Service Fabric 叢集 | Microsoft Docs"
description: "深入了解 Azure Service Fabric 叢集的安全性情節，以及可用來加以實作的各種技術。"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: chackdan
ms.translationtype: HT
ms.sourcegitcommit: 4eb426b14ec72aaa79268840f23a39b15fee8982
ms.openlocfilehash: e75929ee5d3f57af77c66910cc294a7c0fb6629a
ms.contentlocale: zh-tw
ms.lasthandoff: 09/06/2017

---
# <a name="service-fabric-cluster-security-scenarios"></a>Service Fabric 叢集安全性案例
Azure Service Fabric 叢集是您擁有的資源。 您必須保護叢集，從而協助避免未經授權的使用者與它們連線。 在叢集上執行生產工作負載時，安全的叢集尤其重要。 雖然可以建立不安全的叢集，但如果叢集向公用網際網路公開管理端點，匿名使用者就可以連線叢集。 

本文是 Azure 叢集和獨立式叢集的安全性情節的概觀，以及可用來實作它們的各種技術：

* 節點對節點安全性
* 用戶端對節點安全性
* 角色型存取控制 (RBAC)

## <a name="node-to-node-security"></a>節點對節點安全性
節點對節點安全性會協助保護叢集中 VM 與電腦之間的通訊。 此安全性情節可確保只有獲得授權加入叢集的電腦可以參與裝載應用程式和叢集中的服務。

![節點對節點通訊的圖表][Node-to-Node]

在 Azure 上執行的叢集和在 Windows 上執行的獨立叢集都可以使用[憑證安全性](https://msdn.microsoft.com/library/ff649801.aspx)或針對 Windows Server 電腦使用 [Windows 安全性](https://msdn.microsoft.com/library/ff649396.aspx)。

### <a name="node-to-node-certificate-security"></a>節點對節點憑證安全性
Service Fabric 會使用您建立叢集時，在節點類型組態中指定的 X.509 伺服器憑證。 在本文結尾處，您可以看到這些憑證是什麼，以及如何取得或建立這些憑證的快速概觀。

建立叢集時，可在 Azure 入口網站中，使用 Azure Resource Manager 範本或使用獨立 JSON 範本設定憑證安全性。 您可以設定用於憑證變換的主要憑證和選用次要憑證。 您設定的主要和次要憑證，應該不同於您為[用戶端對節點安全性](#client-to-node-security)設定的系統管理用戶端憑證和唯讀用戶端憑證。

若要了解如何在適用於 Azure 的叢集中設定憑證安全性，請參閱[使用 Azure Resource Manager 範本來設定叢集](service-fabric-cluster-creation-via-arm.md)。

若要了解如何在適用於獨立 Windows Server 叢集的叢集中設定憑證安全性，請參閱[使用 X.509 憑證來保護 Windows 上的獨立叢集](service-fabric-windows-cluster-x509-security.md)。

### <a name="node-to-node-windows-security"></a>節點對節點 Windows 安全性
若要了解如何設定適用於獨立 Windows Server 叢集的憑證安全性，請參閱[使用 Windows 安全性保護 Windows 上的獨立叢集](service-fabric-windows-cluster-windows-security.md)。

## <a name="client-to-node-security"></a>用戶端對節點安全性
用戶端對節點安全性會驗證用戶端，並協助保護用戶端與叢集中個別節點之間的通訊。 這個類型的安全性可協助確保只有獲得授權的使用者能存取叢集與叢集上部署的應用程式。 用戶端是透過其 Windows 安全性認證或其憑證安全性認證唯一識別。

![用戶端對節點通訊的圖表][Client-to-Node]

在 Azure 上執行的叢集或在 Windows 上執行的獨立叢集可以使用[憑證安全性](https://msdn.microsoft.com/library/ff649801.aspx)或 [Windows 安全性](https://msdn.microsoft.com/library/ff649396.aspx)。

### <a name="client-to-node-certificate-security"></a>用戶端對節點憑證安全性
建立叢集時，可在 Azure 入口網站中，使用 Resource Manager 範本或使用獨立 JSON 範本來設定用戶端對節點憑證安全性。 若要建立憑證，請指定管理員用戶端憑證或使用者用戶端憑證。 您指定的系統管理用戶端憑證和使用者用戶端憑證，應不同於您針對[節點對節點安全性](#node-to-node-security)指定的主要和次要憑證，以作為最佳做法。 節點對節點安全性的叢集憑證預設會新增到允許的用戶端系統管理員憑證清單中。

用戶端如果是使用系統管理憑證來連接到叢集，就會擁有管理功能的完整存取權。 用戶端如果是使用唯讀使用者用戶端憑證來連接到叢集，則只會擁有管理功能的唯讀存取權。 這些憑證是用於我們在本文稍後所述的 RBAC。

若要了解如何在適用於 Azure 的叢集中設定憑證安全性，請參閱[使用 Azure Resource Manager 範本來設定叢集](service-fabric-cluster-creation-via-arm.md)。

若要了解如何在適用於獨立 Windows Server 叢集的叢集中設定憑證安全性，請參閱[使用 X.509 憑證來保護 Windows 上的獨立叢集](service-fabric-windows-cluster-x509-security.md)。

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Azure 上的用戶端對節點 Azure Active Directory 安全性
針對在 Azure 上執行的叢集，您也可以使用 Azure Active Directory (Azure AD) 來保護對管理端點的存取。 若要了解如何建立必要的 Azure AD 構件、如何在建立叢集時填入這些構件，以及後續如何連線到這些叢集，請參閱[使用 Azure Resource Manager 範本來設定叢集](service-fabric-cluster-creation-via-arm.md)。

## <a name="security-recommendations"></a>安全性建議
對於 Azure 叢集，建議您針對節點對節點安全性使用 Azure AD 安全性來驗證用戶端和憑證。

對於獨立 Windows Server 叢集，如果您有 Windows Server 2012 R2 和 Windows Active Directory，建議您使用 Windows 安全性與群組受管理的服務帳戶。 否則，使用 Windows 安全性與 Windows 帳戶。

## <a name="role-based-access-control-rbac"></a>角色型存取控制 (RBAC)
您可以使用存取控制來限制存取不同使用者群組的特定叢集作業。 這樣有助於讓叢集更安全。 針對連線到叢集的用戶端，支援兩種存取控制類型：系統管理員角色和使用者角色。

指派為系統管理員角色的使用者可以完整存取管理功能，包括讀取和寫入功能。 獲指派使用者角色的使用者預設只具有管理功能的讀取存取權 (例如查詢功能)。 它們也可以解析應用程式和服務。

當您建立叢集時，請設定系統管理員和使用者用戶端角色。 為每種角色類型提供個別的身分識別 (例如，使用憑證或 Azure AD)，從而 指派角色。 如需有關預設存取控制設定及如何變更預設設定的詳細資訊，請參閱[適用於 Service Fabric 用戶端的角色型存取控制](service-fabric-cluster-security-roles.md)。

## <a name="x509-certificates-and-service-fabric"></a>X.509 憑證和 Service Fabric
X509 數位憑證通常用來驗證用戶端與伺服器。 它們也用來加密及數位簽署訊息。 如需關於 X.509 數位憑證的詳細資訊，請參閱[使用憑證](http://msdn.microsoft.com/library/ms731899.aspx)。

一些需要考量的重要事項︰

* 若要建立執行生產環境工作負載之叢集的憑證，請使用已正確設定的 Windows Server 憑證服務，或是已核准[憑證授權單位 (CA)](https://en.wikipedia.org/wiki/Certificate_authority)中的服務。
* 請勿使用您透過諸如 MakeCert.exe 等工具，在生產環境中建立的任何暫存或測試憑證。
* 您可以使用自我簽署的憑證，但只能在測試叢集中。 請勿在生產環境中使用自我簽署的憑證。

### <a name="server-x509-certificates"></a>伺服器的 X.509 憑證
伺服器憑證的主要工作是向用戶端驗證伺服器 (節點) 或是向伺服器 (節點) 驗證伺服器 (節點)。 當用戶端或節點驗證節點時，其中一項初始檢查是 [主體] 欄位中的一般名稱值。 此一般名稱或其中一個憑證主體別名 (SAN) 必須存在於允許的一般名稱清單中。

若要深入了解如何產生具有 SAN 的憑證，請參閱[如何將主體別名新增至安全 LDAP 憑證](http://support.microsoft.com/kb/931351)。

[主體] 欄位可以有多個值。 每個值前面都會加上起首字母來表示實值類型。 通常，起首字母是 **CN** (針對一般名稱)；例如 **CN = www.contoso.com**。[主體] 欄位可以是空白。 如果選擇性 [主體別名] 欄位已填入資料，此欄位就必須具有憑證的一般名稱，以及每個 SAN 的一個項目。 這些會以 **DNS 名稱**值輸入。

憑證的 [預定目的] 欄位值應包含適當的值，例如**伺服器驗證**或**用戶端驗證**。

### <a name="client-x509-certificates"></a>用戶端 X.509 憑證
用戶端憑證通常不是由第三方 CA 所發行。 然而，目前使用者位置的個人存放區通常包含由根授權單位所放置的用戶端憑證，其**預定目的**為**用戶端驗證**。 用戶端可以在需要相互驗證時使用這個憑證。

> [!NOTE]
> Service Fabric 叢集上的所有管理作業都需要伺服器憑證。 用戶端憑證無法用於管理作業。

## <a name="next-steps"></a>後續步驟
* [使用 Resource Manager 範本在 Azure 中建立叢集](service-fabric-cluster-creation-via-arm.md) 
* [使用 Azure 入口網站建立叢集](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

