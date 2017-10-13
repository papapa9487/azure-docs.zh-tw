---
title: "使用動態 DNS 來登錄主機名稱"
description: "此頁面會提供詳細資料，告知您如何設定動態 DNS，以便在您的 DNS 伺服器中註冊主機名稱。"
services: dns
documentationcenter: na
author: GarethBradshawMSFT
manager: timlt
editor: 
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: garbrad
ms.openlocfilehash: 440a062e5fff73526b2d77d7d0a7c52ca72a66f1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="using-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>在您自己的 DNS 伺服器中使用動態 DNS 來登錄主機名稱
[Azure 會為虛擬機器 (VM) 及角色執行個體提供名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md) 。 但是，當您的名稱解析需求超過 Azure 所提供的名稱解析時，您可以提供自己的 DNS 伺服器。 這讓您有能力量身打造自己的 DNS 方案，來符合您特定的需求。 例如，您可能需要透過 Active Directory 網域控制站存取內部部署資源。

當您的自訂 DNS 伺服器裝載為 Azure VM 時，您可以將主機名稱查詢 (適用於相同的虛擬網路) 轉送至 Azure，以解析主機名稱。 如果您不想使用此路由，您可以在您的 DNS 伺服器中使用動態 DNS 註冊您的 VM 主機名稱。  Azure 沒有能力 (例如認證) 直接在您的 DNS 伺服器中建立記錄，因此通常需要有其他的安排。 以下是一些常見案例與替代方案。

## <a name="windows-clients"></a>Windows 用戶端
未加入網域的 Windows 用戶端在開機或在 IP 位址改變時，會嘗試進行不安全的動態 DNS (DDNS) 更新。 DNS 名稱就是主機名稱加上主要 DNS 尾碼。 Azure 會讓主要 DNS 尾碼留白，但您可以在 VM 中透過 [UI](https://technet.microsoft.com/library/cc794784.aspx) 或[使用自動化](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix)設定這個項目。

已加入網域的 Windows 用戶端會使用安全的動態 DNS，向網域控制站註冊自己的 IP 位址。 網域加入程序會設定用戶端上的主要 DNS 尾碼，並建立和維護信任關係。

## <a name="linux-clients"></a>Linux 用戶端
Linux 用戶端通常不會在啟動時向 DNS 伺服器自行登錄，其假設 DHCP 伺服器會這麼做。 Azure 的 DHCP 伺服器並不具能力或認證，無法在您的 DNS 伺服器登錄記錄。  您可以使用稱為 *nsupdate*的工具，該工具隨附於繫結封裝，以傳送動態 DNS 更新。 因為動態 DNS 通訊協定已標準化，您甚至可以在您並非於 DNS 伺服器上使用繫結時，使用 *nsupdate* 。

您可以使用 DHCP 用戶端所提供的勾點，在 DNS 伺服器中建立及維護主機名稱實體。 在 DHCP 週期中，用戶端在 */etc/dhcp/dhclient-exit-hooks.d/*中執行指令碼。 這可以用來使用 *nsupdate*註冊新的 IP 位址。 例如：

        #!/bin/sh
        requireddomain=mydomain.local

        # only execute on the primary nic
        if [ "$interface" != "eth0" ]
        then
            return
        fi

        # when we have a new IP, perform nsupdate
        if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
           [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
        then
            host=`hostname`
            nsupdatecmds=/var/tmp/nsupdatecmds
              echo "update delete $host.$requireddomain a" > $nsupdatecmds
              echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
              echo "send" >> $nsupdatecmds

              nsupdate $nsupdatecmds
        fi

        
        

您也可以使用 *nsupdate* 命令來執行安全動態 DNS 更新。 例如，當您使用繫結 DNS 伺服器時，會 [產生](http://linux.yyz.us/nsupdate/)公開-私密金鑰組。  DNS 伺服器已使用金鑰的公開部分進行 [設定](http://linux.yyz.us/dns/ddns-server.html) ，因此其可驗證要求的簽章。 您必須使用 -k 選項以提供金鑰組給 nsupdate，以便簽署動態 DNS 更新要求。

當您使用 Windows DNS 伺服器時，您可以使用 Kerberos 驗證搭配 nsupdate 的 -g 參數 (nsupdate 的 Windows 版本未提供)。 若要這樣做，請使用 *kinit* 載入認證 (例如從 [keytab 檔案](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html))。 然後 *nsupdate -g* 會從快取挑選認證。

如有需要，您可以將 DNS 搜尋尾碼加入您的 VM。 DNS 尾碼是在 */etc/resolv.conf* 檔案中指定。 大多數的 Linux 發行版會自動管理這個檔案的內容，因此通常您無法編輯該檔案。 不過，您可以使用 DHCP 用戶端的 *supersede* 命令以覆寫尾碼。 若要這樣做，請在 */etc/dhcp/dhclient.conf*中，加入：

        supersede domain-name <required-dns-suffix>;

