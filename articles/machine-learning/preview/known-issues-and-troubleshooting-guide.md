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
ms.openlocfilehash: f79910ba4fea81e88fce90dd67ba7cb4db2e8220
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench - 已知問題和疑難排解指南 
本文可協助您尋找和修正使用 Azure Machine Learning Workbench 應用程式過程中遇到的錯誤與失敗。 

> [!IMPORTANT]
> 與技術支援小組通訊時，務必先備妥組建編號。 您可以按一下 [說明] 功能表來找出應用程式的組建編號。 按一下組建編號，將它複製到剪貼簿。 您可以將它貼到電子郵件或支援論壇，有助於報告問題。

![檢查版本號碼](media/known-issues-and-troubleshooting-guide/buildno.png)

## <a name="machine-learning-msdn-forum"></a>機器學習服務 MSDN 論壇
您可以在我們的 MSDN 論壇張貼問題。 產品小組會主動監視論壇。 論壇 URL 是 [https://aka.ms/azureml-forum](https://aka.ms/azureml-forum)。 

## <a name="gather-diagnostics-information"></a>蒐集診斷資訊
當您在尋求協助時，如果能夠提供診斷資訊，有時可能會相當有幫助。 以下是記錄檔的所在位置：

### <a name="installer"></a>安裝程式
如果您在安裝期間遇到問題，可以在以下位置找到安裝程式記錄檔：

```
# Windows:
%TEMP%\amlinstaller\logs\*

# macOS:
/tmp/amlinstaller/logs/*
```
您可以將這些目錄的內容壓縮，然後傳送給我們進行診斷。

### <a name="workbench-desktop-app"></a>Workbench 傳統型應用程式
如果 Workbench 傳統型應用程式當機，您可以在以下位置找到記錄檔：
```
# Windows
%APPDATA%\AmlWorkbench

# macOS
~/Library/Application Support/AmlWorkbench
``` 
您可以將這些目錄的內容壓縮，然後傳送給我們進行診斷。

### <a name="experiment-execution"></a>實驗執行
如果從傳統型應用程式提交時，特定指令碼發生失敗，請嘗試透過 CLI 使用 `az ml experiment submit` 命令來重新提交。 這應該會以 JSON 格式提供您完整的錯誤訊息，最重要的是，它會包含**作業識別碼**值。 請將包含**作業識別碼**的 JSON 檔案傳送給我們，我們將可協助進行診斷。 

如果特定指令碼在提交時成功，但在執行時失敗，它應該會顯示**執行識別碼**來識別該特定執行。 您可以使用下列命令來封裝相關的記錄檔︰

```azurecli
# Create a ZIP file that contains all the diagnostics information
$ az ml experiment diagnostics -r <run_id> -t <target_name>
```

`az ml experiment diagnostics` 命令會在專案根資料夾中產生 `diagnostics.zip` 檔案。 此 ZIP 套件會包含整個專案資料夾 (處於執行時的狀態)，再加上記錄資訊。 將診斷檔案傳送給我們之前，請務必先移除您不想要包含的任何敏感性資訊。

## <a name="send-us-a-frown-or-a-smile"></a>將皺眉 (或笑臉) 傳送給我們

當您使用 Azure ML Workbench 時，您也可以按一下應用程式 shell 左下角的笑臉圖示，將皺眉 (或笑臉) 傳送給我們。 您可以選擇性選擇要包含您的電子郵件地址 (因此我們可以回覆您)，及/或目前狀態的螢幕擷取畫面。 

## <a name="known-service-limits"></a>已知的服務限制
- 允許的專案資料夾大小上限：25 MB。
    >[!NOTE]
    >這項限制不適用於 `.git`、`docs` 和 `outputs` 資料夾。 這些資料夾名稱會區分大小寫。 如果您使用大型檔案，請參閱[保存變更和處理大型檔案](how-to-read-write-files.md)。

- 允許的實驗執行時間上限：7 天
- 執行之後 `outputs` 資料夾中追蹤檔案的大小上限：512 MB
  - 這表示如果指令碼在 outputs 資料夾中產生大於 512 MB 的檔案，就不會在那裡收集該檔案。 如果您使用大型檔案，請參閱[保存變更和處理大型檔案](how-to-read-write-files.md)。

- 透過 SSH 連線至遠端電腦或 Spark 叢集時，不支援 SSH 金鑰。 目前只支援使用者名稱/密碼模式。

- Mac 不支援文字叢集轉換。

- 只有在 Windows 或 Linux (在 Docker 容器中) 上不支援 RevoScalePy 程式庫。 在 macOS 上不提供支援。

## <a name="docker-error-read-connection-refused"></a>Docker 錯誤 "read: connection refused"
對本機 Docker 容器執行時，有時您可能會看到下列錯誤： 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```

將 Docker DNS Server 從 `automatic` 變更為固定值 `8.8.8.8`，即可修正此錯誤。

## <a name="remove-vm-execution-error-no-tty-present"></a>移除 VM 執行錯誤 "no tty present"
對遠端 Linux 機器上的本機 Docker 容器執行時，您可能會看到下列錯誤訊息：
```
sudo: no tty present and no askpass program specified.
``` 
如果您使用 Azure 入口網站來變更 Ubuntu Linux VM 的根密碼，可能會發生這種情形。 

Azure Machine Learning Workbench 需要無密碼 sudoers 存取權，才能在遠端主機上執行。 若要這麼做，最簡單的方式是使用 _visudo_ 來編輯下列檔案 (如果此檔案不存在，您可加以建立)：

```
$ sudo visudo -f /etc/sudoers
```

>[!IMPORTANT]
>務必使用 _visudo_ (而非另一個命令) 編輯此檔案。 _visudo_ 語法會自動檢查所有 sudo 組態檔，而無法產生正確語法的 sudoers 檔案可能讓您無法存取 sudo。

在檔案結尾處插入下列這一行︰

```
username ALL=(ALL) NOPASSWD:ALL
```

其中 _username_ 是 Azure Machine Learning Workbench 將用來登入遠端主機的名稱。

這一行必須放在 #includedir"/ etc/sudoers.d" 之後，否則它可能會被另一個規則所覆寫。

如果您有更複雜的 sudo 組態，建議您查閱 Ubuntu 的 sudo 文件，網址如下：https://help.ubuntu.com/community/Sudoers

如果您未在 Azure 中使用以 Ubuntu 為基礎的 Linux VM 作為執行目標，也會發生上述錯誤。 我們只支援以 Ubuntu 為基礎的 Linux VM 進行遠端執行。 

## <a name="vm-disk-is-full"></a>VM 磁碟已滿
依預設，當您在 Azure 中建立新的 Linux VM 時，您可取得 30 GB 磁碟以供作業系統使用。 Docker 引擎預設會使用相同的磁碟來提取映像和執行容器。 這可能會填滿作業系統磁碟，而您會在發生此狀況時看到「VM 磁碟已滿」錯誤。

快速修正方法是移除您不再使用的所有 Docker 映像。 下列 Docker 命令可執行該作業。 (當然您必須透過 SSH 連至 VM，以便從 bash shell 執行 Docker 命令。)

```
$ docker system prune -a
```

您也可以新增資料磁碟並將 Docker 引擎設定為使用資料磁碟來儲存映像。 以下說明[如何新增資料磁碟](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/add-disk)。 您可以接著[變更 Docker 儲存映像的位置](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169)。

或者，您可以展開作業系統磁碟，但不必碰觸 Docker 引擎組態。 以下說明[如何展開作業系統磁碟](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/add-disk)。

## <a name="sharing-c-drive-on-windows"></a>在 Windows 上共用 C 磁碟機
如果您在 Windows 上的本機 Docker 容器中執行，將 `aml_config` 底下 `docker.compute` 檔案中的 `sharedVolumes` 設定為 `true` 可以改善執行效能。 不過，您必須在 _Docker for Windows 工具_ 中共用 C 磁碟機。 如果您不能共用 C 磁碟機，請嘗試下列秘訣：

* 使用 [檔案總管] 來檢查 C 磁碟機上的共用
* 開啟網路介面卡設定，並為 vEthernet 解除安裝/重新安裝 [File and Printer Sharing for Microsoft Networks]
* 開啟 Docker 設定並在 Docker 設定內共用 C 磁碟機
* 變更 Windows 密碼會影響共用。 開啟 [檔案總管]、重新共用 C 磁碟機，然後輸入新密碼。
* 使用 Docker 嘗試共用您的 C 磁碟機時，可能也會遇到防火牆問題。 這篇 [Stack Overflow 文章](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) \(英文\) 提供有用的資訊。
* 使用網域認證共用 C 磁碟機時，若所在網路無法連線網域控制站 (例如，家用網路、公用 WiFi 等)，則共用可能會停止運作。 如需詳細資訊，請參閱[這篇文章](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/) \(英文\)。

您也可以在 `docker.compute` 檔案中將 `sharedVolumne` 設定為`false`，以很少的效能成本避開共用問題。

## <a name="some-useful-docker-commands"></a>一些實用的 Docker 命令

以下是一些實用的 Docker 命令：

```sh
# display all running containers
$ docker ps

# dislplay all containers (running or stopped)
$ docke ps -a

# display all images
$ docker images

# show Docker logs of a container
$ docker logs <container_id>

# create a new container and launch into a bash shell
$ docker run <image_id> /bin/bash

# launch into a bash shell on a running container
$ docker exec -it <container_id> /bin/bash

# stop an running container
$ docker stop <container_id>

# delete a container
$ docker rm <container_id>

# delete an image
$ docker rmi <image_id>

# delete all unussed Docker images 
$ docker system prune -a

```