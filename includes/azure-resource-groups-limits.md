| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| 每個[資源群組](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)的資源 (每個資源類型) |800 |每個資源類型而異 |
| 部署歷程記錄中每個資源群組的部署 |800 |800 |
| 每個部署的資源 |800 |800 |
| 管理鎖定 (每個唯一的範圍) |20 |20 |
| 標記數目 (每個資源或資源群組) |15 |15 |
| 標記金鑰長度 |512 |512 |
| 標記值長度 |256 |256 |


#### <a name="template-limits"></a>範本限制

| 值 | 預設限制 | 上限 |
| --- | --- | --- |
| 參數 |256 |256 |
| 變數 |256 |256 |
| 資源 (包括複本計數) |800 |800 |
| 輸出 |64 |64 |
| 範本運算式 |24,576 個字元 |24,576 個字元 |
| 已匯出範本中的資源 |200 |200 | 
| 範本大小 |1 MB |1 MB |
| 參數檔案大小 |64 KB |64 KB |

使用巢狀範本，即可超出一些範本限制。 如需詳細資訊，請參閱[在部署 Azure 資源時使用連結的範本](../articles/azure-resource-manager/resource-group-linked-templates.md)。 若要減少參數、變數或輸出數目，您可以將數個值合併成一個物件。 如需詳細資訊，請參閱[物件作為參數](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)。

如果每個資源群組的部署達到 800 個數量限制，請從歷程記錄中刪除不再需要的部署。 您可以使用 [az group deployment delete](/cli/azure/group/deployment#az_group_deployment_delete) (Azure CLI) 或 [Remove-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/remove-azurermresourcegroupdeployment) (PowerShell) 以從歷程記錄中刪除項目。 從部署歷程記錄中刪除項目時，並不會影響部署資源。 