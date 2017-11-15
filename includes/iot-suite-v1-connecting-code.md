## <a name="specify-the-behavior-of-the-iot-device"></a>指定 IoT 裝置的行為

IoT 中樞序列化程式用戶端程式庫會使用模型來指定裝置與 IoT 中樞交換之訊息的格式。

1. 在 `#include` 陳述式之後新增下列變數宣告。 使用您在遠端監視解決方案儀表板中為裝置記下的值來取代 [Device ID] 和 [Device Key] 預留位置值。 使用解決方案儀表板中的「IoT 中樞主機名稱」來取代 [IoTHub Name]。 例如，若您的 IoT 中樞主機名稱是 **contoso.azure-devices.net**，請使用 **contoso** 取代 [IoTHub Name]：
   
    ```c
    static const char* deviceId = "[Device Id]";
    static const char* connectionString = "HostName=[IoTHub Name].azure-devices.net;DeviceId=[Device Id];SharedAccessKey=[Device Key]";
    ```

1. 新增下列程式碼以定義可讓裝置與 IoT 中樞通訊的模型。 此模型會指定裝置：

   - 可以傳送溫度、外部溫度、濕度及裝置識別碼作為遙測資料。
   - 可以將裝置相關中繼資料傳送給 IoT 中樞。 裝置會在啟動時傳送 **DeviceInfo** 物件中的基本中繼資料。
   - 可以將回報的屬性傳送給 IoT 中樞內的裝置對應項。 這些回報的屬性會依組態、裝置及系統屬性分組。
   - 可以接收 IoT 中樞內裝置對應項中設定的所需屬性，並根據這些屬性採取動作。
   - 可以回應透過解決方案入口網站叫用的 **Reboot** 和 **InitiateFirmwareUpdate** 直接方法。 裝置會使用回報的屬性來傳送它所支援之直接方法的相關資訊。
   
    ```c
    // Define the Model
    BEGIN_NAMESPACE(Contoso);

    /* Reported properties */
    DECLARE_STRUCT(SystemProperties,
      ascii_char_ptr, Manufacturer,
      ascii_char_ptr, FirmwareVersion,
      ascii_char_ptr, InstalledRAM,
      ascii_char_ptr, ModelNumber,
      ascii_char_ptr, Platform,
      ascii_char_ptr, Processor,
      ascii_char_ptr, SerialNumber
    );

    DECLARE_STRUCT(LocationProperties,
      double, Latitude,
      double, Longitude
    );

    DECLARE_STRUCT(ReportedDeviceProperties,
      ascii_char_ptr, DeviceState,
      LocationProperties, Location
    );

    DECLARE_MODEL(ConfigProperties,
      WITH_REPORTED_PROPERTY(double, TemperatureMeanValue),
      WITH_REPORTED_PROPERTY(uint8_t, TelemetryInterval)
    );

    /* Part of DeviceInfo */
    DECLARE_STRUCT(DeviceProperties,
      ascii_char_ptr, DeviceID,
      _Bool, HubEnabledState
    );

    DECLARE_DEVICETWIN_MODEL(Thermostat,
      /* Telemetry (temperature, external temperature and humidity) */
      WITH_DATA(double, Temperature),
      WITH_DATA(double, ExternalTemperature),
      WITH_DATA(double, Humidity),
      WITH_DATA(ascii_char_ptr, DeviceId),

      /* DeviceInfo */
      WITH_DATA(ascii_char_ptr, ObjectType),
      WITH_DATA(_Bool, IsSimulatedDevice),
      WITH_DATA(ascii_char_ptr, Version),
      WITH_DATA(DeviceProperties, DeviceProperties),

      /* Device twin properties */
      WITH_REPORTED_PROPERTY(ReportedDeviceProperties, Device),
      WITH_REPORTED_PROPERTY(ConfigProperties, Config),
      WITH_REPORTED_PROPERTY(SystemProperties, System),

      WITH_DESIRED_PROPERTY(double, TemperatureMeanValue, onDesiredTemperatureMeanValue),
      WITH_DESIRED_PROPERTY(uint8_t, TelemetryInterval, onDesiredTelemetryInterval),

      /* Direct methods implemented by the device */
      WITH_METHOD(Reboot),
      WITH_METHOD(InitiateFirmwareUpdate, ascii_char_ptr, FwPackageURI),

      /* Register direct methods with solution portal */
      WITH_REPORTED_PROPERTY(ascii_char_ptr_no_quotes, SupportedMethods)
    );

    END_NAMESPACE(Contoso);
    ```

## <a name="implement-the-behavior-of-the-device"></a>實作裝置的行為
現在，請新增程式碼來實作模型中所定義的行為。

1. 新增下列函式，這些函式會處理在解決方案儀表板中設定的所需屬性。 這些所需屬性是在模型中定義：

    ```c
    void onDesiredTemperatureMeanValue(void* argument)
    {
      /* By convention 'argument' is of the type of the MODEL */
      Thermostat* thermostat = argument;
      printf("Received a new desired_TemperatureMeanValue = %f\r\n", thermostat->TemperatureMeanValue);

    }

    void onDesiredTelemetryInterval(void* argument)
    {
      /* By convention 'argument' is of the type of the MODEL */
      Thermostat* thermostat = argument;
      printf("Received a new desired_TelemetryInterval = %d\r\n", thermostat->TelemetryInterval);
    }
    ```

1. 新增下列函式，這些函式會處理透過 IoT 中樞叫用的直接方法。 這些直接方法是在模型中定義：

    ```c
    /* Handlers for direct methods */
    METHODRETURN_HANDLE Reboot(Thermostat* thermostat)
    {
      (void)(thermostat);

      METHODRETURN_HANDLE result = MethodReturn_Create(201, "\"Rebooting\"");
      printf("Received reboot request\r\n");
      return result;
    }

    METHODRETURN_HANDLE InitiateFirmwareUpdate(Thermostat* thermostat, ascii_char_ptr FwPackageURI)
    {
      (void)(thermostat);

      METHODRETURN_HANDLE result = MethodReturn_Create(201, "\"Initiating Firmware Update\"");
      printf("Recieved firmware update request. Use package at: %s\r\n", FwPackageURI);
      return result;
    }
    ```

1. 新增下列函式，此函式會傳送訊息給預先設定的解決方案：
   
    ```c
    /* Send data to IoT Hub */
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, NULL, NULL) != IOTHUB_CLIENT_OK)
        {
          printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
          printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
      }
      free((void*)buffer);
    }
    ```

1. 新增下列回呼處理常式，此處理常式會在裝置將新回報的屬性值傳送給預先設定的解決方案後執行：

    ```c
    /* Callback after sending reported properties */
    void deviceTwinCallback(int status_code, void* userContextCallback)
    {
      (void)(userContextCallback);
      printf("IoTHub: reported properties delivered with status_code = %u\n", status_code);
    }
    ```

1. 新增下列函式，以將您的裝置連接到雲端中的預先設定解決方案，然後交換資料。 此函式會執行下列步驟：

    - 將平台初始化。
    - 使用序列化程式庫來註冊 Contoso 命名空間。
    - 使用裝置連接字串將用戶端初始化。
    - 建立「控溫器」模型的執行個體。
    - 建立並傳送回報的屬性值。
    - 傳送 **DeviceInfo** 物件。
    - 建立迴圈來每秒傳送遙測資料。
    - 將所有資源取消初始化。

    ```c
    void remote_monitoring_run(void)
    {
      if (platform_init() != 0)
      {
        printf("Failed to initialize the platform.\n");
      }
      else
      {
        if (SERIALIZER_REGISTER_NAMESPACE(Contoso) == NULL)
        {
          printf("Unable to SERIALIZER_REGISTER_NAMESPACE\n");
        }
        else
        {
          IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, MQTT_Protocol);
          if (iotHubClientHandle == NULL)
          {
            printf("Failure in IoTHubClient_CreateFromConnectionString\n");
          }
          else
          {
    #ifdef MBED_BUILD_TIMESTAMP
            // For mbed add the certificate information
            if (IoTHubClient_SetOption(iotHubClientHandle, "TrustedCerts", certificates) != IOTHUB_CLIENT_OK)
            {
                printf("Failed to set option \"TrustedCerts\"\n");
            }
    #endif // MBED_BUILD_TIMESTAMP
            Thermostat* thermostat = IoTHubDeviceTwin_CreateThermostat(iotHubClientHandle);
            if (thermostat == NULL)
            {
              printf("Failure in IoTHubDeviceTwin_CreateThermostat\n");
            }
            else
            {
              /* Set values for reported properties */
              thermostat->Config.TemperatureMeanValue = 55.5;
              thermostat->Config.TelemetryInterval = 3;
              thermostat->Device.DeviceState = "normal";
              thermostat->Device.Location.Latitude = 47.642877;
              thermostat->Device.Location.Longitude = -122.125497;
              thermostat->System.Manufacturer = "Contoso Inc.";
              thermostat->System.FirmwareVersion = "2.22";
              thermostat->System.InstalledRAM = "8 MB";
              thermostat->System.ModelNumber = "DB-14";
              thermostat->System.Platform = "Plat 9.75";
              thermostat->System.Processor = "i3-7";
              thermostat->System.SerialNumber = "SER21";
              /* Specify the signatures of the supported direct methods */
              thermostat->SupportedMethods = "{\"Reboot\": \"Reboot the device\", \"InitiateFirmwareUpdate--FwPackageURI-string\": \"Updates device Firmware. Use parameter FwPackageURI to specify the URI of the firmware file\"}";

              /* Send reported properties to IoT Hub */
              if (IoTHubDeviceTwin_SendReportedStateThermostat(thermostat, deviceTwinCallback, NULL) != IOTHUB_CLIENT_OK)
              {
                printf("Failed sending serialized reported state\n");
              }
              else
              {
                printf("Send DeviceInfo object to IoT Hub at startup\n");
    
                thermostat->ObjectType = "DeviceInfo";
                thermostat->IsSimulatedDevice = 0;
                thermostat->Version = "1.0";
                thermostat->DeviceProperties.HubEnabledState = 1;
                thermostat->DeviceProperties.DeviceID = (char*)deviceId;

                unsigned char* buffer;
                size_t bufferSize;

                if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties) != CODEFIRST_OK)
                {
                  (void)printf("Failed serializing DeviceInfo\n");
                }
                else
                {
                  sendMessage(iotHubClientHandle, buffer, bufferSize);
                }

                /* Send telemetry */
                thermostat->Temperature = 50;
                thermostat->ExternalTemperature = 55;
                thermostat->Humidity = 50;
                thermostat->DeviceId = (char*)deviceId;

                while (1)
                {
                  unsigned char*buffer;
                  size_t bufferSize;

                  (void)printf("Sending sensor value Temperature = %f, Humidity = %f\n", thermostat->Temperature, thermostat->Humidity);

                  if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != CODEFIRST_OK)
                  {
                    (void)printf("Failed sending sensor value\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize);
                  }

                  ThreadAPI_Sleep(1000);
                }

                IoTHubDeviceTwin_DestroyThermostat(thermostat);
              }
            }
            IoTHubClient_Destroy(iotHubClientHandle);
          }
          serializer_deinit();
        }
      }
      platform_deinit();
    }
    ```
   
    以下是一個傳送給預先設定之解決方案的範例「遙測」訊息，可供您參考：
   
    ```
    {"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
    ```