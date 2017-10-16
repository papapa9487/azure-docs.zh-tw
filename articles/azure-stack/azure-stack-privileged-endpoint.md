---
title: "使用 Azure Stack 中具有特殊權限的端點 | Microsoft Docs"
description: "示範如何使用 Azure Stack 中具有特殊權限的端點 (適用於 Azure Stack 操作員)。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 9769b12064216680bb1b2db8c1fd7449927c7771
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="using-the-privileged-endpoint-in-azure-stack"></a>使用 Azure Stack 中具有特殊權限的端點

適用於：Azure Stack 整合系統和 Azure Stack 開發套件

作為 Azure Stack 操作員，您應該在大部分的日常管理工作使用系統管理員入口網站、PowerShell 或 Azure Resource Manager API。 不過，針對較非一般的作業，您必須使用具有特殊權限的端點。 這個端點是預先設定的遠端 PowerShell 主控台，能提供恰到好處的功能來協助您執行必要的工作。 端點會利用 PowerShell JEA (恰到好處的系統管理) 只公開一組有限的 Cmdlet。 若要存取具有特殊權限的端點，並叫用一組有限的 Cmdlet，可使用低權限的帳戶。 無須系統管理員帳戶。 為了增加安全性，不允許使用指令碼。

您可以使用具有特殊權限的端點來執行如下所示的工作：

- 執行低階工作，例如[收集診斷記錄](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics#log-collection-tool)。
- 執行許多整合系統的部署後資料中心整合工作，例如在部署之後新增網域名稱系統 (DNS) 轉送工具、設定 Graph 整合、Active Directory Federation Services (AD FS) 整合、憑證旋轉等等。
- 與支援人員共同合作，以取得整合系統深入疑難排解的暫時、高階存取權。 

具有特殊權限的端點會記錄您在 PowerShell 工作階段中執行的每個動作 (和其對應的輸出)。 這會提供完全透明化和完整稽核的作業。 您可以保留這些記錄檔以供未來稽核使用。

> [!NOTE]
> 在 Azure Stack 開發套件 (ASDK) 中，您可以在開發套件主機上，直接從 PowerShell 工作階段執行具有特殊權限的端點中的一些可用命令。 不過，您需要使用具有特殊權限的端點來測試一些作業，例如記錄收集，因為這是在整合系統環境中執行特定作業的唯一可用方法。

## <a name="access-the-privileged-endpoint"></a>存取具有特殊權限的端點

您可在裝載具有特殊權限端點的虛擬機器上，透過遠端 PowerShell 工作階段來存取具有特殊權限的端點。 在 ASDK 中，此虛擬機器名為 AzS ERCS01。 如果您是使用整合系統，則有三個具有特殊權限端點的執行個體，每個都會在不同主機上的虛擬機器內部執行 (Prefix-ERCS01、Prefix-ERCS02 或 Prefix-ERCS03) 以提供恢復功能。 

在開始針對整合系統進行此程序之前，請確定您可以透過 IP 位址或 DNS 來存取具有特殊權限的端點。 在初始部署 Azure Stack 後，您只能使用 IP 位址來存取具有特殊權限的端點，因為尚未設定 DNS 整合。 OEM 硬體廠商會提供名為 "AzureStackStampDeploymentInfo" 的 JSON 檔案，其中包含具有特殊權限端點的 IP 位址。

建議您僅從硬體生命週期主機，或從專用、鎖定的電腦，例如[特殊權限存取工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)來連線到具有特殊權限的端點。

1. 根據您的環境執行下列動作：

    - 在整合系統上執行下列命令，可在硬體生命週期主機或特殊權限存取工作站上，將具有特殊權限的端點新增為受信任的主機。

      ````PowerShell
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ````
    - 如果您是執行 ADSK，請登入開發套件主機。

2. 在硬體生命週期主機或特殊權限存取工作站上，開啟提升權限的 Windows PowerShell 工作階段。 執行下列命令，在裝載具有特殊權限端點的虛擬機器上建立遠端工作階段：
 
    - 在整合系統上：
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ````
      `ComputerName` 參數可以是其中一部裝載具有特殊權限端點之虛擬機器的 IP 位址或 DNS 名稱。 
    - 如果您是執行 ADSK：
     
      ````PowerShell
        $cred = Get-Credential

        Enter-PSSession -ComputerName azs-ercs01`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```` 
   出現輸入提示時，使用下列認證：

      - **使用者名稱**：指定 CloudAdmin 帳戶，格式為 **&lt;*Azure Stack 網域*&gt;\cloudadmin**。 (若為 ASDK，使用者名稱是 **azurestack\cloudadmin**。)
      - **密碼**：輸入與 AzureStackAdmin 網域系統管理員帳戶安裝期間所提供的相同密碼。
    
3.  連線之後，系統會提示您變更為 **[*IP 位址或 ERCS VM 名稱*]: PS>** 或變更為 **[azs-ercs01]: PS>**，取決於環境。 從這裡執行 `Get-Command` 可檢視可用的 Cmdlet 清單。

    ![顯示可用命令清單的 Get-command Cmdlet 輸出](media/azure-stack-privileged-endpoint/getcommandoutput.png)

    這些 Cmdlet 有許多僅供整合系統環境 (例如與資料中心整合相關的 cmdlet) 使用。 在 ASDK 中，下列 Cmdlet 已經過驗證：

    - Clear-Host
    - Close-PrivilegedEndpoint
    - Exit-PSSession
    - Get-AzureStackLog
    - Get-AzureStackStampInformation
    - Get-Command
    - Get-FormatData
    - Get-Help
    - Get-ThirdPartyNotices
    - Measure-Object
    - New-CloudAdminUser
    - Out-Default
    - Remove-CloudAdminUser
    - Select-Object
    - Set-CloudAdminUserPassword
    - Stop-AzureStack
    - Get-ClusterLog

4.  因為不允許指令碼，您無法將索引標籤完成用於參數值。 若要取得特定 cmdlet 的參數清單，請執行下列命令：

    ````PowerShell
    Get-Command <cmdlet_name> -Syntax
    ```` 
    如果您嘗試任何一種指令碼作業，作業就會失敗，並出現錯誤 **ScriptsNotAllowed**。 這是預期行為。

## <a name="close-the-privileged-endpoint-session"></a>關閉具有特殊權限的端點工作階段

 如先前所述，具有特殊權限的端點會記錄您在 PowerShell 工作階段中執行的每個動作 (和其對應的輸出)。 您應該使用 `Close-PrivilegedEndpoint`Cmdlet 來關閉工作階段。 此 Cmdlet 會正確關閉端點，並將記錄檔傳送至外部檔案共用作為保留。

若要關閉端點工作階段：

1. 建立可由具有特殊權限的端點存取的外部檔案共用。 在開發套件環境中，您只能在開發套件主機上建立檔案共用。
2. 執行 `Close-PrivilegedEndpoint` Cmdlet。 
3. 系統會提示您要存放文字記錄記錄檔的路徑。 指定在稍早建立的檔案共用，格式為 &#92;&#92;servername&#92;sharename。 如果您未指定路徑，Cmdlet 就會失敗，且工作階段維持開啟狀態。 

    ![會顯示您指定文字記錄目的地路徑的 Close-PrivilegedEndpoint Cmdlet 輸出](media/azure-stack-privileged-endpoint/closeendpoint.png)

文字記錄記錄檔成功傳送至檔案共用之後，會自動從具有特殊權限的端點刪除。 如果您使用 `Exit-PSSession` 或 `Exit` Cmdlet 來關閉具有特殊權限的端點工作階段，或您只關閉 PowerShell 主控台，文字記錄記錄檔並不會傳送至檔案共用。 它們會留在具有特殊權限的端點工作階段。 下次您執行 `Close-PrivilegedEndpoint` 並包含檔案共用時，也將會傳送上一個工作階段的文字記錄記錄。

## <a name="next-steps"></a>後續步驟
[Azure Stack 診斷工具](azure-stack-diagnostics.md)







