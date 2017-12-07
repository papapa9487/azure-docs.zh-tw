---
title: "透過 Azure Migrate 使用機器相依性分組機器 | Microsoft Docs"
description: "說明如何透過 Azure Migrate 服務使用機器相依性來建立評量。"
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0527e34e-a078-405e-aeb9-c91a5808112a
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 5ed49f3dc71af6a8c1c7b6c9e38fd84452505aec
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2017
---
# <a name="group-machines-using-machine-dependency-mapping"></a>使用機器相依性對應分組機器

本文說明如何使用機器相依性對應，建立機器群組以進行 [Azure Migrate](migrate-overview.md) 評量。 在執行評量前，若想要透過交叉檢查機器相依性的方式，對信心等級較高的虛擬機器群組進行評估，通常會使用此方法。



## <a name="prepare-machines-for-dependency-mapping"></a>準備機器以進行相依性對應
若要將機器加入相依性對應，您需要在待評估的每個內部部署機器上，下載及安裝代理程式。 此外，如果您的機器沒有網際網路連線，則需要下載並安裝 [OMS 閘道](../log-analytics/log-analytics-oms-gateway.md)。

### <a name="download-and-install-the-vm-agents"></a>下載並安裝虛擬機器代理程式
1. 在 [概觀] 中，按一下 [管理] > [機器]，並選取所需的機器。
2. 在 [相依性] 資料行中，按一下 [安裝代理程式]。 
3. 至 [相依性] 頁面，下載 Microsoft Monitoring Agent (MMA) 及相依性代理程式，安裝到要進行評估的每個虛擬機器上。
4. 複製工作區識別碼與金鑰。 在內部部署機器上安裝 MMA 時，需要用到這些識別碼與金鑰。

### <a name="install-the-mma"></a>安裝 MMA

在 Windows 電腦上安裝代理程式：

1. 按兩下下載的代理程式。
2. 在 [歡迎] 頁面中按 [下一步]。 在 [授權條款] 頁面上，按一下 [我同意] 以接受授權。
3. 在 [目的地資料夾] 中，保留或修改預設的安裝資料夾 > [下一步]。 
4. 在 [代理程式安裝選項] 中，選取 [Azure Log Analytics (OMS)] > [下一步]。 
5. 按一下 [新增] 以新增新的 OMS 工作區。 貼上您從入口網站複製的工作區識別碼和金鑰。 按一下 [下一步] 。


在 Linux 電腦上安裝代理程式：

1. 使用 scp/sftp 將適當的套件組合 (x86 或 x64) 傳輸到 Linux 電腦。
2. 使用 --安裝引數安裝套件組合。

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```


### <a name="install-the-dependency-agent"></a>安裝相依性代理程式
1. 若要在 Windows 電腦上安裝相依性代理程式，請按兩下安裝檔案，並遵循精靈的指示。
2. 若要在 Linux 電腦上安裝相依性代理程式，請使用下列命令以 root 身分安裝：

    ```sh InstallDependencyAgent-Linux64.bin```

[深入了解](../operations-management-suite/operations-management-suite-service-map-configure.md#supported-operating-systems)相依性代理程式支援的作業系統。 

## <a name="create-a-group"></a>建立群組

1. 安裝代理程式之後，請移至入口網站，然後按一下[管理] > [機器]。
2. [相依性] 資料行現在應會顯示為**檢視相依性**。 按一下資料行以檢視相依性。
3. 您可以針對每部機器確認：
    - 是否已安裝 MMA 與相依性代理程式，以及是否已探索到機器。
    - 在電腦上執行的客體作業系統。
    - 傳入和傳出的 IP 連線與連接埠。
    - 電腦上執行的處理程序。
    - 電腦之間的相依性。

4. 如需更細微的相依性，請按一下時間範圍以進行修改。 根據預設，範圍是一小時。 您可以修改時間範圍，或指定開始和結束日期，以及持續時間。
5. 找出您想要劃為同一群組的相依機器後，請選取對應中的機器，然後按一下 [分組機器]。
6. 指定群組名稱。 確認 Azure Migrate 已探索到機器。 如果未執行，則探索程序會重新進行內部部署。 如果您願意，可以立即進行評量。
7. 按一下 [確定] 以儲存群組。

    ![利用機器相依性建立群組](./media/how-to-create-group-machine-dependencies/create-group.png)

## <a name="next-steps"></a>後續步驟

- [了解如何](how-to-create-group-dependencies.md)藉由檢查群組相依性來調整群組
- [深入了解](concepts-assessment-calculation.md)評定的計算方式。
