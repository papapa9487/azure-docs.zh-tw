---
title: "Azure 資訊安全中心和 Azure 中的 Linux 虛擬機器 | Microsoft Docs"
description: "了解如何使用 Azure 資訊安全中心來確保 Azure Linux 虛擬機器的安全性。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/07/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: dbba39f5b9f18aaca6449e08aa584224fc2126d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-virtual-machine-security-by-using-azure-security-center"></a>使用 Azure 資訊安全中心來監視虛擬機器

Azure 資訊安全中心可協助您了解 Azure 資源的安全性作法。 資訊安全中心提供了整合式的安全性監視功能。 它可以偵測到可能不會被察覺的威脅。 在本教學課程中，您將會了解 Azure 資訊安全中心，以及要如何︰
 
> [!div class="checklist"]
> * 設定資料收集功能
> * 設定安全性原則
> * 檢視及修正組態的健康狀態問題
> * 檢閱所偵測到的威脅  

## <a name="security-center-overview"></a>資訊安全中心概觀

資訊安全中心會找出潛在的虛擬機器 (VM) 組態問題和針對性的安全性威脅。 這些項目可能包括缺少網路安全性群組的 VM、磁碟未加密，以及遠端桌面通訊協定 (RDP) 暴力破解攻擊。 此資訊會以容易看懂的圖表形式顯示在資訊安全中心儀表板上。

若要存取資訊安全中心儀表板，請在 Azure 入口網站的功能表上選取 [資訊安全中心]。 在儀表板上，您可以看到 Azure 環境的安全性健康狀態、找到目前建議項目的計數，以及檢視目前的威脅警示狀態。 展開每個高階圖表就能查看更多詳細資料。

![資訊安全中心儀表板](./media/tutorial-azure-security/asc-dash.png)

資訊安全中心不只能探索資料，它還會提供建議以讓您解決所偵測到的問題。 例如，如果 VM 在部署時未連結網路安全性群組，資訊安全中心便會顯示建議，並指出可供採取的補救步驟。 您不需要離開資訊安全中心便能自動補救。  

![建議](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>設定資料收集功能

您必須先設定資訊安全中心的資料收集功能，才能了解 VM 的安全性組態。 設定過程中，您會開啟資料收集功能，並建立 Azure 儲存體帳戶以保存收集到的資料。 

1. 在資訊安全中心儀表板上，按一下 [安全性原則] 並選取您的訂用帳戶。 
2. 在 [資料收集] 中選取 [開啟]。
3. 若要建立儲存體帳戶，請選取 [選擇儲存體帳戶]。 然後選取 [確定]。
4. 在 [安全性原則] 刀鋒視窗中，選取 [儲存]。 

系統隨即會在所有 VM 上安裝資訊安全中心的資料收集代理程式，並開始收集資料。 

## <a name="set-up-a-security-policy"></a>設定安全性原則

安全性原則可用來定義原則項目，讓資訊安全中心收集其資料並提出建議。 您可以對不同的 Azure 資源集合套用不同的安全性原則。 雖然系統預設會根據所有原則項目來評估 Azure 資源，但您可以針對所有 Azure 資源或某個資源群組來關閉個別的原則項目。 若要深入了解資訊安全中心的安全性原則，請參閱[在 Azure 資訊安全中心設定安全性原則](../../security-center/security-center-policies.md)。 

若要設定所有 Azure 資源的安全性原則：

1. 在資訊安全中心儀表板上，選取 [安全性原則] 並選取您的訂用帳戶。
2. 選取 [預防原則]。
3. 開啟您要對所有 Azure 資源套用的原則項目，或將項目關閉。
4. 當您選好設定時，請選取 [確定]。
5. 在 [安全性原則] 刀鋒視窗中，選取 [儲存]。 

若要設定特定資源群組的原則︰

1. 在資訊安全中心儀表板上，選取 [安全性原則] 並選取資源群組。
2. 選取 [預防原則]。
3. 開啟您要對該資源群組套用的原則項目，或將項目關閉。
4. 在 [繼承] 底下選取 [唯一]。
5. 當您選好設定時，請選取 [確定]。
6. 在 [安全性原則] 刀鋒視窗中，選取 [儲存]。  

您也可以在此頁面關閉特定資源群組的資料收集功能。

下列範例已為名為 myResoureGroup 的資源群組建立唯一的原則。 此原則會關閉磁碟加密和 Web 應用程式防火牆的建議。

![唯一原則](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>檢視 VM 組態健康狀態

在開啟資料收集功能並設定好安全性原則後，資訊安全中心會開始提供警示和建議。 VM 在部署時就已安裝好資料收集代理程式。 之後，系統就會在資訊安全中心內填入新 VM 的資料。 若要深入了解 VM 組態的健康狀態，請參閱[在資訊安全中心內保護您的 VM](../../security-center/security-center-virtual-machine-recommendations.md)。 

隨著資料的收集，系統會彙總每個 VM 和相關 Azure 資源的資源健康狀態。 此資訊會以容易看懂的圖表形式來顯示。 

若要檢視資源健康狀態︰

1.  在資訊安全中心儀表板的 [資源安全性健康狀態] 底下，選取 [計算]。 
2.  在 [計算] 刀鋒視窗中選取 [虛擬機器]。 此檢視會提供所有 VM 組態的狀態摘要。

![計算健康狀態](./media/tutorial-azure-security/compute-health.png)

若要查看某個 VM 的所有建議，請選取該 VM。 本教學課程會在下一節詳述各項建議和補救步驟。

## <a name="remediate-configuration-issues"></a>補救組態問題：

在資訊安全中心開始填入組態資料後，系統會根據您設定的安全性原則來提出建議。 例如，如果 VM 在設置時沒有相關聯的網路安全性群組，系統會建議您建立一個安全性群組。 

若要查看所有建議項目的清單︰ 

1. 在資訊安全中心儀表板上，選取 [建議]。
2. 選取特定建議。 適用該項建議的所有資源清單隨即會出現。
3. 若要套用建議，請選取特定資源。 
4. 遵循補救步驟的指示來進行。 

在許多情況下，資訊安全中心會提供可行步驟，供您執行建議卻又無須離開資訊安全中心。 在下列範例中，資訊安全中心會偵測到輸入規則未受限制的網路安全性群組。 在建議頁面中，您可以選取 [編輯輸入規則] 按鈕。 用以修改規則的 UI 會隨即出現。 

![建議](./media/tutorial-azure-security/remediation.png)

建議在執行補救後會標示為已解決。 

## <a name="view-detected-threats"></a>檢視偵測到的威脅

除了資源組態建議外，資訊安全中心也會提供威脅偵測警示。 安全性警示功能會彙總從每個 VM、Azure 網路記錄和連線合作夥伴解決方案所收集到的資料，以偵測不利於 Azure 資源的安全性威脅。 若要深入了解資訊安全中心的威脅偵測功能，請參閱 [Azure 資訊安全中心的偵測功能](../../security-center/security-center-detection-capabilities.md)。

若要使用安全性警示功能，須將資訊安全中心的定價層從「免費」提升為「標準」。 當您改用這個較高的定價層時，您會有 30 天的**免費試用**期。 

若要變更定價層：  

1. 在資訊安全中心儀表板上，按一下 [安全性原則] 並選取您的訂用帳戶。
2. 選取 [定價層]。
3. 選取新的定價層，然後選取 [選取]。
4. 在 [安全性原則] 刀鋒視窗中，選取 [儲存]。 

在變更定價層後，一旦系統偵測到安全性威脅，安全性警示圖表就會開始填入資料。

![安全性警示](./media/tutorial-azure-security/security-alerts.png)

選取警示以檢視資訊。 例如，您可以看到威脅、偵測時間、所有威脅嘗試和建議補救步驟等項目的描述。 在下列範例中，系統會偵測到 RDP 暴力破解攻擊，且這項 RDP 攻擊嘗試已失敗 294 次。 資訊安全中心會提供建議的解決方案。

![RDP 攻擊](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已設定 Azure 資訊安全中心，然後在資訊安全中心檢閱了 VM。 您已了解如何︰

> [!div class="checklist"]
> * 設定資料收集功能
> * 設定安全性原則
> * 檢視及修正組態的健康狀態問題
> * 檢閱所偵測到的威脅

前進到下一個教學課程，深入了解如何利用 Jenkins、GitHub、Docker 建立 CI/CD 管線。

> [!div class="nextstepaction"]
> [以 Jenkins、GitHub 及 Docker 建立 CI/CD 基礎結構](tutorial-jenkins-github-docker-cicd.md)

