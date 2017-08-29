# 概觀
## [監視 Azure 中的工具](monitoring-overview.md)
## [Azure 監視器](monitoring-overview-azure-monitor.md)
## [計量](monitoring-overview-metrics.md)
## [警示](monitoring-overview-alerts.md)
## [Autoscale](monitoring-overview-autoscale.md)
## [活動記錄檔](monitoring-overview-activity-logs.md)
## [動作群組](monitoring-action-groups.md)
## [診斷記錄檔](monitoring-overview-of-diagnostic-logs.md)
## [合作夥伴整合](monitoring-partners.md)
## [Azure 診斷擴充功能](azure-diagnostics.md)


# 開始使用
## [開始使用 Azure 監視器](monitoring-get-started.md)
## [開始使用自動調整](monitoring-autoscale-get-started.md)
## [角色權限與安全性](monitoring-roles-permissions-security.md)


# 作法
## 使用警示
### [在 Azure 入口網站中設定警示](insights-alerts-portal.md)
### [使用 CLI 設定警示](insights-alerts-command-line-interface.md)
### [使用 PowerShell 設定警示](insights-alerts-powershell.md)
### [讓計量警示呼叫 Webhook](insights-webhooks-alerts.md)
### [使用 Resource Manager 範本建立度量警示](monitoring-enable-alerts-using-template.md)
## 使用自動調整
### [最佳做法](insights-autoscale-best-practices.md)
### [常見計量](insights-autoscale-common-metrics.md)
### [常見模式](monitoring-autoscale-common-scale-patterns.md)
### [使用自訂度量進行自動調整](monitoring-autoscale-scale-by-custom-metric.md)
### [使用 Resource Manager 範本自動調整 VM 擴展集](insights-advanced-autoscale-virtual-machine-scale-sets.md)
### [在 VM 擴展集內自動縮放機器](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
### [設定自動調整規模的 Webhook 與電子郵件通知](insights-autoscale-to-webhook-email.md)
## 使用活動記錄
### [檢視活動記錄中的事件](../azure-resource-manager/resource-group-audit.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
### [在活動記錄事件中設定警示](monitoring-activity-log-alerts.md)
### [封存活動記錄](monitoring-archive-activity-log.md)
### [將活動記錄串流至事件中樞](monitoring-stream-activity-logs-event-hubs.md)
### [使用 Resource Manager 來稽核作業](../azure-resource-manager/resource-group-audit.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
### [透過 Resource Manager 建立活動記錄警示](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
### [移轉至活動記錄警示](monitoring-migrate-management-alerts.md)
## 使用服務通知
### [檢視服務通知](monitoring-service-notifications.md)
### [在服務通知上設定警示](monitoring-activity-log-alerts-on-service-notifications.md)
## 使用動作群組
### [深入了解 webhook 結構描述](monitoring-activity-log-alerts-webhook.md)
### [SMS 警示行為](monitoring-sms-alert-behavior.md)
### [警示速率限制](monitoring-alerts-rate-limiting.md)
### [透過 Resource Manager 建立動作群組](monitoring-create-action-group-with-resource-manager-template.md)
## 管理診斷記錄檔
### [封存](monitoring-archive-diagnostic-logs.md)
### [串流至事件中樞](monitoring-stream-diagnostic-logs-to-event-hubs.md)
### [使用 Resource Manager 範本啟用診斷設定](monitoring-enable-diagnostic-logs-using-template.md)
## 使用 REST API
### [逐步解說如何使用 REST API](monitoring-rest-api-walkthrough.md)
## 使用 Azure 診斷擴充功能
### [傳送至 Application Insights](azure-diagnostics-configure-application-insights.md)
### [送至事件中樞](azure-diagnostics-streaming-event-hubs.md)
### [疑難排解](azure-diagnostics-troubleshooting.md)

# 參考
## [程式碼範例](https://azure.microsoft.com/en-us/resources/samples/?service=monitor)
## [監視資料的來源](monitoring-data-sources.md)
## [支援的計量清單](monitoring-supported-metrics.md)
## [活動記錄檔事件結構描述](monitoring-activity-log-schema.md)
## [診斷記錄支援的服務、類別和結構描述](monitoring-diagnostic-logs-schema.md)
## [PowerShell](/powershell/module/azurerm.insights)
## [.NET](https://msdn.microsoft.com/library/azure/dn802153)
## [REST](/rest/api/monitor/)
## [Azure 診斷延伸結構描述](azure-diagnostics-schema.md)
### [1.0](azure-diagnostics-schema-1dot0.md)
### [1.2](azure-diagnostics-schema-1dot2.md)
### [1.3 和更新版本](azure-diagnostics-schema-1dot3-and-later.md)

# 資源
## [Azure CLI 1.0 範例](insights-cli-samples.md)
## [Azure 藍圖](https://azure.microsoft.com/roadmap/?category=monitoring-management)
## [PowerShell 範例](insights-powershell-samples.md)
## [定價計算機](https://azure.microsoft.com/pricing/calculator/)
## [影片](https://azure.microsoft.com/resources/videos/index/?services=monitor)
## [快速入門範本](https://azure.microsoft.com/en-us/resources/templates/?resourceType=Microsoft.Insights)
