---
title: "使用 PowerShell 設定已加入網域的 HDInsight 叢集 - Azure | Microsoft Docs"
description: "了解如何使用 Azure PowerShell 安裝及設定已加入網域的 HDInsight 叢集"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: a13b2f7a-612d-4800-bc92-7fc0524f3e89
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: saurinsh
ms.openlocfilehash: 9da76bb5f649817cd2f027f3d0eb46d58a996b4f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="configure-domain-joined-hdinsight-clusters-preview-using-azure-powershell"></a>使用 Azure PowerShell 設定已加入網域的 HDInsight 叢集 (預覽)
了解如何使用 Azure PowerShell 設定具有 Azure Active Directory (Azure AD) 和 [Apache Ranger](http://hortonworks.com/apache/ranger/) 的 Azure HDInsight 叢集。 Azure PowerShell 指令碼可供用來加快設定速度並減少出錯機會。 您可以只在 Linux 架構的叢集上設定已加入網域的 HDInsight。 如需詳細資訊，請參閱[已加入網域的 HDInsight 叢集簡介](hdinsight-domain-joined-introduction.md)。

> [!IMPORTANT]
> Oozie 未在已加入網路的 HDInsight 上啟用。

已加入網域之 HDInsight 叢集的典型設定步驟如下︰

1. 為您的 Azure AD 建立 Azure 傳統 VNet。  
2. 建立和設定 Azure AD 與 Azure AD DS。
3. 將 VM 新增至傳統 VNet 以建立組織單位。 
4. 建立 Azure AD DS 的組織單位。
5. 在 Azure 資源管理模式中建立 HDInsight VNet。
6. 設定 Azure AD DS 的反向 DNS 區域。
7. 對等互連兩個 VNet。
8. 建立 HDInsight 叢集。

所提供的 PowerShell 指令碼會執行步驟 3 到步驟 7。 您必須手動進行步驟 1 和步驟 2。  如果您不想使用 Azure PowerShell，請參閱[設定已加入網域的 HDInsight 叢集](hdinsight-domain-joined-configure.md)。 

最終的拓樸範例如下：

![已加入網域的 HDInsight 拓樸](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-topology.png)

因為 Azure AD 目前只支援傳統虛擬網路 (VNet)，而 Linux 架構的 HDInsight 叢集僅支援 Azure Resource Manager 架構的 VNet，HDInsight Azure AD 整合需要兩個 VNet 以及在兩者之間的對等互連。 如需兩種部署模型的比較資訊，請參閱 [Azure Resource Manager 與傳統部署比較：了解資源的部署模型和狀態](../azure-resource-manager/resource-manager-deployment-model.md)。 這兩個 VNet 必須位於與 Azure AD DS 相同的區域中。

> [!NOTE]
> 本教學課程假設您已沒有 Azure AD。 如果您有的話，可以略過步驟 2 的部分。
> 
> 

## <a name="prerequisites"></a>必要條件
您必須具備下列項目才能進行本教學課程：

* 您熟悉 [Azure AD 網域服務](https://azure.microsoft.com/services/active-directory-ds/)及其 [價格](https://azure.microsoft.com/pricing/details/active-directory-ds/)結構。
* 確定您的訂用帳戶已列入此公開預覽版本的允許清單中。 您可以傳送電子郵件與您的訂用帳戶識別碼給 hdipreview@microsoft.com 要求列入。
* SSL 憑證，需由您的網域的簽章授權單位簽署。 設定安全的 LDAP 需有此憑證。 不可使用自我簽署憑證。
* Azure PowerShell。  請參閱[安裝和設定 Azure PowerShell](/powershell/azure/overview)。

## <a name="create-an-azure-classic-vnet-for-your-azure-ad"></a>為您的 Azure AD 建立 Azure 傳統 VNet。
如需指示，請參閱[這裡](hdinsight-domain-joined-configure.md#create-an-azure-virtual-network-classic)。

## <a name="create-and-configure-azure-ad-and-azure-ad-ds"></a>建立和設定 Azure AD 與 Azure AD DS。
如需指示，請參閱[這裡](hdinsight-domain-joined-configure.md#create-and-configure-azure-ad-ds-for-your-azure-ad)。

## <a name="run-the-powershell-script"></a>執行 PowerShell 指令碼
您可以從 [GitHub](https://github.com/hdinsight/DomainJoinedHDInsight) 下載 PowerShell 指令碼。 解壓縮 zip 檔，並將其中的檔案儲存在本機。

**編輯 PowerShell 指令碼**

1. 使用 Windows PowerShell ISE 或任何文字編輯器開啟 run.ps1。
2. 填入下列變數的值：
   
   * **$SubscriptionName** – 要用來建立 HDInsight 叢集的 Azure 訂用帳戶名稱。 您已在此訂用帳戶中建立傳統虛擬網路，而且將會在訂用帳戶下建立 HDInsight 叢集的 Azure Resource Manager 虛擬網路。
   * **$ClassicVNetName** – 包含 Azure AD DS 的傳統虛擬網路。 此虛擬網路必須位於和前面所提供者相同的訂用帳戶。 此虛擬網路必須使用 Azure 入口網站來建立，而非使用傳統入口網站。 如果您依照[設定已加入網域的 HDInsight 叢集 (預覽)](hdinsight-domain-joined-configure.md#create-an-azure-virtual-network-classic) 中的指示進行，則預設名稱是 contosoaadvnet。
   * **$ClassicResourceGroupName** – 前述傳統虛擬網路的 Resource Manager 群組名稱。 例如 contosoaadrg。 
   * **$ArmResourceGroupName** – 要在其中建立 HDInsight 叢集的資源群組名稱。 您可以使用和 $ArmResourceGroupName 相同的資源群組。  如果資源群組不存在，指令碼會建立資源群組。
   * **$ArmVNetName** – 要在其中建立 HDInsight 叢集的 Resource Manager 虛擬網路名稱。 此虛擬網路將會置入 $ArmResourceGroupName。  如果 VNet 不存在，PowerShell 指令碼會予以建立。 如果存在，則應該會成為您在前面所提供之資源群組的一部分。
   * **$AddressVnetAddressSpace** – Resource Manager 虛擬網路的網路位址空間。 請確定此位址空間可供使用。 此位址空間不得與傳統虛擬網路的位址空間重疊。 例如，「10.1.0.0/16」
   * **$ArmVnetSubnetName** – 要在其中放置 HDInsight 叢集 VM 的 Resource Manager 虛擬網路子網路名稱。 如果子網路不存在，PowerShell 指令碼會予以建立。 如果存在，則應該會成為您在前面所提供之虛擬網路的一部分。
   * **$AddressSubnetAddressSpace** – Resource Manager 虛擬網路子網路的網路位址範圍。 HDInsight 叢集 VM 的 IP 位址會來自此子網路位址範圍。 例如，「10.1.0.0/24」。
   * **$ActiveDirectoryDomainName** – 要將 HDInsight 叢集 VM 加入到的 Azure AD 網域名稱。 例如，「contoso.onmicrosoft.com」
   * **$ClusterUsersGroups** – 要同步處理至 HDInsight 叢集之 AD 的安全性群組一般名稱。 此安全性群組內的使用者將能夠使用其 Active Directory 網域認證登入叢集儀表板。 這些安全性群組必須存在於 Active Directory。 例如，「hiveusers」或者「clusteroperatorusers」。
   * **$OrganizationalUnitName** – 要在其中放置叢集所用 HDInsight 叢集 VM 和服務主體之網域中的組織單位。 如果此 OU (組織單位) 不存在，PowerShell 指令碼會予以建立。 例如，「HDInsightOU」。
3. 儲存變更。

**執行指令碼**

1. 以系統管理員的身分執行 **Windows PowerShell**。
2. 瀏覽至 run.ps1 的資料夾。 
3. 輸入檔案名稱以執行指令碼，然後按 **ENTER**。  它會跳出 3 個登入對話方塊︰
   
   1. **登入 Azure 傳統入口網站** – 輸入您用來登入 Azure 傳統入口網站的認證。 您必須已使用這些認證建立 Azure AD 與 Azure AD DS。
   2. **登入 Azure Resource Manager 入口網站** – 輸入您用來登入 Azure Resource Manager 入口網站的認證。
   3. **網域使用者名稱** – 輸入您想要讓其成為 HDInsight 叢集系統管理員之網域使用者名稱的認證。 如果您是從頭開始建立 Azure AD，您必須已使用這份文件建立此使用者。 
      
      > [!IMPORTANT]
      > 以下列格式輸入使用者名稱︰ 
      > 
      > Domainname\username (例如 contoso.onmicrosoft.com\clusteradmin)
      > 
      > 
      
      此使用者必須有 3 個權限︰可將機器加入所提供的 Active Directory 網域；可在所提供的組織單位內建立服務主體與機器物件；可新增反向 DNS Proxy 規則。

在建立反向 DNS 區域時，指令碼會提示您輸入網路識別碼。 此網路識別碼必須是 Resource Manager 虛擬網路的位址首碼。 例如，如果 Resource Manager 虛擬網路子網路的位址空間是 10.2.0.0/24，則請在工具提示您輸入網路識別碼時輸入 10.2.0.0/24。 

## <a name="create-hdinsight-cluster"></a>建立 HDInsight 叢集
在本節中，您會使用 Azure 入口網站或 [Azure Resource Manager 範本](../azure-resource-manager/resource-group-template-deploy.md)，在 HDInsight 中建立 Linux 架構的 Hadoop 叢集。 如需其他叢集建立方法及了解各項設定，請參閱 [建立 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md)。 如需有關使用 Resource Manager 範本在 HDInsight 中建立 Hadoop 叢集的詳細資訊，請參閱[使用 Resource Manager 範本在 HDInsight 中建立 Hadoop 叢集](hdinsight-hadoop-create-windows-clusters-arm-templates.md)。

**使用 Azure 入口網站建立已加入網域的 HDInsight 叢集**

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 依序選取 [新增]、[情報 + 分析] 及 [HDInsight]。
3. 在 [新增 HDInsight 叢集] 刀鋒視窗中，輸入或選取下列值：
   
   * **叢集名稱**︰為已加入網域的 HDInsight 叢集輸入新叢集名稱。
   * **訂用帳戶**：選取用來建立此叢集的 Azure 訂用帳戶。
   * **叢集組態**：
     
     * **叢集類型**：Hadoop。 目前在 Hadoop 叢集上僅支援已加入網域的 HDInsight。
     * **作業系統**：Linux。  目前在 Linux 架構的 HDInsight 叢集上僅支援已加入網域的 HDInsight。
     * **版本**：Hadoop 2.7.3 (HDI 3.5)。 在 HDInsight 叢集版本 3.5 上僅支援已加入網域的 HDInsight。
     * **叢集類型**：進階 PREMIUM
       
       按一下 [選取] 儲存變更。
   * **認證**︰設定叢集使用者和 SSH 使用者的認證。
   * **資料來源**：建立新的儲存體帳戶或使用現有的儲存體帳戶，做為 HDInsight 叢集的預設儲存體帳戶。 此位置必須與兩個 VNet 的位置相同。  此位置也是 HDInsight 叢集的位置。
   * **價格**：選取叢集的背景工作角色節點數目。
   * **進階組態**： 
     
     * **加入網域與 VNet/子網路**： 
       
       * **網域設定**： 
         
         * **網域名稱**：contoso.onmicrosoft.com
         * **網域使用者名稱**：輸入網域使用者名稱。 這個網域必須有下列權限︰將機器加入網域，並將它們放在您稍早設定的組織單位內；在您稍早設定的組織單位內建立服務主體；建立反向 DNS 項目。 此網域使用者會成為這個已加入網域的 HDInsight 叢集的系統管理員。
         * **網域密碼**︰輸入網域使用者密碼。
         * **組織單位**︰輸入您稍早設定之 OU 的辨別名稱。 例如︰OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com
         * **LDAPS URL**：ldaps://contoso.onmicrosoft.com:636
         * **存取使用者群組**︰指定您要將其使用者同步至叢集的安全性群組。 例如，HiveUsers。
           
           按一下 [選取] 儲存變更。
           
           ![已加入網域的 HDInsight 入口網站的設定網域](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
       * **虛擬網路**：contosohdivnet
       * **子網路**：Subnet1
         
         按一下 [選取] 儲存變更。        
         按一下 [選取] 儲存變更。
   * **資源群組**：選取用於 HDInsight VNet 的資源群組 (contosohdirg)。
4. 按一下 [建立] 。  

建立加入網域的 HDInsight 叢集的另一個選擇，是使用 Azure Resource Management 範本。 下列程序說明做法：

**使用 Azure Resource Manager 範本建立加入網域的 HDInsight 叢集**

1. 在 Azure 入口網站中，按一下以下影像以開啟 Resource Manager 範本。 Resource Manager 範本位於公用 Blob 容器中。 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="./media/hdinsight-domain-joined-configure-use-powershell/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. 在 [參數] 刀鋒視窗中輸入下列值：
   
   * **訂用帳戶**：(選取您的 Azure 訂用帳戶。)
   * **資源群組**︰ 按一下 [使用現有的]，並指定您剛才使用的同一資源群組。  例如 contosohdirg。 
   * **位置**：指定資源群組的位置。
   * **叢集名稱**：輸入您將建立的 Hadoop 叢集的名稱。 例如 contosohdicluster。
   * **叢集類型**：選取叢集類型。  預設值為 **hadoop**。
   * **位置**：選取叢集的位置。  預設儲存體帳戶會使用相同的位置。
   * **叢集背景工作節點計數**︰選取背景工作角色節點的數目。
   * **叢集登入名稱和密碼**：預設登入名稱是 **admin**。
   * **SSH 使用者名稱和密碼**：預設使用者名稱是 **sshuser**。  您可以將它重新命名。 
   * **虛擬網路識別碼**：/subscriptions/&lt;SubscriptionID>/resourceGroups/&lt;ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/&lt;VNetName>
   * **虛擬網路子網路**：/subscriptions/&lt;SubscriptionID>/resourceGroups/&lt;ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/&lt;VNetName>/subnets/Subnet1
   * **網域名稱**：contoso.onmicrosoft.com
   * **組織單位辨別名稱**：OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com
   * **叢集使用者群組辨別名稱**："\"CN=HiveUsers,OU=AADDC Users,DC=<DomainName>,DC=onmicrosoft,DC=com\""
   * **LDAPS URL**：["ldaps://contoso.onmicrosoft.com:636"]
   * **DomainAdminUserName**：(輸入網域系統管理員使用者名稱)
   * **DomainAdminPassword**：(輸入網域系統管理員使用者密碼)
   * **我同意上方所述的條款及條件**：(勾選)
   * **釘選到儀表板**：(勾選)
3. 按一下 [購買]。 您將會看到標題為 [進行範本部署] 的新圖格。 大約需要 20 分鐘的時間來建立叢集。 一旦建立叢集後，您可以在入口網站按一下 [叢集] 刀鋒視窗來開啟它。

完成本教學課程之後，您可以刪除叢集。 利用 HDInsight，您的資料會儲存在 Azure 儲存體中，以便您在未使用叢集時安全地進行刪除。 您也需支付 HDInsight 叢集的費用 (即使未使用)。 由於叢集費用是儲存體費用的許多倍，所以刪除未使用的叢集符合經濟效益。 如需有關刪除叢集的指示，請參閱[使用 Azure 入口網站管理 HDInsight 中的 Hadoop 叢集](hdinsight-administer-use-management-portal.md#delete-clusters)。

## <a name="next-steps"></a>後續步驟

* 如需設定 Hive 原則和執行 Hive 查詢，請參閱[針對已加入網域的 HDInisight 叢集設定 Hive 原則](hdinsight-domain-joined-run-hive.md)。
* 若要使用 SSH 連線到已加入網域的 HDInsight 叢集，請參閱[搭配 HDInsight 使用 SSH](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。

