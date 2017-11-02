---
title: "對持續傳遞啟用遠端偵錯 | Microsoft Docs"
description: "了解在使用連續傳遞來部署至 Azure 時如何啟用遠端偵錯"
services: cloud-services
documentationcenter: .net
author: mikejo
manager: ghogen
editor: 
ms.assetid: 7d423639-3b2f-4ca5-ac5a-9ac19a217c29
ms.service: cloud-services
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/18/2016
ms.author: mikejo
ms.openlocfilehash: c2bd67afc0c289de94019497e57b57f97a759f3a
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2017
---
# <a name="enable-remote-debugging-when-using-continuous-delivery-to-publish-to-azure"></a>使用連續傳遞來發行至 Azure 時啟用遠端偵錯
當您使用 [連續傳遞](cloud-services-dotnet-continuous-delivery.md) 來發行至 Azure 時，您可以依照下列步驟在 Azure 中為雲端服務或虛擬機器啟用遠端偵錯。

## <a name="enabling-remote-debugging-for-cloud-services"></a>對雲端服務啟用遠端偵錯
1. 在組建代理程式上，設定 Azure 的初始環境，如 [Azure 的命令列建置](http://msdn.microsoft.com/library/hh535755.aspx)所述。
2. 因為封裝需要遠端偵錯執行階段 (msvsmon.exe)，請安裝 **Remote Tools for Visual Studio**。

    * [Remote Tools for Visual Studio 2017](https://go.microsoft.com/fwlink/?LinkId=746570)
    * [Remote Tools for Visual Studio 2015](https://go.microsoft.com/fwlink/?LinkId=615470)
    * [Remote Tools for Visual Studio 2013 Update 5](https://www.microsoft.com/download/details.aspx?id=48156)
    
    或者，您也可以從已安裝 Visual Studio 的系統中複製遠端偵錯二進位檔案。

3. 建立憑證，如 [Azure 雲端服務憑證概觀](cloud-services-certs-create.md)中所述。 保留 .pfx 和 RDP 憑證指紋，並將憑證上傳至目標雲端服務。
4. 在 MSBuild 命令列中使用下列選項，指定啟用遠端偵測來建置和封裝 (以系統和專案檔案的實際路徑替代角括弧括住的項目)。
   
        msbuild /TARGET:PUBLISH /PROPERTY:Configuration=Debug;EnableRemoteDebugger=true;VSX64RemoteDebuggerPath="<remote tools path>";RemoteDebuggerConnectorCertificateThumbprint="<thumbprint of the certificate added to the cloud service>";RemoteDebuggerConnectorVersion="2.7" "<path to your VS solution file>"
   
    `VSX64RemoteDebuggerPath` 是存放 Visual Studio 遠端工具中 msvsmon.exe 的資料夾的路徑。
    `RemoteDebuggerConnectorVersion` 是雲端服務中的 Azure SDK 版本。 它也應符合隨 Visual Studio 一起安裝的版本。
5. 使用前一個步驟中產生的封裝和 .cscfg 檔案來發行至目標雲端服務。
6. 將憑證匯入 (.pfx file) 已安裝包含 Azure SDK for .NET 的 Visual Studio  的機器。 請務必匯入至 `CurrentUser\My` 憑證存放區，否則附加至 Visual Studio 中的偵錯工具將會失敗。

## <a name="enabling-remote-debugging-for-virtual-machines"></a>對虛擬機器啟用遠端偵錯
1. 建立 Azure 虛擬機器。 請參閱[建立執行 Windows Server 的虛擬機器](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)或[在 Visual Studio 中建立及管理 Azure 虛擬機器](../virtual-machines/windows/classic/manage-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。
2. 在 [Azure 傳統入口網站頁面](http://go.microsoft.com/fwlink/p/?LinkID=269851)，檢視虛擬機器儀表板來查看虛擬機器的 **RDP 憑證指紋**。 此值是用於延伸模組組態中的 `ServerThumbprint` 值。
3. 建立用戶端憑證，如 [Azure 雲端服務憑證概觀](cloud-services-certs-create.md) 中所述 (保留 .pfx 和 RDP 憑證指紋)。
4. 如 [如何安裝及設定 Azure PowerShell](/powershell/azure/overview)中所述安裝 Azure Powershell (0.7.4 版或更新版本)
5. 執行下列指令碼來啟用 RemoteDebug 延伸模組。 將路徑和個人資料替換成您自己的路徑和個人資料，例如您的訂用帳戶、服務名稱和指紋。
   
   > [!NOTE]
   > 此指令碼是針對 Visual Studio 2015 所設計。 如果您使用 Visual Studio 2013 或 Visual Studio 2017，請將以下的 `$referenceName` 和 `$extensionName` 指派修改為 `RemoteDebugVS2013` 或 `RemoteDebugVS2017`。

    ```powershell   
    Add-AzureAccount

    Select-AzureSubscription "My Microsoft Subscription"

    $vm = Get-AzureVM -ServiceName "mytestvm1" -Name "mytestvm1"

    $endpoints = @(
                    ,@{Name="RDConnVS2013"; PublicPort=30400; PrivatePort=30398}
                    ,@{Name="RDFwdrVS2013"; PublicPort=31400; PrivatePort=31398}
                )

    foreach($endpoint in $endpoints)
    {
        Add-AzureEndpoint -VM $vm -Name $endpoint.Name -Protocol tcp -PublicPort $endpoint.PublicPort -LocalPort $endpoint.PrivatePort
    }

    $referenceName = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug.RemoteDebugVS2015"
    $publisher = "Microsoft.VisualStudio.WindowsAzure.RemoteDebug"
    $extensionName = "RemoteDebugVS2015"
    $version = "1.*"
    $publicConfiguration = "<PublicConfig><Connector.Enabled>true</Connector.Enabled><ClientThumbprint>56D7D1B25B472268E332F7FC0C87286458BFB6B2</ClientThumbprint><ServerThumbprint>E7DCB00CB916C468CC3228261D6E4EE45C8ED3C6</ServerThumbprint><ConnectorPort>30398</ConnectorPort><ForwarderPort>31398</ForwarderPort></PublicConfig>"

    $vm | Set-AzureVMExtension -ReferenceName $referenceName -Publisher $publisher -ExtensionName $extensionName -Version $version -PublicConfiguration $publicConfiguration

    foreach($extension in $vm.VM.ResourceExtensionReferences)
    {
        if(($extension.ReferenceName -eq $referenceName) `
        -and ($extension.Publisher -eq $publisher) `
        -and ($extension.Name -eq $extensionName) `
        -and ($extension.Version -eq $version))
        {
            $extension.ResourceExtensionParameterValues[0].Key = 'config.txt'
            break
        }
    }

    $vm | Update-AzureVM
    ```

6. 將憑證 (.pfx) 匯入已安裝 Visual Studio with Azure SDK for .NET 的機器。

