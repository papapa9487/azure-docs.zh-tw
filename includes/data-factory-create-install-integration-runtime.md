## <a name="create-a-self-hosted-ir"></a>建立自我裝載 IR

在本節中，您可以建立自我裝載整合執行階段，並使用 SQL Server 資料庫將它與內部部署電腦產生關聯。 自我裝載的整合執行階段是一項元件，可將資料從您電腦上的 SQL Server 複製到 Azure blob 儲存體。 

1. 建立整合執行階段名稱的變數。 使用唯一的名稱，並記下此名稱。 您將在本教學課程稍後使用它。 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
1. 建立自我裝載整合執行階段。 

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   以下是範例輸出：

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. 執行下列命令，以取出建立的整合執行階段的狀態。 確認 **State** 屬性的值已設定為 **NeedRegistration**。 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   以下是範例輸出：

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. 執行下列命令來取出**驗證金鑰**，用以向雲端中的 Data Factory 服務註冊自我裝載整合執行階段。 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   以下是範例輸出：

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    
4. 複製其中一個金鑰 (排除雙引號括住)，以註冊您在下一個步驟中安裝於電腦上的自我裝載整合執行階段。  

## <a name="install-integration-runtime"></a>安裝整合執行階段
1. 如果您的機器上已經有 **Microsoft 整合執行階段**，請使用 [新增或移除程式] 將其解除安裝。 
2. 在本機 Windows 電腦上[下載](https://www.microsoft.com/download/details.aspx?id=39717)自我裝載的整合執行階段並執行安裝。 
3. 在 [歡迎使用 Microsoft Integration Runtime 設定精靈] 中，按 [下一步]。  
4. 在 [使用者授權合約] 頁面上，接受條款和授權合約，然後按 [下一步]。 
5. 在 [目的地資料夾] 頁面上，按 [下一步]。 
6. 在 [準備好要安裝 Microsoft Integration Runtime] 上按一下 [安裝]。 
7. 如果您看到一則有關正在設定不使用時要進入睡眠或休眠模式之電腦的警告訊息，請按一下 [確定]。 
8. 如果您看到**電源選項**視窗，請將它關閉，然後切換到安裝視窗。 
9. 在 [完成 Microsoft Integration Runtime 設定精靈] 頁面，按一下 [完成]。
10. 在 [註冊 Integration Runtime (自我裝載)] 頁面上，貼上您在上一節中儲存的金鑰，然後按一下 [註冊]。 

   ![監視整合執行階段](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)
2. 自我裝載整合執行階段註冊成功時，您會看到下列訊息：

   ![已成功註冊](media/data-factory-create-install-integration-runtime/registered-successfully.png)

3. 在 [新增 Integration Runtime (自我裝載) 節點] 頁面上，按 [下一步]。 

    ![新增 Integration Runtime 節點頁面](media/data-factory-create-install-integration-runtime/new-integration-runtime-node-page.png)
4. 在 [內部網路通訊通道] 上，按一下 [略過]。 您可以選取 TLS/SSL 憑證，以便保護在多節點整合執行階段環境中的內部節點通訊。 

    ![內部網路通訊通道頁面](media/data-factory-create-install-integration-runtime/intranet-communication-channel-page.png)
5. 在 [註冊 Integration Runtime (自我裝載)] 頁面上，按一下 [啟動組態管理員]。 
6. 當節點已連線至雲端服務時，您會看到下列頁面：

   ![節點已連線](media/data-factory-create-install-integration-runtime/node-is-connected.png)
7. 現在，測試您 SQL Server 資料庫的連線。

    ![[診斷] 索引標籤](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    - 在 [組態管理員] 視窗中，切換到 [診斷] 索引標籤。
    - 選取 [SqlServer] 作為 [資料來源類型]。
    - 輸入**伺服器**名稱。
    - 輸入**資料庫**名稱。 
    - 選取**驗證**方法。 
    - 輸入**使用者**名稱。 
    - 輸入使用者名稱的**密碼**。
    - 按一下 [測試] 以確認整合執行階段可以連線到 SQL Server。 如果連線成功，您會看到綠色的核取記號。 否則，您會看到與失敗相關的錯誤訊息。 修正所有問題，並確定整合執行階段可連線到您的 SQL Server。    

    > [!NOTE]
    > 請記下這些值 (驗證類型、伺服器、資料庫、使用者、密碼)。 您稍後會在本教學課程中用到。 
    
