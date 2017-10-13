---
title: "待用資料的 Azure 儲存體服務加密 | Microsoft Docs"
description: "使用 Azure 儲存體服務加密功能，在儲存資料時於服務端加密您的 Azure Blob 儲存體，在擷取資料時將它解密。"
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: tamram
ms.openlocfilehash: 772c36c8310a4bf30c62def507382fe74427e0d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>待用資料的 Azure 儲存體服務加密
待用資料的 Azure 儲存體服務加密 (SSE) 會協助您保護資料安全，以符合組織安全性和法規遵循承諾。 利用此功能，Azure 儲存體會自動加密資料，再保存到儲存體，以及在擷取之前解密。 以完全無感的方式處理所有加密、解密和金鑰管理。

下列章節提供有關如何使用儲存體服務加密功能的詳細指引，以及支援的案例和使用者體驗。

## <a name="overview"></a>概觀
Azure 儲存體提供一組完整的安全性功能，讓開發人員能夠共同建置安全應用程式。 您可以使用[用戶端加密](../storage-client-side-encryption.md)、HTTPs 或 SMB 3.0，在應用程式和 Azure 之間進行傳輸時保護資料的安全。 儲存體服務加密可提供待用加密，並以完全透明的方式處理加密、解密和金鑰管理。 所有資料都使用 256 位元 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (可用的最強區塊加密方式之一) 進行加密。

SSE 的運作方式是在將資料寫入「Azure 儲存體」時加密資料，並且可用於「Azure Blob 儲存體」和「檔案儲存體」。 它也適用於下列各項︰

* 標準儲存體：Blob 和檔案儲存體的一般用途帳戶及 Blob 儲存體帳戶
* 進階儲存體 
* 所有備援層級 (LRS、ZRS、GRS、RA-GRS)
* Azure Resource Manager 儲存體帳戶 (但不是傳統帳戶) 
* 所有區域。

若要深入了解，請參閱常見問題集。

若要針對儲存體帳戶啟用或停用儲存體服務加密，請登入 [Azure 入口網站](https://portal.azure.com)並選取儲存體帳戶。 在 [設定] 刀鋒視窗上，尋找此螢幕擷取畫面所示的 [Blob 服務] 區段，並按一下 [加密]。

![顯示 [加密] 選項的入口網站螢幕擷取畫面](./media/storage-service-encryption/image1.png)
<br/>*圖 1：為 Blob 服務啟用 SSE (步驟 1)*

![顯示 [加密] 選項的入口網站螢幕擷取畫面](./media/storage-service-encryption/image3.png)
<br/>*圖 2：為檔案服務啟用 SSE (步驟 1)*

按一下 [加密] 設定之後，您可以啟用或停用「儲存體服務加密」。

![顯示 [加密] 屬性的入口網站螢幕擷取畫面](./media/storage-service-encryption/image2.png)
<br/>*圖 3：為 Blob 和檔案服務啟用 SSE (步驟 2)*

## <a name="encryption-scenarios"></a>加密案例
可以在儲存體帳戶層級啟用儲存體服務加密。 啟用之後，客戶將選擇要加密的服務。 它支援下列客戶案例：

* 加密 Resource Manager 帳戶中的「Blob 儲存體」和「檔案儲存體」。
* 加密從傳統儲存體帳戶移轉至 Resource Manager 儲存體帳戶的「Blob 和檔案服務」。

SSE 有下列限制：

* 不支援傳統儲存體帳戶的加密。
* 現有資料 - SSE 只會加密啟用加密之後新建立的資料。 例如，如果您建立新的 Resource Manager 儲存體帳戶但是未開啟加密，然後您將 Blob 或封存的 VHD 上傳至該儲存體帳戶並開啟 SSE，則那些 Blob 將不會加密，除非它們被重新寫入或複製。
* Marketplace 支援 - 允許使用 [Azure 入口網站](https://portal.azure.com)、PowerShell 及 Azure CLI 來加密從 Marketplace 建立的 VM。 VHD 基底影像將保持未加密狀態；不過，在 VM 啟動之後完成的任何寫入將會加密。
* 系統不會將資料表和佇列資料加密。

## <a name="getting-started"></a>開始使用
### <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>步驟 1：[建立新的儲存體帳戶](../storage-create-storage-account.md)。
### <a name="step-2-enable-encryption"></a>步驟 2︰啟用加密。
您可以使用 [Azure 入口網站](https://portal.azure.com)來啟用加密。

> [!NOTE]
> 如果您想要以程式設計方式啟用或停用儲存體帳戶上的儲存體服務加密，您可以使用 [Azure 儲存體資源提供者 REST API](https://msdn.microsoft.com/library/azure/mt163683.aspx)、[適用於 .NET 的儲存體資源提供者用戶端程式庫](https://msdn.microsoft.com/library/azure/mt131037.aspx)、[Azure PowerShell](/powershell/azureps-cmdlets-docs) 或 [Azure CLI](../storage-azure-cli.md)。
> 
> 

### <a name="step-3-copy-data-to-storage-account"></a>步驟 3︰將資料複製到儲存體帳戶
若已針對 Blob 服務啟用 SSE，將會加密寫入到該儲存體帳戶的所有 Blob。 已經位於該儲存體帳戶的任何 Blob 直到改寫後，才會加密。 您可以將資料從一個儲存體帳戶複製到已啟用 SSE 的儲存體帳戶，或甚至啟用 SSE 並在容器之間複製 Blob，以確保先前的資料已加密。 您也可以使用下列任何工具來完成此作業。 對於檔案儲存體，行為也是如此。

#### <a name="using-azcopy"></a>使用 AzCopy
AzCopy 是個 Windows 命令列公用程式，專為使用簡單命令高效率地將資料複製到和複製出 Microsoft Azure Blob、檔案和表格儲存體所設計。 您可以使用此公用程式將 Blob 或檔案從某個儲存體帳戶複製到另一個已啟用 SSE 的儲存體帳戶。 

若要深入了解，請參閱[使用 AzCopy 命令列公用程式傳輸資料](storage-use-azcopy.md)。

#### <a name="using-smb"></a>使用 SMB
Azure 檔案服務可在雲端中使用標準的 SMB 通訊協定提供檔案共用。 您可以從內部部署或 Azure 中的用戶端掛接檔案共用。 一旦掛接，您就能使用如 Robocopy 的工具來將檔案複製到 Azure 檔案共用。 如需詳細資訊，請參閱[如何在 Windows 上掛接 Azure 檔案共用](../files/storage-how-to-use-files-windows.md)與[如何在 Linux 上掛接 Azure 檔案共用](../files/storage-how-to-use-files-linux.md)。


#### <a name="using-the-storage-client-libraries"></a>使用儲存體用戶端程式庫
您可以使用我們豐富的儲存體用戶端程式庫集合 (包括 .NET、C++、Java、Android、Node.js、PHP、Python 和 Ruby)，從 Blob 儲存體來回複製 Blob 或檔案資料或在儲存體帳戶之間進行複製。

若要深入了解，請參閱[以 .NET 開始使用 Azure Blob 儲存體](../blobs/storage-dotnet-how-to-use-blobs.md)。

#### <a name="using-a-storage-explorer"></a>使用儲存體總管
您可以使用儲存體總管來建立儲存體帳戶、上傳和下載資料、檢視 blob 的內容，以及瀏覽目錄。 您可以使用其中一種將 blob 上傳至儲存體帳戶，並且啟用加密。 使用某些儲存體總管，您也可以將資料從現有的 Blob 儲存體複製到儲存體帳戶中的不同容器或已啟用 SSE 的新儲存體帳戶。

若要深入了解，請參閱 [Azure 儲存體總管](../storage-explorers.md)。

### <a name="step-4-query-the-status-of-the-encrypted-data"></a>步驟 4︰查詢加密資料的狀態
已部署儲存體用戶端程式庫的更新版本，可讓您查詢物件的狀態，以判斷它是否加密。 這目前只能供 Blob 儲存體使用。 針對檔案儲存體的支援已在我們的藍圖規劃中。 

在此同時，您可以呼叫[取得帳戶屬性](https://msdn.microsoft.com/library/azure/mt163553.aspx)來確認儲存體帳戶是否已啟用加密，或在 Azure 入口網站中檢視儲存體帳戶屬性。

## <a name="encryption-and-decryption-workflow"></a>加密和解密工作流程
以下是加密/解密工作流程的簡短描述：

* 客戶會在儲存體帳戶上啟用加密。
* 當客戶將新資料 (放置 Blob、放置區塊、放置頁面、放置檔案等等) 寫入至 Blob 或檔案儲存體時，每次寫入的內容都會使用 256 位元 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)進行加密，這是可用的最強區塊加密方式之一。
* 當客戶需要存取資料 (取得 Blob 等) 時，資料會在傳回給使用者之前自動解密。
* 如果已停用加密，就不會再加密新的寫入，現有加密資料在使用者重新寫入之前會保持加密。 啟用加密時，系統將會加密寫入至 Blob 或檔案儲存體的內容。 資料的狀態在使用者於啟用/停用儲存體帳戶的加密之間切換時不會變更。
* 所有加密金鑰會由 Microsoft 儲存、加密及管理。

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>待用資料的儲存體服務加密的常見問題集
**問：我有現有的傳統儲存體帳戶。可以在其上啟用 SSE 嗎？**

答︰否，只有 Resource Manager 儲存體帳戶支援 SSE。

**問：我要如何在我的傳統儲存體帳戶中加密資料？**

答︰您可以建立新的 Resource Manager 儲存體帳戶，並且使用 [AzCopy](storage-use-azcopy.md) ，從現有的傳統儲存體帳戶將資料複製到新建立的 Resource Manager 儲存體帳戶。 

如果您將傳統儲存體帳戶移轉至 Resource Manager 儲存體帳戶，此作業會立即進行，它會變更您帳戶的類型，但不會影響您現有的資料。 只有在啟用加密之後，才會加密所有新資料。 如需詳細資訊，請參閱 [Platform Supported Migration of IaaS Resources from Classic to Resource Manager (從傳統移轉至 Resource Manager 的平台支援 IaaS 資源移轉)](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/)。 請注意，只有針對 Blob 和「檔案」服務才支援此功能。

**問：我有現有的 Resource Manager 儲存體帳戶。可以在其上啟用 SSE 嗎？**

答︰是，但只會加密新寫入的資料。 並不會返回及加密已經存在的資料。 尚未針對檔案儲存體預覽支援此功能。

**問︰我想要加密現有 Resource Manager 儲存體帳戶中目前的資料？**

答︰您可以在 Resource Manager 儲存體帳戶中隨時啟用 SSE。 不過，不會加密已經存在的資料。 若要加密現有的資料，您可以將它們複製到另一個名稱或另一個容器，然後移除未加密的版本。

**問︰我正在使用進階儲存體，我是否可以使用 SSE？**

答︰是，SSE 支援標準儲存體和進階儲存體。  「檔案服務」不支援「進階儲存體」。

**問︰如果我建立新的儲存體帳戶，並啟用 SSE，然後使用該儲存體帳戶建立新的 VM，是否表示我的 VM 會加密？**

答： 會。 使用新的儲存體帳戶建立的任何磁碟將會加密，只要它們是在啟用 SSE 之後建立。 如果 VM 是使用 Azure Market Place 建立，VHD 基底影像將保持未加密狀態；不過，在 VM 啟動之後完成的任何寫入將會加密。

**問︰是否可以使用 Azure PowerShell 和 Azure CLI 建立新的儲存體帳戶並且啟用 SSE？**

答： 會。

**問︰如果已啟用 SSE，Azure 儲存體的成本會多出多少？**

答：沒有任何額外成本。

**問︰誰負責管理加密金鑰？**

答︰金鑰是由 Microsoft 管理。

**問︰我可以使用自己的加密金鑰嗎？**

答︰我們正在努力提供功能讓客戶使用他們自己的加密金鑰。

**問︰是否可以撤銷加密金鑰的存取權？**

答︰目前不行。金鑰完全是由 Microsoft 管理。

**問︰我建立新的儲存體帳戶時是否預設啟用 SSE？**

答：預設未啟用 SSE；您可以使用 Azure 入口網站來啟用它。 您也可以以程式設計方式，使用儲存體資源提供者 REST API 來啟用這項功能。

**問︰這項功能與 Azure 磁碟加密有什麼不同？**

答︰這項功能是用來加密 Azure Blob 儲存體中的資料。 Azure 磁碟加密可用來加密作業系統和 IaaS VM 中的資料磁碟。 如需詳細資訊，請參閱我們的[儲存體安全性指南](../storage-security-guide.md)。

**問︰如果我啟用 SSE，然後在磁碟上進入並啟用 Azure 磁碟加密？**

答︰這會順暢地運作。 這兩種方法都會加密您的資料。

**問︰我的儲存體帳戶設定為異地備援複寫。如果啟用 SSE，我的備援複本是否也會加密？**

答︰是，會加密儲存體帳戶的所有複本，並且支援所有備援選項 - 本地備援儲存體 (LRS)、區域備援儲存體 (ZRS)、異地備援儲存體 (GRS) 和讀取權限異地備援儲存體 (RA-GRS)。

**問︰我無法在我的儲存體帳戶上啟用加密。**

答︰這是 Resource Manager 儲存體帳戶嗎？ 不支援傳統儲存體帳戶。 

**問︰是否只有在特定區域中允許 SSE？**

答︰針對 Blob 儲存體，所有區域均可使用 SSE。 對於檔案儲存體，請檢查 [可用性] 區段。 

**問︰如果我有任何問題或想要提供意見反應，要如何連絡相關人員？**

答︰如果有儲存體服務加密的相關問題，請連絡 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) 。

## <a name="next-steps"></a>後續步驟
Azure 儲存體提供一組完整的安全性功能，讓開發人員能夠共同建置安全應用程式。 如需詳細資訊，請參閱[儲存體安全性指南](../storage-security-guide.md)。

