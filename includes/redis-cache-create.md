若要建立快取，請先登入 [Azure 入口網站](https://portal.azure.com)，然後按一下新增 > [資料庫] > [Redis 快取]。

> [!NOTE]
> 如果您沒有 Azure 帳戶，只需要幾分鐘的時間就可以 [免費申請 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) 。
> 
> 

![New cache](media/redis-cache-create/redis-cache-new-cache-menu.png)

> [!NOTE]
> 除了在 Azure 入口網站中建立快取，您也可以使用 Resource Manager 範本、PowerShell 或 Azure CLI 來建立。
> 
> * 若要使用 Resource Manager 範本建立快取，請參閱 [使用範本建立 Redis 快取](../articles/redis-cache/cache-redis-cache-arm-provision.md)。
> * 若要使用 Azure PowerShell 建立快取，請參閱 [使用 Azure PowerShell 管理 Azure Redis 快取](../articles/redis-cache/cache-howto-manage-redis-cache-powershell.md)。
> * 若要使用 Azure CLI 建立快取，請參閱 [如何使用 Azure 命令列介面 (Azure CLI) 建立並管理 Azure Redis 快取](../articles/redis-cache/cache-manage-cli.md)。
> 
> 

在 [新增 Redis 快取]  分頁中，指定所需的快取組態。

![Create cache](media/redis-cache-create/redis-cache-cache-create.png) 

* 在 [DNS 名稱] 中，輸入要用於快取端點的唯一快取名稱。 快取名稱必須是介於 1 到 63 個字元的字串，而且只能包含數字、字母和 `-` 字元。 快取名稱的開頭或結尾不能是 `-` 字元，且連續的 `-` 字元無效。
* 針對 [訂用帳戶] ，選取您要用於快取的 Azure 訂用帳戶。 如果您的帳戶僅有一個訂用帳戶，則會自動加以選取，而且不會顯示 [訂用帳戶]  下拉式清單。
* 在 [資源群組] 中，選取或建立快取的資源群組。 如需詳細資訊，請參閱[使用資源群組管理您的 Azure 資源](../articles/azure-resource-manager/resource-group-overview.md)。 
* 使用 [位置]  來指定管理快取所在的地理位置。 為獲得最佳效能，Microsoft 強烈建議您在與快取用戶端應用程式相同的區域中建立快取。
* 使用 [價格層] 來選取需要的快取大小和功能。
* **Redis 叢集** 可讓您建立大於 53 GB 的快取，以及將資料分散於多個 Redis 節點。 如需詳細資訊，請參閱 [如何設定進階 Azure Redis 快取的叢集](../articles/redis-cache/cache-how-to-premium-clustering.md)。
* **Redis 持續性** 可讓您將您的快取保存至 Azure 儲存體帳戶。 如需設定永續性的相關指示，請參閱 [如何設定進階 Azure Redis Cache 的永續性](../articles/redis-cache/cache-how-to-premium-persistence.md)。
* **虛擬網路** 藉由將您的快取存取權限制於指定的 Azure 虛擬網路內的用戶端，以提供增強的安全性和隔離。 您可以使用 VNet 的所有功能，例如子網路、存取控制原則和其他功能，進一步限制對 Redis 的存取權。 如需詳細資訊，請參閱 [如何設定進階 Azure Redis 快取的虛擬網路支援](../articles/redis-cache/cache-how-to-premium-vnet.md)。
* 根據預設，新的快取會停用非 SSL 存取。 若要啟用非 SSL 連接埠，請核取 [解除封鎖連接埠 6379 (未以 SSL 加密)]。

設定了新的快取選項之後，請按一下 [建立新快取]。 建立快取可能需要數分鐘的時間。 若要檢查狀態，您可以監視開始面板上的進度。 建立了快取之後，新快取的狀態會是**執行中**，而且準備好與[預設設定](../articles/redis-cache/cache-configure.md#default-redis-server-configuration)搭配使用。

![Cache created](media/redis-cache-create/redis-cache-cache-created.png)

