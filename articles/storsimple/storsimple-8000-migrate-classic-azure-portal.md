---
title: "移轉您的 StorSimple 裝置管理員服務的儲存體帳戶、訂用帳戶 | Microsoft Docs"
description: "了解如何移轉您的 StorSimple 裝置管理員服務 8000 的訂用帳戶、儲存體帳戶。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: alkohli
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: a64cf0ba2a4646a03c864193fb93de4f54f38039
ms.contentlocale: zh-tw
ms.lasthandoff: 09/14/2017

---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>移轉與 StorSimple 裝置管理員服務相關聯的訂用帳戶和儲存體帳戶

您可能需要將 StorSimple 服務移至新的註冊或新的訂用帳戶。 這些移轉案例是帳戶變更或資料中心變更。 使用下表來了解支援哪些案例，包括移動的詳細步驟。

## <a name="account-changes"></a>帳戶變更

| 您可以移動...| 支援| 停機| Azure 支援流程| 方法|
|-----|-----|-----|-----|-----|
| 整個訂用帳戶 (包括 StorSimple 服務和儲存體帳戶) 到另一個註冊嗎？ | 是       | 否       | **註冊轉移**<br>使用：<li>當您購買新合約底下的新 Azure 承諾用量。</li><li>您想要將所有帳戶和訂用帳戶從舊的註冊移轉到新的註冊。 這包括舊訂用帳戶底下的所有 Azure 服務。</li> | **步驟 1：開啟 Azure 企業作業支援票證。**<li>移至 [http://aka.ms/AzureEnt](http://aka.ms/AzureEnt)。</li><li> 選取 [註冊管理]，然後選取 [從一個註冊轉移到新的註冊]。<br>**步驟 2：提供要求的資訊**<br>包含：<li>來源註冊號碼</li><li> 目的地註冊號碼</li><li>轉移生效日期|
| StorSimple 服務從現有的帳戶到新的註冊嗎？    | 是       | 否       | **帳戶轉移**<br>使用︰<li>當您不想要完整註冊轉移時。</li><li>您只想要將特定帳戶移至新的註冊。</li>| **步驟 1：開啟 Azure 企業作業支援票證。**<li>移至 [http://aka.ms/AzureEnt](http://aka.ms/AzureEnt)。</li><li>選取 [註冊管理]，然後選取 [將 EA 帳戶轉移到新的註冊]。<br>**步驟 2：提供要求的資訊**<br>包含：<li>來源註冊號碼</li><li> 目的地註冊號碼</li><li>轉移生效日期|
| StorSimple 服務從一個訂用帳戶到其他訂用帳戶嗎？      | 否        |    是         | 否，手動程序|<li>移轉 StorSimple 裝置的資料。</li><li>執行將裝置重設成出廠預設值，這會刪除裝置上的任何本機資料。</li><li>為裝置向 StorSimple 裝置管理員服務註冊新的訂用帳戶。</li><li>將資料移轉回裝置。|
| StorSimple 裝置從一個 StorSimple 裝置管理員服務到不同區域中的另一個服務嗎？      | 否        | 是            | 否，手動程序 |同上。|

## <a name="datacenter-changes"></a>資料中心變更

| 您可以移動...| 支援|停機| Azure 支援流程| 方法|
|-----|-----|-----|-----|-----|-----|
| StorSimple 服務從一個 Azure 資料中心到另一個嗎？ | 否 | 是 |否，手動程序  |<li>移轉 StorSimple 裝置的資料。</li><li>執行將裝置重設成出廠預設值，這會刪除裝置上的任何本機資料。</li><li>為裝置向新的 StorSimple 裝置管理員服務註冊新的訂用帳戶。</li><li>將資料移轉回裝置。|
| 儲存體帳戶從一個 Azure 資料中心到另一個嗎？ | 否 |是  |否，手動程序  | 同上。|

## <a name="next-steps"></a>後續步驟

* [部署 StorSimple 裝置管理員服務](storsimple-8000-manage-service.md)
* [在 Azure 入口網站中部署 StorSimple 8000 系列裝置](storsimple-8000-deployment-walkthrough-u2.md)
