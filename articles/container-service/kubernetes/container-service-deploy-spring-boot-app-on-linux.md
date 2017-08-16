---
title: "在 Azure Container Service 的 Linux 上部署 Spring Boot Web 應用程式 | Microsoft Docs"
description: "本教學課程會逐步引導您將 Spring Boot 應用程式部署為 Microsoft Azure 上之 Linux Web 應用程式的步驟。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 06/21/2017
ms.author: asirveda;robmcm
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 6035b0e2fe27b46c441012144caf545eb17e1825
ms.contentlocale: zh-tw
ms.lasthandoff: 08/08/2017

---

# <a name="deploy-a-spring-boot-application-on-linux-in-azure-container-service"></a>將 Spring Boot 應用程式部署到 Azure Container Service 中的 Linux

[Spring Framework] 是一個開放原始碼解決方案，可協助 Java 開發人員建立企業級應用程式。 建立在 Java 之基礎上的其中一個更熱門的專案是 [Spring Boot]，其中會提供用來建立獨立 Java 應用程式的簡化方法。

[Docker] 是開放原始碼解決方案，可協助開發人員自動化部署、調整及管理容器中執行的應用程式。

本教學課程會逐步指導您如何使用 Docker 來開發 Spring Boot 應用程式，並且將其部署至 [Azure Container Service] 中的 Linux 主機。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程中的步驟，您需要下列項目：

* Azure 訂用帳戶。 如果您還沒有 Azure 訂用帳戶，則可以啟用 [MSDN 訂戶權益]或註冊[免費的 Azure 帳戶]。
* [Azure 命令列介面 (CLI)]。
* 最新的 [Java 開發工具組 (JDK)]。
* Apache 的 [Maven] 建置工具 (第 3 版)。
* [Git] 用戶端。
* [Docker] 用戶端。

> [!NOTE]
>
> 由於本教學課程有虛擬化需求，您不能完全按照本文中關於虛擬機器的步驟來進行。 您必須改為使用已啟用虛擬化功能的實體電腦。
>

## <a name="create-the-spring-boot-on-the-docker-getting-started-web-app"></a>建立 Spring Boot on Docker Getting Started Web 應用程式

下列步驟會協助您建立簡單的 Spring Boot Web 應用程式，並於本機進行其測試。

1. 開啟命令提示字元。 然後建立本機目錄來保存您的應用程式，並變更至該目錄；例如：
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   -- 或 --
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

2. 將 [Spring Boot on Docker Getting Started] 範例專案複製到您所建立的目錄中；例如：
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

3. 將目錄變更至已完成的專案；例如：
   ```
   cd gs-spring-boot-docker/complete
   ```

4. **選擇性步驟**：如果您想要在連接埠 80 而非預設的連接埠 8080 上執行內嵌的 Tomcat 伺服器 (例如，如果您要在本機測試 Spring Boot 專案)，請使用下列步驟來設定連接埠：

   a. 將目錄變更為資源目錄；例如：
   ```
   cd src/main/resources
   ```

   b.這是另一個 C# 主控台應用程式。 在文字編輯器中開啟 application.yml 檔案。

   c. 修改 [伺服器:] 設定，讓伺服器可在連接埠 80 上執行；例如：
   ```
   server:
      port: 80
   ```

   d. 儲存並關閉 application.yml 檔案。

   e. 將目錄變更回到已完成專案的根資料夾；例如：
   ```
   cd ../../..
   ```

5. 使用 Maven 建立 JAR 檔案；例如：
   ```
   mvn package
   ```

6. 建立好 Web 應用程式之後，前往 JAR 檔案所在的 `target` 目錄，然後啟動 Web 應用程式；例如：
   ```
   cd target
   java -jar gs-spring-boot-docker-0.1.0.jar
   ```

7. 測試 Web 應用程式，方法是使用網頁瀏覽器在本機瀏覽它。 例如，如果 curl 可用，而且您已將 Tomcat 伺服器設定為在連接埠 80 上執行，您可以使用下列命令：
   ```
   curl http://localhost
   ```

8. 您應該會看到顯示下列訊息：**Hello Docker World!**

   ![在本機瀏覽範例應用程式][SB01]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>建立 Azure Container Registry 以用作私人 Docker 登錄

下列步驟會逐步指導您如何使用 Azure 入口網站來建立 Azure Container Registry。

> [!NOTE]
> 如果您想要使用 Azure CLI 而不是 Azure 入口網站，請遵循下列[使用 Azure CLI 2.0 建立私人 Docker 容器登錄][1]中的步驟。

1. 登入 [Azure 入口網站]。

    在 Azure 入口網站上登入到您的帳戶後，請遵循[使用 Azure 入口網站建立私人 Docker 容器登錄]中的步驟。 該文中的步驟摘要如下：

2. 選取 [+ 新增] 的功能表圖示。

3. 選取 [容器]，然後選取 [Azure Container Registry]。

   ![建立新的 Azure Container Registry][AR01]

4. 當 Azure Container Registry 範本的資訊頁面顯示時，按一下 [建立]。

   ![建立新的 Azure Container Registry][AR02]

5. 當 [建立容器登錄] 刀鋒視窗顯示時：

   a. 輸入您的 [登錄名稱] 和 [資源群組]。  
   
   b. 針對 [管理使用者] 選取 [啟用]。
   
   c. 選取 [ **建立**]。

   ![設定 Azure Container Registry 設定][AR03]

6. 容器登錄建立完成後，請在 Azure 入口網站中前往該登錄。 然後選取 [存取金鑰]。 記下使用者名稱和密碼以供後續步驟使用。

   ![Azure Container Registry 存取金鑰][AR04]

## <a name="configure-maven-to-use-your-azure-container-registry-access-keys"></a>設定要使用 Azure Container Registry 存取金鑰的 Maven

1. 前往 Maven 安裝的設定目錄。 然後使用文字編輯器開啟 **settings.xml** 檔案。

2. 將本教學課程上一節的 Azure Container Registry 存取設定新增至 settings.xml 檔案中的 `<servers>` 集合；例如：

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

3. 前往 Spring Boot 應用程式已完成的專案目錄 (例如，**C:\SpringBoot\gs-spring-boot-docker\complete** 或 **/users/robert/SpringBoot/gs-spring-boot-docker/complete**)。 然後使用文字編輯器開啟 **pom.xml** 檔案。

1. 更新 **pom.xml** 檔案中的 `<properties>` 集合。 使用從本教學課程上一節取得的 Azure Container Registry 登入伺服器值；例如：

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. 更新 **pom.xml** 檔案中的 `<plugins>` 集合，讓 `<plugin>` 包含從本教學課程上一節取得的 Azure Container Registry 登入伺服器位址和登錄名稱。 例如：

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
         <serverId>wingtiptoysregistry</serverId>
         <registryUrl>https://wingtiptoysregistry.azurecr.io</registryUrl>
      </configuration>
   </plugin>
   ```

1. 前往 Spring Boot 應用程式的已完成專案目錄。 然後執行下列命令以重建應用程式，並將容器推送至 Azure 容器登錄：

   ```
   mvn package docker:build -DpushImage
   ```

> [!NOTE]
>
> 當您要將 Docker 容器推送至 Azure 時，您可能會收到類似下列其中一項的錯誤訊息，即使您已成功建立 Docker 容器也一樣：
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: no basic auth credentials`
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: Incomplete Docker registry authorization credentials. Please provide all of username, password, and email or none.`
>
> 如果發生這種情況，您可能需要從 Docker 命令列登入 Azure；例如：
>
> `docker login -u wingtiptoysregistry -p "AbCdEfGhIjKlMnOpQrStUvWxYz" wingtiptoysregistry.azurecr.io`
>
> 接著，您可以從命令列推送您的容器；例如：
>
> `docker push wingtiptoysregistry.azurecr.io/gs-spring-boot-docker`
>

## <a name="create-a-web-app-on-linux-on-azure-app-service-by-using-your-container-image"></a>使用您的容器映像在 Azure App Service 上建立 Linux 的 Web 應用程式

1. 登入 [Azure 入口網站]。

1. 依序按一下 [+ 新增] 的功能表圖示和 [Web + 行動]。

2.  按一下 [Linux 上的 Web 應用程式]。

   ![在 Azure 入口網站中建立新的 Web 應用程式][LX01]

3. 當 [Linux 上的 Web 應用程式] 刀鋒視窗顯示時，執行下列步驟：

   a. 為 [應用程式名稱] 輸入唯一名稱；例如：wingtiptoyslinux。

   b. 從下拉式清單選擇 [訂用帳戶]。

   c. 若要建立新的資源群組，請選擇現有 [資源群組]，或指定名稱。

   d. 按一下 [設定容器]，然後輸入下列資訊：

      * 選擇 [私人登錄]。

      * **映像和選擇性標記**：指定您先前的容器名稱，例如：wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest。

      * **伺服器 URL**：指定您先前的登錄 URL，例如：https://wingtiptoysregistry.azurecr.io。

      * **登入使用者名稱**和**密碼**：指定您在先前步驟使用之**存取金鑰**中的登入認證。

   e. 在輸入所有先前的資訊之後，選取 [確定]。

   ![設定 Web 應用程式設定][LX02]

1. 按一下 [建立] 。

> [!NOTE]
> Azure 會自動將網際網路要求對應到在標準連接埠 80 或 8080 上執行的內嵌 Tomcat 伺服器。 不過，如果您將內嵌 Tomcat 伺服器設定為在自訂連接埠上執行時，則必須將環境變數新增至您的 web 應用程式，其可定義內嵌 Tomcat 伺服器的連接埠。 若要這樣做，請執行下列步驟：
>
>1. 登入 [Azure 入口網站]。

>2. 選取 [應用程式服務] 的圖示。 (見下圖項目 #1)。

>3. 從清單中選取 Web 應用程式。 (見下圖項目 #2)。

>4. 按一下 [ **應用程式設定**]。 (見下圖項目 #3)。

>5. 在 [應用程式設定] 區段中，新增名為 **PORT** 的新環境變數。 然後輸入自訂連接埠號碼作為該變數的值。 (見下圖項目 #4)。

>6. 選取 [ **儲存**]。 (見下圖項目 #5)。

> ![在 Azure 入口網站中儲存自訂連接埠號碼][LX03]
>

## <a name="next-steps"></a>後續步驟

如需在 Azure 上使用 Spring Boot 應用程式的詳細資訊，請參閱下列文章：

- [將 Spring Boot 應用程式部署到 Azure App Service][2]


- [在 Azure Container Service 的 Kubernetes 叢集上執行 Spring Boot 應用程式](container-service-deploy-spring-boot-app-on-kubernetes.md)

## <a name="additional-resources"></a>其他資源

如需有關使用 Azure 搭配 Java 的詳細資訊，請參閱 [Azure Java 開發人員中心]和[適用於 Visual Studio Team Services 的 Java 工具]。

如需 Spring Boot on Docker 範例專案的詳細資訊，請參閱 [Spring Boot on Docker Getting Started]。

如需開始使用您自己的 Spring Boot 應用程式的說明，請參閱 [Spring Initializr](https://start.spring.io/)。

如需開始建立簡單 Spring Boot 應用程式的相關詳細資訊，請參閱 [Spring Initializr](https://start.spring.io/)。

如需會說明如何搭配 Azure 使用自訂 Docker 映像的其他範例，請參閱[針對 Linux 上的 Azure Web 應用程式使用自訂 Docker 映像]。

<!-- URL List -->

[Azure 命令列介面 (CLI)]: /cli/azure/overview
[Azure Container Service]: https://azure.microsoft.com/services/container-service/
[Azure Java 開發人員中心]: https://azure.microsoft.com/develop/java/
[Azure 入口網站]: https://portal.azure.com/
[使用 Azure 入口網站建立私人 Docker 容器登錄]: /azure/container-registry/container-registry-get-started-portal
[針對 Linux 上的 Azure Web 應用程式使用自訂 Docker 映像]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[免費的 Azure 帳戶]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java 開發工具組 (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[適用於 Visual Studio Team Services 的 Java 工具]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[MSDN 訂戶權益]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Boot on Docker Getting Started]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-on-linux/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-on-linux/SB02.png

[AR01]: ./media/container-service-deploy-spring-boot-app-on-linux/AR01.png
[AR02]: ./media/container-service-deploy-spring-boot-app-on-linux/AR02.png
[AR03]: ./media/container-service-deploy-spring-boot-app-on-linux/AR03.png
[AR04]: ./media/container-service-deploy-spring-boot-app-on-linux/AR04.png

[LX01]: ./media/container-service-deploy-spring-boot-app-on-linux/LX01.png
[LX02]: ./media/container-service-deploy-spring-boot-app-on-linux/LX02.png
[LX03]: ./media/container-service-deploy-spring-boot-app-on-linux/LX03.png

<!--Reference links in article-->
[1]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli/
[2]: https://docs.microsoft.com/azure/app-service/app-service-deploy-spring-boot-web-app-on-azure/


---

