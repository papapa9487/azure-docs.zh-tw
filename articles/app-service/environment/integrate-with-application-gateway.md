---
title: "整合 ILB ASE 與 Azure 應用程式閘道"
description: "逐步解說如何將 ILB ASE 中的應用程式整合 Azure 應用程式閘道"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: ccompy
ms.openlocfilehash: eedad8824add7fe425d34975dab640fbee82c2bc
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2017
---
# <a name="integrating-your-ilb-ase-with-an-application-gateway"></a>整合 ILB ASE 與應用程式閘道 #

[Azure App Service Environment (ASE)](./intro.md) 是 Azure App Service 部署，位於客戶之 Azure 虛擬網路的子網路中。 可使用應用程式存取的公用或私用端點進行部署。 使用私用端點部署 ASE 稱為 ILB ASE。  
Azure 應用程式閘道是虛擬設備，其提供第 7 層負載平衡、SSL 卸載，以及 WAF 保護。 它可以接聽公用 IP 位址，並將流量路由到您的應用程式端點。 下列資訊說明如何將 WAF 設定的應用程式閘道與 ILB ASE 上的應用程式做整合。  

使用 ILB ASE 整合應用程式閘道是在應用程式層級上進行。  當使用 ILB ASE 設定應用程式閘道時，您會針對 ILB ASE 中的特定應用程式進行。 這可在單一 ILB ASE 中裝載安全的多租用戶應用程式。  

![在 ILB ASE 上指向應用程式的應用程式閘道][1]

在本逐步解說中，您將：

* 建立應用程式閘道
* 將應用程式閘道設定為指向 ILB ASE 上的應用程式
* 設定應用程式以接受自訂網域名稱
* 編輯指向應用程式閘道的公用 DNS 主機名稱

若要將您的應用程式閘道與 ILB ASE 整合，您需要：

* ILB ASE
* ILB ASE 中執行的應用程式
* 可與 ILB ASE 中的應用程式搭配使用的網際網路可路由網域名稱
* ILB ASE 使用的 ILB 位址 (這是在 [設定] -> [IP 位址] 下的 ASE 入口網站)

    ![ILB ASE 使用的 IP 位址][9]
    
* 稍後用來指向您的應用程式閘道的公開 DNS 名稱 

如需如何建立 ILB ASE 的詳細資訊，請閱讀文件[建立和使用 ILB ASE][ilbase]

本指南假設您想要在 ASE 部署到的相同 Azure 虛擬網路中放置應用程式閘道。 開始建立應用程式閘道前，挑選或建立將用來裝載應用程式閘道的子網路。 您應該使用名稱不是 GatewaySubnet 的子網路，或 ILB ASE 使用的子網路。
如果您將應用程式閘道放在 GatewaySubnet，則稍後將無法建立虛擬網路閘道。 您也不能將它置於 ILB ASE 使用的子網路，因為只有 ASE 可在其子網路。

## <a name="steps-to-configure"></a>設定步驟 ##

1. 從 Azure 入口網站中，移至 [新增] > [網路] > [應用程式閘道] 
    1. 提供：
        1. 應用程式閘道的名稱
        1. 選取 WAF
        1. 選取用於 ASE VNet 的相同訂用帳戶
        1. 建立或選取資源群組
        1. 選取 ASE VNet 所在的位置

    ![新應用程式閘道的建立基本概念][2]   
    1. 在 [設定] 區設中設定：
        1. ASE VNet
        1. 必須將應用程式閘道部署到的子網路。 請勿使用 GatewaySubnet，因它將阻止建立 VPN 閘道
        1. 選取公用
        1. 選取的公用 IP 位址。 如果沒有，此時請建立一個
        1. 為 HTTP 或 HTTPS 設定。 如果設定 HTTPS，則需要提供 PFX 憑證
        1. 選取 Web 應用程式 fireway 設定。 您可在這裡啟用防火牆，並視需要將它設定為「偵測」或「預防」。

    ![新應用程式閘道的建立設定][3]
    
    1. 在摘要區段檢閱中，選取 [確定]。 可能需要稍微超過 30 分鐘才能完成應用程式閘道安裝。  

2. 完成應用程式閘道安裝後，進入您的應用程式閘道入口網站。 選取 [後端集區]。  新增 ILB ASE 的 ILB 位址。

    ![設定後端集區][4]

3. 完成後端集區的設定程序後，選取 [健康狀態探查]。 建立要用於應用程式之網域名稱的健康狀態探查。 

    ![設定健全狀態探查][5]
    
4. 完成健康狀態探查的設定程序後，選取 [HTTP 設定]。  在那裡編輯現有的設定，選取 [使用自訂探查]，並挑選您所設定的探查。

    ![進行 HTTP 設定][6]
    
5. 移至應用程式閘道**概觀**，並複製使用於應用程式閘道的公用 IP 位址。  將該 IP 位址設定為應用程式網域名稱的 A 記錄，或在 CNAME 記錄中使用該位址的 DNS 名稱。  選取公用 IP 位址，並從公用 IP 位址 UI 複製它，比從 [應用程式閘道概觀] 區段的連結中複製它更容易。 

    ![應用程式閘道入口網站][7]

6. 在 ILB ASE 中設定應用程式的自訂網域名稱。  在入口網站中移至應用程式，並在 [設定] 下選取 [自訂網域]

![在應用程式上設定自訂網域名稱][8]

這裡的[設定 web 應用程式的自訂網域名稱][custom-domain]說明了為 web 應用程式設定自訂網域名稱的資訊。 ILB ASE 中的應用程式與該文件的差異是，不會對網域名稱進行任何驗證。  由於您擁有用來管理應用程式端點的 DNS，因此您可以在那裡放置任何內容。 在此情況下新增的自訂網域名稱不需要位於您的 DNS，但仍必須使用您的應用程式設定它。 

完成安裝程式，而且您允許短暫的時間來傳播 DNS 變更後，您可以依照您建立的自訂網域名稱來存取應用程式。 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: http://docs.microsoft.com/azure/application-gateway/application-gateway-introduction
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
