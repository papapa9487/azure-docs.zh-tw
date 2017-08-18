---
title: "Azure 中 Linux VM 的概觀 | Microsoft Docs"
description: "描述 Azure 計算、儲存體和網路服務與 Linux 虛擬機器。"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: rickstercdn
manager: timlt
editor: 
ms.assetid: 7965a80f-ea24-4cc2-bc43-60b574101902
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/14/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017, mvc
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 84406b3068a6705bf0a4c5f17ace5a586398da4f
ms.contentlocale: zh-tw
ms.lasthandoff: 08/11/2017

---
# <a name="azure-and-linux"></a>Azure 和 Linux
Microsoft Azure 集結了各種整合式公用雲端服務且數量不斷增加，包括分析、虛擬機器、資料庫、行動、網路、儲存體和 Web&mdash;因此很適合用來裝載您的方案。  Microsoft Azure 提供可調整的運算平台，可讓您在需要時用多少就付多少，而不需投資內部部署的硬體。  Azure 可供您將您的方案相應增加和放大至任何您需要的規模，以滿足您所服務之客戶的需求。

如果您熟悉 Amazon AWS 的各項功能，您可以查看 Azure 與 AWS 的對照 [定義對應文件](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/)。

## <a name="regions"></a>區域
Microsoft Azure 資源分散在世界各地的多個地理區域。  「區域」代表單一地理區域中的多個資料中心。  我們已在世界各地正式推出 34 個區域，並即將推出另外 4 個區域。 我們會繼續擴大在全球各地的涵蓋範圍，因此我們有一份涵蓋現有和新宣布區域的更新清單。

* [Azure 區域](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Availability
我們已宣布推出業界領先的單一執行個體虛擬機器 99.9% 服務等級協定 (前提是您部署了所有磁碟都是進階儲存體的 VM)。  為了讓您的部署符合標準的 99.95% VM 服務等級協定資格，您還必須部署兩個或更多在可用性設定組內執行工作負載的 VM。 這可確保您的 VM 會分散在我們資料中心內的多個容錯網域，以及部署至具有不同維護期間的主機。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) 說明保證的 Azure 整體可用性。

## <a name="managed-disks"></a>受控磁碟

受控磁碟可在背景中為您處理 Azure 儲存體帳戶的建立和管理作業，確保您不需要擔心儲存體帳戶的延展性限制。 您只需指定磁碟大小和效能層級 (標準或進階)，Azure 就會為您建立和管理磁碟。 即便是新增磁碟或相應增加和減少 VM，您都不必擔心所使用的儲存體。 如果您要建立新的 VM，請[使用 Azure CLI 2.0](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 或 Azure 入口網站來建立具有受控 OS 和資料磁碟的 VM。 如果您有具備非受控磁碟的 VM，您可以[將 VM 轉換成由受控磁碟提供支援](convert-unmanaged-to-managed-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

您也可以在每個 Azure 區域中使用單一儲存體帳戶管理自訂映像，並使用映像在相同訂用帳戶中建立數百個 VM。 如需受控磁碟的詳細資訊，請參閱[受控磁碟概觀](../../storage/storage-managed-disks-overview.md)。

## <a name="azure-virtual-machines--instances"></a>Azure 虛擬機器和執行個體
Microsoft Azure 支援執行由多家合作夥伴提供和維護的眾多熱門 Linux 散發套件。  您可以在 Azure Marketplace 中找到 Red Hat Enterprise、CentOS、Debian、Ubuntu、CoreOS、RancherOS 及 FreeBSD 等散發套件。 我們與各個 Linux 社群積極合作，以便為[經 Azure 背書的 Linux 散發套件](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)清單新增更多版本選項。

如果您慣用的 Linux 散發套件選項目前未出現在映像庫中，您可以[建立 Linux VHD 並上傳到 Azure](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)來「自備 Linux」VM。

Azure 虛擬機器可供您靈活部署各種運算方案。 您可以在大部分的作業系統 (Windows、Linux，或來自我們持續增加合作夥伴的自訂建立作業系統)，部署幾乎任何工作負載和任何語言。 還是沒找到您在尋找的映像嗎？  別擔心，您也可以使用您在內部部署中擁有的映像。

## <a name="vm-sizes"></a>VM 大小
當您在 Azure 中部署 VM 時，會從我們一系列的大小中選取一個適合您工作負載的 VM 大小。 大小也會影響虛擬機器的處理能力、記憶體和儲存體容量。 收費的依據則是 VM 執行和使用其配置資源的時間長短。 完整的[虛擬機器大小](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)清單。

以下是一些從我們提供的系列 (A、D、DS、G 和 GS) 中選取一個 VM 大小的基本指導方針。
* A 系列 VM 是物超所值的入門級 VM，適用於輕度工作負載和開發/測試案例。 所有區域皆廣泛提供此系列 VM，其可用來連接並使用虛擬機器可用的所有標準資源。
* A 系列大小 (A8 - A11) 則是特殊的可進行大量運算的組態，適用於高效能的運算叢集應用程式。
* D 系列 VM 是為了執行要求更高計算能力和暫存磁碟效能的應用程式所設計。 D 系列 VM 提供更快的處理器、較高的記憶體與核心比率，以及適用於暫存磁碟的固態硬碟 (SSD)。
* Dv2 系列是 D 系列的最新版本，其配備有功能更強大的 CPU。 Dv2 系列 CPU 比 D 系列 CPU 快約 35%。 它以最新一代的 2.4 GHz Intel Xeon® E5-2673 v3 (Haskell) 處理器為基礎，搭配 Intel Turbo Boost Technology 2.0，最高可達 3.2 GHz。 Dv2 系列的記憶體和磁碟組態和 D 系列一樣。
* G 系列 VM 提供最大的記憶體，並且是在具有 Intel Xeon E5 V3 系列處理器的主機上執行。

注意：DS 系列和 GS 系列 VM 可存取進階儲存體，這個搭載 SSD 的高效能、低延遲儲存體適合需要進行密集 I/O 工作的工作負載。 僅特定地區可用進階儲存體。 如需詳細資訊，請參閱：

* [進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體](../../storage/storage-premium-storage.md)

## <a name="automation"></a>自動化
為了實現適當的 DevOps 文化特性，所有基礎結構都必須是程式碼。  當所有基礎結構都留存在程式碼中時，便可以輕鬆地重新建立基礎結構 (Phoenix 伺服器)。  Azure 可以與所有主要的自動化工具 (例如 Ansible、Chef、SaltStack 及 Puppet) 搭配運作。  Azure 也有自己的自動化工具：

* [Azure 範本](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Azure 將針對支援 [cloud-init](http://cloud-init.io/) 的大多數 Linux 散發套件推出 cloud-init 支援。  目前 Canonical 的 Ubuntu VM 部署預設即已啟用 cloud-init。  Red Hats RHEL、CentOS 及 Fedora 支援 cloud-init，不過由 RedHat 維護的 Azure 映像並未安裝 cloud-init。  若要在 RedHat 系列 OS 上使用 cloud-init，您必須建立已安裝 cloud-init 的自訂映像。

* [在 Azure Linux VM 上使用 cloud-init](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quotas"></a>配額
每個 Azure 訂用帳戶都有適當的預設配額限制，而此限制會在您要為專案部署大量 VM 時產生影響。 每一訂用帳戶目前的限制是每一區域 20 個 VM。  只要提出支援票證來要求增加限制，即可快速且輕鬆地提高配額限制。  如需有關配額限制的詳細資料：

* [Azure 訂用帳戶服務限制](../../azure-subscription-service-limits.md)

## <a name="partners"></a>合作夥伴
Microsoft 與我們的合作夥伴密切合作，以確保更新可用的映像並針對 Azure 執行階段進行最佳化。  如需有關我們合作夥伴的詳細資訊，請查看下方他們的市集頁面。

* [Azure 背書散發套件上的 Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* SUSE - [Azure Marketplace - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=%27SUSE%27)
* Redhat - [Azure Marketplace - RedHat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical - [Azure Marketplace - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian - [Azure Marketplace - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD - [Azure Marketplace - FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
* CoreOS - [Azure Marketplace - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami - [適用於 Azure 的 Bitnami 程式庫](https://azure.bitnami.com/)
* Mesosphere - [Azure Marketplace - Azure 上的 Mesosphere DC/OS](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker - [Azure Marketplace - 與 Docker Swarm 搭配使用的 Azure Container Service](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)

## <a name="getting-started-with-linux-on-azure"></a>在 Azure 上開始使用 Linux
若要開始使用 Azure，您需要一個 Azure 帳戶、安裝 Azure CLI，以及一組 SSH 公開和私密金鑰。

### <a name="sign-up-for-an-account"></a>註冊帳戶
使用「Azure 雲端」的第一個步驟就是註冊 Azure 帳戶。  請前往 [Azure 帳戶註冊](https://azure.microsoft.com/pricing/free-trial/) 頁面來開始註冊。

### <a name="install-the-cli"></a>安裝 CLI
有了您的新 Azure 帳戶之後，您便可以立即開始使用 Azure 入口網站，這是一個 Web 型系統管理面板。  若要透過命令列管理「Azure 雲端」，您需安裝 `azure-cli`。  請在您的 Mac 或 Linux 工作站上安裝 [Azure CLI 2.0](/cli/azure/install-azure-cli)。

### <a name="create-an-ssh-key-pair"></a>建立 SSH 金鑰組
現在您已擁有 Azure 帳戶、Azure Web 入口網站及 Azure CLI。  下一個步驟是建立 SSH 金鑰組，使用此金鑰組即可透過 SSH 連線到 Linux，而不需使用密碼。  [在 Linux 和 Mac 上建立 SSH 金鑰](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)，以便啟用無密碼登入功能並提升安全性。

### <a name="create-a-vm-using-the-cli"></a>使用 CLI 來建立 VM
使用 CLI 來建立 Linux VM 是一個讓您不需離開正在工作的終端機即可快速部署 VM 的方法。  所有您可以在 Web 入口網站上指定的項目，透過命令列旗標或參數也都可以指定。  

* [使用 CLI 建立 Linux VM](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="create-a-vm-in-the-portal"></a>在入口網站中建立 VM
在 Azure Web 入口網站中建立 Linux VM 可讓您輕鬆點選各種選項來進行部署。  您將不使用命令列旗標或參數，而是會看到含有各種選項和設定的美觀 Web 版面配置。  所有您可以透過命令列介面使用的項目，在入口網站中也都可以使用。

* [使用入口網站建立 Linux VM](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="login-using-ssh-without-a-password"></a>使用 SSH 登入而不提供密碼
VM 現在已在 Azure 上執行，而您已經可以登入。  使用密碼透過 SSH 登入既不安全又費時。  使用 SSH 金鑰是最安全的登入方式，也是最省時的登入方式。  當您透過入口網站或 CLI 建立 Linux VM 時，有兩種驗證選擇。  如果您為 SSH 選擇了密碼，Azure 就會將 VM 設定成允許透過密碼登入。  如果您選擇使用 SSH 公開金鑰，Azure 就會將 VM 設定成只允許透過 SSH 金鑰登入，並停用密碼登入。 若要藉由只允許使用 SSH 金鑰登入的方式來保護 Linux VM，請在透過入口網站或 CLI 建立 VM 時，使用 SSH 公用金鑰選項。

## <a name="related-azure-components"></a>相關的 Azure 元件
## <a name="storage"></a>儲存體
* [Microsoft Azure 儲存體簡介](../../storage/storage-introduction.md)
* [使用 azure-cli 將磁碟新增到 Linux VM](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [如何在 Azure 入口網站中將資料磁碟連結到 Linux VM](attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="networking"></a>網路
* [虛擬網路概觀](../../virtual-network/virtual-networks-overview.md)
* [Azure 中的 IP 位址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [對 Azure 中的 Linux VM 開啟連接埠](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [在 Azure 入口網站中建立完整格式的網域名稱](portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="containers"></a>容器
* [Azure 中的虛擬機器和容器](containers.md)
* [Azure 容器服務簡介](../../container-service/container-service-intro.md)
* [部署 Azure 容器服務叢集](../../container-service/dcos-swarm/container-service-deployment.md)

## <a name="next-steps"></a>後續步驟
您現在已概略了解 Azure 上的 Linux。  下一個步驟是深入了解並建立一些 VM！

* [探索透過 AzureCLI 執行一般工作的指令碼範例清單 (不斷增加中)](cli-samples.md)

