---
title: "Azure Service Fabric 容器安全性 | Microsoft Docs"
description: "立即了解如何保護容器服務。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 3e41e293cc5340c0e32cf2cc6ef7ab7534330884
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="container-security"></a>容器安全性

Service Fabric 為容器內的服務提供了一種機制，供其存取 Windows 或 Linux 叢集 (5.7 版或更高版本) 節點上所安裝的憑證。 此外，Service Fabric 也會為 Windows 容器支援 gMSA (群組受管理的服務帳戶)。 

## <a name="certificate-management-for-containers"></a>容器的憑證管理

您可以藉由指定憑證來保護您的容器服務。 此憑證必須安裝在叢集所有節點上的 LocalMachine 中。 憑證資訊會在應用程式資訊清單的 `ContainerHostPolicies` 標記底下提供，如下列程式碼片段所示：

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

對於 Windows 叢集，在啟動應用程式時，執行階段會讀取憑證，並為每個憑證產生 PFX 檔和密碼。 此 PFX 檔和密碼可使用下列環境變數從容器內部存取： 

* **Certificate_ServicePackageName_CodePackageName_CertName_PFX**
* **Certificate_ServicePackageName_CodePackageName_CertName_Password**

對於 Linux 叢集，只會從容器上 X509StoreName 指定的存放區複製憑證 (PEM)。 Linux 上對應的環境變數為：

* **Certificate_ServicePackageName_CodePackageName_CertName_PEM**
* **Certificate_ServicePackageName_CodePackageName_CertName_PrivateKey**

或者，如果您已經有所需格式的憑證，且只要在容器內存取該憑證，您可以在應用程式套件內建立資料套件，並在應用程式資訊清單中指定下列項目：

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
   <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

容器服務或流程會負責將憑證檔案匯入容器。 若要匯入憑證，您可以使用 `setupentrypoint.sh` 指令碼，或在容器流程內執行自訂程式碼。 用來匯入 PFX 檔的 C# 程式碼範例如下：

```c#
    string certificateFilePath = Environment.GetEnvironmentVariable("Certificate_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
    string passwordFilePath = Environment.GetEnvironmentVariable("Certificate_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
    string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
    password = password.Replace("\0", string.Empty);
    X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
    store.Open(OpenFlags.ReadWrite);
    store.Add(cert);
    store.Close();
```
此 PFX 憑證可用於驗證應用程式或服務，也可以用來保護與其他服務的通訊。 根據預設，檔案只會列入系統的 ACL。 您可以視服務的需求，將它列入其他帳戶的 ACL。


## <a name="set-up-gmsa-for-windows-containers"></a>為 Windows 容器設定 gMSA

為了設定 gMSA (群組受管理的服務帳戶)，所有叢集節點上都會放置認證規格檔案 (`credspec`)。 您可以使用 VM 擴充功能將此檔案複製到所有節點上。  `credspec` 檔案必須包含 gMSA 帳戶資訊。 如需 `credspec` 檔案的詳細資訊，請參閱[服務帳戶](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/live/windows-server-container-tools/ServiceAccounts)。 認證規格和 `Hostname` 標記會於應用程式資訊清單中指定。 `Hostname` 標記必須符合用來執行容器的 gMSA 帳戶名稱。  `Hostname` 標記可讓容器使用 Kerberos 驗證向網域中的其他服務驗證其自身。  下列程式碼片段會顯示用來在應用程式資訊清單中指定 `Hostname` 和 `credspec` 的範例：

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" Hostname="gMSAAccountName">
    <SecurityOption Value="credentialspec=file://WebApplication1.json"/>
  </ContainerHostPolicies>
</Policies>
```
## <a name="next-steps"></a>後續步驟

* [將 Windows 容器部署至 Windows Server 2016 上的 Service Fabric](service-fabric-get-started-containers.md)
* [將 Docker 容器部署至 Linux 上的 Service Fabric](service-fabric-get-started-containers-linux.md)
