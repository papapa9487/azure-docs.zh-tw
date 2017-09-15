---
title: "如何搭配使用 Azure Redis 快取與 Node.js | Microsoft Docs"
description: "開始搭配使用 Azure Redis 快取與 Node.js 和 node_redis。"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: v-lincan
ms.assetid: 06fddc95-8029-4a8d-83f5-ebd5016891d9
ms.service: cache
ms.devlang: nodejs
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/10/2017
ms.author: sdanie
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: f2c448af24e180db58f3ef3d39e90036dda3f7eb
ms.contentlocale: zh-tw
ms.lasthandoff: 09/07/2017

---
# <a name="how-to-use-azure-redis-cache-with-nodejs"></a>如何搭配使用 Azure Redis 快取與 Node.js
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Azure Redis 快取可讓您存取 Microsoft 所管理的專用安全 Redis 快取。 從 Microsoft Azure 內的任何應用程式都可以存取您的快取。

本主題說明如何開始搭配使用 Azure Redis 快取與 Node.js。 

## <a name="prerequisites"></a>必要條件
安裝 [node_redis](https://github.com/mranney/node_redis)：

    npm install redis

本教學課程使用 [node_redis](https://github.com/mranney/node_redis)。 如需使用其他 Node.js 用戶端的範例，請參閱列在 [Node.js Redis 用戶端](http://redis.io/clients#nodejs)之 Node.js 用戶端的個別文件。

## <a name="create-a-redis-cache-on-azure"></a>在 Azure 上建立 Redis 快取
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>擷取主機名稱和存取金鑰
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>使用 SSL 安全地連接到快取
[node_redis](https://github.com/mranney/node_redis) 的最新組建提供了使用 SSL 連接到 Azure Redis 快取的支援。 下列範例示範如何使用 6380 的 SSL 端點連接到 Azure Redis 快取。 以您的快取名稱取代 `<name>`，並以先前[擷取主機名稱和存取金鑰](#retrieve-the-host-name-and-access-keys)一節中所述的主要或次要金鑰取代 `<key>`。

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

> [!NOTE]
> 新的 Azure Redis 快取執行個體已停用非 SSL 連接埠。 如果您使用不支援 SSL 的不同用戶端，請參閱[如何啟用非 SSL 連接埠](cache-configure.md#access-ports)。
> 
> 

## <a name="add-something-to-the-cache-and-retrieve-it"></a>在快取中加入項目並擷取該項目
下列範例示範如何連接到 Azure Redis 快取執行個體，以及儲存和擷取快取中的項目。 如需更多搭配使用 Redis 與 [node_redis](https://github.com/mranney/node_redis) 用戶端的範例，請參閱 [http://redis.js.org/](http://redis.js.org/)。

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

    client.set("key1", "value", function(err, reply) {
            console.log(reply);
        });

    client.get("key1",  function(err, reply) {
            console.log(reply);
        });

輸出：

    OK
    value


## <a name="next-steps"></a>後續步驟
* [啟用快取診斷](cache-how-to-monitor.md#enable-cache-diagnostics)，以[監視](cache-how-to-monitor.md)您快取的健全狀況。
* 閱讀官方 [Redis 文件](http://redis.io/documentation)。


