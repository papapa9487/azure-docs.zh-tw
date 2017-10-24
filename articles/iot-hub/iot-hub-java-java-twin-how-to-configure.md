---
title: "使用 Azure IoT 中樞裝置對應項屬性 (Java) | Microsoft Docs"
description: "如何使用 Azure IoT 中樞裝置對應項來設定裝置。 您可以使用適用於 Java 的 Azure IoT 裝置 SDK 來實作模擬裝置應用程式，也可以使用適用於 Java 的 Azure IoT 服務 SDK 來實作服務應用程式，以使用裝置對應項來修改裝置設定。"
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: dobett
ms.openlocfilehash: 3881e3791c97baf2922722f01f9e6e6ea55ed2e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>使用所需屬性來設定裝置

[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

在本教學課程結尾處，您會有兩個 Java 主控台應用程式：

* **simulated-device** - 這是模擬裝置應用程式，會等候所需的設定更新，然後回報模擬設定更新程序的狀態。
* **set-configuration** - 這是後端應用程式，會在裝置上設定所需的設定，然後查詢設定更新程序。

> [!NOTE]
> [Azure IoT SDK][lnk-hub-sdks] 一文提供可用來建置裝置和後端應用程式之 Azure IoT SDK 的相關資訊。
> 
> 

若要完成此教學課程，您需要下列項目：

* 最新的 [Java SE 開發套件 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。)

如果您遵循[開始使用裝置對應項][lnk-twin-tutorial]教學課程進行，您便已經有一個 IoT 中樞和一個稱為 **myDeviceId** 的裝置身分識別。 在該情況下，您可以直接跳到[建立模擬裝置應用程式][lnk-how-to-configure-createapp]一節。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>建立模擬裝置應用程式
在本節中，您將建立 Java 主控台應用程式，此應用程式會以 **myDeviceId** 身分連接到您的中樞、等候所需的設定更新，然後回報模擬設定更新程序上的更新。

1. 建立名為 dt-get-started 的空資料夾。

1. 在 [dt-get-started] 資料夾中，於命令提示字元處使用下列命令，以建立名為 **simulated-device** 的 Maven 專案。 注意，這是一個單一且非常長的命令：

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 在命令提示字元中，瀏覽到 simulated-device 資料夾。

1. 使用文字編輯器，在 simulated-device 資料夾中開啟 pom.xml 檔案，並對 [相依性] 節點新增下列相依性。 這個相依性可讓您在應用程式中使用 iot-service-client 套件與 IoT 中樞通訊：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.30</version>
    </dependency>
    ```

    > [!NOTE]
    > 您可以使用 [Maven 搜尋][lnk-maven-device-search] 來檢查最新版的 **iot-device-client**。

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

1. 使用文字編輯器開啟 simulated-device\src\main\java\com\mycompany\app\App.java 來源檔案。

1. 在此檔案中新增下列 **import** 陳述式：

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. 將下列類別層級變數新增到 **App** 類別中。 將 **{yourdeviceconnectionstring}** 取代為您在＜建立裝置識別＞一節中所記下的裝置連接字串：

    ```java
    private static String deviceId = "myDeviceId";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    private static TelemetryConfig telemetryConfig;
    ```

1. 若要實作裝置對應項狀態事件的回呼處理常式 (用來進行偵錯)，請將下列巢狀類別新增至 **App** 類別：

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback 
    {
        public void execute(IotHubStatusCode status, Object context) 
        {
            //System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. 將下列巢狀類別新增到 **App** 類別中：

    ```java
    private static class TelemetryConfig extends Device 
    {
        private String configId = "0000";
        private String sendFrequency = "45m";

        private Boolean initialRun = true;
        
        private Property telemetryConfig = new Property("telemetryConfig", "{configId=" + configId + ", sendFrequency=" + sendFrequency + "}");

        public void InitTelemetry() throws IOException 
        {
            System.out.println("Report initial telemetry config:");
            System.out.println(this.telemetryConfig);

            this.setReportedProp(this.telemetryConfig);

            client.sendReportedProperties(this.getReportedProp());
        }

        private void UpdateReportedConfiguration() 
        {
            try {
                System.out.println("Initiating config change");
                    
                this.setReportedProp(this.telemetryConfig);
                client.sendReportedProperties(this.getReportedProp());
                    
                System.out.println("Simulating device reset");
                
                Thread.sleep(10000);
                
                System.out.println("Completing config change");
                System.out.println("Config change complete");
            } catch (Exception e) {
                System.out.println("Exception \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
            }
        }

        @Override
        public void PropertyCall(String propertyKey, Object propertyValue, Object context) 
        {
            if (propertyKey.equals("telemetryConfig")) {
                if (!(initialRun)) {
                    System.out.println("Desired property change:");
                    System.out.println(propertyKey + ":" + propertyValue);

                    telemetryConfig.setValue(propertyValue);

                    UpdateReportedConfiguration();
                } else {
                    initialRun = false;
                }
            } 
        }
    }
    ```

    > [!NOTE]
    > TelemetryConfig 類別會延伸 [Device 類別][lnk-java-device-class]，來取得所需屬性回呼的存取權。

1. 修改 **main** 方法的簽章以擲回下列例外狀況：

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. 將下列程式碼新增至 **main** 方法，以將 **DeviceClient** 和 **TelemetryConfig**具現化：

    ```java
    client = new DeviceClient(connString, protocol);

    telemetryConfig = new TelemetryConfig();
    ```

1. 將下列程式碼新增至 **main** 方法，以啟動裝置對應項服務：

    ```java
    try 
    {
        System.out.println("Connecting to hub");
        client.open();
        client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, telemetryConfig, null);

        telemetryConfig.InitTelemetry();

        System.out.println("Wait for desired telemetry...");
        client.subscribeToDesiredProperties(telemetryConfig.getDesiredProp());
    }
    catch (Exception e) 
    {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
        telemetryConfig.clean();
        client.close();
        System.out.println("Shutting down...");
    }
    ```

1. 將下列程式碼新增至 **main** 方法，以在必要時關閉裝置模擬器：

    ```java
    System.out.println("Press enter to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    telemetryConfig.clean();
    client.close();
    ```

1. 儲存並關閉 simulated-device\src\main\java\com\mycompany\app\App.java 檔案。

1. 建置 **simulated-device** 後端應用程式，並更正所有錯誤。 在命令提示字元中，瀏覽到 simulated-device 資料夾，並執行下列命令︰

    `mvn clean package -DskipTests`

   > [!NOTE]
   > 本教學課程不會模擬並行組態更新的任何行為。 某些組態更新程序可能在更新執行時能夠接受目標組態的變更，部分程序可能必須將變更排入佇列，還有部分程序可能會因錯誤條件而拒絕變更。 請務必考慮您的特定程序所需的行為，並在初始化組態變更之前新增適當的邏輯。
   > 
   > 

## <a name="create-the-service-app"></a>建立服務應用程式
在本節中，您將建立 Java 主控台應用程式，此應用程式會以新的遙測組態物件，更新與 **myDeviceId** 關聯之裝置對應項上的「所需屬性」。 然後，它會查詢儲存在 IoT 中樞的裝置對應項，並顯示裝置的所需和所報告組態之間的差異。

1. 在 [dt-get-started] 資料夾中，於命令提示字元處使用下列命令，以建立名為 **set-configuration** 的 Maven 專案。 注意，這是一個單一且非常長的命令：

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=set-configuration -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 在命令提示字元處，瀏覽至 [set-configuration] 資料夾。

1. 使用文字編輯器，在 trigger-reboot 資料夾中開啟 pom.xml 檔案，並對 [相依性] 節點新增下列相依性。 這個相依性可讓您在應用程式中使用 iot-service-client 套件與 IoT 中樞通訊：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > 您可以使用 [Maven 搜尋][lnk-maven-device-search] 來檢查最新版的 **iot-service-client**。

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

1. 使用文字編輯器來開啟 set-configuration\src\main\java\com\mycompany\app\App.java 來源檔案。

1. 在此檔案中新增下列 **import** 陳述式：

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

1. 將下列類別層級變數新增到 **App** 類別中。 將 **{youriothubconnectionstring}** 取代為您在＜建立 IoT 中樞＞一節中所記下的 IoT 中樞連接字串：

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";
    ```

1. 若要查詢及更新模擬裝置上的裝置對應項，請將下列程式碼新增至 **main** 方法：

    ```java
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);

    String sendFrequency= "12h";

    try {
        twinClient.getTwin(device);

        // make sure a differing value exists before calling the updateTwin() method, else a null device exception will be thrown
        // this allows the program to be run multiple times for demonstration purposes
        String desiredProperties = device.desiredPropertiesToString();
        if (desiredProperties.contains("sendFrequency=" + sendFrequency))
        {
            sendFrequency = "8h";
        }
            
        Set<Pair> tags = new HashSet<Pair>();
        tags.add(new Pair("telemetryConfig", "{configId=0001, sendFrequency=" + sendFrequency + "}"));

        twinClient.getTwin(device);
        device.setDesiredProperties(tags);

        System.out.println("Config report for: " + deviceId);   
        System.out.println(device);

        twinClient.updateTwin(device);

        String reportedProperties = device.reportedPropertiesToString();
        Boolean waitFlag = true;

        while (waitFlag) {
            if (!reportedProperties.contains("sendFrequency=" + sendFrequency)) {
                Thread.sleep(10000);
            }
            else 
            {
                waitFlag = false;
            }

            twinClient.getTwin(device);
            reportedProperties = device.reportedPropertiesToString();
        }
            
        SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "properties.reported.telemetryConfig='{configId=0001, sendFrequency=" + sendFrequency + "}'", null);

        Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
        while (twinClient.hasNextDeviceTwin(twinQuery)) {
            DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
            System.out.println(d.getDeviceId() + " found with changed telemetryConfig");
        }

        System.out.println("Config report for: " + deviceId);
        twinClient.getTwin(device);
        System.out.println(device);

        } catch (IotHubException e) {
            System.out.println(e.getMessage());
        } catch (IOException e) {
            System.out.println(e.getMessage());
        } catch (InterruptedException e) {
            System.out.println(e.getMessage());
        }
    ```

1. 儲存並關閉 set-desired-configuration\src\main\java\com\mycompany\app\App.java 檔案。

1. 建置 **set-configuration** 後端應用程式，並更正所有錯誤。 在命令提示字元處，瀏覽至 [set-configuration] 資料夾，然後執行下列命令︰

    `mvn clean package -DskipTests`
   
    此程式碼會擷取 **myDeviceId** 的裝置對應項，然後以新的遙測組態物件來更新其所需屬性。
    之後，它每隔 10 秒就會查詢儲存在 IoT 中樞的裝置對應項，並列印所需和所報告的遙測組態。 請參閱 [IoT 中樞查詢語言][lnk-query]，以了解如何產生跨所有裝置的實用報告。
   
   > [!IMPORTANT]
   > 此應用程式會每隔 10 秒查詢一次「IoT 中樞」來提供說明，直到已將裝置更新為止。 跨許多裝置時，請使用查詢來產生適合使用者的報告，而非偵測變更。 如果您的解決方案需要裝置事件的即時通知，請使用[對應項通知][lnk-twin-notifications]。
   > 

   > [!IMPORTANT]
   > 裝置報告作業和查詢結果之間的延遲最多一分鐘。 這是為了讓查詢基礎結構能夠以非常高的延展性運作。  若要擷取單一裝置對應項的一致檢視，請使用 **DeviceTwin** 類別中的 **getDeviceTwin** 方法。
   > 
   > 

## <a name="run-the-apps"></a>執行應用程式

您現在可以開始執行應用程式。

1. 在 [simulated-device] 資料夾中的命令提示字元處執行下列命令，以開始接聽來自您 IoT 中樞的裝置對應項呼叫：

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. 在 [set-configuration] 資料夾中的命令提示字元處執行下列命令，以從您的 IoT 中樞查詢和更新模擬裝置上的裝置對應項：

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. 模擬裝置會回應 reboot 直接方法呼叫：

    ![Java IoT 中樞模擬裝置應用程式回應裝置對應項呼叫][img-deviceconfigured]

## <a name="next-steps"></a>後續步驟
在本教學課程中，您將會從解決方案後端將所需的組態設為「所需屬性」，還會撰寫裝置應用程式來偵測該變更並模擬多步驟更新程序，以透過報告屬性來回報其狀態。

使用下列資源來了解如何：

* 從裝置傳送遙測，請參閱[開始使用 IoT 中樞][lnk-iothub-getstarted]教學課程，
* 在大量裝置上排定或執行作業，請參閱[排定及廣播作業][lnk-schedule-jobs]教學課程。
* 以互動方式控制裝置 (例如，從使用者控制的應用程式開啟風扇)，請參閱[使用直接方法][lnk-methods-tutorial]教學課程。

<!-- images -->
[img-deviceconfigured]: media/iot-hub-java-java-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-java-device-class]: https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.sdk.iot.device._device_twin._device

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app
