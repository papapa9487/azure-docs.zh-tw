---
title: "使用 Azure IoT 中樞直接方法 (Java) | Microsoft Docs"
description: "如何使用 Azure IoT 中樞直接方法。 您可以使用適用於 Java 的 Azure IoT 裝置 SDK，實作模擬的裝置應用程式 (包含直接方法)，也可以使用適用於 Java 的 Azure IoT 服務 SDK，實作服務應用程式 (叫用直接方法)。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 5fa42c4fe7ad04bc74f70b023715bb61f81806ab
ms.contentlocale: zh-tw
ms.lasthandoff: 08/09/2017

---
# <a name="use-direct-methods-java"></a>使用直接方法 (Java)

[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

在本教學課程中，您將建立兩個 Java 主控台應用程式：

* **invoke-direct-method**，這是 Java 後端應用程式，可在模擬裝置應用程式中呼叫方法，並顯示回應。
* **simulated-device**，這是 Java 應用程式，可使用您所建立的裝置身分識別，模擬連線到您 IoT 中樞的裝置。 此應用程式會回應來自後端的直接叫用。

> [!NOTE]
> 如需可用來建置應用程式，以在裝置與您的解決方案後端執行之 SDK 的資訊，請參閱 [Azure IoT SDK][lnk-hub-sdks]。

若要完成本教學課程，您需要：

* Java SE 8。 <br/> [準備您的開發環境][lnk-dev-setup]說明如何在 Windows 或 Linux 上安裝本教學課程的 Java。
* Maven 3。  <br/> [準備您的開發環境][lnk-dev-setup]說明如何在 Windows 或 Linux 上安裝本教學課程的 [Maven][lnk-maven]。
* [Node.js 版本 0.10.0 或更新版本](http://nodejs.org)。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>建立模擬裝置應用程式

在本節中，您會建立 Java 主控台應用程式，回應解決方案後端所呼叫的方法。

1. 建立名為 iot-java-direct-method 的空資料夾。

1. 在 iot-java-direct-method 資料夾的命令提示字元下，使用下列命令建立名為 **simulated-device** 的 Maven 專案。 下列命令是完整的單一命令：

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 在命令提示字元中，瀏覽到 simulated-device 資料夾。

1. 使用文字編輯器，在 simulated-device 資料夾中開啟 pom.xml 檔案，並對 [相依性]  節點新增下列相依性。 這個相依性可讓您在應用程式中使用 iot-device-client 套件與 IoT 中樞通訊：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > 您可以使用 [Maven 搜尋][lnk-maven-device-search]來檢查最新版的 **iot-device-client**。

1. 將下列 [建置] 節點新增至 [相依性] 節點之後。 此設定會指示 Maven 使用 Java 1.8 來建置應用程式：

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. 儲存並關閉 pom.xml 檔案。

1. 使用文字編輯器開啟 simulated-device\src\main\java\com\mycompany\app\App.java 檔案。

1. 在此檔案中新增下列 **import** 陳述式：

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. 將下列類別層級變數新增到 **App** 類別中。 以您的 IoT 中樞名稱取代 `{youriothubname}`，並以您在＜建立裝置身分識別＞一節中產生的裝置金鑰值取代 `{yourdevicekey}`：

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    此範例應用程式在具現化 **DeviceClient** 物件時使用 **protocol** 變數。 目前，若要使用直接方法，您必須使用 MQTT 通訊協定。

1. 若要將狀態碼傳回 IoT 中樞，請將下列巢狀類別新增到 **App** 類別中：

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. 若要處理解決方案後端的直接方法引動過程，請將下列巢狀類別新增到 **App** 類別中︰

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "writeLine" :
          {
            int status = METHOD_SUCCESS;
            System.out.println(new String((byte[])methodData));
            deviceMethodData = new DeviceMethodData(status, "Executed direct method " + methodName);
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

1. 若要建立 **DeviceClient** 及接聽直接方法引動過程，請將 **main** 方法新增至 **App** 類別︰

    ```java
    public static void main(String[] args)
      throws IOException, URISyntaxException
    {
      System.out.println("Starting device sample...");

      DeviceClient client = new DeviceClient(connString, protocol);

      try
      {
        client.open();
        client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
        System.out.println("Subscribed to direct methods. Waiting...");
      }
      catch (Exception e)
      {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
        client.close();
        System.out.println("Shutting down...");
      }

      System.out.println("Press any key to exit...");
      Scanner scanner = new Scanner(System.in);
      scanner.nextLine();
      scanner.close();
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. 儲存並關閉 simulated-device\src\main\java\com\mycompany\app\App.java 檔案

1. 建置 **simulated-device** 應用程式，並更正所有錯誤。 在命令提示字元中，瀏覽到 simulated-device 資料夾，並執行下列命令︰

    `mvn clean package -DskipTests`

## <a name="call-a-direct-method-on-a-device"></a>在裝置上呼叫直接方法

在本節中，您會建立 Java 主控台應用程式，以叫用直接方法，然後顯示回應。 此主控台應用程式會連線到您的 IoT 中樞來叫用直接方法。

1. 在 iot-java-direct-method 資料夾的命令提示字元下，使用下列命令建立名為 **invoke-direct-method** 的 Maven 專案。 下列命令是完整的單一命令：

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=invoke-direct-method -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 在命令提示字元中，瀏覽到 invoke-direct-method 資料夾。

1. 使用文字編輯器，在 invoke-direct-method 資料夾中將 pom.xml 檔案開啟，並對 [相依性] 節點新增下列相依性。 這個相依性可讓您在應用程式中使用 iot-service-client 套件與 IoT 中樞通訊：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > 您可以使用 [Maven 搜尋][lnk-maven-service-search]來檢查最新版的 **iot-service-client**。

1. 將下列 [建置] 節點新增至 [相依性] 節點之後。 此設定會指示 Maven 使用 Java 1.8 來建置應用程式：

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. 儲存並關閉 pom.xml 檔案。

1. 使用文字編輯器開啟 invoke-direct-method\src\main\java\com\mycompany\app\App.java 檔案。

1. 在此檔案中新增下列 **import** 陳述式：

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    ```

1. 將下列類別層級變數新增到 **App** 類別中。 以您在＜建立 IoT 中樞＞一節中所記下的 IoT 中樞連接字串取代 `{youriothubconnectionstring}`：

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String methodName = "writeLine";
    public static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    public static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    public static final String payload = "a line to be written";
    ```

1. 若要在模擬的裝置上叫用方法，請將下列程式碼新增至 **main** 方法：

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
        System.out.println("Invoke direct method");
        MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, payload);

        if(result == null)
        {
            throw new IOException("Direct method invoke returns null");
        }
        System.out.println("Status=" + result.getStatus());
        System.out.println("Payload=" + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }

    System.out.println("Shutting down sample...");
    ```

1. 儲存並關閉 invoke-direct-method\src\main\java\com\mycompany\app\App.java 檔案

1. 建置 **invoke-direct-method** 應用程式，並更正所有錯誤。 在命令提示字元中，巡覽至 invoke-direct-method 資料夾，並執行下列命令：

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>執行應用程式

您現在已經準備好執行主控台應用程式。

1. 在 simulated-device 資料夾的命令提示字元中，執行下列命令以開始接聽來自您 IoT 中樞的方法呼叫：

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![會接聽直接方法呼叫的 Java IoT 中樞模擬裝置應用程式][8]

1. 在 invoke-direct-method 資料夾的命令提示字元中，執行下列命令以呼叫您模擬裝置上來自 IoT 中樞的方法：

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![會呼叫直接方法的 Java IoT 中樞服務應用程式][7]

1. 會回應直接方法呼叫的模擬裝置︰

    ![Java IoT 中樞模擬裝置會回應直接方法呼叫][9]

## <a name="next-steps"></a>後續步驟

在此教學課程中，您在 Azure 入口網站中設定了新的 IoT 中樞，然後在 IoT 中樞的身分識別登錄中建立了裝置身分識別。 您會將此裝置識別用於啟用模擬的裝置應用程式，以將雲端所叫用的方法進行反應。 您也會建立應用程式，在裝置上叫用方法，並且顯示來自裝置的回應。

若要瀏覽其他的 IoT 案例，請參閱[排程多個裝置上的作業][lnk-devguide-jobs]。

若要了解如何擴充您的 IoT 解決方案以及在多個裝置上排程方法呼叫，請參閱[排程及廣播作業][lnk-tutorial-jobs]教學課程。

<!-- Images. -->
[7]: ./media/iot-hub-java-java-direct-methods/invoke-method.png
[8]: ./media/iot-hub-java-java-direct-methods/device-listen.png
[9]: ./media/iot-hub-java-java-direct-methods/device-respond.png

<!-- Links -->
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md
[lnk-maven]: https://maven.apache.org/what-is-maven.html
[lnk-hub-sdks]: iot-hub-devguide-sdks.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22

