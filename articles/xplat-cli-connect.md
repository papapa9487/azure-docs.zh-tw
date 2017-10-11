---
title: "從 CLI 登入 Azure | Microsoft Docs"
description: "從適用於 Mac、Linux 和 Windows 的 Azure 命令列介面 (Azure CLI) 連接到您的 Azure 訂用帳戶"
editor: tysonn
manager: timlt
documentationcenter: 
author: squillace
services: virtual-machines-linux,virtual-network,storage,azure-resource-manager
tags: azure-resource-manager,azure-service-management
ms.assetid: ed856527-d75e-4e16-93fb-253dafad209d
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: rasquill
"\"/": 
ms.openlocfilehash: 31efab60690b54faf7992251fcd01e307c4464f2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2017
---
# <a name="log-in-to-azure-from-the-azure-cli"></a>從 Azure CLI 登入 Azure
Azure CLI 是一組開放原始碼的跨平台命令，可供您運用在 Azure 資源上。 本文描述提供 Azure 帳戶認證以將 Azure CLI 連線至 Azure 訂用帳戶的不同方式：

* 透過 Azure Active Directory 執行 `azure login` CLI 命令以進行驗證。 這個方法可讓您在兩個[命令模式](#cli-command-modes)中存取 CLI 命令。 當您執行命令時若沒有其他選項，`azure login` 會提示您透過 Web 入口網站以互動方式繼續登入。 如需其他 `azure login` 命令選項，請參閱本文中的案例，或輸入 `azure login --help`。
* 如果您只需要使用 Azure 服務管理模式 CLI 命令 (不建議用於大部分的新部署)，您可以在您的電腦上下載並安裝發佈設定檔。

如果您尚未安裝 CLI，請參閱 [安裝 Azure CLI](cli-install-nodejs.md)。 如果您沒有 Azure 訂用帳戶，則只需要幾分鐘的時間就可以建立 [免費帳戶](http://azure.microsoft.com/free/) 。

如需不同帳戶身分識別與 Azure 訂用帳戶的背景，請參閱 [Azure 訂用帳戶如何與 Azure Active Directory 產生關聯](active-directory/active-directory-how-subscriptions-associated-directory.md)。

## <a name="scenario-1-azure-login-with-interactive-login"></a>案例 1：使用互動式登入方法來登入 Azure
使用特定帳戶，CLI 會要求您執行 `azure login`，然後透過 Web 入口網站使用 Web 瀏覽器繼續進行登入程序，此程序稱為*互動式登入*。 常見的原因是您有工作或學校帳戶 (也稱為*組織帳戶*) 設定為需要多重要素驗證。 當您想要使用 Resource Manager 模式命令時，也會使用互動方式登入您的 Microsoft 帳戶。

互動式登入很簡單︰輸入 `azure login` 即可，不需要任何選項，如下列範例所示：

```
azure login
```                                                                                             

輸出畫面如下所示：

```         
info:    Executing command login
info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code XXXXXXXXX to authenticate.
```
複製命令輸出中提供的程式碼，並開啟瀏覽器至 http://aka.ms/devicelogin，或其他頁面 (若已指定)。 (您可以在同一台電腦上，或在不同電腦或裝置上開啟瀏覽器。)輸入程式碼後，系統會提示您針對您要使用之身分識別輸入使用者名稱和密碼。 完成此程序之後，命令殼層便完成登入。 會出現類似下面的畫面：

    info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Added subscription Azure Free Trial
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

> [!NOTE]
> 透過互動式登入，驗證和授權都是使用 Azure Active Directory 來執行。 如果您使用 Microsoft 帳戶身分識別，登入程序就會存取您的 Azure Active Directory 預設網域。 (如果您註冊免費 Azure 帳戶，Azure Active Directory 會為您的帳戶自動建立預設網域。)
>
>

## <a name="scenario-2-azure-login-with-a-username-and-password"></a>案例 2：利用使用者名稱和密碼來登入 Azure
當您想要使用不需要多重要素驗證的工作或學校帳戶時，可以使用 `azure login` 命令搭配使用者名稱 (`-u`) 參數以進行驗證。 系統會提示您在命令列中的密碼 (或者可以選擇性地傳遞密碼做為 `azure login` 命令的其他參數)。 下列範例會傳遞組織帳戶的使用者名稱︰

    azure login -u myUserName@contoso.onmicrosoft.com

系統會提示您輸入密碼：

    info:    Executing command login
    Password: *********

然後完成登入程序。

    info:    Added subscription Visual Studio Ultimate with MSDN
    +
    info:    login command OK

如果這是您第一次使用這些認證來登入，系統會要求您確認是否要快取驗證權杖。 如果您先前已使用 (如本文稍後所述的) `azure logout` 命令，也會出現此提示。 如果要在自動化作業中略過此提示，請使用 `azure login` 參數執行 `-q`。

## <a name="scenario-3-azure-login-with-a-service-principal"></a>案例 3：使用服務主體來登入 Azure
如果您已建立 Active Directory 應用程式的服務主體，而且服務主體擁有訂用帳戶的權限，您就可以使用 `azure login` 命令來驗證服務主體。 根據您的案例，您可以提供服務主體的認證做為 `azure login` 命令的明確參數。 例如，下列命令會傳遞服務主體名稱與 Active Directory 租用戶識別碼：

    azure login -u https://www.contoso.org/example --service-principal --tenant myTenantID

接著，系統會提示您提供密碼。 您也可以透過 CLI 指令碼或應用程式程式碼提供認證，或針對自動化案例使用憑證以非互動的方式驗證服務主體。 如需詳細資料與範例，請參閱[使用 Azure Resource Manager 驗證服務主體](resource-group-authenticate-service-principal-cli.md)。

## <a name="scenario-4-use-a-publish-settings-file"></a>案例 4：使用發佈設定檔
如果您只需要使用 Azure 服務管理模式 CLI 命令 (例如，在傳統部署模型中部署 Azure VM)，您可以使用發佈設定檔連線。 此方法會在您的本機電腦上安裝憑證，只要訂用帳戶和憑證有效，該憑證便可讓您執行管理工作。

* **若要下載您帳戶的發佈設定檔**，請輸入 `azure config mode asm` 以確保 CLI 是在服務管理模式中。 然後，執行下列命令：

        azure account download

這會開啟您的預設瀏覽器，提示您登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。 登入後便會下載 `.publishsettings` 檔案。 請記下此檔案的儲存位置。

> [!NOTE]
> 如果您的帳戶與多個 Azure Active Directory 租用戶相關聯，則可能會提示您選取要在其中下載發行設定檔的 Active Directory。
>
>

在使用下載頁面或是造訪 Azure 傳統入口網站選定之後，所選的 Active Directory 會成為傳統入口網站與下載頁面使用的預設值。 建立好預設值之後，您會在下載頁面上方看到 [click here to return to the selection page]。 請使用提供的連結，返回選取頁面。

* **若要匯入發佈設定檔案**，請執行下列命令：

        azure account import <path to your .publishsettings file>

> [!IMPORTANT]
> 匯入發佈設定之後，請刪除 `.publishsettings` 檔案。 Azure CLI 已不再需要它，而且它可用於存取您的訂用帳戶，因此造成安全風險。
>
>

## <a name="cli-command-modes"></a>CLI 命令模式
Azure CLI 提供兩種命令模式來使用具備不同命令集的 Azure 資源︰

* **Resource Manager 模式** - 適用於以 Resource Manager 部署模型使用 Azure 資源。 若要設定此模式，請執行 `azure config mode arm`。
* **服務管理模式** - 適用於以傳統部署模型使用 Azure 資源。 若要設定此模式，請執行 `azure config mode asm`。

初次安裝時，最新版本的 CLI 是在 Resource Manager 模式中。

> [!NOTE]
> Resource Manager 模式與服務管理模式是互斥的。 亦即，任一模式所建立的資源，將無法由另一種模式來管理。
>
>

## <a name="multiple-subscriptions"></a>多重訂閱
如果您擁有多個 Azure 訂用帳戶，連接到 Azure 會針對與您的認證相關聯的所有訂用帳戶授予存取權限。 系統會選取一個訂用帳戶做為預設值，以供 Azure CLI 用來執行作業。 您可以使用 `azure account list` 命令檢視訂用帳戶，包括目前的預設訂用帳戶。 此命令會傳回類似以下的資訊：

    info:    Executing command account list
    data:    Name              Id                                    Current
    data:    ----------------  ------------------------------------  -------
    data:    Azure-sub-1       ####################################  true
    data:    Azure-sub-2       ####################################  false

在上述清單中，[目前] 欄會指出目前的預設訂用帳戶為 Azure-sub-1。 若要變更預設訂用帳戶，請使用 `azure account set` 命令，並指定您想要設為預設值的訂用帳戶。 例如：

    azure account set Azure-sub-2

此動作會將預設訂用帳戶變更為 Azure-sub-2。

> [!NOTE]
> 變更預設的訂用帳戶會立即生效，而且是全域變更；無論您是從相同的命令列執行個體或不同的執行個體執行新的 Azure CLI 命令，它們都會使用新的預設訂用帳戶。
>
>

如果您要 Azure CLI 使用非預設的訂用帳戶，但又不想變更目前的預設值，可以使用適用於該命令的 `--subscription` 選項，並提供希望該作業使用的訂用帳戶名稱。

一旦您連接到 Azure 訂用帳戶之後，就可以開始搭配使用 Azure CLI 命令和 Azure 資源。

## <a name="storage-of-cli-settings"></a>CLI 設定的儲存位置
無論您是使用 `azure login` 命令登入或是匯入發佈設定，CLI 設定檔和記錄檔都會儲存在您 `user` 目錄中的 `.azure` 目錄內。 `user` 目錄已受到作業系統的保護。 不過，建議您採取額外的步驟來加密 `user` 目錄。 做法如下：

* 在 Windows 中，修改目錄屬性或使用 BitLocker。
* 在 Mac 中，開啟目錄的 FileVault。
* 在 Ubuntu 中，使用「加密主目錄」功能。 其他 Linux 散發套件提供類似的功能。

## <a name="logging-out"></a>登出
若要登出，請使用下列命令：

    azure logout -u <username>

如果與帳戶關聯的訂用帳戶僅能對 Active Directory 驗證，進行登出時就會從本機設定檔中刪除訂用帳戶資訊。 不過，如果也已匯入訂用帳戶的發佈設定檔，則進行登出時只會從本機設定檔中刪除 Active Directory 相關的資訊。

## <a name="next-steps"></a>後續步驟
* 若要使用 Azure CLI 命令，請參閱 [Resource Manager 模式中的 Azure CLI 命令](virtual-machines/azure-cli-arm-commands.md)和[服務管理模式中的 Azure CLI 命令](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)。
* 若要深入了解 Azure CLI、下載來源程式碼、回報問題，或是參與專案，請造訪 [Azure CLI 的 GitHub 儲存機制](https://github.com/azure/azure-xplat-cli)。
* 如果您在使用 Azure CLI 或 Azure 方面遇到問題，請造訪 [Azure 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting)(英文)。
