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
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 1e2452ccc6d3f1859a39e1ee09cdde32f53767ba
ms.contentlocale: zh-tw
ms.lasthandoff: 08/28/2017

---
# <a name="troubleshooting-mobility-service-push-install-issues"></a>針對行動服務推送安裝問題進行疑難排解

本文詳細說明嘗試在來源伺服器上安裝行動服務以便啟用保護時所面臨的常見問題。

## <a name="error-code-95107-protection-could-not-be-enabled"></a>(錯誤碼 95107) 無法啟用保護。
**錯誤碼** | **可能的原因** | **特定錯誤的建議**
--- | --- | ---
95107 </br>***訊息 -***  將行動服務推送安裝到來源電腦失敗，錯誤碼為 ***EP0858***。 <br> 可能是所提供用以安裝行動服務的認證不正確，或使用者帳戶的權限不足 | 為了在來源電腦上安裝行動服務所提供的使用者認證不正確 | 請確定組態伺服器上針對來源電腦提供的使用者認證正確無誤。 <br> 若要新增/編輯使用者認證：前往組態伺服器 > Cspsconfigtool 圖示 > 管理帳戶。 </br> 此外，確保已檢查以下必要條件，才能成功完成推送安裝。

## <a name="error-code-95015-protection-could-not-be-enabled"></a>(錯誤碼 95015) 無法啟用保護。
**錯誤碼** | **可能的原因** | **特定錯誤的建議**
--- | --- | ---
95105 </br>***訊息 -***  將行動服務推送安裝到來源電腦失敗，錯誤碼為 ***EP0856***。 <br> 不是來源電腦不允許 [檔案及印表機共用]，就是處理序伺服器與來源電腦之間有網路連線問題| 未啟用檔案及印表機共用 | Windows 防火牆中的來源電腦允許 [檔案及印表機共用]，請移至來源電腦 > 在 Windows 防火牆設定之下 > [允許應用程式或功能通過防火牆] > 為所有設定檔選取 [檔案及印表機共用]。 </br> 此外，確保已檢查以下必要條件，才能成功完成推送安裝。

## <a name="error-code-95117-protection-could-not-be-enabled"></a>(錯誤碼 95117) 無法啟用保護。
**錯誤碼** | **可能的原因** | **特定錯誤的建議**
--- | --- | ---
95117 </br>***訊息 -***  將行動服務推送安裝到來源電腦失敗，錯誤碼為 ***EP0865***。 <br> 不是來源電腦不在執行中，就是處理序伺服器與來源電腦之間有網路連線問題 | 處理序伺服器與來源伺服器之間的網路連線能力 | 檢查處理序伺服器與來源伺服器之間的連線能力。 </br> 此外，確保已檢查以下必要條件，才能成功完成推送安裝。

## <a name="error-code-95103-protection-could-not-be-enabled"></a>(錯誤碼 95103) 無法啟用保護。
**錯誤碼** | **可能的原因** | **特定錯誤的建議**
--- | --- | ---
95103 </br>***訊息 -***  將行動服務推送安裝到來源電腦失敗，錯誤碼為 ***EP0854***。 <br> 不是來源電腦不允許 [Windows Management Instrumentation (WMI)]，就是處理序伺服器與來源電腦之間有網路連線問題| Windows 防火牆中封鎖的 Windows Management Instrumentation (WMI) | 允許 Windows 防火牆中封鎖的 Windows Management Instrumentation (WMI)。 在 Windows 防火牆設定之下 > [允許應用程式或功能通過防火牆] > [為所有設定檔選取 WMI]。 </br> 此外，確保已檢查以下必要條件，才能成功完成推送安裝。

## <a name="check-push-install-logs-for-errors"></a>檢查推送安裝記錄中的錯誤

在組態/處理序伺服器上，瀏覽至位於 <Microsoft Azure Site Recovery Install Location>\home\svsystems\pushinstallsvc\ 的 'PushinstallService' 檔案，以了解問題來源並使用下列疑難排解步驟來解決問題。</br>
![pushiinstalllogs](./media/site-recovery-protection-common-errors/pushinstalllogs.png)

## <a name="push-install-pre-requisites-for-windows"></a>適用於 Windows 的推送安裝必要元件
### <a name="ensure-file-and-printer-sharing-is-enabled"></a>確保已啟用 [檔案及印表機共用]
在 Windows 防火牆中的來源電腦允許 [檔案及印表機共用] 和 [Windows Management Instrumentation] </br>
#### <a name="if-source-machine-is-domain-joined-br"></a>如果來源電腦已加入網域： </br>
使用群組原則管理主控台 (GPMC) 來進行防火牆設定。
1. 以系統管理員身分登入 Active Directory 網域電腦並開啟 [群組原則管理主控台] (GPMC.MSC，從 [開始] > [執行] 來執行)。</br>
3. 如果未安裝 GPMC，請依照下列連結來[安裝 GPMC](https://technet.microsoft.com/library/cc725932.aspx) </br>
4. 在 GPMC 主控台樹狀目錄中，連按兩下樹系和網域中的 [群組原則物件]，並瀏覽至 [預設網域原則]。 </br>
![gpmc1](./media/site-recovery-protection-common-errors/gpmc1.png) </br>
</br>
5. 以滑鼠右鍵按一下 [預設網域原則] > 編輯 > 將會開啟新的 [群組原則管理編輯器] 視窗。 </br>
![gpmc2](./media/site-recovery-protection-common-errors/gpmc2.png) </br>
</br>
6. 在 [群組原則管理編輯器] 中，瀏覽至 [電腦設定] > [原則] > [系統管理範本] > [網路] > [網路連線] > [Windows 防火牆]。 </br>
![gpmc3](./media/site-recovery-protection-common-errors/gpmc3.png) </br>
</br>
7. 啟用網域設定檔和標準設定檔的下列設定 </br>
a)  連按兩下「Windows 防火牆：允許輸入檔案及印表機共用例外狀況」。 選取 [已啟用] 並按一下 [確定]。 </br>
b)  連按兩下「Windows 防火牆：允許輸入遠端系統管理例外狀況」。 選取 [已啟用] 並按一下 [確定]。 </br>
![gpmc4](./media/site-recovery-protection-common-errors/gpmc4.png) </br>
</br>

###### <a name="if-source-machine-is-not-domain-joined-and-part-of-workgroup-br"></a>如果來源電腦未加入網域並屬於工作群組的一部分 </br>
在遠端電腦上設定防火牆設定 (適用於工作群組)：
1. 請移至來源電腦上，</br>
2. 在 Windows 防火牆設定之下 > [允許應用程式或功能通過防火牆] > 為所有設定檔選取 [檔案及印表機共用]。 </br>
3. 在 Windows 防火牆設定之下 > [允許應用程式或功能通過防火牆] > [為所有設定檔選取 WMI]。 </br>

#### <a name="disable-remote-user-account-control-uac"></a>停用遠端使用者帳戶控制 (UAC)
使用登錄機碼來停用 UAC 以推送行動服務。
1. 按一下 [開始] > [執行] > 鍵入 regedit > ENTER
2. 尋找而後按一下下列登錄子機碼：HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
3. 如果 LocalAccountTokenFilterPolicy 登錄項目不存在，請遵循下列步驟：
4. 在 [編輯] 功能表上 > [新增] > 按一下 [DWORD 值]。
5. 鍵入 LocalAccountTokenFilterPolicy，然後按 ENTER 鍵。
6. 在 LocalAccountTokenFilterPolicy 上按一下滑鼠右鍵，然後按一下 [修改]。
7. 在 [值] 資料方塊中鍵入 1，然後按一下 [確定]。
8. 結束 [登錄編輯器]。


## <a name="push-install-pre-requisites-for-linux"></a>適用於 Linux 的推送安裝必要元件：

1. 在來源 Linux 伺服器上建立 root 使用者。 (此帳戶僅適用於推送安裝和更新)</br>
2. 檢查來源 Linux 伺服器上的 /etc/hosts 檔案，該檔案具有將本機主機名稱對應到所有網路介面卡相關聯之 IP 位址的項目。 </br>
3. 確定來源 Linux 伺服器上已安裝最新的 openssh、openssh-server 和 openssl 套件。 </br>
檢查 ssh 連接埠 22 已啟用並在執行中。 </br>
4. 檢查代理程式的過時項目是否已存在於來源伺服器上，將舊版的代理程式解除安裝，並重新啟動伺服器、重新安裝代理程式。 </br>

#### <a name="enable-sftp-subsystem-and-password-authentication-in-the-sshdconfig-file"></a>在 sshd_config 檔案中啟用 SFTP 子系統與密碼驗證
1. 在來源伺服器上，以 root 的身分登入。 </br>
2. 在 /etc/ssh/sshd_config 檔案中，</br> 尋找以 PasswordAuthentication 開頭的行。 </br>
3. d.   取消該行的註解並將值從 “no” 變更為 “yes”。 </br>
![Linux1](./media/site-recovery-protection-common-errors/linux1.png)
4. 尋找以 “Subsystem” 為開頭的行並取消其註解。 </br>
![Linux2](./media/site-recovery-protection-common-errors/linux2.png)
5. 儲存變更並重新啟動 sshd 服務。 </br>

## <a name="push-installation-checks-on-configurationprocess-server"></a>在組態/處理序伺服器上檢查推送安裝。
#### <a name="validate-credentials-for-discovery-and-installation"></a>驗證認證以供探索和安裝

1. 從組態伺服器啟動 Cspsconfigtool</br>
![WMItestConnect5](./media/site-recovery-protection-common-errors/wmitestconnect5.png) </br>

2. 確定用於保護的帳戶具有來源電腦的系統管理員權限。 </br>

#### <a name="check-connectivity-between-process-server-and-source-server"></a>檢查處理序伺服器與來源伺服器之間的連線能力
1. 確定處理序伺服器有網際網路連線。
2. 使用 wbemtest.exe 驗證 WMI 連線。 </br>
在處理序伺服器上，按一下 [開始] > [執行] > wbemtest.exe > [Windows Management Instrumentation 測試器] 視窗將會開啟 (如下所示)。</br>
   ![WMItestConnect1](./media/site-recovery-protection-common-errors/wmitestconnect1.png) </br>
   </br>
按一下 [連線] > 在 [命名空間輸入使用者名稱和密碼] 中輸入來源伺服器 IP (如果來源電腦已加入網域，請以 "domainName\username" 形式提供網域名稱及使用者名稱。 如果來源電腦在工作群組中，僅提供使用者名稱。)將 [驗證層級] 選取為 [封包私密性]。 </br>
![WMItestConnect2](./media/site-recovery-protection-common-errors/wmitestconnect2.png) </br>
   </br>
   按一下 [連線]。 使用所提供資料進行的 WMI 連線現在應會成功，而 [Windows Management Instrumentation 測試器] 視窗應如下所示： </br>
   ![WMItestConnect3](./media/site-recovery-protection-common-errors/wmitestconnect3.png) </br>
</br>
   如果 WMI 連線不成功，將有出現錯誤訊息快顯視窗。 如果未在 Windows 防火牆允許的應用程式中啟用 [WMI/遠端系統管理]，則下列螢幕擷取畫面會顯示失敗的嘗試。 </br>
   ![WMItestConnect4](./media/site-recovery-protection-common-errors/wmitestconnect4.png) </br>
</br>

3. 檢查 WMI 狀態和連線能力。</br>
在組態/處理序伺服器上， </br>
按一下 [開始] > [執行] > wmimgmt.msc > [動作] > [其他動作] > 連線到另一部電腦 (來源電腦)。 </br>
輸入用於防護之帳戶的認證，並檢查連線是否正常。 </br>

#### <a name="verify-network-shared-folders-of-source-machine-is-accessible-from-process-server-ps-remotely-using-specified-credentials"></a>確認可使用指定的認證從處理序伺服器 (PS) 遠端存取來源電腦的網路共用資料夾。
  1. 登入處理序伺服器 (PS) 電腦，開啟 [檔案總管] > 在位址列類型中 > "\\\source-machine-ip\C$" > 按一下 Enter 鍵。 </br>
  ![Fileshare1](./media/site-recovery-protection-common-errors/fileshare1.png) </br>
  2. 檔案總管會提示輸入認證。 輸入使用者名稱和密碼，然後按一下 [確定]。</br>
   如果來源電腦已加入網域，請以 "domainName\username" 形式提供網域名稱及使用者名稱。</br>
   如果來源電腦在工作群組中，僅提供使用者名稱。 </br>
  ![Fileshare2](./media/site-recovery-protection-common-errors/fileshare2.png) </br>
  3. 如果連線成功，您可以從處理序伺服器 (PS) 遠端檢視來源電腦的資料夾 </br>
  ![Fileshare3](./media/site-recovery-protection-common-errors/fileshare3.png) </br>

> [!NOTE] 
> 如果連線不成功，請檢查是否符合所有必要條件。
>

如果您不想開啟 "Windows Management Instrumentation"，您也可以在來源電腦上手動安裝行動服務。</br> [透過 GUI 手動安裝行動服務](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) </br>
[透過組態管理員指引進行安裝](site-recovery-install-mobility-service-using-sccm.md) </br>

## <a name="next-steps"></a>後續步驟
- [啟用 VMware 虛擬機器的複寫](vmware-walkthrough-enable-replication.md)

