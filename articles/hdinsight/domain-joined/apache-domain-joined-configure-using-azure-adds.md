---
title: "使用 Azure Active Directory Domain Services 設定已加入網域的 HDInsight 叢集 - Azure | Microsoft Docs"
description: "了解如何使用 Azure Active Directory Domain Services 設定已加入網域的 HDInsight 叢集"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/10/2017
ms.author: saurinsh
ms.openlocfilehash: cf8532334f03f72691fa09e8dbdd02b78072cf38
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2017
---
# <a name="configure-domain-joined-hdinsight-clusters-using-azure-active-directory-domain-services"></a>使用 Azure Active Directory Domain Services 設定已加入網域的 HDInsight 叢集

已加入網域的叢集可在 HDInsight 上提供多使用者企業的安全性功能。 已加入網域的 HDInsight 叢集會與 Active Directory 網域連線，讓網域使用者可以使用其網域認證來驗證叢集並執行巨量資料作業。 

有三種方式可設定網域控制站，讓已加入網域的 HDInsight 叢集可以連線到：

- Azure Active Directory Domain Services (Azure AD DS)
- 內部部署 Active Directory
- Azure IaaS VM 上的 Active Directory 網域控制站

在此文章中，您會了解如何使用 Azure Active Directory Domain Services 設定已加入網域的 HDInsight 叢集。

## <a name="create-azure-adds"></a>建立 Azure ADDS

您必須先建立 Azure AD DS，才能建立 HDInsight 叢集。 若要建立 Azure ADDS，請參閱[使用 Azure 入口網站啟用 Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-getting-started.md)。 

> [!NOTE]
> 只有租用戶的管理員具有建立網域服務的權限。 

佈建網域服務之後，您需要在 **Azure AD DC 管理員**群組中建立服務帳戶，用於建立 HDInsight 叢集。 服務帳戶必須是 Azure AD 的全域管理員。

## <a name="create-a-domain-joined-hdinsight-cluster"></a>建立已加入網域的 HDInsight 叢集

下一個步驟是使用 AAD DS 以及在上一節建立的服務帳戶來建立 HDInsight 叢集。

將 Azure AD 的網域服務和 HDInsight 叢集放在相同的 Azure 虛擬網路 (VNET) 中比較容易。 如果它們在不同的 Vnet 中，您必須對等互連兩個 VNet。 如需詳細資訊，請參閱[虛擬網路對等互連](../../virtual-network/virtual-network-peering-overview.md)。

建立已加入網域的 HDInsight 叢集時，必須提供下列參數：

- **網域名稱**：與 Azure AD DS 相關聯的網域名稱。 例如，contoso.onmicrosoft.com。
- **網域使用者名稱**：上一節在 Azure AD DC 系統管理員群組中建立的服務帳戶。 例如： hdiadmin@contoso.onmicrosoft.com。此網域使用者是這個已加入網域的 HDInsight 叢集的管理員。
- **網域密碼**：服務帳戶的密碼。
- **組織單位**︰HDInsight 叢集要搭配使用的組織單位 (OU) 的辨別名稱。 例如：OU=HDInsightOU,DC=contoso,DC=onmicrosohift,DC=com。如果此 OU 不存在，HDInsight 叢集會嘗試建立此 OU。 
- **LDAPS URL**：例如 ldaps://contoso.onmicrosoft.com:636。
- **存取使用者群組**︰您要將其使用者同步至叢集的安全性群組。 例如，HiveUsers。 如果要指定多個使用者群組，以逗號 (,) 分隔它們。
 
以下螢幕擷取畫面顯示 Azure 入口網站上中的設定：

![已加入 Azure HDInsight 網域的 Active Directory Domain Services 的設定](./media/apache-domain-joined-configure-using-azure-adds/hdinsight-domain-joined-configuration-azure-aads-portal.png).


## <a name="next-steps"></a>後續步驟
* 如需設定 Hive 原則和執行 Hive 查詢，請參閱[針對已加入網域的 HDInisight 叢集設定 Hive 原則](apache-domain-joined-run-hive.md)。
* 如需使用 SSH 連線到已加入網域的 HDInsight 叢集，請參閱[從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。

