---
title: "在 Azure Data Lake Store 中使用 Java SDK 開發應用程式 | Microsoft Docs"
description: "使用 Azure Data Lake Store Java SDK 建立 Data Lake Store 帳戶，並在 Data Lake Store 中執行基本作業"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 91128b53a2f1cd3ddcbee5b07da0d67668944fb4
ms.contentlocale: zh-tw
ms.lasthandoff: 06/07/2017

---
# <a name="get-started-with-azure-data-lake-store-using-java"></a>使用 Java 開始使用 Azure Data Lake Store
> [!div class="op_single_selector"]
> * [入口網站](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

了解如何使用 Azure Data Lake Store Java SDK 來執行基本作業，例如建立資料夾、上傳和下載資料檔案等等。如需有關 Data Lake 的詳細資訊，請參閱 [Azure Data Lake Store](data-lake-store-overview.md)。

您可以在 [Azure Data Lake Store Java API 文件](https://azure.github.io/azure-data-lake-store-java/javadoc/)存取適用於 Azure Data Lake Store 的 Java SDK API 文件。

## <a name="prerequisites"></a>必要條件
* Java Development Kit (JDK 7 或更新版本，使用 Java 1.7 版或更新版本)
* Azure Data Lake Store 帳戶。 遵循 [使用 Azure 入口網站開始使用 Azure 資料湖存放區](data-lake-store-get-started-portal.md)的指示。
* [Maven](https://maven.apache.org/install.html). 本教學課程使用 Maven 來處理組建和專案相依性。 雖有可能不使用 Maven 或 Gradle 等組建系統進行建置，但這些系統讓相依性管理變得輕鬆許多。
* (選擇性) [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) 或 [Eclipse](https://www.eclipse.org/downloads/) 之類的 IDE。

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>如何使用 Azure Active Directory 驗證？
在本教學課程中，我們使用 Azure AD 應用程式用戶端密碼來擷取 Azure Active Directory 權杖 (服務間驗證)。 我們使用此權杖來建立 Data Lake Store 用戶端物件，以執行檔案作業和目錄作業。 如需有關如何使用用戶端密碼向 Azure Data Lake Store 進行驗證的指示，我們可以執行下列高階步驟︰

1. 建立 Azure AD Web 應用程式
2. 擷取 Azure AD Web 應用程式的用戶端識別碼、用戶端密碼和權杖端點。
3. 針對您想要從您所建立之 Java 應用程式存取的 Data Lake Store 檔案/資料夾上的 Azure AD Web 應用程式，設定存取權。

如需有關如何執行這些步驟的指示，請參閱[建立 Active Directory 應用程式](data-lake-store-authenticate-using-active-directory.md)。

Azure Active Directory 還提供其他選項來擷取權杖。 您可以從數個不同的驗證機制中挑選符合您案例的驗證機制，例如，在瀏覽器中執行的應用程式、以桌面應用程式形式散發的應用程式，或在內部部署或 Azure 虛擬機器中執行的伺服器應用程式。 您也可以從不同類型的認證進行挑選，例如密碼、憑證、雙因子驗證等。此外，Azure Active Directory 可讓您同步處理您的內部部署 Active Directory 使用者與雲端。 如需詳細資訊，請參閱 [Azure Active Directory 的驗證案例](../active-directory/active-directory-authentication-scenarios.md)。 

## <a name="create-a-java-application"></a>建立 Java 應用程式
[GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) 上可用的程式碼範例會逐步引導您完成在存放區中建立檔案，串連檔案、下載檔案，以及刪除存放區中一些檔案的程序。 本文的這一節會逐步解說程式碼的主要部分。

1. 從命令列或透過 IDE，使用 [mvn 原型](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html)建立 Maven 專案。 如需有關如何使用 IntelliJ 建立 Java 專案的指示，請參閱[這裡](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html)。 如需有關如何使用 Eclipse 建立專案的指示，請參閱[這裡](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm)。 
2. 將下列相依性新增至 Maven **pom.xml** 檔案。 將下列程式碼片段文字新增至 **\</version>** 標籤與 **\</project>** 標籤之間︰
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.5</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    第一個相依性是使用來自 maven 儲存機制的 Data Lake Store SDK (`azure-data-lake-store-sdk`)。 第二個相依性 (`slf4j-nop`) 是指定要用於此應用程式的紀錄架構。 Data Lake Store SDK 會使用 [slf4j](http://www.slf4j.org/) 記錄外觀，讓您從數個熱門的記錄架構中進行選擇，例如 log4j、Java 記錄、logback 等或不記錄。 此範例中，我們將停用記錄，因此會使用 **slf4j-nop** 繫結。 若要在應用程式中使用其他記錄選項，請參閱[這裡](http://www.slf4j.org/manual.html#projectDep)。

### <a name="add-the-application-code"></a>新增應用程式程式碼
程式碼有三個主要部分。

1. 取得 Azure Active Directory 權杖
2. 使用權杖來建立 Data Lake Store 用戶端。
3. 使用 Data Lake Store 用戶端來執行作業。

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>步驟 1︰取得 Azure Active Directory 權杖。
Data Lake Store SDK 提供簡便的方法，讓您管理與 Data Lake Store 帳戶互動所需的安全性權杖。 不過，SDK 不會要求只能使用這些方法。 您也可以使用任何其他方法來取得權杖，像是使用 [Azure Active Directory SDK](https://github.com/AzureAD/azure-activedirectory-library-for-java)，或您自己的自訂程式碼。

若要使用 Data Lake Store SDK 來為您稍早建立的 Active Directory Web 應用程式取得權杖，請使用 `AccessTokenProvider` 的其中一個子類別 (以下範例使用 `ClientCredsTokenProvider`)。 權杖提供者會快取認證，以便用來取得記憶體中的權杖，以及自動更新即將過期的權杖。 您可建立自己的 `AccessTokenProvider` 子類別，以便依照您的客戶代碼取得權杖，但我們現在只會使用 SDK 中提供的權杖。

以 Azure Active Directory Web 應用程式的實際值取代 **FILL-IN-HERE**。

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>步驟 2︰建立 Azure Data Lake Store 用戶端 (ADLStoreClient) 物件
建立 [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) 物件時，您需要指定 Data Lake Store 帳戶名稱以及您在上一個步驟中產生的權杖提供者。 請注意，Data Lake Store 帳戶名稱必須是完整的網域名稱。 例如，以 **mydatalakestore.azuredatalakestore.net** 之類的資料取代 **FILL-IN-HERE**。

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>步驟 3︰使用 ADLStoreClient 來執行檔案和目錄作業
下面的程式碼包含一些常見作業的範例程式碼片段。 您可以查看 **ADLStoreClient** 物件的完整 [Data Lake Store Java SDK API 文件](https://azure.github.io/azure-data-lake-store-java/javadoc/)，以了解其他作業。

請注意，檔案是使用標準 Java 串流進行讀取和寫入。 這表示將任何 Java 串流放在 Data Lake Store 串流的上方，即可受惠於標準 Java 功能 (例如，格式化輸出的列印串流，或上方其他功能的任何壓縮或加密串流等)。

     // create file and write some content
     String filename = "/a/b/c.txt";
     OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
     PrintStream out = new PrintStream(stream);
     for (int i = 1; i <= 10; i++) {
         out.println("This is line #" + i);
         out.format("This is the same line (%d), but using formatted output. %n", i);
     }
     out.close();
    
    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>步驟 4：建置並執行應用程式
1. 若要從 IDE 執行，請找到並按下 [執行] 按鈕。 若要從 Maven 執行，請使用 [exec: exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html)。
2. 若要產生您可以從命令列執行的獨立 jar，請使用 [Maven 組件外掛程式](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html)建置內含所有相依性的 jar。 [GitHub 上的範例原始程式碼](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml)中的 pom.xml 有這項操作的範例。

## <a name="next-steps"></a>後續步驟
* [瀏覽 Java SDK 的 JavaDoc](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [保護 Data Lake Store 中的資料](data-lake-store-secure-data.md)
* [搭配 Data Lake Store 使用 Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [搭配 Data Lake Store 使用 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)


