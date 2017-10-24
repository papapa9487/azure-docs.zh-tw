---
title: "使用 Azure IoT 中樞進行裝置韌體更新 (Java/Java) | Microsoft Docs"
description: "如何使用 Azure IoT 中樞上的裝置管理來起始裝置韌體更新。 您可以使用適用於 Java 的 Azure IoT 裝置 SDK，實作模擬裝置應用程式和服務應用程式，以觸發韌體更新。"
services: iot-hub
documentationcenter: java
author: msebolt
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: v-masebo
ms.openlocfilehash: 024c2e9bf580f97b412a85913ca29d757872556a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/11/2017
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-javajava"></a>使用裝置管理來起始裝置韌體更新 (Java/Java)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

在[開始使用裝置管理][lnk-dm-getstarted]教學課程中，您已了解如何使用[裝置對應項][lnk-devtwin]和[直接方案][lnk-c2dmethod]基礎，從遠端重新啟動裝置。 本教學課程使用相同的 IoT 中樞基元，並示範如何進行端對端模擬韌體更新。  此模式用於 [Raspberry Pi 裝置實作範例][lnk-rpi-implementation]的韌體更新實作。

本教學課程說明如何：

* 建立 Java 主控台應用程式，以透過您的 IoT 中樞在模擬裝置應用程式中呼叫 **firmwareUpdate** 直接方法。
* 建立 Java 主控台應用程式，以模擬裝置及實作 **firmwareUpdate** 直接方法。 此方法會起始多階段程序，此程序會等候下載韌映像、下載韌體映像，最後再套用韌體映像。 在更新的每個階段，裝置都會使用回報的屬性來回報進度。

在本教學課程結尾處，您會有兩個 Java 主控台應用程式：

**firmware-update**，呼叫模擬裝置上的直接方法、顯示回應，並定期顯示報告的屬性更新

**simulated-device**，使用先前建立的裝置身分識別連線到您的 IoT 中樞、接收 firmwareUpdate 直接方法呼叫，並透過韌體更新模擬執行

若要完成此教學課程，您需要下列項目：

* 最新的 [Java SE 開發套件 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* 使用中的 Azure 帳戶。 (如果您沒有帳戶，只需要幾分鐘的時間就可以建立[免費帳戶][lnk-free-trial]。)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>使用直接方法在裝置上觸發遠端韌體更新
在此節中，您會建立 Java 主控台應用程式，此應用程式會在裝置上起始遠端韌體更新。 此應用程式使用直接方法來起始更新，並使用裝置對應項查詢來定期取得作用中韌體更新的狀態。

1. 建立名為 fw-get-started 的空資料夾。

1. 在 fw-get-started 資料夾的命令提示字元下，使用下列命令建立名為 **firmware-update** 的 Maven 專案。 注意，這是一個單一且非常長的命令：

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=firmware-update -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 在命令提示字元中，巡覽至 firmware-update 資料夾。

1. 使用文字編輯器，在 firmware-update 資料夾中開啟 pom.xml 檔案，並對 [相依性] 節點新增下列相依性。 這個相依性可讓您在應用程式中使用 iot-service-client 套件與 IoT 中樞通訊：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > 您可以使用 [Maven 搜尋](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)來檢查最新版的 **iot-service-client**。

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

1. 使用文字編輯器開啟 firmware-update\src\main\java\com\mycompany\app\App.java 來源檔案。

1. 在此檔案中新增下列 **import** 陳述式：

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    ```

1. 將下列類別層級變數新增到 **App** 類別中。 將 **{youriothubconnectionstring}** 取代為您在＜建立 IoT 中樞＞一節中所記下的 IoT 中樞連接字串：

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "firmwareUpdate";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. 若要實作方法以從裝置對應項讀取回報屬性，請將下列項目新增至 **App** 類別：

    ```java
    public static void ShowReportedProperties() 
    {
        try 
        {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          
          Boolean firmwareUpdated = false;
          Integer timeoutCycle = 0;
    
          while (!firmwareUpdated)
          {
            if (timeoutCycle > 5)
            {
              System.out.println("Operation timed out");
              break;
            }
    
            Thread.sleep(1000);
              
            deviceTwins.getTwin(twinDevice);
    
            String reportedProperties = twinDevice.reportedPropertiesToString();
              
            if(reportedProperties.contains("status=waiting"))
            {
              System.out.println("Waiting on device...");
            }
            else if(reportedProperties.contains("status=downloadComplete"))
            {
              System.out.println("Download complete, applying firmware...");
            }
            else if (reportedProperties.contains("status=applyComplete"))
            {
              System.out.println("Firmware applied");
              System.out.println("Get reported properties from device twin");
              System.out.println(twinDevice.reportedPropertiesToString());
              firmwareUpdated = true;
            }
            else
            {
              timeoutCycle++;
            }
          }
        } catch (Exception ex) {
            System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
    }
    ```

1. 修改 **main** 方法的簽章以擲回下列例外狀況：

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. 若要在模擬的裝置上叫用 firmwareUpdate 直接方法，請將下列程式碼新增至 **main** 方法：

    ```java
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      String payload = "https://someurl";
      
      System.out.println("Invoked firmware update on device.");
      System.out.println("Sent URL: " + payload);
      
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, payload);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }

      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
      System.out.println(e.getMessage());
    }
    ```

1. 若要從模擬的裝置輪詢回報屬性，請將下列程式碼新增至 **main** 方法：

    ```java
    ShowReportedProperties();
    ```

1. 若要讓您停止應用程式，請將下列程式碼新增至 **main** 方法：

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    System.out.println("Shutting down sample...");
    ```

1. 儲存並關閉 firmware-update\src\main\java\com\mycompany\app\App.java 檔案。

1. 建置 **firmware-update** 後端應用程式，並更正所有錯誤。 在命令提示字元中，巡覽至 firmware-update 資料夾，並執行下列命令：

    `mvn clean package -DskipTests`

## <a name="simulate-a-device-to-handle-direct-method-calls"></a>模擬裝置來處理直接方法呼叫
在本節中，您要建立 Java 主控台模擬裝置應用程式，才可接收 firmwareUpdate 直接方法。 應用程式接著會執行多重狀態程序，以使用 reportedProperties 模擬韌體更新來通訊狀態。

1. 在 fw-get-started 資料夾的命令提示字元下，使用下列命令建立名為 **simulated-device** 的 Maven 專案。 注意，這是一個單一且非常長的命令：

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. 在命令提示字元中，瀏覽到 simulated-device 資料夾。

1. 使用文字編輯器，在 firmware-update 資料夾中開啟 pom.xml 檔案，並對 [相依性] 節點新增下列相依性。 這個相依性可讓您在應用程式中使用 iot-service-client 套件與 IoT 中樞通訊：

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > 您可以使用 [Maven 搜尋](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)來檢查最新版的 **iot-device-client**。

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
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    import java.util.HashMap;
    ```

1. 將下列類別層級變數新增到 **App** 類別中。 將 **{yourdeviceconnectionstring}** 取代為您在＜建立裝置身分識別＞一節中所記下的裝置連接字串：

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring";
    private static DeviceClient client;

    private static String downloadURL = "unknown";
    ```

1. 若要實作直接方法的功能，請將下列巢狀類別新增至 **App** 類別來提供回呼：

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "firmwareUpdate" :
          {
            System.out.println("Response to method '" + methodName + "' sent successfully");
    
            downloadURL = new String((byte[])methodData);
    
            int status = METHOD_SUCCESS;
            System.out.println("Starting firmware update");
            deviceMethodData = new DeviceMethodData(status, "Started firmware update");
            FirmwareUpdateThread firmwareUpdateThread = new FirmwareUpdateThread();
            Thread t = new Thread(firmwareUpdateThread);
            t.start();
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

1. 若要實作裝置對應項的功能，請將下列巢狀類別新增至 **App** 類別來提供回呼：

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

1. 若要實作韌體更新，請將下列巢狀類別新增至 **App** 類別：

    ```java
    protected static class FirmwareUpdateThread implements Runnable {
      public void run() {
        try {      
          HashMap initialUpdate = new HashMap();
          Property sentProperty = new Property("firmwareUpdate", initialUpdate);
          Set<Property> sentPackage = new HashSet<Property>();
          
          System.out.println("Downloading from " + downloadURL);
    
          initialUpdate.put("status", "waiting");
          initialUpdate.put("fwPackageUri", downloadURL);
          initialUpdate.put("startedWaitingTime", LocalDateTime.now().toString());   
          sentPackage.add(sentProperty);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Download complete");
    
          HashMap downloadUpdate = new HashMap();
          downloadUpdate.put("status","downloadComplete");
          downloadUpdate.put("downloadCompleteTime", LocalDateTime.now().toString());
          downloadUpdate.put("startedApplyingImage", LocalDateTime.now().toString());
          sentProperty.setValue(downloadUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Apply complete");
    
          HashMap applyUpdate = new HashMap();
          applyUpdate.put("status","applyComplete");
          applyUpdate.put("lastFirmwareUpdate", LocalDateTime.now().toString());
          sentProperty.setValue(applyUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          HashMap resetUpdate = new HashMap();
          applyUpdate.put("status","reset");
          sentProperty.setValue(resetUpdate);
    
          client.sendReportedProperties(sentPackage);
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

1. 修改 **main** 方法的簽章以擲回下列例外狀況：

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. 若要起始直接方法和裝置對應項常式，請將下列程式碼新增至 **main** 方法：

    ```java
    client = new DeviceClient(connString, protocol);

    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. 若要讓您停止應用程式，請將下列程式碼新增至 **main** 方法的結尾：

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. 儲存並關閉 simulated-device\src\main\java\com\mycompany\app\App.java 檔案。

1. 建置 **simulated-device** 應用程式，並更正所有錯誤。 在命令提示字元中，瀏覽到 simulated-device 資料夾，並執行下列命令︰

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>執行應用程式
您現在可以開始執行應用程式。

1. 在 **simulated-device** 資料夾的命令提示字元中，執行下列命令以開始接聽韌體更新直接方法。
   
    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. 在 **firmware-update** 資料夾中的命令提示字元處執行下列命令，以從您的 IoT 中樞叫用韌體更新，並查詢模擬裝置上的裝置對應項：

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

3. 您可以在主控台中看到直接方法的模擬裝置回應。

    ![已順利更新韌體][img-fwupdate]

## <a name="next-steps"></a>後續步驟
在此教學課程中，您使用直接方法來觸發裝置上的遠端韌體更新，並且使用回報的屬性追蹤韌體更新的進度。

若要了解如何擴充您的 IoT 解決方案以及在多個裝置上排程方法呼叫，請參閱[排程及廣播作業][lnk-tutorial-jobs]教學課程。

<!-- images -->
[img-fwupdate]: media/iot-hub-java-java-firmware-update/firmwareUpdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-java-java-device-management-getstarted.md
[lnk-tutorial-jobs]: iot-hub-java-java-schedule-jobs.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device