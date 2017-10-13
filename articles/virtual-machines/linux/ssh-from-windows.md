---
title: "搭配 Linux VM 的 Windows 使用 SSH 金鑰 |Microsoft Docs"
description: "了解如何在 Windows 電腦上產生並使用 SSH 金鑰來連線到 Azure 上的 Linux 虛擬機器。"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 2cacda3b-7949-4036-bd5d-837e8b09a9c8
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: danlep
ms.openlocfilehash: 66837a3a153cda041f5351c52c8ccb1f8ccfea50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>如何在 Azure 上搭配 Windows 使用 SSH 金鑰
> [!div class="op_single_selector"]
> * [Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> * [Linux/Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
>
>

當您連接到 Azure 中的 Linux 虛擬機器 (VM) 時，您應該使用[公開金鑰加密 (public-key cryptography)](https://wikipedia.org/wiki/Public-key_cryptography) 提供更安全的方式來登入 Linux VM。 此程序包括使用安全殼層 (SSH) 命令 (而非使用者名稱和密碼) 來驗證自己的公用和私密金鑰交換。 密碼很容易遭受暴力密碼破解攻擊，尤其是在網際網路對向 VM (例如 Web 伺服器) 上。 本文提供 SSH 金鑰的概觀，以及如何在 Windows 電腦上產生適當的金鑰。

## <a name="overview-of-ssh-and-keys"></a>SSH 和金鑰的概觀
您可以使用公開和私密金鑰安全地登入 Linux VM︰

* **公開金鑰**會置於您的 Linux VM 或任何您要搭配公開金鑰加密使用的其他服務上。
* 當您登入 Linux VM 時，對其呈現**私密金鑰**以驗證您的身分識別。 保護此私密金鑰。 不要共用它。

這些公用和私密金鑰可以使用於多個 VM 和服務。 您想要存取的 VM 或服務不需要各有一對金鑰。 如需更詳細的概觀，請參閱[公開金鑰加密](https://wikipedia.org/wiki/Public-key_cryptography)。

SSH 是允許透過不安全的連線進行安全登入的加密連線通訊協定。 它是 Azure 中裝載之 Linux VM 的預設連線通訊協定。 雖然 SSH 本身提供加密的連線，但使用密碼搭配 SSH 連線仍會讓 VM 容易遭受暴力密碼破解攻擊或密碼猜測。 使用這些公開和私密金鑰 (也就是 SSH 金鑰) 連線至 VM 是更安全且慣用的方法。

如果您不想使用 SSH 金鑰，您仍然可以使用密碼登入 Linux VM。 如果您的 VM 並未公開至網際網路，則使用密碼可能就以足夠。 不過，您仍然需要管理每個 Linux VM 的密碼，以及維護狀況良好的密碼原則和作法，例如最小密碼長度和定期進行更新。 使用 SSH 金鑰可降低橫跨多個 VM 管理個別認證的複雜度。

## <a name="windows-packages-and-ssh-clients"></a>Windows 套件和 SSH 用戶端
您可使用 **SSH 用戶端**來連線及管理 Azure 中的 Linux VM。 Windows 電腦通常不會安裝 SSH 用戶端。 Windows 10 年度更新版已新增 Bash for Windows，且最新版 Windows 10 Creators Update 提供了其他更新。 適用於 Linux 的 Windows 子系統可讓您在 Bash 殼層內以原生方式執行和存取公用程式 (例如 SSH 用戶端)。 接著您可以參照任何 Linux 文件的說明，例如[如何產生適用於 Linux 的 SSH 金鑰組](mac-create-ssh-keys.md)。 Bash for Windows 仍處於開發階段，而且被視為測試版。 如需 Bash for Windows 的詳細資訊，請參閱 [Bash on Ubuntu on Windows](https://msdn.microsoft.com/commandline/wsl/about)。

如果您想要使用 Bash for Windows 以外的產品，以下套件包含一般您可以安裝的 Windows SSH 用戶端︰

* [Git For Windows](https://git-for-windows.github.io/)
* [puTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
* [MobaXterm](http://mobaxterm.mobatek.net/)
* [Cygwin](https://cygwin.com/)


## <a name="which-key-files-do-you-need-to-create"></a>您需要建立哪些金鑰檔案？
Azure 需要至少 2048 位元的 **ssh-rsa** 格式公開和私密金鑰。 如果您要使用傳統部署模型管理 Azure 資源，您也需要產生 PEM (`.pem` 檔案)。

以下是部署案例，以及您在每個案例中使用的檔案類型：

1. 任何使用 [Azure 入口網站](https://portal.azure.com)進行的部署，以及使用 [Azure CLI](../../cli-install-nodejs.md) 進行的 Resource Manager 部署都需要 **ssh-rsa**。
   * 通常大部分的人都需要這些金鑰。
2. 需要 `.pem` 檔案才能使用傳統部署來建立 VM。 使用 [Azure 入口網站](https://portal.azure.com)或 [Azure CLI](../../cli-install-nodejs.md) 時，傳統部署可支援這些金鑰。
   * 如果您要管理使用傳統部署模型建立的資源，您只需要建立額外的這些金鑰和憑證。

## <a name="install-git-for-windows"></a>安裝 Git for Windows
上一節列出了數個套件，包括適用於 Windows 的 `openssl` 工具。 這是建立公開和私密金鑰所需的工具。 下列範例會詳細說明如何安裝和使用 **Git for Windows**，不過您可以選擇您偏好的套件。 **Git for Windows** 可讓您存取其他某些開放原始碼軟體 ([OSS](https://en.wikipedia.org/wiki/Open-source_software)) 工具和公用程式，這些對於使用 Linux VM 可能有所幫助。

1. 從下列位置下載並安裝 **Git for Windows**：[https://git-for-windows.github.io/](https://git-for-windows.github.io/)。
2. 接受安裝過程中的預設選項，除非您特別需要加以變更。
3. 從 [開始] 功能表 > [Git] > [Git Bash] 執行 **Git Bash**。 主控台大致如下列範例所示︰

    ![Git for Windows Bash Shell](./media/ssh-from-windows/git-bash-window.png)

## <a name="create-a-private-key"></a>建立私密金鑰
1. 在您的 [Git Bash] 視窗中，使用 `openssl.exe` 建立私密金鑰。 下列範例建立名為 `myPrivateKey` 的金鑰和名為 `myCert.pem` 的憑證：

    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```

    輸出大致如下列範例所示：

    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```

   如果 Bash 報告錯誤，嘗試以提高的權限開啟新的 **Git Bash** 視窗。 然後，重新執行 `openssl` 命令。

2. 請回答國家/地區名稱、位置、組織名稱等提示。
3. 您的私密金鑰和憑證已建立在您目前的工作目錄中。 基於安全性考量，您應該設定私密金鑰的權限，只有您可存取它︰

    ```bash
    chmod 0600 myPrivateKey.key
    ```

4. [下一節](#create-a-private-key-for-putty)詳細資料對兩個檢視使用 PuTTYgen，並且使用公開金鑰，建立私密金鑰以將 PuTTY 用於 SSH 和 Linux VM。 下列命令會產生名為 `myPublicKey.key` 的公開金鑰檔案，您可以立即使用︰

    ```bash
    openssl.exe rsa -pubout -in myPrivateKey.key -out myPublicKey.key
    ```

5. 如果您也需要管理傳統資源，請將 `myCert.pem` 轉換為 `myCert.cer` (DER 編碼的 X509 憑證)。 只有在您需要特別管理較舊的傳統資源時，才需執行這個選擇性步驟。

    使用下列命令轉換憑證︰

    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>建立 PuTTY 的私密金鑰
PuTTY 是適用於 Windows 的常見 SSH 用戶端。 您可以免費使用任何您想要的 SSH 用戶端。 若要使用 PuTTY，您需要建立其他類型的金鑰 - PuTTY 私密金鑰 (PPK)。 如果您不想使用 PuTTY，請略過本節。

下列範例會特別建立此額外的私密金鑰，以供 PuTTY 使用︰

1. 使用 **Git Bash** 將您的私密金鑰轉換成 PuTTYgen 可認得的 RSA 私密金鑰。 下列範例從名為 `myPrivateKey` 的現有金鑰建立名為 `myPrivateKey_rsa` 的金鑰：

    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```

    基於安全性考量，您應該設定私密金鑰的權限，只有您可存取它︰

    ```bash
    chmod 0600 myPrivateKey_rsa
    ```
2. 從下列位置下載並執行 PuTTYgen：[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
3. 按一下功能表：[檔案] > [載入私密金鑰]
4. 找出您的私密金鑰 (先前範例中的 `myPrivateKey_rsa`)。 當您啟動 **Git Bash** 時的預設目錄是 `C:\Users\%username%`。 變更檔案篩選以顯示 所有檔案 \(\*.\*)：

    ![將現有的私密金鑰載入 PuTTYgen 中](./media/ssh-from-windows/load-private-key.png)
5. 按一下 [開啟] 。 以下提示表示已成功匯入金鑰︰

    ![金鑰已成功匯入 PuTTYgen](./media/ssh-from-windows/successfully-imported-key.png)
6. 按一下 [確定] 以關閉提示。
7. 公開金鑰會顯示在 **PuTTYGen** 視窗的頂端。 當您建立 Linux VM 時，您可以複製此公開金鑰並貼到 Azure 入口網站或 Azure Resource Manager 範本中。 您也可以按一下 [儲存公開金鑰]，將複本儲存到您的電腦︰

    ![儲存 PuTTY 公用金鑰檔案](./media/ssh-from-windows/save-public-key.png)

    下列範例顯示當您建立 Linux VM 時，如何複製此公開金鑰並貼到 Azure 入口網站中。 此公開金鑰通常會接著儲存在新 VM 上的 `~/.ssh/authorized_keys` 中。

    ![當您在 Azure 入口網站建立 VM 時使用公開金鑰](./media/ssh-from-windows/use-public-key-azure-portal.png)
8. 回到 **PuTTYgen**，按一下 [儲存私密金鑰]：

    ![儲存 PuTTY 私密金鑰檔案](./media/ssh-from-windows/save-ppk-file.png)

   > [!WARNING]
   > 此時會出現提示，詢問您是否繼續執行，而不輸入金鑰的複雜密碼。 複雜密碼就像是您的私密金鑰附加的密碼。 即使有人取得您的私密金鑰，他們仍無法僅使用該金鑰進行驗證。 他們還需要複雜密碼。 若沒有複雜密碼，如果有人取得您的私密金鑰，他們即可登入使用該金鑰的任何 VM 或服務。 我們建議您建立複雜密碼。 不過如果您忘記此複雜密碼，將無法加以復原。
   >
   >

    如果您想要輸入複雜密碼，請按一下 [否]，在主要 PuTTYgen 視窗中輸入複雜密碼，然後再次按一下 [儲存私密金鑰]。 否則，按一下 [是] 繼續執行，而不提供選擇性複雜密碼。
9. 輸入名稱和位置以儲存 PPK 檔案。

## <a name="use-putty-to-ssh-to-a-linux-machine"></a>使用 Putty 透過 SSH 連線到 Linux 機器
同樣地，PuTTY 是適用於 Windows 的常見 SSH 用戶端。 您可以免費使用任何您想要的 SSH 用戶端。 下列步驟詳細說明如何使用私密金鑰向使用 SSH 的 Azure VM 進行驗證。 就需要載入您的私密金鑰來驗證 SSH 連線來說，步驟類似於其他 SSH 金鑰用戶端。

1. 從下列位置下載並執行 putty：[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. 從 Azure 入口網站填入 VM 的主機名稱或 IP 位址：

    ![開啟新的 PuTTY 連線](./media/ssh-from-windows/putty-new-connection.png)
3. 選取 [開啟] 之前，按一下 [連線] > [SSH] > [Auth] 索引標籤。瀏覽至您的私密金鑰並加以選取︰

    ![選取您的 PuTTY 私密金鑰進行驗證](./media/ssh-from-windows/putty-auth-dialog.png)
4. 按一下 [開啟]  以連線到虛擬機器。

## <a name="next-steps"></a>後續步驟
您也可以[使用 OS X 和 Linux](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 產生公用和私密金鑰。

如需 Bash for Windows 的詳細資訊以及在 Windows 電腦上備妥 OSS 工具的優點，請參閱 [Bash on Ubuntu on Windows](https://msdn.microsoft.com/commandline/wsl/about)。

如果您無法使用 SSH 連接至 Linux VM，請參閱[針對 Azure Linux VM 的 SSH 連線進行疑難排解](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
