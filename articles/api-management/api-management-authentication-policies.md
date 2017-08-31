---
title: "Azure API 管理驗證原則 | Microsoft Docs"
description: "了解「Azure API 管理」中可用的驗證原則。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.translationtype: Human Translation
ms.sourcegitcommit: 77fd7b5b339a8ede8a297bec96f91f0a243cc18d
ms.openlocfilehash: f447e43799e56114d52b0dc0f5c36265f2870c8e
ms.contentlocale: zh-tw
ms.lasthandoff: 01/12/2017

---
# <a name="api-management-authentication-policies"></a>API 管理驗證原則
本主題提供下列「API 管理」原則的參考。 如需有關新增和設定原則的資訊，請參閱 [API 管理中的原則](http://go.microsoft.com/fwlink/?LinkID=398186)。  
  
##  <a name="AuthenticationPolicies"></a> 驗證原則  
  
-   [使用基本驗證進行驗證](api-management-authentication-policies.md#Basic) - 使用基本驗證來驗證後端服務。  
  
-   [使用用戶端憑證進行驗證](api-management-authentication-policies.md#ClientCertificate) - 使用用戶端憑證來驗證後端服務。  
  
##  <a name="Basic"></a> 使用基本驗證進行驗證  
 使用 `authentication-basic` 原則以利用「基本」驗證向後端服務進行驗證。 此原則會將「HTTP 授權」標頭有效地設定為與此原則中所提供認證對應的值。  
  
### <a name="policy-statement"></a>原則陳述式  
  
```xml  
<authentication-basic username="username" password="password" />  
```  
  
### <a name="example"></a>範例  
  
```xml  
<authentication-basic username="testuser" password="testpassword" />  
```  
  
### <a name="elements"></a>元素  
  
|名稱|說明|必要|  
|----------|-----------------|--------------|  
|authentication-basic|根元素。|是|  
  
### <a name="attributes"></a>屬性  
  
|名稱|說明|必要|預設值|  
|----------|-----------------|--------------|-------------|  
|username|指定「基本驗證」認證的使用者名稱。|是|N/A|  
|password|指定「基本驗證」認證的密碼。|是|N/A|  
  
### <a name="usage"></a>使用量  
 此原則可用於下列原則[區段](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[範圍](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)。  
  
-   **原則區段︰**inbound  
  
-   **原則範圍︰**API  
  
##  <a name="ClientCertificate"></a> 使用用戶端憑證進行驗證  
 使用 `authentication-certificate` 原則以利用用戶端憑證向後端服務進行驗證。 憑證必須先[安裝到 API 管理中](http://go.microsoft.com/fwlink/?LinkID=511599)且會以其指紋作為識別。  
  
### <a name="policy-statement"></a>原則陳述式  
  
```xml  
<authentication-certificate thumbprint="thumbprint" />  
```  
  
### <a name="example"></a>範例  
  
```xml  
<authentication-certificate thumbprint="....." />  
```  
  
### <a name="elements"></a>元素  
  
|名稱|說明|必要|  
|----------|-----------------|--------------|  
|authentication-certificate|根元素。|是|  
  
### <a name="attributes"></a>屬性  
  
|名稱|說明|必要|預設值|  
|----------|-----------------|--------------|-------------|  
|thumbprint|用戶端憑證的指紋。|是|N/A|  
  
### <a name="usage"></a>使用量  
 此原則可用於下列原則[區段](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections)和[範圍](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)。  
  
-   **原則區段︰**inbound  
  
-   **原則範圍︰**API  
  

## <a name="next-steps"></a>後續步驟
如需有關使用原則的詳細資訊，請參閱 [API 管理中的原則](api-management-howto-policies.md)。  
