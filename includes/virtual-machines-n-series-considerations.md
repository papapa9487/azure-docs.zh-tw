## <a name="deployment-considerations"></a>部署考量

* 如需了解 N 系列 VM 的可用性，請參閱[依區域提供的產品](https://azure.microsoft.com/en-us/regions/services/)。

* N 系列 VM 只能部署在 Resource Manager 部署模型。

* 使用 Azure 入口網站建立 N 系列 VM 時，請在 [基本概念] 刀鋒視窗中選取 [HDD] 做為 [VM 磁碟類型]。 若要選擇可用的 N 系列大小，請在 [大小] 刀鋒視窗中，按一下 [檢視全部]。

* N 系列 VM 不支援以 Azure 進階儲存體為後盾的 VM 磁碟。

* 如果您想要部署不只一些 N 系列 VM ，請考慮隨用隨付訂用帳戶或其他購買選項。 如果您使用 [Azure 免費帳戶](https://azure.microsoft.com/free/)，您只能使用有限數目的 Azure 計算核心。

* 您可能需要增加 Azure 訂用帳戶的核心配額 (依地區)，以及增加 NC 或 NV 核心的個別配額。 若要要求增加配額，可免費[開啟線上客戶支援要求](../articles/azure-supportability/how-to-create-azure-support-request.md)。 預設限制會視您的訂用帳戶類別而有所不同。

* 您可以在 N 系列 VM 上部署的一個 VM 映像是 [Azure 資料科學虛擬機器](../articles/machine-learning/data-science-virtual-machine/overview.md)。 資料科學虛擬機器會預先安裝和設定許多常用的資料科學和深入學習工具。 它也會針對 NC 執行個體預先安裝 NVIDIA Tesla GPU 驅動程式。





