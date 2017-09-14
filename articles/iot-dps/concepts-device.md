---
title: "Azure 裝置佈建的裝置概念 | Microsoft Docs"
description: "說明具有裝置佈建服務和 IoT 中樞之裝置的特定裝置佈建概念"
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
ms.openlocfilehash: 5297bc57729d9e983d63244c71eb21995cf73f0e
ms.contentlocale: zh-tw
ms.lasthandoff: 09/06/2017

---

# <a name="iot-hub-device-provisioning-service-device-concepts"></a>IoT 中樞裝置佈建服務的裝置概念

IoT 中樞裝置佈建服務是 IoT 中樞適用的協助程式服務，用於設定在指定 IoT 中樞上的全自動佈建裝置作業。 這項裝置佈建服務可以讓您以安全且可調整的方式佈建數百萬個裝置。

本文說明裝置佈建的裝置概念概觀。 本文與為裝置進行[製造步驟](about-iot-dps.md#manufacturing-step)，也就是部署準備工作的角色相關。

## <a name="attestation-mechanism"></a>證明機制

證明機制是確認裝置身分識別的方法。 證明機制也與申請清單相關，該清單會讓佈建服務知道各裝置對應的證明方法。

> [!NOTE]
> IoT 中樞會針對該服務中的類似概念使用「驗證配置」。

裝置佈建服務支援兩種形式的證明：
* 以標準 X.509 憑證驗證流程為基礎的 **X.509 憑證**。
* 以針對金鑰使用 TPM 標準之 nonce 挑戰為基礎的 **SAS 權杖**。 使用此種形式時，裝置不需要有實體 TPM 即可執行此動作，但該服務會因為 [TPM 規格](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)的需求，而預期裝置使用簽署金鑰進行證明。

## <a name="hardware-security-module"></a>硬體安全模組

我們將硬體安全模組 (也稱為 HSM) 作為安全的硬體式裝置密碼儲存體使用，這是最安全的密碼儲存體。 X.509 憑證和 SAS 權杖都可以儲存在硬體安全模組 (HSM) 中。 硬體安全模組 (HSM) 可以和佈建服務支援的兩種證明機制搭配使用。

> [!TIP]
> 我們強烈建議裝置使用硬體安全模組 (HSM) 以在裝置上安全地儲存密碼。

您也可以將裝置密碼儲存在軟體 (記憶體) 上，但相較於硬體安全模組 (HSM)，該儲存方式比較不安全。

## <a name="registration-id"></a>註冊識別碼

註冊識別碼是裝置佈建服務中之裝置的唯一識別碼。 每個裝置識別碼在佈建服務的[識別碼範圍](#id-scope)中都必須是獨一無二的。 每個裝置都必須要有註冊識別碼。 註冊識別碼中有英數字元、小寫字母且可能包含連字號。

* 若為 TPM，TPM 本身會提供註冊識別碼。
* 若為 X.509 式證明方法，則會提供註冊識別碼作為憑證的主體名稱。

## <a name="device-id"></a>裝置識別碼

裝置識別碼是 IoT 中樞裡顯示的識別碼。 您可以在申請項目中設定想要的裝置識別碼，但這並非為必要設定。 如果未在申請清單中未指定任何想的裝置識別碼，系統會在您註冊裝置時使用註冊識別碼作為裝置識別碼。 深入了解 [IoT 中樞裡的裝置識別碼](../iot-hub/iot-hub-devguide-identity-registry.md)。

## <a name="id-scope"></a>識別碼範圍

使用者建立識別碼範圍，並且將其作為裝置將註冊之特定佈建服務的唯一識別碼範圍後，系統會將該範圍指派給裝置佈建服務。 服務會產生識別碼範圍且該範圍將維持不變，以確保唯一性。

> [!NOTE]
> 唯一性在長期執行部署作業及合併和收購的情況下很重要。

