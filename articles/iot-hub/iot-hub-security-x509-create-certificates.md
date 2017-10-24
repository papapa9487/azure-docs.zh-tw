---
title: "如何使用 PowerShell 來建立 X.509 憑證 | Microsoft Docs"
description: "如何使用 PowerShell 在本機建立 X.509 憑證，然後在您 Azure IoT 中樞內的模擬環境中啟用 X.509 憑證型安全性。"
services: iot-hub
documentationcenter: 
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2017
ms.author: dkshir
ms.openlocfilehash: 31f94686fed376fbeda2ccdcbc5ed001bcda8126
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="powershell-scripts-to-manage-ca-signed-x509-certificates"></a>可管理 CA 簽署之 X.509 憑證的 PowerShell 指令碼

「IoT 中樞」中的 X.509 憑證型安全性會要求您從 [X.509 憑證鏈結](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification) \(英文\) 開始著手，此鏈結包含根憑證，以及向上包含任何中繼憑證，一直到分葉憑證為止。 這份「做法」指南將逐步引導您執行範例 PowerShell 指令碼，以使用 [OpenSSL](https://www.openssl.org/) 來建立和簽署 X.509 憑證。 建議您將本指南僅用於進行測試，因為這些步驟當中有許多會在實際的製造程序期間進行。 您可以藉由使用「X.509 憑證驗證」，利用這些憑證在您的 Azure IoT 中樞內模擬安全性。 本指南中的步驟會在您 Windows 電腦的本機建立憑證。 

## <a name="prerequisites"></a>必要條件
本教學課程假設您已取得 OpenSSL 二進位檔。 您可以
    - 下載 OpenSSL 原始程式碼並在您的電腦上組建二進位檔，或是 
    - 下載並安裝任何[協力廠商 OpenSSL 二進位檔](https://wiki.openssl.org/index.php/Binaries) \(英文\)，例如從 [SourceForge 上的這個專案](https://sourceforge.net/projects/openssl/) \(英文\) 下載並安裝。

<a id="createcerts"></a>

## <a name="create-x509-certificates"></a>建立 X.509 憑證
下列步驟示範一個如何在本機建立 X.509 根憑證的範例。 

1. 以「系統管理員」身分開啟 PowerShell 視窗。 
2. 瀏覽至您的工作目錄。 執行下列指令碼來設定全域變數。 
    ```PowerShell
    $openSSLBinSource = "<full_path_to_the_binaries>\OpenSSL\bin"
    $errorActionPreference    = "stop"

    # Note that these values are for test purpose only
    $_rootCertSubject         = "CN=Azure IoT Root CA"
    $_intermediateCertSubject = "CN=Azure IoT Intermediate {0} CA"
    $_privateKeyPassword      = "123"

    $rootCACerFileName          = "./RootCA.cer"
    $rootCAPemFileName          = "./RootCA.pem"
    $intermediate1CAPemFileName = "./Intermediate1.pem"
    $intermediate2CAPemFileName = "./Intermediate2.pem"
    $intermediate3CAPemFileName = "./Intermediate3.pem"

    $openSSLBinDir              = Join-Path $ENV:TEMP "openssl-bin"

    # Whether to use ECC or RSA.
    $useEcc                     = $true
    ```
3. 執行下列程式碼，以將 OpenSSL 二進位檔複製到您的工作目錄並設定環境變數：

    ```PowerShell
    function Initialize-CAOpenSSL()
    {
        Write-Host ("Beginning copy of openssl binaries to {0} (and setting up env variables...)" -f $openSSLBinDir)
        if (-not (Test-Path $openSSLBinDir))
        {
            mkdir $openSSLBinDir | Out-Null
        }

        robocopy $openSSLBinSource $openSSLBinDir * /s 
        robocopy $openSSLBinSource . * /s 

        Write-Host "Setting up PATH and other environment variables."
        $ENV:PATH += "; $openSSLBinDir"
        $ENV:OPENSSL_CONF = Join-Path $openSSLBinDir "openssl.cnf"

        Write-Host "Success"
    }
    Initialize-CAOpenSSL
    ```
4. 接著，執行下列指令碼來依指定的「主體名稱」搜尋是否已安裝憑證，以及是否已在您電腦上正確設定 OpenSSL：
    ```PowerShell
    function Get-CACertBySubjectName([string]$subjectName)
    {
        $certificates = gci -Recurse Cert:\LocalMachine\ |? { $_.gettype().name -eq "X509Certificate2" }
        $cert = $certificates |? { $_.subject -eq $subjectName -and $_.PSParentPath -eq "Microsoft.PowerShell.Security\Certificate::LocalMachine\My" }
        if ($NULL -eq $cert)
        {
            throw ("Unable to find certificate with subjectName {0}" -f $subjectName)
        }
    
        write $cert
    }
    function Test-CAPrerequisites()
    {
        $certInstalled = $null
        try
        {
            $certInstalled = Get-CACertBySubjectName $_rootCertSubject
        }
        catch {}

        if ($NULL -ne $certInstalled)
        {
            throw ("Certificate {0} already installed.  Cleanup CA certs 1st" -f $_rootCertSubject)
        }

        if ($NULL -eq $ENV:OPENSSL_CONF)
        {
            throw ("OpenSSL not configured on this system.  Run 'Initialize-CAOpenSSL' (even if you've already done so) to set everything up.")
        }
        Write-Host "Success"
    }
    Test-CAPrerequisites
    ```
    如果一切都設定正確，您應該會看到「成功」訊息。 

<a id="createcertchain"></a>

## <a name="create-x509-certificate-chain"></a>建立 X.509 憑證鏈結
執行下列 PowerShell 指令碼，以建立此範例所要使用且包含根 CA 的憑證鏈結，例如 "CN=Azure IoT Root CA"。 此指令碼除了會在您的工作目錄中建立憑證檔案，也會更新您的 Windows OS 憑證存放區。 
    1. 下列指令碼會針對指定的「主體名稱」和簽署授權單位，建立 PowerShell 函式來建立自我簽署憑證。 
    ```PowerShell
    function New-CASelfsignedCertificate([string]$subjectName, [object]$signingCert, [bool]$isASigner=$true)
    {
        # Build up argument list
        $selfSignedArgs =@{"-DnsName"=$subjectName; 
                           "-CertStoreLocation"="cert:\LocalMachine\My";
                           "-NotAfter"=(get-date).AddDays(30); 
                          }

        if ($isASigner -eq $true)
        {
            $selfSignedArgs += @{"-KeyUsage"="CertSign"; }
            $selfSignedArgs += @{"-TextExtension"= @(("2.5.29.19={text}ca=TRUE&pathlength=12")); }
        }
        else
        {
            $selfSignedArgs += @{"-TextExtension"= @("2.5.29.37={text}1.3.6.1.5.5.7.3.2,1.3.6.1.5.5.7.3.1", "2.5.29.19={text}ca=FALSE&pathlength=0")  }
        }

        if ($signingCert -ne $null)
        {
            $selfSignedArgs += @{"-Signer"=$signingCert }
        }

        if ($useEcc -eq $true)
        {
            $selfSignedArgs += @{"-KeyAlgorithm"="ECDSA_nistP256";
                             "-CurveExport"="CurveName" }
        }

        # Now use splatting to process this
        Write-Host ("Generating certificate {0} which is for prototyping, NOT PRODUCTION.  It will expire in 30 days." -f $subjectName)
        write (New-SelfSignedCertificate @selfSignedArgs)
    }
    ``` 
    2. 下列 PowerShell 函式除了使用 OpenSSL 二進位檔之外，還會使用之前的函式，來建立中繼 X.509 憑證。 
    ```PowerShell
    function New-CAIntermediateCert([string]$subjectName, [Microsoft.CertificateServices.Commands.Certificate]$signingCert, [string]$pemFileName)
    {
        $certFileName = ($subjectName + ".cer")
        $newCert = New-CASelfsignedCertificate $subjectName $signingCert
        Export-Certificate -Cert $newCert -FilePath $certFileName -Type CERT | Out-Null
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\CA" -FilePath $certFileName | Out-Null

        # Store public PEM for later chaining
        openssl x509 -inform deer -in $certFileName -out $pemFileName

        del $certFileName
   
        write $newCert
    }  
    ```
    3. 下列 PowerShell 函式會建立 X.509 憑證鏈結。 如需詳細資訊，請參閱[憑證鏈結](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification) \(英文\)。
    ```PowerShell
    function New-CACertChain()
    {
        Write-Host "Beginning to install certificate chain to your LocalMachine\My store"
        $rootCACert =  New-CASelfsignedCertificate $_rootCertSubject $null
    
        Export-Certificate -Cert $rootCACert -FilePath $rootCACerFileName  -Type CERT
        Import-Certificate -CertStoreLocation "cert:\LocalMachine\Root" -FilePath $rootCACerFileName

        openssl x509 -inform der -in $rootCACerFileName -out $rootCAPemFileName

        $intermediateCert1 = New-CAIntermediateCert ($_intermediateCertSubject -f "1") $rootCACert $intermediate1CAPemFileName
        $intermediateCert2 = New-CAIntermediateCert ($_intermediateCertSubject -f "2") $intermediateCert1 $intermediate2CAPemFileName
        $intermediateCert3 = New-CAIntermediateCert ($_intermediateCertSubject -f "3") $intermediateCert2 $intermediate3CAPemFileName
        Write-Host "Success"
    }    
    ```
    此指令碼會在您的工作目錄中建立一個名為 *RootCA.cer* 的檔案。 
    4. 最後，藉由在 PowerShell 視窗中執行 `New-CACertChain` 命令，使用上述 PowerShell 函式來建立 X.509 憑證鏈結。 


<a id="signverificationcode"></a>

## <a name="proof-of-possession-of-your-x509-ca-certificate"></a>X.509 CA 憑證的所有權證明

此指令碼會針對您的 X.509 憑證執行「所有權證明」流程。 

在您桌面上的 PowerShell 視窗中，執行下列程式碼：
    ```PowerShell
    function New-CAVerificationCert([string]$requestedSubjectName)
    {
        $cnRequestedSubjectName = ("CN={0}" -f $requestedSubjectName)
        $verifyRequestedFileName = ".\verifyCert4.cer"
        $rootCACert = Get-CACertBySubjectName $_rootCertSubject
        Write-Host "Using Signing Cert:::" 
        Write-Host $rootCACert
    
        $verifyCert = New-CASelfsignedCertificate $cnRequestedSubjectName $rootCACert $false

        Export-Certificate -cert $verifyCert -filePath $verifyRequestedFileName -Type Cert
        if (-not (Test-Path $verifyRequestedFileName))
        {
            throw ("Error: CERT file {0} doesn't exist" -f $verifyRequestedFileName)
        }
    
        Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnRequestedSubjectName, (Join-Path (get-location).path $verifyRequestedFileName)) 
    }
    New-CAVerificationCert "<your verification code>"
    ```
   這會在您的工作目錄中建立憑證，此憑證會具有指定的主體名稱、由 CA 簽署且檔案名稱為 *VerifyCert4.cer*。 此憑證會協助向您擁有此 CA 之簽署權限 (亦即私密金鑰) 的 IoT 中樞進行驗證。


<a id="createx509device"></a>

## <a name="create-leaf-x509-certificate-for-your-device"></a>為您的裝置建立分葉 X.509 憑證

本區段說明如何使用 PowerShell 指令碼來建立分葉裝置憑證和對應的憑證鏈結。 

在您本機電腦上的 PowerShell 視窗中，執行下列指令碼來為此裝置建立一個 CA 簽署的 X.509 憑證：
    ```PowerShell
    function New-CADevice([string]$deviceName, [string]$signingCertSubject=$_rootCertSubject)
    {
        $cnNewDeviceSubjectName = ("CN={0}" -f $deviceName)
        $newDevicePfxFileName = ("./{0}.pfx" -f $deviceName)
        $newDevicePemAllFileName      = ("./{0}-all.pem" -f $deviceName)
        $newDevicePemPrivateFileName  = ("./{0}-private.pem" -f $deviceName)
        $newDevicePemPublicFileName   = ("./{0}-public.pem" -f $deviceName)
    
        $signingCert = Get-CACertBySubjectName $signingCertSubject ## "CN=Azure IoT CA Intermediate 1 CA"

        $newDeviceCertPfx = New-CASelfSignedCertificate $cnNewDeviceSubjectName $signingCert $false
    
        $certSecureStringPwd = ConvertTo-SecureString -String $_privateKeyPassword -Force -AsPlainText

        # Export the PFX of the cert we've just created.  The PFX is a format that contains both public and private keys.
        Export-PFXCertificate -cert $newDeviceCertPfx -filePath $newDevicePfxFileName -password $certSecureStringPwd
        if (-not (Test-Path $newDevicePfxFileName))
        {
            throw ("Error: CERT file {0} doesn't exist" -f $newDevicePfxFileName)
        }

        # Begin the massaging.  First, turn the PFX into a PEM file which contains public key, private key, and other attributes.
        Write-Host ("When prompted for password by openssl, enter the password as {0}" -f $_privateKeyPassword)
        openssl pkcs12 -in $newDevicePfxFileName -out $newDevicePemAllFileName -nodes

        # Convert the PEM to get formats we can process
        if ($useEcc -eq $true)
        {
            openssl ec -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
        }
        else
        {
            openssl rsa -in $newDevicePemAllFileName -out $newDevicePemPrivateFileName
        }
        openssl x509 -in $newDevicePemAllFileName -out $newDevicePemPublicFileName
 
        Write-Host ("Certificate with subject {0} has been output to {1}" -f $cnNewDeviceSubjectName, (Join-Path (get-location).path $newDevicePemPublicFileName)) 
    }
    ```
   然後，在您的 PowerShell 視窗中，使用您用來建立裝置的易記名稱來執行 `New-CADevice "<yourTestDevice>"`。 出現輸入 CA 私密金鑰的密碼提示時，輸入 "123"。 這會在您的工作目錄中建立一個 _<yourTestDevice>.pfx_ 檔案。

