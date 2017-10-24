---
title: "管理檔案與資料夾層級的使用者權限 - Azure HDInsight | Microsoft Docs"
description: "如何管理已加入網域之 HDInsight 叢集的檔案與資料夾權限。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 42d617ffeb8c2fee6be6d747b39d80b09774a1c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="manage-user-permissions-at-the-file-and-folder-levels"></a>管理檔案與資料夾層級的使用者權限

[已加入網域的 HDInsight 叢集](hdinsight-domain-joined-introduction.md)會搭配 Azure Active Directory (Azure AD) 使用者使用增強式驗證，也會針對各種服務 (例如 YARN 和 Hive) 使用*角色型存取控制* (RBAC) 原則。 如果您的叢集預設資料存放區是「Azure 儲存體」或「Windows Azure 儲存體 Blob」(WASB)，則您也可以強制執行檔案層級與資料夾層級的權限。 您可以使用 Apache Ranger，針對已同步的 Azure AD 使用者和群組，控制對叢集檔案的存取權。
<!-- [synchronized Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md). -->

已加入網域之 HDInsight 叢集的 Apache Ranger 執行個體已預先設定了 Ranger-WASB 服務。 Ranger-WASB 服務是一個與 Ranger-HDFS 類似的原則管理引擎，但在 Ranger 存取原則的強制執行方面有所不同。 在 Ranger-WASB 服務中，如果連入的資源要求沒有相符的 Ranger 原則，預設回應就會是 DENY。 Ranger 服務不會將權限檢查移交給 WASB。

## <a name="permission-and-policy-model"></a>權限和原則模型

評估資源存取要求時，會使用以下流程：

![Apache Ranger 原則評估流程](./media/hdinsight-add-acls-at-file-folder-levels/ranger-policy-evaluation-flow.png)

DENY 規則的評估順序優先，之後才是 ALLOW 規則。 在比對結束時，如果沒有任何相符的原則，就會傳回 DENY。

### <a name="user-variable"></a>USER 變數

您可以在指派原則以供使用者存取 `/{username}` 子目錄時，使用 `{USER}` 變數，例如：

```
resource: path=/app-logs/{USER}, user: {USER}, recursive=true, permissions: all, delegateAdmin=true
```

上述原則會授權使用者存取他們在 `/app-logs/` 目錄底下的子目錄。 以下是此原則在 Ranger 使用者介面中的樣子：

![USER 變數的範例用法](./media/hdinsight-add-acls-at-file-folder-levels/user-variable.png)

### <a name="policy-model-examples"></a>原則模型範例

下表列出原則模型運作方式的一些範例：

| Ranger 原則 | 現有檔案系統 | 使用者要求 | 結果 |
| -- | -- | -- | -- |
| /data/finance/, bob, WRITE | /data | bob, Create file /data/finance/mydatafile.txt | ALLOW - 因為會進行上階檢查，所以會建立中繼資料夾 [finance] |
| /data/finance/, bob, WRITE | /data | alice, Create file /data/finance/mydatafile.txt | DENY - 沒有相符的原則 |
| /data/finance*, bob, WRITE | /data | bob, Create file /data/finance/mydatafile.txt | ALLOW - 在此案例中有選擇性的遞迴原則 (`*`) 存在，請參閱[萬用字元](#wildcards) |
| /data/finance/mydatafile.txt, bob, WRITE | /data | bob, Create file /data/finance/mydatafile.txt | DENY - 在 [/data] 進行上階檢查失敗，因為沒有原則 |
| /data/finance/mydatafile.txt, bob, WRITE | /data/finance | bob, Create file /data/finance/mydatafile.txt | DENY - 在 [/data/finance] 沒有用於進行上階檢查的原則 |

視作業類型而定，必須有資料夾層級或檔案層級的權限。 例如，"read/open" 呼叫需要有檔案層級的讀取存取權，而 "create" 呼叫則需要有上階資料夾層級的權限。

### <a name="wildcards-"></a>萬用字元 (*)

當原則路徑中有萬用字元 (`*`) 存在時，該萬用字元會套用至該路徑及其整個樹狀子目錄。 此遞迴行為與使用 `recurse-flag`時相同。 在 Ranger-WASB 中，萬用字元同時表示遞迴和部分名稱比對。

## <a name="manage-file-and-folder-level-permissions-with-apache-ranger"></a>使用 Apache Ranger 來管理檔案層級與資料夾層級的權限

如果您尚未這麼做，請依照[這些指示](hdinsight-domain-joined-configure.md)來佈建一個已加入網域的新叢集。

請瀏覽至 `https://<YOUR CLUSTER NAME>.azurehdinsight.net/ranger/`來開啟 Ranger-WASB。 輸入您建立叢集時所定義的叢集系統管理員使用者名稱和密碼。

登入之後，您看到 Ranger 儀表板：

![Ranger 儀表板](./media/hdinsight-add-acls-at-file-folder-levels/ranger-dashboard.png)

若要檢視您叢集相關「Azure 儲存體」帳戶目前的檔案與資料夾權限，請按一下 WASB 控制項方塊中的 [*CLUSTERNAME*_wasb] 連結。

![Ranger 儀表板](./media/hdinsight-add-acls-at-file-folder-levels/wasb-dashboard-link.png)

隨即會顯示目前的原則清單。 其中包含數個一般原則作為起點 - 請查看每個原則的詳細資料來了解範例使用方式。

針對每個原則，您可以查看該原則是否已啟用、是否已設定稽核記錄功能，以及任何已指派的群組和使用者。 每個原則有兩個動作按鈕：[Edit] \(編輯\) 和 [Delete] \(刪除\)。

![原則清單](./media/hdinsight-add-acls-at-file-folder-levels/policy-list.png)

### <a name="adding-a-new-policy"></a>新增原則

1. 在 WASB 原則頁面的右上方，選取 [Add New Policy] \(新增原則\)。

    ![新增原則](./media/hdinsight-add-acls-at-file-folder-levels/add-new.png)

2. 輸入描述性的 [Policy Name] \(原則名稱\)。 指定您叢集的 Azure [Storage Account] \(儲存體帳戶\) (*ACCOUNT_NAME*.blob.core.windows.net)，以及您建立叢集時所指定的 [Storage Account Container] \(儲存體帳戶容器\)。 輸入所存取資料夾或檔案的 [Relative Path] \(相對路徑\) (相對於叢集)。

    ![[新增原則] 表單](./media/hdinsight-add-acls-at-file-folder-levels/new-policy.png)

3. 在此表單底下，指定此新資源的 [Allow Conditions] \(允許條件\)。 選取適用的群組和使用者，然後設定其權限。 在以下範例中，我們將允許 `sales` 群組擁有讀取/寫入存取權。

    ![允許 sales](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales.png)

4. 選取 [ **儲存**]。

### <a name="example-policy-conditions"></a>範例原則條件

Apache Ranger [原則評估流程](#permission-and-policy-model)可讓您指定允許和拒絕條件的任意組合來滿足您的需求。 以下是一些範例：

1. 允許所有 sales 使用者，但不允許任何 interns 使用者：

    ![允許 sales，拒絕 interns](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns.png)

2. 允許所有 sales 使用者並拒絕所有 interns 使用者，但名為 "hiveuser3" 的 interns 使用者除外，此使用者應該具有 Read (讀取) 存取權：

    ![允許 sales，拒絕 interns，但 hiveuser3 除外](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns-except-hiveuser3.png)

## <a name="next-steps"></a>後續步驟

* [在已加入網域的 HDInsight 中設定 Hive 原則](hdinsight-domain-joined-run-hive.md)
* [管理已加入網域的 HDInsight 叢集](hdinsight-domain-joined-manage.md)
* [管理 Ambari - 授權使用者存取 Ambari](hdinsight-authorize-users-to-ambari.md)

<!-- * [Synchronize Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md) -->

