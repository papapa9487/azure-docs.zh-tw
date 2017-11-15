---
title: "針對 StorSimple 8000 系列記錄支援票證 | Microsoft Docs"
description: "了解如何建立支援要求和在 StorSimple 裝置上啟動支援工作階段。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 2ebc20fe-f490-4749-8e43-c9fac86f1676
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: alkohli;anbacker
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8d25fb4902d37faca5358e5a9010e9e146e344e1
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2017
---
# <a name="contact-microsoft-support-for-your-storsimple"></a>連絡 Microsoft 支援服務以解決 StorSimple 問題
> [!NOTE]
> StorSimple 的傳統入口網站已過時。 按照淘汰排程，StorSimple 裝置管理員會自動移至新的 Azure 入口網站。 您將收到關於此移動的電子郵件和入口網站通知。 本文件也很快就會淘汰。 若要檢視適用於新 Azure 入口網站的文章版本，請移至[連絡 Microsoft 支援服務以解決 StorSimple 問題](storsimple-8000-contact-microsoft-support.md)。 若有關於移動的任何問題，請參閱[常見問題集：移至 Azure 入口網站](storsimple-8000-move-azure-portal-faq.md)。

如果您使用 Microsoft Azure StorSimple 解決方案時遇到任何問題，您可以向技術支援人員提出服務要求。 在與支援工程師進行線上工作階段時，可能也需要在您的 StorSimple 裝置上啟動支援工作階段。 本文將引導您：

* 如何建立支援要求。
* 如何在 StorSimple 裝置的 Windows PowerShell 介面中啟動支援工作階段。

建立支援要求之前，請先檢閱 [StorSimple 8000 系列支援 SLA 和資訊](https://msdn.microsoft.com/library/mt433077.aspx) 。

## <a name="create-a-support-request"></a>建立支援要求
執行下列步驟來建立支援要求：

#### <a name="to-create-a-support-request"></a>建立支援要求
1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/)的右上角，依序按一下您的帳戶名稱和 [連絡 Microsoft 支援服務]。
   
    ![透過 ManagementPortal 連絡 MS 支援服務](./media/storsimple-contact-microsoft-support/Ibiza1.png)
2. 系統便會將您重新導向至新的 Azure 入口網站 (portal.azure.com)。 按一下 [新的支援要求]  磚。
   
    ![透過新的入口網站連絡 MS 支援服務](./media/storsimple-contact-microsoft-support/Ibiza2.png)
   
    畫面右側則會出現 [新的支援要求]  窗格。 
   
    ![新的支援要求窗格](./media/storsimple-contact-microsoft-support/Ibiza3a.png)
3. 在 [基本資料]  對話方塊中，完成下列操作：                                
   
   1. 從 [問題類型] 下拉式清單中，選取 [技術]。
   2. 從下拉式清單中，選取 [訂用帳戶]  。
   3. 從 [服務] 下拉式清單中，選取 [StorSimple]。 
   4. 從下拉式清單中，選取 [支援方案]  。 您必須已付費購買支援方案，才能啟用技術支援。
4. 按一下 [下一步] 。 [問題]  對話方塊隨即出現。
   
    ![新的支援要求窗格](./media/storsimple-contact-microsoft-support/Ibiza5a.png) 
5. 在 [問題]  對話方塊中，完成下列操作：
   
   1. 從下拉式清單中，選取 [嚴重性]  層級。
   2. 從下拉式清單中，選取 [問題類型]  。
   3. 從下拉式清單中，選取 [類別]  。 
   4. 在 [詳細資料]  方塊中，簡短描述您的問題。
   5. 在 [時間範圍]  方塊中，指出對應到最近發生問題的日期、時間和時區。
   6. 在 [檔案上傳] 底下，按一下資料夾圖示，即可瀏覽至您的支援封裝。
   7. 選取 [共用診斷資訊]  核取方塊。
6. 按一下 [下一步] 。 [連絡資訊]  對話方塊隨即出現。
   
    ![新的支援要求窗格](./media/storsimple-contact-microsoft-support/Ibiza6a.png) 
7. 輸入您的連絡資訊，並選取一個連絡方法 (電話或電子郵件)。 
8. 選取 [儲存連絡人變更以供未來的支援要求使用]  核取方塊。
9. 按一下 [建立] 。

提交您的要求之後，支援工程師會儘速連絡您來處理您的要求。

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>在 Windows PowerShell for StorSimple 中啟動支援工作階段
若要對您使用 StorSimple 裝置時可能會遇到的任何問題進行疑難排解，您必須連絡 Microsoft 支援服務小組。 Microsoft 支援服務可能需要使用支援工作階段來登入您的裝置。 

執行下列步驟來啟動支援工作階段：

#### <a name="to-start-a-support-session"></a>啟動支援工作階段
1. 從遠端電腦使用序列主控台或透過 telnet 工作階段，直接存取裝置。 如果要這樣做，請依照 [使用 PuTTY 來連接至裝置序列主控台](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)中的步驟進行。
2. 在開啟的工作階段中，按 **Enter** 鍵來取得命令提示字元。
3. 在序列主控台功能表中，選取選項 1 [使用完整存取權登入] 。
4. 在提示字元中輸入下列密碼： 
   
    `Password1`
5. 在出現提示時輸入下列命令：
   
    `Enable-HcsSupportAccess`
6. 您會看到加密的字串。 將此字串複製到 [記事本] 之類的文字編輯器。
7. 儲存這個字串，並透過電子郵件傳送給 Microsoft 支援服務。 

> [!IMPORTANT]
> 您可以執行 `Disable-HcsSupportAccess` 來停用支援存取。 在起始工作階段之後 8 小時，StorSimple 裝置也會嘗試停用支援存取。 最好在起始支援工作階段之後變更 StorSimple 裝置認證。
> 
> 

