---
title: "安裝 Python 和 SDK - Azure"
description: "了解如何安裝 Python 和搭配 Azure 的 SDK。"
services: 
documentationcenter: python
author: lmazuel
manager: wpickett
editor: 
ms.assetid: f36294be-daeb-4caf-9129-fce18130f552
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/06/2016
ms.author: lmazuel
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: e69fff29be5b12c3c0004b4101eba69c7da87d3d
ms.contentlocale: zh-tw
ms.lasthandoff: 09/20/2017

---
# <a name="installing-python-and-the-sdk"></a>安裝 Python 和 SDK
在 Windows 上設定 Python 相當簡單，而在 Mac、Linux 及[適用於 Windows 的 Bash](https://msdn.microsoft.com/commandline/wsl/about) 中則是已預先安裝 Python。 本指南將逐步引導您完成安裝作業，並讓機器做好搭配 Azure 的準備。

## <a name="whats-in-the-python-azure-sdk"></a>Python Azure SDK 含有哪些內容？
Azure SDK for Python 內含的元件可讓您開發、部署及管理適用於 Azure 的 Python 應用程式。 尤其是 Azure SDK for Python 包含下列各項：

* **管理程式庫**。 這些類別庫提供一個可管理 Azure 資源 (例如儲存體帳戶、虛擬機器) 的介面。
* **執行階段程式庫**。 這些類別庫提供一個可存取 Azure 功能 (例如儲存體和服務匯流排) 的介面。

## <a name="which-python-and-which-version-to-use"></a>該使用哪個 Python 和哪個版本
可用的 Python 解譯器有數種，範例包括：

* CPython - 標準和最常見的 Python 解譯器
* PyPy - CPython 的快速、相容替代實作
* IronPython - 可在 .Net/CLR 上執行的 Python 解譯器
* Jython - 可在「Java 虛擬機器」上執行的 Python 解譯器

**CPython** v2.7 或 v3.3+ 及 PyPy 5.4.0 已針對 Python Azure SDK 進行過測試並確定支援。

## <a name="where-to-get-python"></a>可在哪裡取得 Python？
取得 CPython 的方法有數種：

* 直接從 [www.python.org][www.python.org]
* 從聲譽良好的散發網站取得，如 [www.continuum.io][www.continuum.io]、[www.enthought.com][www.enthought.com] 或 [www.activestate.com][www.activestate.com]
* 從原始碼組建！

除非您有特定的需求，否則我們建議您採用前兩個選項。

## <a name="sdk-installation-on-windows-linux-and-macos-client-libraries-only"></a>Windows、Linux 及 MacOS 上的 SDK 安裝 (僅限用戶端程式庫)
如果您已經安裝 Python，您可以使用 PIP 在現有的 Python 2.7 或 Python 3.3+ 環境中，安裝所有用戶端程式的組合。 此作業會從 [Python 套件索引][Python Package Index] (PyPI) 下載套件。

您可能需要系統管理員權限：

* Linux 和 MacOS：使用 `sudo` 命令︰`sudo pip install azure-mgmt-compute`。
* Windows：以系統管理員身分開啟 PowerShell/命令提示字元。

您可以為每個 Azure 服務個別安裝程式庫：

```console
   $ pip install azure-batch          # Install the latest Batch runtime library
   $ pip install azure-mgmt-scheduler # Install the latest Storage management library
```

可以使用 `--pre` 旗標來安裝預覽版套件︰

```console
   $ pip install --pre azure-mgmt-compute # installs only the latest Compute Management library
```

您也可以在單行中使用 `azure` 中繼套件來安裝一組 Azure 程式庫。 由於此中繼套件中並非所有套件都已發佈為穩定版，因此 `azure` 中繼套件仍處於預覽版狀態。
不過，從程式碼品質/完整性的觀點來看，目前可以將核心套件視為「穩定版」。

* 我們會儘快將它正式標示為穩定版以與其他語言一致。
  在那之前，我們不打算進行任何進一步的重大變更。

由於它是預覽版，因此您必須使用 `--pre` 旗標︰

```console
   $ pip install --pre azure
```

或直接使用

```console
   $ pip install azure==2.0.0rc6
```

## <a name="getting-more-packages"></a>取得更多封裝
[Python 封裝索引][Python Package Index] (PyPI) 具有選擇性豐富的 Python 程式庫。  如果您選擇了安裝散發版本，則您將已擁有從 Web 開發到「工程運算」的各種案例中大多數令人關注的部分。

## <a name="python-tools-for-visual-studio"></a>Python Tools for Visual Studio
[適用於 Visual Studio 的 Python 工具][適用於 Visual Studio 的 Python 工具] (PTVS) 是 Microsoft 提供的免費/OSS 外掛程式，它能將 VS 轉變為成熟的 Python IDE：

![how-to-install-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

您可以選擇是否要使用 PTVS，不過我們建議您使用，因為它能提供 Python 和 Web 專案/方案支援、偵錯、分析、互動式視窗、範本編輯和 IntelliSense。

PTVS 也能讓您使用部署至[雲端服務](cloud-services/cloud-services-python-ptvs.md)和[網站](app-service/app-service-web-overview.md)的支援，輕鬆部署至 Microsoft Azure。

PTVS 可以和您現有的 Visual Studio 2013、2015 或 2017 安裝一同運作。  如需文件、下載項目和相關討論，請參閱[適用於 Visual Studio 的 Python 工具]。  

## <a name="python-azure-scenarios-for-linux-and-macos"></a>適用於 Linux 和 MacOS 的 Python Azure 案例
就 Linux 或 MacOS 而言，支援的 Azure 案例包括：

1. 使用 Python 的用戶端程式庫取用 Azure 服務
2. 在 Linux VM 上執行應用程式
3. 使用 Git 開發和發佈至 Azure 網站

第一個案例可讓您撰寫能透過 Azure REST API 的 Python 風格包裝函式利用 Azure PaaS 功能 (例如 [Blob 儲存體](virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)、[佇列儲存體](storage/queues/storage-python-how-to-use-queue-storage.md)、[資料表儲存體](cosmos-db/table-storage-how-to-use-python.md)等) 的豐富 Web 應用程式。 這些 Web 應用程式在 Windows、Mac 和 Linux 上的運作方式相同。  您也可以從您的本機開發電腦或 Azure 上執行的 Linux VM，使用這些用戶端程式庫。

對於 VM 案例，您只需要啟動選擇的 Linux VM (Ubuntu、CentOS、Suse)，便能執行/管理所需的項目。  例如，您可以在 Windows/Mac/Linux 機器上執行 [IPython][IPython] REPL/notebook，然後將瀏覽器指向在 Azure 上執行 IPython Engine 的 Linux 或 Windows 多重處理器 VM。

如需如何設定 Linux VM 的資訊，請參閱[建立執行 Linux 的虛擬機器](virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)教學課程。

您可以使用 Git 部署開發 Python Ｗeb 應用程式，並從任何作業系統將其發佈至 Azure 網站中。  當您將您的存放庫推送至 Azure 時，就會自動建立虛擬環境和 pip 安裝所需的套件。

如需更多有關使用任何 WSGI 相容架構的資訊，請參閱[在 Azure 網站上設定 Python](app-service/web-sites-python-configure.md)。

## <a name="additional-software-and-resources"></a>其他軟體和資源：
* [Azure SDK for Python ReadTheDocs](http://azure-sdk-for-python.readthedocs.io/en/latest/)
* [Azure SDK for Python GitHub](https://github.com/Azure/azure-sdk-for-python)
* [適用於 Python 的官方 Azure 範例](https://azure.microsoft.com/documentation/samples/?platform=python)
* [Continuum Analytics Python 發佈][Continuum Analytics Python Distribution]
* [Enthought Python 發佈][Enthought Python Distribution]
* [ActiveState Python 發佈][ActiveState Python Distribution]
* [SciPy - Scientific Python 程式庫套件][SciPy - A suite of Scientific Python libraries]
* [NumPy - Python 的數值程式庫][NumPy - A numerics library for Python]
* [Django 專案 - 成熟的 Web 架構/CMS][Django Project - A mature web framework/CMS]
* [IPython - 先進的 Python REPL/Notebook][IPython - an advanced REPL/Notebook for Python]
* [GitHub 上適用於 Visual Studio 的 Python 工具][Python Tools for Visual Studio on GitHub]
* [Python 開發人員中心](/develop/python/)

[Continuum Analytics Python Distribution]: http://continuum.io
[Enthought Python Distribution]: http://www.enthought.com
[ActiveState Python Distribution]: http://www.activestate.com
[www.python.org]: http://www.python.org
[www.continuum.io]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy - A suite of Scientific Python libraries]: http://www.scipy.org
[NumPy - A numerics library for Python]: http://www.numpy.org
[Django Project - A mature web framework/CMS]: http://www.djangoproject.com
[IPython - an advanced REPL/Notebook for Python]: http://ipython.org
[IPython]: http://ipython.org
[適用於 Visual Studio 的 Python 工具]: http://aka.ms/ptvs
[Python Tools for Visual Studio on GitHub]: https://github.com/microsoft/ptvs
[Python Package Index]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[blob storage]:storage/blobs/storage-python-how-to-use-blob-storage.md

