---
title: "已知問題和疑難排解指南 | Microsoft Docs"
description: "已知問題清單和協助疑難排解的指南"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ef5e058d81b64c46e8bad6f85c0bef0c69fb1512
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---

# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench - 已知問題和疑難排解指南 
本文可協助您尋找和修正使用 Azure Machine Learning Workbench 應用程式過程中遇到的錯誤與失敗。 

> [!NOTE]
> 當您透過電子郵件或論壇文章來與技術支援小組通訊時，最好先備妥組建編號。 您可以按一下 [說明] 功能表來找出應用程式的組建編號。 按一下組建編號，將它複製到剪貼簿。 您可以將它貼到電子郵件或支援論壇，有助於報告問題。

![檢查版本號碼](media/known-issues-and-troubleshooting-guide/version.png)

## <a name="windows-and-mac"></a>Windows 和 Mac
### <a name="centos-based-azure-data-science-virtual-machine"></a>CentOS 架構的 Azure 資料科學虛擬機器 
* 不支援將作業提交至 CentOS 架構的 Azure 資料科學虛擬機器 (DSVM)。 您可以將目標鎖定為 [Ubuntu 架構的 DSVM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) \(英文\)。

### <a name="docker-error"></a>Docker 錯誤 
* 針對本機 Docker 容器執行時，如果看見下列錯誤，可將 Docker DNS 伺服器從「自動」變更為「固定」的 8.8.8.8 來修正此問題。 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```
![映像](media/known-issues-and-troubleshooting-guide/docker_dns.png)


### <a name="linux-vm-password"></a>Linux VM 密碼 
* 如果您在 Ubuntu Linux VM 上從 Azure 入口網站變更密碼，則在其上執行作業時可能會出現下列錯誤：
  ```
  sudo: no tty present and no askpass program specified.
  ``` 

  因應措施是在 _/etc/sudoers.d_ 中新增檔案 (範例為 myDockerUsers)，其中含有下列內容：
  ```
  <your_username> ALL = NOPASSWD: /usr/bin/docker, /usr/bin/nvidia-docker
  ```

### <a name="ssh-keys"></a>SSH 金鑰 
* 透過 SSH 連線至遠端電腦或 Spark 叢集時，不支援 SSH 金鑰。 只支援使用者名稱/密碼模式。

## <a name="windows-only"></a>僅限 Windows 
### <a name="sharing-c-drive-in-docker"></a>在 Docker 中共用 C 磁碟機 
* 使用 [檔案總管] 來檢查 C 磁碟機上的共用
* 開啟網路介面卡設定，並為 vEthernet 解除安裝/重新安裝 [File and Printer Sharing for Microsoft Networks]
* 開啟 Docker 設定並在 Docker 設定內共用 C 磁碟機
* 變更 Windows 密碼會影響共用。 開啟 [檔案總管]、重新共用 C 磁碟機，然後輸入新密碼。
* 使用 Docker 嘗試共用您的 C 磁碟機時，可能也會遇到防火牆問題。 這篇 [Stack Overflow 文章](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) \(英文\) 提供有用的資訊。
* 使用網域認證共用 C 磁碟機時，若所在網路無法連線網域控制站 (例如，家用網路、公用 WiFi 等)，則共用可能會停止運作。 如需詳細資訊，請參閱[這篇文章](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/) \(英文\)。



## <a name="mac-only"></a>僅限 Mac 
* Mac 不支援文字叢集轉換。
* Mac 不支援 RevoScalePy 程式庫。

## <a name="azure-machine-learning-service-limits"></a>Azure Machine Learning 服務限制

- 允許的專案資料夾大小上限：25 MB
    >[!Note]
    >這項限制不適用於 `.git`、`docs` 和 `outputs` 資料夾。 這些資料夾名稱會區分大小寫。

- 允許的實驗執行時間上限：7 天
- 執行之後 `outputs` 資料夾中追蹤檔案的大小上限：512 MB 

>[!NOTE]
>如果您使用大型檔案，請參閱[保存變更和處理大型檔案](how-to-read-write-files.md)。

## <a name="other-issues"></a>其他問題
如果您還有其他未記載的問題，請透過「傳送笑臉/苦臉」傳送意見反應給我們。 




