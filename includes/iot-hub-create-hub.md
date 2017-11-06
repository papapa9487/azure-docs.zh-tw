1. 登入 [Azure 入口網站][lnk-portal]。
1. 選取 [新增] > [物聯網] > [IoT 中樞]。
   
    ![Azure 入口網站 Jumpbar][1]

1. 在 [IoT 中樞] 窗格中，輸入 IoT 中樞的下列資訊︰

   * **名稱**：建立 IoT 中樞的名稱。 如果您輸入的名稱有效，則會出現綠色核取記號。

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   * **定價與級別層**︰針對此教學課程，請選取 [F1 - 免費層]。 如需詳細資訊，請參閱[定價與級別層][lnk-pricing]。

   * **資源群組**：建立用以裝載 IoT 中樞的資源群組，或使用現有資源群組。 如需詳細資訊，請參閱[使用資源群組管理您的 Azure 資源][lnk-resource-groups]

   * **位置**：選取最接近您的位置。

   * **釘選至儀表板**︰核取此選項可讓您從儀表板輕鬆地存取 IoT 中樞。

    ![IoT 中樞視窗][2]

1. 按一下 [建立] 。 建立 IoT 中樞可能需要數分鐘。 您可以在 [通知] 窗格中監視進度。
<!-- Images -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
<!-- Links -->
[lnk-portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-resource-groups]: ../articles/azure-resource-manager/resource-group-portal.md