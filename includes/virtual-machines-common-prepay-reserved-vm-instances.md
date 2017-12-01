# <a name="prepay-for-virtual-machines-with-reserved-vm-instances"></a>預付具有保留的 VM 執行個體的虛擬機器

預付虛擬機器，並使用保留的虛擬機器執行個體節省成本。 如需詳細資訊，請參閱[保留的虛擬機器執行個體供應項目](https://azure.microsoft.com/pricing/reserved-vm-instances/)。

您可以在 [Azure 入口網站](https://portal.azure.com)中購買保留的虛擬機器執行個體。 購買保留的虛擬機器執行個體：
-   您必須至少為一個企業或預付型方案訂用帳戶的「擁有者」角色。
-   針對企業訂用帳戶，必須在 [EA 入口網站](https://ea.azure.com)中啟用保留項目購買。

## <a name="buy-a-reserved-virtual-machine-instance"></a>購買保留的虛擬機器執行個體
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [更多服務] > [保留項目]。
3. 選取 [新增] 以購買新的保留項目。
4. 填寫必要欄位。 符合您選取之屬性的執行中 VM 執行個體符合取得保留項目折扣的資格。 取得折扣的 VM 執行個體實際數目取決於選取的範圍和數量。

    | 欄位      | 描述|
    |:------------|:--------------|
    |名稱        |此保留項目的名稱。| 
    |訂用帳戶|用來支付保留項目的訂用帳戶。 保留項目的預付費用會透過訂用帳戶的付款方式收取。 訂用帳戶類型必須是 Enterprise 合約 (供應項目號碼：MS-AZR-0017P) 或預付型方案 (供應項目號碼：MS-AZR-0003P)。 針對企業訂用帳戶，費用會從註冊的承諾用量金額餘額扣除或作為超額部分收費。 針對預付型方案訂用帳戶，費用是透過訂用帳戶的信用卡或發票付款方式收取。|    
    |範圍       |保留項目範圍可以涵蓋一個訂用帳戶或多個訂用帳戶 (共用範圍)。 如果您選取： <ul><li>單一訂用帳戶 - 保留項目折扣會套用至此訂用帳戶中的 VM。 </li><li>共用 - 保留項目折扣會套用至計費內容內任何訂用帳戶中執行的 VM。 針對企業客戶，共用範圍是註冊，並包含註冊中的所有訂用帳戶 (開發/測試訂用帳戶除外)。 針對預付型方案客戶，共用範圍是帳戶系統管理員所建立的所有預付型方案訂用帳戶。</li></ul>|
    |位置    |保留項目所涵蓋的 Azure 區域。|    
    |VM 大小     |VM 執行個體的大小。|
    |期間        |一年或三年。|
    |數量    |保留項目內所購買的執行個體數目。 數量是可以取得帳單折扣之執行中 VM 執行個體的數目。 例如，如果您在美國東部執行 10 個 Standard_D2 VM，那麼您會指定數量為 10，以最大化所有執行中機器的效益。 |
5. 當您選取 [計算成本] 時，可以檢視保留項目的成本。

    ![提交保留項目購買之前的螢幕擷取畫面](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. 選取 [購買]。
7. 選取 [檢視此保留] 以查看您的購買狀態。

    ![提交保留項目購買之前的螢幕擷取畫面](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps-after-buying-a-reservation"></a>購買保留項目之後的後續步驟
保留項目折扣會自動套用到符合保留項目範圍和屬性之執行中虛擬機器的數目。 您可以透過 [Azure 入口網站](https://portal.azure.com)、PowerShell、CLI 或 API 來更新保留項目的範圍。 

若要了解如何管理保留項目，請參閱[管理 Azure 保留的虛擬機器執行個體](https://go.microsoft.com/fwlink/?linkid=861613)。

