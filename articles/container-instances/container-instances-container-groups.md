---
title: "Azure Container Instances 容器群組"
description: "了解容器群組在 Azure Container Instances 中的運作方式"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 08/08/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 568a99d44a5a32339d438ed1025670d12ecce791
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2017
---
# <a name="container-groups-in-azure-container-instances"></a>Azure Container Instances 中的容器群組

在 Azure Container Instances 中，最上層的資源就是容器群組。 本文說明容器群組為何，以及這些群組能夠實現哪些類型的案例。

## <a name="how-a-container-group-works"></a>容器群組的運作方式

容器群組是容器的集合，這些容器會排程在相同的主機電腦上，並共用生命週期、區域網路和存放磁碟區。 容器群組類似於 [Kubernetes](https://kubernetes.io/docs/concepts/workloads/pods/pod/) 和 [DC/OS](https://dcos.io/docs/1.9/deploying-services/pods/) 中的 *Pod* 概念。

下圖舉例說明包含多個容器的容器群組。

![容器群組範例][container-groups-example]

請注意：

- 此群組排程在單一主機電腦上。
- 此群組會公開單一的公用 IP 位址，以及一個公開的連接埠。
- 此群組是由兩個容器所組成。 一個容器接聽連接埠 80，另一個容器接聽連接埠 5000。
- 此群組包含兩個 Azure 檔案共用來作為磁碟區掛接，而且每個容器會在本機掛接其中一個共用。

### <a name="networking"></a>網路

容器群組會共用 IP 位址以及該 IP 位址上的連接埠命名空間。 若要讓外部用戶端能夠連線到該群組內的容器，您必須從該容器公開該 IP 位址上的連接埠。 群組內的容器會共用連接埠命名空間，因此不支援連接埠對應。 群組內的容器可以在它們已公開的連接埠上透過 localhost 彼此連線，即使這些連接埠並未在群組的 IP 位址上對外公開也是如此。

### <a name="storage"></a>儲存體

您可以指定要在容器群組內掛接的外部磁碟區。 您可以將這些磁碟區對應到群組之個別容器內的特定路徑。

## <a name="common-scenarios"></a>常見案例

如果您想要將單一功能的工作分割成少量的容器映像，以便由不同小組來提供並讓這些映像具有不同的資源需求，則多個容器的群組會很實用。 使用範例可能包括：

- 一個應用程式容器和一個記錄容器。 記錄容器收集主應用程式所輸出的記錄和計量，並將這些資料寫入到長期存放區。
- 一個應用程式和一個監視容器。 監視容器會定期地向應用程式發出要求，以確保它會保持執行狀態並正確回應，如果沒有正確回應則引發警示。
- 一個容器提供 Web 應用程式，一個容器從原始檔控制提取最新內容。

## <a name="next-steps"></a>後續步驟

了解如何使用 Azure Resource Manager 範本來[部署多個容器的群組](container-instances-multi-container-group.md)。

<!-- IMAGES -->

[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png