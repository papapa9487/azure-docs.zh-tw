---
title: "Azure AD 同盟相容性清單"
description: "此頁面包含可用來實作單一登入的非 Microsoft 識別提供者。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: bce5867017647764546d872d97943d5d4f01f2d2
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-federation-compatibility-list"></a>Azure AD 同盟相容性清單
Azure Active Directory 在不需要任何非 Microsoft 解決方案的情況下，針對 Office 365 和混合式與僅限雲端實作的其他 Microsoft 線上服務，提供單一登入和增強的應用程式存取安全性。 Office 365，就像大部分 Microsoft 線上服務一樣，已經與 Azure Active Directory 整合以提供目錄服務、驗證及授權。 Azure Active Directory 也為數千個 SaaS 應用程式和內部部署 Web 應用程式提供單一登入。 請參閱 Azure Active Directory 應用程式庫以了解支援的 SaaS 應用程式有哪些。

對於已經投資非 Microsoft 同盟方案的組織，此主題包含使用來自下面「Azure Active Directory 同盟相容性清單」中的非 Microsoft 識別提供者，為其 Windows Server Active Directory 使用者在 Microsoft 線上服務設定單一登入的指引。 

![](./media/active-directory-aadconnect-federation-compatibility/oxford2.jpg)   
[Oxford Computer Group](http://oxfordcomputergroup.com/)這家代表 Microsoft 的協力廠商已使用非 Microsoft 識別提供者，針對一組常見的 Azure Active Directory 使用案例測試這些單一登入體驗。

如需如何取得此處所列的協力廠商識別提供者資訊，請與 Oxford Computer Group 連絡： [idp@oxfordcomputergroup.com](mailto:idp@oxfordcomputergroup.com)。

> [!IMPORTANT]
> Oxford Computer Group 僅測試這些單一登入案例的同盟功能。 Oxford Computer Group 並未針對這些單一登入案例的同步處理、雙因素驗證等等元件執行任何測試。
> 
> 此計劃中也未測試使用替代識別碼登入 UPN。
> 
> 

* [Azure Active Directory](#azure-active-directory)
* [AuthAnvil Single Sign On 4.5](#authanvil-single-sign-on-45)
* [BIG-IP with Access Policy Manager BIG-IP ver.11.3x – 11.6x](#big-ip-with-access-policy-manager-big-ip-ver-113x--116x) 
* [BitGlass](#bitglass)
* [CA Secure Cloud](#ca-secure-cloud) 
* [CA SiteMinder 12.52](#ca-siteminder-1252-sp1-cumulative-release-4) 
* [Centrify](#centrify) 
* [Dell One Identity Cloud Access Manager v7.1](#dell-one-identity-cloud-access-manager-v71) 
* [DigitalPersona Composite 驗證](#digitalpersona-composite-authentication)
* [IBM Tivoli Federated Identity Manager 6.2.2](#ibm-tivoli-federated-identity-manager-622) 
* [IceWall Federation Version 3.0](#icewall-federation-version-30) 
* [Memority](#memority)
* [NetIQ Access Manager 4.x](#netiq-access-manager-4x) 
* [Okta](#okta) 
* [OneLogin](#onelogin) 
* [Optimal IDM Virtual Identity Server Federation Services](#optimal-idm-virtual-identity-server-federation-services) 
* [PingFederate 6.11, 7.2, 8.x](#pingfederate-611-72-8x)
* [RadiantOne CFS 3.0](#radiantone-cfs-30) 
* [Sailpoint IdentityNow](#sailpoint-identitynow)
* [SecureAuth IdP 7.2.0](#secureauth-idp-720) 
* [Sign&go 5.3](#signgo-53) 
* [SoftBank Technology Online Service Gate](#softbank)
* [VMware Workspace One](#vmware-workspace-one)



> [!IMPORTANT]
> 由於這些都是協力廠商產品，因此對於與這些識別提供者有關的部署、設定、疑難排解、最佳作法等等問題，Microsoft 並沒有提供支援。 對於和這些識別提供者有關的支援與問題，請直接連絡支援的協力廠商。
> 
> 這些協力廠商識別提供者僅使用 WS-同盟與 WS-Trust 通訊協定，針對與 Microsoft 雲端服務的互通性進行測試。 測試並不包含使用 SAML 通訊協定。
> 


## <a name="azure-active-directory"></a>Azure Active Directory

以下是支援此登入體驗之矩陣的案例： 

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |None |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |
| 使用 ADAL 的現代應用程式，例如 Office 2016 |支援 |None |

如需搭配使用 Azure Active Directory 與 AD FS 的詳細資訊，請參閱 [Active Directory 同盟服務 (ADFS)](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)。

如需搭配密碼同步使用 Azure Active Directory 的相關詳細資訊，請參閱 [Azure AD Connect](active-directory-aadconnect.md)。

## <a name="authanvil-single-sign-on-45"></a>AuthAnvil Single Sign On 4.5

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |不支援整合式 Windows 驗證 |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |不支援整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需詳細資訊，請參閱 [AuthAnvil 單一登入](https://help.scorpionsoft.com/entries/26538603-How-can-I-Configure-Single-Sign-On-for-Office-365-)。


## <a name="big-ip-with-access-policy-manager-big-ip-ver-113x--116x"></a>BIG-IP with Access Policy Manager BIG-IP ver. 11.3x – 11.6x

以下是支援此單一登入體驗之矩陣的案例： 

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |不支援 |不支援 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 BIG-IP Access Policy Manager 的相關詳細資訊，請參閱 [BIG-IP Access Policy Manager](https://f5.com/products/modules/access-policy-manager) 

如需如何設定此 STS 來為您的 Active Directory 使用者提供單一登入體驗的 BIG-IP Access Policy Manager 指示，請下載 pdf [BIG-IP](http://www.f5.com/pdf/deployment-guides/microsoft-office-365-idp-dg.pdf)。

## <a name="bitglass"></a>BitGlass

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |不支援整合式 Windows 驗證 |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |不支援整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 BitGlass 的詳細資訊，請參閱 [BitGlass](http://www.bitglass.com)。

## <a name="ca-secure-cloud"></a>CA Secure Cloud

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |不支援整合式 Windows 驗證 |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |不支援整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 CA Secure Cloud 的詳細資訊，請參閱 [CA Secure Cloud](http://www.ca.com/us/products/security-as-a-service.aspx)。

## <a name="ca-siteminder-1252-sp1-cumulative-release-4"></a>CA SiteMinder 12.52 SP1 累計版本 4

以下是支援此單一登入體驗之矩陣的案例： 

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |None |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 CA SiteMinder 的詳細資訊，請參閱 [CA SiteMinder Federation](http://www.ca.com/us/products/ca-single-sign-on.html)。 

## <a name="centrify"></a>Centrify

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |None |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |不支援用戶端存取控制 |

如需 Centrify 的詳細資訊，請參閱 [Centrify](http://www.centrify.com/cloud/apps/single-sign-on-for-office-365.asp)。

## <a name="dell-one-identity-cloud-access-manager-v71"></a>Dell One Identity Cloud Access Manager v7.1

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |None |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 Dell One Identity Cloud Access Manager 的詳細資訊，請參閱 [Dell One Identity Cloud Access Manager](http://software.dell.com/products/cloud-access-manager)。

 如需如何設定此 STS 來為您的 Office 365 使用者提供單一登入體驗的指示，請參閱[設定 Office 365 使用者](http://documents.software.dell.com/dell-one-identity-cloud-access-manager/7.1/how-to-configure-microsoft-office-365)。 

## <a name="digitalpersona-composite-authentication"></a>DigitalPersona Composite 驗證  

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |不支援整合式 Windows 驗證|
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |不支援整合式 Windows 驗證|
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需詳細資訊，請參閱 [DigitalPersona Composite 驗證](http://www.crossmatch.com/uploadedFiles/Support/Reference_Material/DigitalPersona-Office-365-Deployment-Guide.pdf)。


## <a name="ibm-tivoli-federated-identity-manager-622"></a>IBM Tivoli Federated Identity Manager 6.2.2

以下是支援此單一登入體驗之矩陣的案例： 

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |None |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 IBM Tivoli Federated Identity Manager 的詳細資訊，請參閱 [IBM Security Access Manager for Microsoft Applications](http://www-01.ibm.com/support/docview.wss?uid=swg24029517)。

## <a name="icewall-federation-version-30"></a>IceWall Federation Version 3.0

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |不支援整合式 Windows 驗證 |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |不支援整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 IceWall Federation 的詳細資訊，請參閱 [IceWall Federation 3.0 版](http://h50146.www5.hp.com/products/software/security/icewall/eng/federation/)和 [IceWall Federation (含 Office 365)](http://h50146.www5.hp.com/products/software/security/icewall/federation/office365.html)。

## <a name="memority"></a>Memority

以下是支援此登入體驗之矩陣的案例： 

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |None |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需使用 Memority 的詳細資訊，請參閱 [Memority](http://www.memority.com)。


## <a name="netiq-access-manager-4x"></a>NetIQ Access Manager 4.x

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None|
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |None|
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需詳細資訊，請參閱 [NetIQ Access Manager](https://www.netiq.com/documentation/access-manager-43/admin/data/b65ogn0.html#b12iqp0m)。

## <a name="okta"></a>Okta

以下是支援此單一登入體驗之矩陣的案例： 

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |整合式 Windows 認證需要設定其他 Web 伺服器與 Okta 應用程式。 |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 Okta 的詳細資訊，請參閱 [Okta](https://www.okta.com/)。

## <a name="onelogin"></a>OneLogin

以下是支援此單一登入體驗之矩陣的案例： 

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |整合式 Windows 驗證 |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 OneLogin 的詳細資訊，請參閱 [OneLogin](https://www.onelogin.com/)。

## <a name="optimal-idm-virtual-identity-server-federation-services"></a>Optimal IDM Virtual Identity Server Federation Services

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |

如需用戶端存取原則的詳細資訊，請參閱[依據用戶端所在位置限制存取 Office 365 服務](https://technet.microsoft.com/library/hh526961.aspx)。





## <a name="pingfederate-611-72-8x"></a>PingFederate 6.11, 7.2, 8.x

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |None |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需有關如何設定此 STS 來為您的 Active Directory 使用者提供單一登入體驗的 PingFederate 指示，請參閱下列其中一項︰ 

- [PingFederate 6.11](http://go.microsoft.com/fwlink/?LinkID=266321)
- [PingFederate 7.2](http://documentation.pingidentity.com/display/PF72/PingFederate+7.2)
- [PingFederate 8.x](http://documentation.pingidentity.com/display/PFS/SSO+to+Office+365+Introduction)

## <a name="radiantone-cfs-30"></a>RadiantOne CFS 3.0

以下是支援此單一登入體驗之矩陣的案例： 

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 RadiantOne CFS 的詳細資訊，請參閱 [RadiantOne CFS](http://www.radiantlogic.com/products/radiantone-cfs/)。

## <a name="sailpoint-identitynow"></a>Sailpoint IdentityNow

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |不支援整合式 Windows 驗證 |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |不支援整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需詳細資訊，請參閱 [Sailpoint IdentityNow](https://www.sailpoint.com/idaas-identity-as-a-service-identitynow/)。

## <a name="secureauth-idp-720"></a>SecureAuth IdP 7.2.0

以下是支援此單一登入體驗之矩陣的案例： 

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |None |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |None |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 SecureAuth 的相關詳細資訊，請參閱 [SecureAuth IdP](http://go.microsoft.com/?linkid=9845293)。














## <a name="signgo-53"></a>Sign&go 5.3

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |支援 Kerberos Contract |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |None |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

Sign&go 5.3 透過 Kerberos Contract 的組態支援 Kerberos 驗證。  如需此設定的協助，請連絡 Ilex 或檢視設定指南 [Sign&go](http://www.ilex-international.com/docs/sign&go_wsfederation_en.pdf)。

## <a name="softbank-technology-online-service-gate"></a>SoftBank Technology Online Service Gate

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |不支援整合式 Windows 驗證 |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |不支援整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需 SoftBank Technology Online Service Gate 的詳細資訊，請參閱 [Softbank](https://www.softbanktech.jp/service/list/osg-pro-ent/)。

## <a name="vmware-workspace-one"></a>VMware Workspace One

以下是支援此單一登入體驗之矩陣的案例：

| 用戶端 | 支援 | 例外狀況 |
| --- | --- | --- |
| Web 用戶端，例如 Exchange Web Access 和 SharePoint Online |支援 |不支援整合式 Windows 驗證 |
| 豐富型用戶端應用程式，例如 Lync、Office 訂閱、CRM |支援 |不支援整合式 Windows 驗證 |
| 豐富型電子郵件用戶端，例如 Outlook 和 ActiveSync |支援 |None |

如需詳細資訊，請參閱 [VMware Workspace One](http://www.vmware.com/pdf/vidm-office365-saml.pdf)。

