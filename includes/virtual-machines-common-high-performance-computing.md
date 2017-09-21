組織有大規模的運算需求。 這些 Big Compute 工作負載包括工程設計和分析、財務風險計算、影像轉譯、複雜模型、Monte Carlo 模擬等等。 

有了 Azure，您就可以使用您選擇的 Azure 基礎結構服務或受控服務，在 Azure 虛擬機器的叢集上執行高效能運算 (HPC) 和批次工作負載。 Azure 可讓您將運算資源彈性地調整為數千個 VM 或核心，然後在您不需要這麼多資源時加以相應減少。 

Azure HPC 解決方案能有效率地執行需要大量運算的 Linux 和 Windows 工作負載，從平行批次作業到傳統 HPC 模擬。 支援在 Azure VM 中執行前置 HPC 應用程式，您可以利用特製化的 VM 大小以及專為需要大量運算作業所設計的映像。 


## <a name="solution-architectures"></a>解決方案架構

Azure 中需要大量運算的解決方案架構之範例包括：

* 使用 Linux 或 Windows 叢集或您選擇的格線管理員，在虛擬機器或[虛擬機器擴展集](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)中執行 HPC 應用程式[進一步了解](https://azure.microsoft.com/en-us/solutions/architecture/hpc-cluster/)
* 建立混合式解決方案，擴充內部部署 HPC 叢集將尖峰工作負載卸載 (「高載」) 到 Azure VM [進一步了解](https://azure.microsoft.com/en-us/solutions/architecture/hpc-on-prem-burst/)
* 使用受控和可擴充的 Azure [Batch](https://azure.microsoft.com/services/batch/) 服務來執行需要大量運算的工作負載，而不需要管理基礎結構[進一步了解](https://azure.microsoft.com/en-us/solutions/architecture/hpc-big-compute-saas/)

## <a name="hpc-cluster-and-grid-solutions"></a>HPC 叢集和格線解決方案
將熟悉的 HPC 工作負載管理工具部署或擴充到 Azure 虛擬機器，併執行需要大量運算的工作負載。 選項包括開放原始碼工具，例如[Slurm 工作負載管理員](https://slurm.schedmd.com/)，以及來自 Microsoft 和其他發行者的叢集或格線管理員。

### <a name="linux-and-oss-cluster-solutions"></a>Linux 與 OSS 叢集解決方案
使用 Azure Resource Manager 範本，在虛擬機器或虛擬機器擴展集中部署 Linux HPC 叢集解決方案，包括：

* [Slurm](https://azure.microsoft.com/documentation/templates/slurm/)
* [Torque](https://azure.microsoft.com/documentation/templates/torque-cluster/)
* [PBS 專業人員](https://github.com/xpillons/azure-hpc/tree/master/Compute-Grid-Infra)

另請參閱[按 5 下範本](https://github.com/tanewill/5clickTemplates)集合。

### <a name="grid-managers"></a>格線管理員
Microsoft 合作夥伴與商業格線管理員，使其解決方案可在 Azure VM 中使用。 範例包括：

* [IBM Spectrum Symphony 和 Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Bright Cluster Manager](http://www.brightcomputing.com/technology-partners/microsoft)
* [Univa Grid Engine](http://www.univa.com/products/grid-engine)

### <a name="microsoft-hpc-pack"></a>Microsoft HPC Pack
[HPC Pack](https://technet.microsoft.com/library/cc514029(v=ws.11).aspx) 是建置在 Microsoft Azure 和 Windows Server 技術上的 Microsoft 免費 HPC 解決方案。 HPC Pack 提供數個選項，可在 [Windows](../articles/virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 和 [Linux](../articles/virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) VM 中執行。 

了解如何：

* [在 Azure 中建立 HPC Pack 2016 叢集](../articles/virtual-machines/windows/hpcpack-2016-cluster.md)
* [使用 HPC Pack 將高載工作負載移至 Azure 與](https://technet.microsoft.com/library/gg481749(v=ws.11).aspx)




## <a name="azure-batch"></a>Azure Batch
[Batch](../articles/batch/batch-technical-overview.md) 是一項平台服務，可用於在雲端有效地執行大規模的平行和高效能運算 (HPC) 應用程式。 Azure Batch 可排程要在受控集區虛擬機器上執行的需要大量運算工作，而且可以調整運算資源以符合工作的需求。 

使用 Batch SDK 和工具將 HPC 應用程式或容器工作負載與 Azure 進行整合、將資料暫存至 Azure，並建置作業執行管線。 您也可以[較低價格](https://azure.microsoft.com/pricing/details/batch/)使用 Surplus (低優先順序) Azure VM 容量，大幅減少在 Batch 中執行某些工作負載的成本。

了解如何：

* [開始使用 Batch 進行開發](../articles/batch/batch-dotnet-get-started.md)
* [使用 Azure Batch 程式碼範例](https://github.com/Azure/azure-batch-samples)
* [使用低優先順序的 VM 搭配 Batch (預覽)](../articles/batch/batch-low-pri-vms.md)
* [使用 Batch Shipyard 執行容器化的 HPC 工作負載](https://github.com/Azure/batch-shipyard)
* [使用 R 語言搭配 Batch](https://github.com/Azure/doAzureParallel)

## <a name="hpc-and-gpu-vm-sizes"></a>HPC 和 GPU VM 大小
Azure 提供一系列適用於 [Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 和 [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) VM 的大小，包括專為需要大量運算的工作負載所設計的大小。 例如，H16r 和 H16mr VM 可以連線到高輸送量後端 RDMA 網路。 此雲端網路可以改善 [Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) 或 Intel MPI 下執行的緊密結合平行應用程式效能。 N 系列 VM 功能 NVIDIA GPU 是專為需要大量運算或需要大量圖形的應用程式所設計，包括人工地智慧 (AI) 學習和視覺效果。 

深入了解：

* [Linux](../articles/virtual-machines/linux/sizes-hpc.md) 和 [Windows](../articles/virtual-machines/windows/sizes-hpc.md) VM 的高效能運算大小 
* [Linux](../articles/virtual-machines/linux/sizes-gpu.md) 和 [Windows](../articles/virtual-machines/windows/sizes-gpu.md) VM 的已啟用 GPU 功能之大小 

了解如何：

* [設定 Linux RDMA 叢集以執行 MPI 應用程式](../articles/virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [使用 Microsoft HPC Pack 設定 Windows RDMA 叢集以執行 MPI 應用程式](../articles/virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [在 Batch 集區中使用需要大量運算的 VM](../articles/batch/batch-pool-compute-intensive-sizes.md)

## <a name="hpc-images"></a>HPC 映像

請瀏覽 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) 以取得針對 HPC 設計的 Linux 和 Windows VM 映像。 範例包括：

* [RogueWave CentOS 型 HPC](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
* [適用於 HPC 的 SUSE Linux Enterprise Server](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
* [TIBCO Grid Server 6.2.0 Engine](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [適用於 Windows 和 Linux 的 Azure 資料科學 VM](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)


 
## <a name="hpc-applications"></a>HPC 應用程式

在 Azure 中執行自訂或商業 HPC 應用程式。 數個會加以基準測試，以使用其他 VM 或運算核心有效地進行擴充。 下列幾節為範例。

> [!IMPORTANT]
> 請向廠商確認任何商業應用程式在雲端中的執行授權或其他限制。 並非所有廠商都提供隨用隨付授權。 您可能需要視您的解決方案，在雲端中授權伺服器，或連線至內部部署授權伺服器。

### <a name="engineering-applications"></a>工程應用程式


* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB Distributed Computing Server](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM+](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



### <a name="graphics-and-rendering"></a>圖形和轉譯器

* Azure Batch 上的 [Autodesk Maya、3ds Max 和 Arnold](../articles/batch/batch-rendering-service.md) (預覽)

### <a name="ai-and-deep-learning"></a>AI 和深入學習

* [Microsoft 辨識工具組](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [Deep learning Toolkit for Data Science VM](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [深入學習的 Batch Shipyard 訣竅](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)

## <a name="solution-partners"></a>解決方案合作夥伴

在 Azure 中開發或提供批次和 HPC 應用程式的合作夥伴包括：

* [循環運算](https://cyclecomputing.com/) (現[已加入 Microsoft](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/))
* [Rescale](https://www.rescale.com/azure/)
* [UberCloud](https://www.theubercloud.com/)

## <a name="hpc-storage"></a>HPC 儲存體

大規模 Batch 和 HPC 工作負載所需要的資料儲存和存取會超過傳統雲端檔案系統的容量。 您可以在 Azure 中實作平行的檔案系統方案，例如 [Lustre](http://lustre.org/) 和 [BeeGFS](http://www.beegfs.com/content/)。

深入了解：

* [Azure 上 HPC 儲存體的平行檔案系統 (英文)](https://blogs.msdn.microsoft.com/azurecat/2017/03/17/parallel-file-systems-for-hpc-storage-on-azure/)


## <a name="related-azure-services"></a>相關的 Azure 服務

Azure 虛擬機器、虛擬機器擴展集、Batch 和相關的運算服務是大部分 Azure HPC 解決方案的基礎。 不過，您的解決方案可以利用許多相關的 Azure 服務。 部份清單如下：

### <a name="storage"></a>儲存體

* [Blob、資料表和佇列儲存體](../articles/storage/storage-introduction.md)
* [檔案儲存體](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>資料與分析
* Azure 上適用於 Hadoop 叢集的 [HDInsight](../articles/hdinsight/hdinsight-hadoop-introduction.md)
* [Data Factory](../articles/data-factory/data-factory-introduction.md)
* [Data Lake Store](../articles/data-lake-store/data-lake-store-overview.md)
* [Machine Learning](../articles/machine-learning/machine-learning-what-is-machine-learning.md)
* [SQL Database](../articles/sql-database/sql-database-technical-overview.md)

### <a name="networking"></a>網路
* [虛擬網路](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>容器
* [容器服務](../articles/container-service/dcos-swarm/container-service-intro.md)
* [容器登錄](../articles/container-registry/container-registry-intro.md)



## <a name="customer-stories"></a>客戶案例

以下範例中的客戶已使用 Azure HPC 解決方案來解決商務問題：

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA Global P&C](https://customers.microsoft.com/story/axa-global-p-and-c)
* [Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
* [d3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
* [Hymans Robertson](https://customers.microsoft.com/story/hymans-robertson)
* [MetLife](https://enterprise.microsoft.com/en-us/customer-story/industries/insurance/metlife/)
* [Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
* [明德精算顧問有限公司](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)


## <a name="next-steps"></a>後續步驟
* 深入了解適用於[工程模擬](https://simulation.azure.com/)、[轉譯器](https://simulation.azure.com/)和[銀行與資本市場](https://finance.azure.com/)的 Big Compute 解決方案。
* 如需最新公告，請參閱 [Microsoft HPC 和 Batch 小組部落格](http://blogs.technet.com/b/windowshpc/)以及[Azure 部落格](https://azure.microsoft.com/blog/tag/hpc/)。


