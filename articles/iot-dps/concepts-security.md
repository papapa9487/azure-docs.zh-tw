---
title: "Azure IoT 中樞裝置佈建服務的安全性概念 | Microsoft Docs"
description: "說明具有裝置佈建服務和 IoT 中樞之裝置的特定安全性佈建概念"
services: iot-dps
keywords: 
author: nberdy
ms.author: nberdy
ms.date: 09/05/2017
ms.topic: article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 266b9b7eb228744075627e1e80710e63c27880cc
ms.openlocfilehash: 3ccbaaf55d2bdfedffcdb5ca069798328e2d75fd
ms.contentlocale: zh-tw
ms.lasthandoff: 09/06/2017

---

# <a name="iot-hub-device-provisioning-service-security-concepts"></a>IoT 中樞裝置佈建服務的安全性概念 

IoT 中樞裝置佈建服務是 IoT 中樞適用的協助程式服務，用於設定在指定 IoT 中樞上的全自動佈建裝置作業。 這項裝置佈建服務可以讓您以安全且可調整的方式佈建數百萬個裝置。 本文提供關於裝置佈建*安全性*概念的概觀。 本文與為裝置進行部署準備工作的所有角色相關。

## <a name="attestation-mechanism"></a>證明機制

證明機制是確認裝置身分識別的方法。 證明機制也與申請清單相關，該清單會讓佈建服務知道各裝置對應的證明方法。

> [!NOTE]
> IoT 中樞會針對該服務中的類似概念使用「驗證配置」。

裝置佈建服務支援兩種形式的證明：
* 以標準 X.509 憑證驗證流程為基礎的 **X.509 憑證**。
* 以針對金鑰使用 TPM 標準之 nonce 挑戰為基礎的 **SAS 權杖**。 使用此種形式時，裝置不需要有實體 TPM 即可執行此動作，但該服務會因為 [TPM 規格](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)的需求，而預期裝置使用簽署金鑰進行證明。

## <a name="hardware-security-module"></a>硬體安全模組

我們將硬體安全模組 (也稱為 HSM) 作為安全的硬體式裝置密碼儲存體使用，這是最安全的密碼儲存體。 X.509 憑證和 SAS 權杖都可以儲存在硬體安全模組 (HSM) 中。 硬體安全模組 (HSM) 可以和佈建支援的兩種證明機制搭配使用。

> [!TIP]
> 我們強烈建議裝置使用硬體安全模組 (HSM) 以在裝置上安全地儲存密碼。

您也可以將裝置密碼儲存在軟體 (記憶體) 上，但相較於硬體安全模組 (HSM)，該儲存方式比較不安全。

## <a name="trusted-platform-module-tpm"></a>信賴平台模組 (TPM)

TPM 可以參考安全地儲存驗證平台所用之金鑰的標準，以及與實作標準之模組互動的 I/O 介面。 TPM 可以是獨立硬體、整合式硬體、韌體或軟體。 深入了解 [TPM 和 TPM 證明](/windows-server/identity/ad-ds/manage/component-updates/tpm-key-attestation)。 裝置佈建服務僅支援 TPM 2.0。

## <a name="endorsement-key"></a>簽署金鑰

簽署金鑰是 TPM 內建的非對稱金鑰，該金鑰在製造 TPM 時插入，且專屬於該 TPM。 您無法變更或移除簽署金鑰。 簽署金鑰的私密部分絕不會釋放到 TPM 外部，而簽署金鑰的公開部分則用於識別正版 TPM。 深入了解[簽署金鑰](https://technet.microsoft.com/library/cc770443(v=ws.11).aspx)。

## <a name="storage-root-key"></a>儲存根金鑰

儲存根金鑰儲存在 TPM 中，可保護應用程式建立的 TPM 金鑰，這樣一來，若沒有 TPM，就無法使用這些金鑰。 取得 TPM 擁有權後，會產生儲存根金鑰；而若您清除 TPM 以讓新的使用者取得擁有權，便又會產生新的儲存根金鑰。 深入了解[儲存根金鑰](https://technet.microsoft.com/library/cc753560(v=ws.11).aspx)。

## <a name="root-certificate"></a>根憑證

根憑證是一種 X.509 憑證，代表憑證授權單位且自我簽署， 是憑證鏈結的終點。

## <a name="intermediate-certificate"></a>中繼憑證

中繼憑證是根憑證 (或其鏈結中具有根憑證的另一個憑證) 簽署的一種 X.509 憑證，用於簽署分葉憑證。

## <a name="leaf-certificate"></a>分葉憑證

分葉憑證也稱為終端實體憑證，用來識別憑證持有者，而且其憑證鏈結中有根憑證。 分葉憑證無法用來簽署其他任何憑證。

