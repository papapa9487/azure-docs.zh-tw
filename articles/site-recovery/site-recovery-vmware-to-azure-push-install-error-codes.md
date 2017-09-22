---
title: "進行從 VMware 到 Azure 的 Azure Site Recovery 疑難排解 | Microsoft Docs"
description: "針對複寫 Azure 虛擬機器時的錯誤進行疑難排解"
services: site-recovery
documentationcenter: 
author: asgang
manager: srinathv
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/24/2017
ms.author: asgang
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: d4d6d273001642ebb8c960333a1c01977e863dbc
ms.contentlocale: zh-tw
ms.lasthandoff: 09/07/2017

---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>針對行動服務推送安裝問題進行疑難排解

本文說明當您嘗試在來源伺服器上安裝 Azure Site Recovery 行動服務以啟用保護時，可能會面臨的常見問題。

## <a name="error-code-95107-protection-could-not-be-enabled"></a>(錯誤碼 95107) 無法啟用保護
**錯誤碼** | **可能的原因** | **特定錯誤的建議**
--- | --- | ---
95107 </br>**訊息：**將行動服務推送安裝到來源電腦失敗，錯誤碼為 **EP0858**。 <br> 可能是所提供用以安裝行動服務的認證不正確，或使用者帳戶的權限不足。 | 為了在來源電腦上安裝行動服務所提供的使用者認證不正確。 | 請確定組態伺服器上針對來源電腦提供的使用者認證正確無誤。 <br> 若要新增/編輯使用者認證，前往組態伺服器，選取 [Cspsconfigtool]  >  [管理帳戶]。 </br> 此外，請檢查下列必要條件，才能順利完成推送安裝。

## <a name="error-code-95015-protection-could-not-be-enabled"></a>(錯誤碼 95015) 無法啟用保護
**錯誤碼** | **可能的原因** | **特定錯誤的建議**
--- | --- | ---
95105 </br>**訊息：**將行動服務推送安裝到來源電腦失敗，錯誤碼為 **EP0856**。 <br> 可能是來源電腦不允許**檔案及印表機共用**，或是處理序伺服器與來源電腦之間有網路連線問題。| 未啟用 [檔案及印表機共用]。 | 在 Windows 防火牆中的來源電腦允許 [檔案及印表機共用]。 在來源電腦的 [Windows 防火牆]  >  [允許應用程式或功能通過防火牆]，為所有設定檔選取 [檔案及印表機共用]。 </br> 此外，請檢查下列必要條件，才能順利完成推送安裝。

## <a name="error-code-95117-protection-could-not-be-enabled"></a>(錯誤碼 95117) 無法啟用保護
**錯誤碼** | **可能的原因** | **特定錯誤的建議**
--- | --- | ---
95117 </br>**訊息：**將行動服務推送安裝到來源電腦失敗，錯誤碼為 **EP0865**。 <br> 可能是來源電腦不在執行中，或是處理序伺服器與來源電腦之間有網路連線問題。 | 處理序伺服器與來源伺服器之間的網路連線問題。 | 請檢查處理序伺服器與來源伺服器之間的網路連線。 </br> 此外，請檢查下列必要條件，才能順利完成推送安裝。

## <a name="error-code-95103-protection-could-not-be-enabled"></a>(錯誤碼 95103) 無法啟用保護
**錯誤碼** | **可能的原因** | **特定錯誤的建議**
--- | --- | ---
95103 </br>**訊息：**將行動服務推送安裝到來源電腦失敗，錯誤碼為 **EP0854**。 <br> 可能是來源電腦不允許 Windows Management Instrumentation (WMI) ，或是處理序伺服器與來源電腦之間有網路連線問題。| Windows 防火牆會封鎖 WMI。 | 在 Windows 防火牆允許 WMI。 在 [Windows 防火牆]  >  [允許應用程式或功能通過防火牆]，為所有設定檔選取 [WMI]。 </br> 此外，請檢查下列必要條件，才能順利完成推送安裝。

## <a name="check-push-installation-logs-for-errors"></a>檢查推送安裝記錄中的錯誤

在組態/處理序伺服器上，移至位於 <Microsoft Azure Site Recovery Install Location>\home\svsystems\pushinstallsvc\ 的 PushinstallService 檔案，以了解問題來源。 使用下列疑難排解步驟解決問題。</br>

![推送安裝記錄](./media/site-recovery-protection-common-errors/pushinstalllogs.png)

## <a name="push-installation-prerequisites-for-windows"></a>Windows 的推送安裝必要條件
### <a name="ensure-that-file-and-printer-sharing-is-enabled"></a>確定已啟用檔案及印表機共用
在 Windows 防火牆中的來源電腦允許 [檔案及印表機共用] 和 [Windows Management Instrumentation]。 </br>
#### <a name="if-the-source-machine-is-domain-joined-br"></a>如果來源電腦已加入網域 </br>
使用 [群組原則管理主控台] 進行防火牆設定。

1. 以系統管理員身分登入 Azure Active Directory。 開啟 [群組原則管理主控台](GPMC.MSC，從 [開始]  >  [執行] 執行)。</br>

2. 如未安裝 [群組原則管理主控台]，請參閱[安裝群組原則管理主控台](https://technet.microsoft.com/library/cc725932.aspx)。 </br>

3. 在群組原則管理主控台樹狀目錄中，按兩下樹系和網域中的 [群組原則物件]。 移至 [預設網域原則]。 </br>

    ![預設網域原則](./media/site-recovery-protection-common-errors/gpmc1.png) </br>
</br>
4. 以滑鼠右鍵按一下 [預設網域原則]  >  [編輯]。 新的 [群組原則管理編輯器] 視窗隨即開啟。 </br>

    ![群組原則管理編輯器](./media/site-recovery-protection-common-errors/gpmc2.png) </br>
</br>
5. 在 [群組原則管理編輯器] 中，移至 [電腦設定]  >  [原則]  >  [系統管理範本]  >  [網路]  >  [網路連線]  >  [Windows 防火牆]。 </br>

    ![Windows 防火牆](./media/site-recovery-protection-common-errors/gpmc3.png) </br>
</br>
6. 啟用 [網域設定檔] 和 [標準設定檔] 的下列設定： </br>

    a. 按兩下 [Windows 防火牆: 允許輸入檔案及印表機共用例外狀況]。 選取 [已啟用] 並按一下 [確定]。 </br>

    b. 按兩下 [Windows 防火牆: 允許輸入遠端系統管理例外狀況]。 選取 [已啟用] 並按一下 [確定]。 </br>

    ![編域設定檔](./media/site-recovery-protection-common-errors/gpmc4.png) </br>
</br>

#### <a name="if-the-source-machine-isnt-domain-joined-and-part-of-a-workgroup-br"></a>如果來源電腦未加入網域，不屬於工作群組的一部分 </br>
在遠端電腦上設定防火牆設定 (適用於工作群組)。

1. 移至來源電腦。</br>

2. 在 [Windows 防火牆]  >  [允許應用程式或功能通過防火牆]，為所有設定檔 [檔案及印表機共用]。 </br>

3. 在 [Windows 防火牆]  >  [允許應用程式或功能通過防火牆]，為所有設定檔選取 [WMI]。 </br>

#### <a name="disable-remote-user-account-control"></a>停用遠端使用者帳戶控制
使用登錄機碼停用使用者帳戶控制，以推送行動服務。

1. 選取 [開始]  >  [執行]，輸入 *regedit*，然後按 **Enter** 鍵。

2. 找到並選取下列登錄子機碼：HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System

3. 如果 LocalAccountTokenFilterPolicy 登錄項目不存在，請遵循下列步驟：

    a. 從 [編輯] 功能表中選取 [新增]，然後選取 [DWORD 值]。

    b. 輸入 *LocalAccountTokenFilterPolicy*，然後按 **ENTER** 鍵。

    c. 在 **LocalAccountTokenFilterPolicy** 上按一下滑鼠右鍵，然後選取 [修改]。

    d. 在 [值] 資料方塊中輸入 *1*，然後選取 [確定]。

4. 關閉 [登錄編輯程式]。


## <a name="push-installation-prerequisites-for-linux"></a>Linux 的推送安裝必要條件

1. 在來源 Linux 伺服器上建立 root 使用者。 (此帳戶僅適用於推送安裝和更新。)</br>

2. 檢查來源 Linux 伺服器上的 /etc/hosts 檔案，該檔案具有將本機主機名稱對應到所有網路介面卡相關聯之 IP 位址的項目。 </br>

3. 確定來源 Linux 伺服器上已安裝最新的 openssh、openssh-server、openssl 套件。 檢查 ssh 連接埠 22 已啟用並在執行中。 </br>

4. 檢查代理程式的過時項目是否已存在於來源伺服器上，將舊版的代理程式解除安裝，並重新啟動伺服器。 重新安裝代理程式。 </br>

#### <a name="enable-sftp-subsystem-and-password-authentication-in-the-sshdconfig-file"></a>在 sshd_config 檔案中啟用 SFTP 子系統與密碼驗證
1. 在來源伺服器上，以 root 的身分登入。 </br>

2. 在 /etc/ssh/sshd_config 檔案中，找到以 "PasswordAuthentication" 開頭這一行。 </br>

3. 取消該行的註解，並將值從 "no" 變更為 "yes"。 </br>

    ![PasswordAuthentication](./media/site-recovery-protection-common-errors/linux1.png)

4. 找到以 "Subsystem" 開頭這一行，並取消其註解。 </br>

    ![Subsystem](./media/site-recovery-protection-common-errors/linux2.png)

5. 儲存變更，重新啟動 sshd 服務。 </br>


## <a name="push-installation-checks-on-the-configurationprocess-server"></a>組態/處理序伺服器上的推送安裝檢查
#### <a name="validate-credentials-for-discovery-and-installation"></a>驗證認證以供探索和安裝

1. 從組態伺服器啟動 Cspsconfigtool。</br>

    ![WMItestConnect5](./media/site-recovery-protection-common-errors/wmitestconnect5.png) </br>

2. 確定用於保護的帳戶具有來源電腦的系統管理員權限。 </br>

#### <a name="check-connectivity-between-the-process-server-and-the-source-server"></a>檢查處理序伺服器與來源伺服器之間的網路連線
1. 確定處理序伺服器有網際網路連線。

2. 使用 wbemtest.exe 確認 WMI 連線。 </br>

    a. 在處理序伺服器上，選取 [開始]  >  [執行]  >  [wbemtest.exe]。 [Windows Management Instrumentation 測試器] 視窗隨即開啟，如下圖：</br>

      ![WMI 測試器](./media/site-recovery-protection-common-errors/wmitestconnect1.png) </br>
   </br>
    b. 選取 [連線]，在 [命名空間] 中輸入來源伺服器 IP。

    c. 輸入 [使用者] 名稱和 [密碼]。 (如果來源電腦已加入網域，請以 "domainName\username" 形式提供網域名稱及使用者名稱。 如果來源電腦在工作群組中，僅提供使用者名稱。)

    d. [驗證] 層級請選取 [封包私密性]。 </br>

      ![封包私密性](./media/site-recovery-protection-common-errors/wmitestconnect2.png) </br>
   </br>
    e. 選取 [ **連接**]。 現在，WMI 連線會顯示提供的資料，並顯示 [Windows Management Instrumentation 測試器] 視窗，如下所示： </br>

      ![WMI 測試器資料](./media/site-recovery-protection-common-errors/wmitestconnect3.png) </br>
   </br>
      如果 WMI 連線不成功，會出現錯誤訊息。 如果未在 Windows 防火牆允許的應用程式中啟用 [WMI/遠端系統管理]，則會如下列螢幕擷取畫面顯示失敗的嘗試： </br>

    ![WMI 測試器錯誤訊息](./media/site-recovery-protection-common-errors/wmitestconnect4.png) </br>
   </br>

3. 檢查 WMI 狀態和連線能力。</br>

    a. 在組態/處理序伺服器上，選取 [開始]  >  [執行]  >  [wmimgmt.msc]  >  [動作]  >  [其他動作]  >  [連線到另一部電腦 (來源機器)]。 </br>

    b. 輸入用於防護之帳戶的認證，並檢查連線是否正常。 </br>

#### <a name="verify-that-network-shared-folders-of-the-source-machine-are-accessible-from-the-process-server-remotely-by-using-specified-credentials"></a>確認可使用指定的認證從處理序伺服器遠端存取來源電腦的網路共用資料夾

  1. 登入處理序伺服器電腦，開啟檔案總管。 在網址列中輸入 *"\\\source-machine-ip\C$"*。 選取 [輸入]。 </br>

      ![檔案總管](./media/site-recovery-protection-common-errors/fileshare1.png) </br>

  2. 檔案總管會提示您輸入認證。 輸入使用者名稱和密碼，選取 [確定]。</br>

      * 如果來源電腦已加入網域，請以 "domainName\username" 形式輸入網域名稱及使用者名稱。</br>

      * 如果來源電腦在工作群組中，僅需輸入使用者名稱。 </br>

      ![認證對話方塊](./media/site-recovery-protection-common-errors/fileshare2.png) </br>

  3. 如果連線成功，您可以從處理序伺服器遠端檢視來源電腦的資料夾。 </br>

      ![資料夾檢視](./media/site-recovery-protection-common-errors/fileshare3.png) </br>

> [!NOTE] 
> 如果連線失敗，請檢查是否符合所有必要條件。
>

如果您不想開啟 [Windows Management Instrumentation]，也可以在來源電腦上手動安裝行動服務。</br> 

如需詳細資訊，請參閱[透過 GUI 手動安裝行動服務](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui)和[透過設定管理員的安裝指南](site-recovery-install-mobility-service-using-sccm.md)。 </br>

## <a name="next-steps"></a>後續步驟
- [啟用 VMware 虛擬機器的複寫](vmware-walkthrough-enable-replication.md)

