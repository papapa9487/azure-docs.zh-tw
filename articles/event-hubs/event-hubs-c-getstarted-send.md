---
title: "使用 C 將事件傳送至 Azure 事件中樞 | Microsoft Docs"
description: "使用 C 將事件傳送至 Azure 事件中樞"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 08/15/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: a615ee39b6c3731cc7df366e9fabeed5219a71b4
ms.contentlocale: zh-tw
ms.lasthandoff: 08/16/2017

---

# <a name="send-events-to-azure-event-hubs-using-c"></a>使用 C 將事件傳送至 Azure 事件中樞

## <a name="introduction"></a>簡介
事件中樞是高度可擴充的擷取系統，每秒可擷取數百萬個事件，讓應用程式能處理並分析已連線裝置與應用程式產生的大量資料。 資料收集到事件中樞後，您可以使用任何即時分析提供者或儲存體叢集來轉換與儲存資料。

如需詳細資訊，請參閱 [事件中樞概觀][Event Hubs overview]。

在本教學課程中，您將了解如何使用以 C 撰寫的主控台應用程式將事件傳送到事件中樞。若要接收事件，請按一下左側目錄中適當的接收語言。

若要完成本教學課程，您需要下列項目：

* C 開發環境。 在本教學課程中，我們假設 Azure Linux VM上的 gcc 堆疊有 Ubuntu 14.04。
* [Microsoft Visual Studio](https://www.visualstudio.com/)。
* 使用中的 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="send-messages-to-event-hubs"></a>將訊息傳送至事件中心
在本節中，我們會撰寫一個 C 應用程式，以將事件傳送到事件中樞。 此程式碼會使用 [Apache Qpid 專案](http://qpid.apache.org/)中的 Proton AMQP 程式庫。 這與搭配使用 Service Bus Queues and Topics 與透過 C 的 AMQP 類似 (如 [這裡](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504)所示)。 如需詳細資訊，請參閱 [Qpid Proton 文件](http://qpid.apache.org/proton/index.html)。

1. 從 [Qpid AMQP Messenger][](https://qpid.apache.org/proton/messenger.html) 頁面，遵循指示以安裝 Qpid Proton (視您的環境而定)。
2. 若要編譯 Proton 程式庫，請安裝下列封裝：
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. 下載 [Qpid Proton 程式庫](http://qpid.apache.org/proton/index.html)，並將它解壓縮，例如：
   
    ```shell
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. 建立組建目錄、編譯並安裝：
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. 在工作目錄中，使用下列程式碼建立一個稱為 **sender.c** 的新檔案。 請記得將值替換成您的事件中樞名稱和命名空間名稱。 您也必須替代先前針對 **SendRule** 建立之金鑰的 URL 編碼版本。 您可以在 [這裡](http://www.w3schools.com/tags/ref_urlencode.asp)對其進行 URL 編碼。
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     
      {                                                                          
        if(pn_messenger_errno(messenger))                                        
        {                                                                        
          printf("check\n");                                                     
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); 
        }                                                                        
      }  
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. 編譯檔案，並假設 **gcc**：
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > 在這個程式碼中，我們使用輸出視窗 1 盡快強制輸出訊息。 應用程式一般應該會嘗試批次處理訊息，以增加輸送量。 如需如何在此環境和其他環境中，以及從提供繫結的平台 (目前是 Perl、PHP、Python 和 Ruby) 中使用 Qpid Proton 程式庫的相關資訊，請參閱 [Qpid AMQP Messenger 頁面](https://qpid.apache.org/proton/messenger.html)。


## <a name="next-steps"></a>後續步驟
您可以造訪下列連結以深入了解事件中樞︰

* [事件中樞概觀](event-hubs-what-is-event-hubs.md
)
* [建立事件中樞](event-hubs-create.md)
* [事件中樞常見問題集](event-hubs-faq.md)

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

