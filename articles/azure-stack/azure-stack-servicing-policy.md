---
title: "Azure Stack 服務原則 | Microsoft Docs"
description: "深入了解 Azure Stack 服務原則，以及如何將整合的系統保持在支援的狀態。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: f2c99f19b30d2cdfdf65dea6dd3909b88ffc15dd
ms.contentlocale: zh-tw
ms.lasthandoff: 09/25/2017

---
# <a name="azure-stack-servicing-policy"></a>Azure Stack 服務原則

*適用於：Azure Stack 整合系統*

本文說明 Azure Stack 整合系統的服務原則，以及必須如何做才能將狀態保持在支援的狀態。 

## <a name="update-package-types"></a>更新封裝類型

整合的系統有兩種更新封裝；Microsoft 軟體更新，以及原始設備製造商 (OEM) 硬體廠商專屬的更新，例如驅動程式與韌體。 這些更新是以個別的 Azure Stack 更新封裝提供，而且獨立管理。

- **Microsoft 軟體更新**。 Microsoft 會負責 Microsoft 軟體更新封裝的端對端服務生命週期。 這些封裝包含最新的 Windows Server 安全性更新、非安全性更新，以及 Azure Stack 功能更新。 您可以直接從 Microsoft 下載這些更新封裝。
- **OEM 硬體廠商提供的更新**。 Azure Stack 硬體合作夥伴負責硬體相關韌體和驅動程式更新封裝的端對端服務生命週期 (包括指引)。 此外，對於硬體生命週期主機上的所有軟體和硬體，Azure Stack 硬體合作夥伴擁有並維護指引。 OEM 硬體廠商在自己的下載網站提供這些更新封裝。

## <a name="update-package-release-cadence"></a>更新封裝發行頻率

Microsoft 預期每月發行軟體更新封裝。 不過，可能一個月內發行多個更新或沒有任何更新。 OEM 硬體廠商會視需要發行更新。

Microsoft 更新封裝包含下列命名慣例，可協助您輕鬆識別發行日期：

*MajorProductVersion.MinorProductVersion.YYMMDD.BuildNumber*

例如，2017 年 6 月 15 日發行的 Microsoft 軟體更新是版本「1.0.170615.1」。

## <a name="keep-your-system-under-support"></a>維持系統獲得支援

若要獲得系統的支援，您必須在特定的時間間隔內更新 Azure Stack。 我們對於 Microsoft 軟體更新的延遲擬定的原則是三個月。 如果系統過期三個月，會被視為不合規。 您必須將系統更新為最低支援版本，才能獲得支援。 

Microsoft 軟體更新封裝不會累積，而且先決條件是需要上一個更新封裝。 如果您決定延後一或多個更新，但是想要取得最新版本，請考慮整體的執行階段。

下表顯示範例更新封裝發行、其先決條件，以及獲得支援必要的系統最低支援版本。 該表是以 Azure Stack 整合系統 (組建 1708) 為依據，第一次更新封裝發行 (1709) 是在 2017 年 9 月。 

| 最新更新封裝 (*範例*) | 必要條件 | 最低支援版本 |
| -- | -- | -- |
| 1709 | 組建 1708 | N/A |
| 1710 | 1709 | N/A |
| 1711 | 1710 | N/A |
| 1712 | 1711 | 1709 |
| 1801 | 1712 | 1710 |
| 1802 | 1801 | 1711 |
| 1803 | 1802 | 1712 |
| 1804 | 1803 | 1801 |
| | | 

## <a name="next-steps"></a>後續步驟

- [在 Azure Stack 中管理更新](azure-stack-updates.md)



