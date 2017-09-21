# 概觀
## [關於虛擬機器](../../virtual-machines-windows-about.md)
## [磁碟與 VHD](../about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
## [虛擬網路](../../../virtual-network/virtual-networks-overview.md)
## [常見問題集](faq.md)
## [比較 VM、網站及雲端服務](../../../app-service/choose-web-site-cloud-service-vm.md)
## [容器](../../virtual-machines-windows-containers.md)

# 開始使用
## [使用入口網站建立 VM](tutorial.md)
## [登入 VM](connect-logon.md)
## [安裝 Azure PowerShell](/powershell/azure/overview)
## [安裝 Azure CLI](../../../cli-install-nodejs.md)

# 作法

## 使用儲存體
### [連接資料磁碟](attach-disk.md)
### [卸離資料磁碟](detach-disk.md)
### [使用 D: 作為資料磁碟](../../virtual-machines-windows-change-drive-letter.md)

## 網路
### [如何設定端點](setup-endpoints.md)
### [連接 VM 與 VNet 或雲端服務](connect-vms.md)
### [將傳統 VNet 連接到 Resource Manager VNet](../../../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
### [建立負載平衡器](../../../load-balancer/load-balancer-get-started-internet-classic-portal.md)
### [使用 Azure PowerShell 管理 NSG](../../../virtual-network/virtual-networks-create-nsg-classic-ps.md)

## 部署
### [建立自訂 VM](createportal.md)
### [使用 Azure PowerShell 建立及設定 VM](create-powershell.md)
### [擷取 Windows VM](capture-image.md)
### [使用 Azure PowerShell 建立和上傳 VHD](createupload-vhd.md)
### [使用 Chef 自動進行 Azure VM 部署](../../virtual-machines-windows-chef-automation.md)
### [在 Visual Studio 中建立和管理 VM](manage-visual-studio.md)
### [使用 Visual Studio 建立 Web 應用程式的 VM](web-app-visual-studio.md)
### [以 Java 執行大量計算工作](java-run-compute-intensive-task.md)
### [Django Hello World Web 應用程式](python-django-web-app.md)

## 設定
### [重設密碼或遠端桌面服務](../../virtual-machines-windows-reset-rdp.md)
### [安裝和設定 Symantec Endpoint Protection](install-symantec.md)
### [安裝和設定 Trend Micro Deep Security as a Service](install-trend.md)
### [設定可用性設定組](configure-availability.md)
### [對以傳統部署模型建立的 Windows VM 調整大小](resize-vm.md)
### [維護](planned-maintenance-schedule.md)

## 管理
### [從傳統移轉至 Resource Manager](../../virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
### [使用 Azure PowerShell 管理您的 VM](manage-psh.md)
### [關於 VM 代理程式與擴充功能](agents-and-extensions.md)
### [管理 VM 擴充功能](manage-extensions.md)
### [VM 的自訂指令碼擴充功能](extensions-customscript.md)
### [將自訂資料插入 Azure VM](inject-custom-data.md)

## 規劃
### [關於映像](about-images.md)
### [VM 的大小](../../virtual-machines-windows-sizes.md)
### [Azure VM 預定進行的維修](../../virtual-machines-windows-planned-maintenance.md)
### [Azure 基礎結構服務實作指導方針](../../virtual-machines-windows-infrastructure-subscription-accounts-guidelines.md)

## 管理工作負載
### [高效能運算 (HPC)](../../virtual-machines-windows-hpcpack-cluster-options.md)
#### [自動調整資源](hpcpack-cluster-node-autogrowshrink.md)
#### [管理計算節點](hpcpack-cluster-node-manage.md)
#### [建立叢集](hpcpack-cluster-powershell-script.md)
#### [設定叢集以執行 MPI 應用程式](hpcpack-rdma-cluster.md)
#### [執行 Excel 和 SOA 工作負載](../../virtual-machines-windows-excel-cluster-hpcpack.md)
#### [使用 Marketplace 映像建立前端節點](../../virtual-machines-windows-hpcpack-cluster-headnode.md)
#### [將內部部署的作業提交至 Azure](../../virtual-machines-windows-hpcpack-cluster-submit-jobs.md)
### [MongoDB](install-mongodb.md)
### [MySQL](mysql-2008r2.md)
### [Oracle](../../workloads/oracle/oracle-considerations.md)
### [SAP](sap-get-started.md)
### [SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
### [Tomcat](java-run-tomcat-app-server.md)

## 疑難排解
### [遠端桌面連線](../../virtual-machines-windows-troubleshoot-rdp-connection.md)
####[遠端桌面連線問題的詳細疑難排解步驟](../../virtual-machines-windows-detailed-troubleshoot-rdp.md)
### [應用程式存取](../../virtual-machines-windows-troubleshoot-app-connection.md)
### [在傳統部署中建立新 VM 的問題](troubleshoot-deployment-new-vm.md)
### [在傳統部署中重新啟動或調整現有 VM 大小的問題](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
### [重設遠端桌面通訊協定 (RDP) 密碼](reset-rdp.md)
### [將虛擬硬碟附加至進行疑難排解的 VM](troubleshoot-recovery-disks-portal.md)

# 參考
## [PowerShell](/powershell/azure/overview)
## [Azure CLI](/cli/azure/vm)
## [Java](/java/api)
## [.NET](/dotnet/api/microsoft.azure.management.compute)
## [製作 Resource Manager 範本](../../../resource-group-authoring-templates.md)
## [社群範本](https://azure.microsoft.com/documentation/templates)
## [計算 REST](/rest/api/compute)
## [網路 REST](/rest/api)
## [儲存體 REST](/rest/api/storageservices)

# 資源
## [Azure 藍圖](https://azure.microsoft.com/roadmap/?category=compute)
## [價格](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)
## [定價計算機](https://azure.microsoft.com/pricing/calculator/)
## [區域可用性](https://azure.microsoft.com/regions/services/)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-machine)
## [影片](https://azure.microsoft.com/documentation/videos/index/?services=virtual-machines)
