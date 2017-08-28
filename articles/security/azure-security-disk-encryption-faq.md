---
title: "Azure 磁碟加密常見問題集 | Microsoft Docs"
description: "本文提供 Windows 和 Linux IaaS VM 適用的 Microsoft Azure 磁碟加密常見問題集的解答。"
services: security
documentationcenter: na
author: deventiwari
manager: avibm
editor: yuridio
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: devtiw
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: 0d15bf42c156ea7a72c54d690f4016877913efe4
ms.contentlocale: zh-tw
ms.lasthandoff: 08/12/2017

---
# <a name="azure-disk-encryption-frequently-asked-questions-faq"></a>Azure 磁碟加密常見問題集 (FAQ)

此常見問題集中會解答 Windows 和 Linux IaaS VM 適用的 Azure 磁碟加密的相關問題，如需這項服務的相關資訊，請參閱 [Windows 和 Linux IaaS VM 適用的 Azure 磁碟加密](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)。

## <a name="general-questions"></a>一般問題
**問：** Azure 磁碟加密在 GA 中的哪一個區域？

**答：**Windows 和 Linux IaaS VM 適用的 Azure 磁碟加密會在所有 Azure 公用區域的 GA 中提供。

**問：**Azure 磁碟加密有哪些使用者體驗？

**答：**Azure 磁碟加密 GA 支援 Azure Resource Manager 範本、Azure PowerShell、Azure CLI。 這為您提供很大的彈性，因為您會有三個不同選項可啟用您 IaaS VM 的磁碟加密。 Azure 磁碟加密部署情節和經驗中提供使用者體驗及逐步指引的更多詳細資料。

**問：**Azure 磁碟加密如何收費？

**答：**使用 Azure 磁碟加密來加密 VM 磁碟完全免費。

**問：**可以使用 Azure 磁碟加密搭配哪些虛擬機器層？

**答：**Azure 磁碟加密僅適用於標準層 VM，包括 [A、D、DS、G、GS、F](https://azure.microsoft.com/pricing/details/virtual-machines/) 等系列，IaaS VM 包括使用進階儲存體的 VM。 它並不適用於基本層 VM。

**問：**Azure 磁碟加密支援哪些 Linux 散發套件？

**答：**在下列 Linux 伺服器的散發套件和版本上支援 Azure 磁碟加密︰

| Linux 散發套件 | 版本 | 支援加密的磁碟區類型|
| --- | --- |--- |
| Ubuntu | 16.04-DAILY-LTS | 作業系統和資料磁碟 |
| Ubuntu | 14.04.5-DAILY-LTS | 作業系統和資料磁碟 |
| RHEL | 7.3 | 作業系統和資料磁碟 |
| RHEL | 7.2 | 作業系統和資料磁碟 |
| RHEL | 6.8 | 作業系統和資料磁碟 |
| RHEL | 6.7 | 資料磁碟 |
| CentOS | 7.3 | 作業系統和資料磁碟 |
| CentOS | 7.2n | 作業系統和資料磁碟 |
| CentOS | 6.8 | 作業系統和資料磁碟 |
| CentOS | 7.1 | 資料磁碟 |
| CentOS | 7.0 | 資料磁碟 |
| CentOS | 6.7 | 資料磁碟 |
| CentOS | 6.6 | 資料磁碟 |
| CentOS | 6.5 | 資料磁碟 |
| openSUSE | 13.2 | 資料磁碟 |
| SLES | 12 SP1 | 資料磁碟 |
| SLES | 優先順序：12-SP1 | 資料磁碟 |
| SLES | HPC 12 | 資料磁碟 |
| SLES | 優先順序：11-SP4 | 資料磁碟 |
| SLES | 11 SP4 | 資料磁碟 |

**問：**如何開始使用 Azure 磁碟加密？

**答：**客戶可以閱讀位於[這裡](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)的 Azure 磁碟加密白皮書以了解如何開始使用

**問：**是否可以使用 Azure 磁碟加密來加密開機和資料磁碟區？

**答：**是，您可以加密 Windows 和 Linux IaaS VM 適用的開機和資料磁碟區。 針對 Windows VM，您若未先將 OS 磁碟區加密，就無法將資料加密。 針對 Linux VM，您無須先將 OS 磁碟區加密，就可將資料磁碟區加密。 一旦您將 Linux 適用的 OS 磁碟區加密後，就不支援將 Linux IaaS VM 適用的 OS 磁碟區加密停用

**問：**Azure 磁碟加密可讓您使用「自備金鑰」(BYOK) 功能嗎？

**答：**是，您可以提供自己的金鑰加密金鑰。 這些金鑰會在 Azure Key Vault 中受到保護，這是 Azure 磁碟加密的金鑰儲存區。 如需有關金鑰加密金鑰的支援情節的詳細資訊，請參閱 Azure 磁碟加密部署情節和體驗

**問：**是否可以使用 Azure 建立的金鑰加密金鑰？

**答：**是，您可以使用 Azure Key Vault 來產生金鑰加密金鑰以供 Azure 磁碟加密使用。 這些金鑰會在 Azure Key Vault 中受到保護，這是 Azure 磁碟加密的金鑰儲存區。 如需有關金鑰加密金鑰的支援情節的詳細資訊，請參閱 Azure 磁碟加密部署情節和體驗

**問：**是否可以使用內部部署金鑰管理服務/HSM 來保護加密金鑰？

**答：**您無法使用內部部署金鑰管理服務/HSM 來保護 Azure 磁碟加密的加密金鑰。 只能使用 Azure Key Vault 服務來保護加密金鑰。 如需有關金鑰加密金鑰的支援情節的詳細資訊，請參閱 Azure 磁碟加密部署情節和體驗

**問：**設定 Azure 磁碟加密的必要條件為何？

**答：**Azure 磁碟加密的必要條件，PowerShell 指令碼，以建立 AAD 應用程式、建立新的金鑰保存庫，或針對磁碟加密存取設定現有的金鑰保存庫，從而啟用加密並保護祕密和金鑰。  如需有關金鑰加密金鑰的支援情節的詳細資訊，請參閱 Azure 磁碟加密的必要條件及部署情節和體驗

**問：**哪裡可以取得有關如何使用 PowerShell 來設定 Azure 磁碟加密的詳細資訊？

**答：**我們有一些絕佳的文章，說明如何執行基本的 Azure 磁碟加密工作，以及更多進階的情節。 如需基本工作，請參閱探索[使用 Azure PowerShell 的 Azure 磁碟加密 - 第 1 部分](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/)。 如需更多進階的情節，請參閱探索[使用 Azure PowerShell 的 Azure 磁碟加密 - 第 2 部分](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/)

**問：**Azure 磁碟加密支援 Azure PowerShell 的什麼版本？

**答：**使用最新版的 Azure PowerShell SDK 版本來設定 Azure 磁碟加密。 下載最新版的 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)。 Azure SDK version 1.1.0 版「不」支援 Azure 磁碟加密。

> [!NOTE]
> Linux Azure 磁碟加密預覽擴充功能已被取代。 如需詳細資訊，請參閱[這裡](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/)的文件

**問：**是否可以在我的自訂 Linux 映像上套用 Azure 磁碟加密？

**答：**無法在您的自訂 Linux 映像上套用 Azure 磁碟加密。 我們僅支援以上叫出支援散發版本的資源庫 Linux 映像。 目前不支援自訂 Linux 映像

**問：**是否可使用 Yum 更新將更新套用至 Linux Red Hat VM？

**答：**是，您可以執行更新和/或修補遵循[這裡](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/)所述之指引的 Red Hat Linnux VM

**問：**我可以在哪裡提出問題或意見反應

**答：**您可以在[這裡](https://social.msdn.microsoft.com/Forums/home?forum=AzureDiskEncryption)的 Azure 磁碟加密論壇提供詢問的問題或意見反應

## <a name="see-also"></a>另請參閱
在本文件中，您已了解有關 Azure 磁碟加密的常見問題，如需有關這項服務的詳細資訊及其功能，請參閱：

- [在 Azure 資訊安全中心套用磁碟加密](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [加密 Azure 虛擬機器](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Azure 資料靜態加密](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)

