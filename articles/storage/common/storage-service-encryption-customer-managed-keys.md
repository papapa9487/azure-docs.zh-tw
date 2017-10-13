---
title: "在 Azure Key Vault 中使用客戶管理的金鑰進行 Azure 儲存體服務加密 | Microsoft 文件"
description: "使用 Azure 儲存體服務加密功能，在儲存資料時於服務端加密您的 Azure Blob 儲存體，並在擷取資料時使用客戶管理的金鑰將它解密。"
services: storage
documentationcenter: .net
author: lakasa
manager: jahogg
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: lakasa
ms.openlocfilehash: 6d1e6752fb631114f5be06cb27a63e40547bf6ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>在 Azure Key Vault 中使用客戶管理金鑰進行儲存體服務加密

Microsoft Azure 承諾協助您保護資料安全，以符合組織安全性和合規性承諾。  您可以透過一種方式保護待用資料，那就是使用「儲存服務加密」(SSE)，它會在將資料寫入到儲存體時自動加密資料，並在擷取時解密資料。 加密與解密是自動進行的，過程完全透明，並且使用 256 位元 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) \(英文\)，這是目前可用最強大的區塊編碼器之一。

您可以搭配 SSE 使用 Microsoft 管理的加密金鑰，或使用自己的加密金鑰。 本文章討論後者。 如需有關使用 Microsoft 管理之金鑰的詳細資訊，或有關 SSE 的一般資訊，請參閱[待用資料的儲存體服務加密](../storage-service-encryption.md)。

為了提供使用自己的加密金鑰之能力，Blob 儲存體的 SSE 已與 Azure Key Vault (AKV) 整合。 您可以建立自己的加密金鑰，並將其儲存在 AKV 中，或者可以使用 AKV 的 API 來產生加密金鑰。 AKV 不僅允許您管理及控制金鑰，也可以讓您稽核金鑰的使用狀況。 

為什麼要建立您自己的金鑰？ 它可賦予您更大的彈性，讓您得以建立、旋轉、停用及定義存取控制。 它也可讓您稽核用來保護資料的加密金鑰。

## <a name="sse-with-customer-managed-keys-preview"></a>使用客戶管理之金鑰的 SSE 預覽

此功能目前為預覽狀態。 若要使用此功能，您需要建立新的儲存體帳戶。 您可以建立新金鑰保存庫與金鑰，或者可以使用現有金鑰保存庫與金鑰。 儲存體帳戶與金鑰保存庫必須位於相同區域，但可位於不同的訂用帳戶中。

若要參與預覽，請連絡 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com)。我們將提供參與預覽的特殊連結。

若要深入了解，請參閱[常見問題集](#frequently-asked-questions-about-storage-service-encryption-for-data-at-rest)。

> [!IMPORTANT]
> 您必須註冊預覽，才能繼續此文章中的後續步驟。 若無預覽存取權，您將無法在入口網站中啟用此功能。

## <a name="getting-started"></a>開始使用
## <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>步驟 1：[建立新的儲存體帳戶](../storage-create-storage-account.md)

## <a name="step-2-enable-encryption"></a>步驟 2︰啟用加密
您可以使用 [Azure 入口網站](https://portal.azure.com)來啟用儲存體帳戶的 SSE。 在儲存體帳戶的 [設定] 刀鋒視窗上，尋找如下圖所示的 [Blob 服務] 區段，並按一下 [加密]。

![顯示 [加密] 選項的入口網站螢幕擷取畫面](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)
<br/>*為 Blob 服務啟用 SSE*

如果您想要以程式設計方式啟用或停用儲存體帳戶上的儲存體服務加密，您可以使用 [Azure 儲存體資源提供者 REST API](https://docs.microsoft.com/en-us/rest/api/storagerp/?redirectedfrom=MSDN)、[適用於 .NET 的儲存體資源提供者用戶端程式庫](https://docs.microsoft.com/en-us/dotnet/api/?redirectedfrom=MSDN)、[Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-4.0.0) 或 [Azure CLI](https://docs.microsoft.com/en-us/azure/storage/storage-azure-cli)。

在這個畫面上，如果沒有看到 [使用您自己的金鑰] 核取方塊，即表示您尚未獲准預覽。 請傳送電子郵件至 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) 並要求核准。

![顯示 [加密預覽] 的入口網站螢幕擷取畫面](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

根據預設，SSE 會使用 Microsoft 管理的金鑰。 若要使用您自己的金鑰，請選取該方塊。 然後您可以指定金鑰 URI，或從選擇器中選取金鑰和 Key Vault。

## <a name="step-3-select-your-key"></a>步驟 3：選取您的金鑰

![顯示 [使用您自己的金鑰加密] 選項的入口網站螢幕擷取畫面](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)

![顯示 [透過輸入金鑰 URI 來加密] 選項的入口網站螢幕擷取畫面](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

如果儲存體帳戶沒有 Key Vault 的存取權，您可以使用 Azure Powershell 執行下列命令，授與儲存體帳戶對於所需金鑰保存庫的存取權。

![顯示金鑰保存庫存取遭拒的入口網站螢幕擷取畫面](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

您也可以移至 Azure 入口網站中的 Azure Key Vault，並將存取權授與儲存體帳戶，以透過 Azure 入口網站授與存取權。

## <a name="step-4-copy-data-to-storage-account"></a>步驟 4︰將資料複製到儲存體帳戶
如果您想要將資料傳輸至新的儲存體帳戶，以對其進行加密，請參閱[待用資料的儲存體服務加密快速入門步驟 3](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption#step-3-copy-data-to-storage-account) \(英文\)。

## <a name="step-5-query-the-status-of-the-encrypted-data"></a>步驟 5︰查詢加密資料的狀態
若要查詢加密資料的狀態，請參閱[待用資料的儲存體服務加密快速入門步驟 4](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption#step-4-query-the-status-of-the-encrypted-data) \(英文\)。

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>待用資料的儲存體服務加密的常見問題集
**問︰我正在使用進階儲存體，是否可以使用具有客戶管理之金鑰的 SSE？**

答：是，在標準儲存體和進階儲存體上都支援搭配 Microsoft 管理與客戶管理之金鑰使用 SSE。 

**問：是否可以搭配客戶管理的金鑰 \(使用 Azure PowerShell 和 Azure CLI 啟用\) 使用 SSE 建立新儲存體帳戶？**

答：是。

**問：如果啟用使用客戶管理金鑰的 SSE，Azure 儲存體的成本會多出多少？**

答：使用 Azure Key Vault 有相關成本。 如需詳細資訊，請瀏覽 [Key Vault 定價](https://azure.microsoft.com/en-us/pricing/details/key-vault/)。 使用 SSE 不會產生額外的費用。

**問︰是否可以撤銷加密金鑰的存取權？**

答：是，您可以隨時撤銷存取權。 有幾種方法可以撤銷對金鑰的存取權。 如需詳細資訊，請參閱 [Azure Key Vault PowerShell](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/?view=azurermps-4.0.0) \(英文\) 與 [Azure Key Vault CLI](https://docs.microsoft.com/en-us/cli/azure/keyvault) \(英文\)。 撤銷存取權將有效封鎖對儲存體帳戶中所有 Blob 的存取權，因為 Azure 儲存體將無法存取帳戶加密金鑰。

**問：是否可以在不同的區域建立儲存體帳戶和金鑰？**

答：否，儲存體帳戶和金鑰保存庫/金鑰必須位於相同區域中。 

**問：建立儲存體帳戶時是否可以啟用使用客戶管理之金鑰的 SSE？**

答：否。 如果您在建立儲存體帳戶時啟用 SSE，便只能使用 Microsoft 管理的金鑰。 如果您想要使用客戶管理的金鑰，則必須更新儲存體帳戶屬性。 您可以使用 REST 或其中一個儲存體用戶端程式庫來以程式設計方式更新儲存體帳戶，或在建立帳戶之後使用 Azure 入口網站更新儲存體帳戶屬性。

**問：使用具有客戶管理之金鑰的 SSE 時是否可以停用加密？**

答：否，使用具有客戶管理之金鑰的 SSE 時無法停用加密。 若要停用加密，您必須改為使用 Microsoft 管理的金鑰。 您可以使用 Azure 入口網站或 PowerShell 來執行此動作。

**問︰我建立新的儲存體帳戶時是否預設啟用 SSE？**

答：預設未啟用 SSE；您可以使用 Azure 入口網站來啟用它。 您也能以程式設計方式，使用儲存體資源提供者 REST API 來啟用此功能。 

**問︰我無法在我的儲存體帳戶上啟用加密。**

答︰這是 Resource Manager 儲存體帳戶嗎？ 不支援傳統儲存體帳戶。 也只能對新建立的資源管理員儲存體帳戶啟用使用客戶管理之金鑰的 SSE。

**問：是否只在特定區域中允許使用客戶管理之金鑰的 SSE？**

答：針對這個預覽，只能在 Blob 儲存體的特定區域中使用 SSE。 請寄電子郵件至 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com)，查詢預覽的可用性與詳細資料。 

**問︰如果我有任何問題或想要提供意見反應，要如何連絡相關人員？**

答︰如果有儲存體服務加密的相關問題，請連絡 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com)。 

## <a name="next-steps"></a>後續步驟

*   如需協助開發人員建置安全應用程式之全方位安全功能的詳細資訊，請參閱[儲存體安全性指南](https://docs.microsoft.com/en-us/azure/storage/storage-security-guide)。
*   如需 Azure Key Vault 的概觀資訊，請參閱[什麼是 Azure Key Vault？](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis)
*   若要開始使用 Azure Key Vault，請參閱[開始使用 Azure Key Vault](../../key-vault/key-vault-get-started.md)。
