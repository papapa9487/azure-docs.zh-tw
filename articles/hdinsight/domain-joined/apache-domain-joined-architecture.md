---
title: "已加入網域的 Azure HDInsight 架構 | Microsoft Docs"
description: "了解如何規劃已加入網域的 HDInsight。"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/21/2017
ms.author: saurinsh
ms.openlocfilehash: 9deb5e4dbd925c4fdc523d8d21afbcfbf85947b8
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2017
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>規劃 HDInsight 中已加入網域的 Azure Hadoop 叢集

傳統的 Hadoop 是單一使用者叢集。 它適合大部分以小型應用程式團隊建置大型資料工作負載的公司。 隨著 Hadoop 日益普及，許多企業都轉而採用由 IT 團隊管理叢集，且多個應用程式團隊共用叢集的模式。 因此，Azure HDInsight 中呼聲最高的就是涉及多使用者叢集的功能。

HDInsight 不會建置自己的多使用者驗證和授權，而是依賴最受歡迎的識別提供者 – Active Directory (AD)。 AD 強大的安全性功能可用來管理 HDInsight 中的多使用者授權。 藉由整合 HDInsight 與 AD，您可以使用 AD 認證來與叢集通訊。 HDInsight 會將 AD 使用者對應至本機 Hadoop 使用者，讓通過驗證的使用者能在 HDInsight 上順暢地執行 Ambari、Hive 伺服器、Ranger、Spark Thrift 伺服器等所有服務。

## <a name="integrate-hdinsight-with-active-directory"></a>整合 HDInsight 與 Active Directory

當您整合 HDInsight 與 Active Directory 時，HDInsight 叢集節點會加入 AD 網域。 HDInsight 會針對在叢集上執行的 Hadoop 服務建立服務主體，並將它們放在網域中指定的組織單位 (OU) 內。 HDInsight 也會針對加入網域的節點 IP 位址，在網域中建立反向 DNS 對應。

Active Directory 有兩個部署選項：
* **[Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md)：**這項服務會提供與 Windows Server Active Directory 完全相容的受管理 Active Directory 網域。 Microsoft 會負責管理、修補及監視 AD 網域。 您可以放心地部署叢集，不必擔心網域控制站的維護問題。 使用者、群組和密碼都會從 Azure Active Directory 同步過來，讓使用者使用其公司認證來登入叢集。

* **Azure IaaS VM 上的 Windows Server Active Directory 網域：**若使用此選項，您將會在 Azure IaaS VM 上部署和管理您自己的 Windows Server Active Directory 網域。 

您可以使用多個架構來達到這種設定。 您可以選擇下列架構。


### <a name="hdinsight-integrated-with-an-azure-ad-domain-services-managed-ad-domain"></a>與 Azure AD Domain Services 的受管理 AD 網域整合的 HDInsight
您可以部署 [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md) (Azure AD DS) 的受管理網域。 Azure AD DS 會在由 Microsoft 管理、更新和監視的 Azure 中提供受管理的 AD 網域。 它會建立兩個網域控制站 (以提供高可用性)，並納入 DNS 服務。 然後，您可以將 HDInsight 叢集整合到這個受管理的網域。 使用此部署選項時，您不必擔心網域控制站的管理、修補、更新和監視問題。

![已加入網域的 HDInsight 叢集拓撲](./media/apache-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

要與 Azure AD Domain Services 整合所需符合的必要條件：

* [佈建 Azure AD Domain Services 的受管理網域](../../active-directory-domain-services/active-directory-ds-getting-started.md)。
* 建立[組織單位](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)，以在其中放置叢集所用的 HDInsight 叢集 VM 和服務主體。
* 在設定 Azure AD DS 時設定 [LDAPS](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md)。 必須使用公開的憑證授權單位所核發的憑證 (而非自我簽署憑證) 來設定 LDAPS。
* 必須在受管理的網域上建立反向 DNS 區域，以供 HDInsight 子網路的 IP 位址範圍使用 (例如上一張圖中的 10.2.0.0/24)。
* 設定[要進行 NTLM 和 Kerberos 驗證所需之密碼雜湊的同步處理](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)，方向是從 Azure AD 到 Azure AD DS 的受管理網域。
* 需要服務帳戶或使用者帳戶。 使用此帳戶來建立 HDInsight 叢集。 此帳戶必須具備下列權限：

    - 在組織單位內建立服務主體物件和電腦物件的權限
    - 建立反向 DNS Proxy 規則的權限
    - 將電腦加入 Azure AD 網域的權限


### <a name="hdinsight-integrated-with-windows-server-ad-running-on-azure-iaas"></a>與正在 Azure IaaS 上執行的 Windows Server AD 整合的 HDInsight

您可以在 Azure 中的一個 (或多個) 虛擬機器 (VM) 上部署 Windows Server Active Directory Domain Services 角色，並將它們升階到網域控制站。 您可以使用資源管理員部署模型將這些網域控制站 VM 部署到與 HDInsight 叢集相同的虛擬網路內。 如果您將網域控制站部署到不同的虛擬網路內，則必須使用 [VNet 對 VNet 對等互連](../../virtual-network/virtual-network-create-peering.md)將這些虛擬網路對等互連。 

[詳細資訊 - 在 Azure VM 上部署 Windows Server Active Directory](../../active-directory/virtual-networks-windows-server-active-directory-virtual-machines.md)

![已加入網域的 HDInsight 叢集拓撲](./media/apache-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> 在此架構中，您無法搭配使用 Azure Data Lake Store 與 HDInsight 叢集。


要與 Azure VM 上的 Windows Server Active Directory 整合所需符合的必要條件：

* 您必須建立[組織單位](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)，以在其中放置叢集所用的 HDInsight 叢集 VM 和服務主體。
* 必須設定[輕量型目錄存取通訊協定](../../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) (LDAP)，才能與 AD 進行通訊。 必須使用真正的憑證 (而非自我簽署憑證) 來設定 LDAPS 的憑證。
* 必須在網域上建立反向 DNS 區域，以供 HDInsight 子網路的 IP 位址範圍使用 (例如上一張圖中的 10.2.0.0/24)。
* 需要服務帳戶或使用者帳戶。 使用此帳戶來建立 HDInsight 叢集。 此帳戶必須具備下列權限：

    - 在組織單位內建立服務主體物件和電腦物件的權限
    - 建立反向 DNS Proxy 規則的權限
    - 將電腦加入 Active Directory 網域的權限


## <a name="next-steps"></a>後續步驟
* 若要設定已加入網域的 HDInsight 叢集，請參閱[設定已加入網域的 HDInisight 叢集](apache-domain-joined-configure.md)。
* 若要管理已加入網域的 HDInsight 叢集，請參閱[管理已加入網域的 HDInisight 叢集](apache-domain-joined-manage.md)。
* 若要設定 Hive 原則和執行 Hive 查詢，請參閱[針對已加入網域的 HDInsight 叢集設定 Hive 原則](apache-domain-joined-run-hive.md)。
* 若要在已加入網域的 HDInsight 叢集上使用 SSH 執行 Hive 查詢，請參閱[搭配 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)。
