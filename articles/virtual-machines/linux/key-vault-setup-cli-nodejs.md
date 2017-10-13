---
title: "使用 Azure CLI 1.0 設定 Linux VM 的 Key Vault | Microsoft Docs"
description: "如何使用 Azure CLI 1.0 設定要與 Azure Resource Manager 虛擬機器搭配使用的 Key Vault。"
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
ms.openlocfilehash: fed612a354d45f34619f2a66bd40d78740c43ac7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager-with-the-azure-cli-10"></a>使用 Azure CLI 1.0 為 Azure Resource Manager 中的虛擬機器設定 Key Vault
在 Azure Resource Manager 堆疊中，密碼/憑證會被塑造成「Key Vault 資源提供者」所提供的資源。 若要深入了解「Azure 金鑰保存庫」，請參閱 [什麼是 Azure 金鑰保存庫？](../../key-vault/key-vault-whatis.md) 為了讓「金鑰保存庫」能與 Azure Resource Manager 虛擬機器搭配使用，必須將「金鑰保存庫」上的 *EnabledForDeployment* 屬性設定為 true。 您可以在各種用戶端中執行這項操作。 本文說明如何設定要與 Azure 虛擬機器搭配使用的 Key Vault。

## <a name="cli-versions-to-complete-the-task"></a>用以完成工作的 CLI 版本
您可以使用下列其中一個 CLI 版本來完成工作

- [Azure CLI 1.0](#quick-commands) – 適用於傳統和資源管理部署模型的 CLI (本文章)
- [Azure CLI 2.0](../windows/key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 適用於資源管理部署模型的新一代 CLI

## <a name="use-cli-10-to-set-up-key-vault"></a>使用 CLI 1.0 來設定 Key Vault
若要使用命令列介面 (CLI) 建立金鑰保存庫，請參閱 [使用 CLI 管理金鑰保存庫](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault)。

若使用 CLI 1.0，您必須在您指派部署原則之前建立金鑰保存庫。 然後，您可以使用下列命令來指派原則︰

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>使用範本來設定金鑰保存庫
當您使用範本時，您需要針對金鑰保存庫資源將 `enabledForDeployment` 屬性設定為 `true`。

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

如需使用範本來建立金鑰保存庫時可以設定的其他選項，請參閱 [Create a Key Vault (建立金鑰保存庫)](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)。
